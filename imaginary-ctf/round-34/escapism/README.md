# escapism

## problem

source code:

```py
#!/usr/bin/env python3

from sqlite3 import *
from os import system
from flask import Flask, request, Response
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address
app = Flask(__name__)
limiter = Limiter(
    get_remote_address,
    app=app,
    default_limits=["50000 per hour"],
    storage_uri="memory://",
)

@app.route('/', methods=["GET"])
@limiter.limit("5/second")
def index():
    if 'lastname' not in request.args:
        return open("index.html").read()
    else:
        con = connect("users.db")
        cur = con.cursor()
        safe_lastname = request.args['lastname'].replace("'", "\\'")
        res = cur.execute(
            f"SELECT * from authors where last = '{safe_lastname}'"
        )
        ret = '<table>'
        ret += '<tr><th>First Name</th> <th>Middle Name</th> <th>Last Name</th></tr>'
        for row in res:
            ret += '<tr>'
            for entry in row:
                ret += f"<td>{entry}</td>"
            ret += '</tr>'
        ret += '</table>'
        return ret

@app.route('/source')
@limiter.limit("5/second")
def source():
    return Response(open(__file__).read(), mimetype="text/plain")

if __name__ == "__main__":
    system("rm -f users.db")
    con = connect("users.db")
    cur = con.cursor()
    cur.execute('''
        CREATE TABLE authors(first, middle, last)
    ''')
    cur.execute(f'''
        INSERT INTO authors VALUES
            ("Tobias", '', "Smollett"),
            ("William", '', "Shakespeare"),
            ("Edward", "Morgan", "Forster"),
            ("George", "", "Eliot"),
            ("Louisa", "May", "Alcott"),
            ("Lucy", "Maud", "Montgomery"),
            ("Frank", "T.", "Merill"),
            ("Herman", "", "Melville"),
            ("Alexandre", "", "Dumas"),
            ("Elizabeth", "", "Von Arnim")
    ''')
    cur.execute('''
        CREATE TABLE users(username, password)
    ''')
    cur.execute(f'''
        INSERT INTO users VALUES
            ("admin", "{open("flag.txt").read()}")
    ''')
    con.commit()
    cur.close()
    con.close()

    app.run('0.0.0.0', 13000)
```

## problem

after taking a look, we can see that this is vulnerable to sql injection.<br>
the backslash does nothing because `\\'` in python is handled later as `\'` by sql which equals just a regular `'` (i think)

anyways, i first tried using one sql statement, but python's `cur.execute` does not allow multiple statements

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/4e8d3ba2-7001-4f30-920b-4699df3b62f8)

therefore, we have to use a union statement.

at first, i tried `' UNION username, password from users`, but that doesn't work because both union sections have to have same number of columns.

we can add a null and the SQL query is now `' UNION select username, password, null from users --`

we get the flag as `ictf{what_do_you_mean_I_have_to_test_my_code_to_make_sure_it_works?}`
