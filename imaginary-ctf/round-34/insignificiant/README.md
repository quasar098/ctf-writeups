# insignificant

## problem

source code:

```py
#!/usr/bin/env python3

from Crypto.Util.number import *

from flask import Flask, request, Response, redirect
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address
app = Flask(__name__)
limiter = Limiter(
    get_remote_address,
    app=app,
    default_limits=["50000 per hour"],
    storage_uri="memory://",
)

users_db = ["admin"]
p = getPrime(1024)
q = getPrime(1024)
n = p * q
phi = (p - 1) * (q - 1)
e = 65537
d = pow(e, -1, phi)

def sign(uid):
    return str(pow(uid, d, n))

def current_user():
    uid = int(request.cookies.get("user"))
    idx = pow(uid, e, n)
    return users_db[idx - 1]

@app.route('/', methods=["GET"])
@limiter.limit("2/second")
def index():
    if "source" in request.args:
        return Response(open(__file__).read(), mimetype='text/plain')
    return open("index.html").read()

@app.route('/create', methods=["POST"])
@limiter.limit("2/second")
def create():
    global users_db
    username = request.form['username']
    if username in users_db:
        return "That user already exists!"
    users_db.append(username)
    user_id = len(users_db)
    cookie = sign(user_id)
    ret = redirect(f'/user/{user_id}')
    ret.set_cookie("user", cookie)
    return ret

@app.route('/user/<path:user_id>', methods=["GET"])
@limiter.limit("2/second")
def view_user(user_id):
    global users_db
    username = users_db[int(user_id) - 1]
    ret = f'''<h1>{username}</h1>
    You are {"not " if username != current_user() else ""} this user.'''
    if username == "admin" and current_user() == "admin":
        ret += f" {open('flag.txt').read()}"
    return ret

if __name__ == "__main__":
    app.run('0.0.0.0', 13001)
```

## solution

here, we can see that the user has to be admin to get the cookie. the admin is the only one<br>

because of this:
```py
def current_user():
    uid = int(request.cookies.get("user"))
    idx = pow(uid, e, n)
    return users_db[idx - 1]
```

we can see that the cookie `user` has to be an integer, uid, to the power of e, and then modulo'd by n.

for value `admin` to be returned by this function, the variable idx has to be `1` because `users_db[1 - 1]` is admin.

there is a problem here, because `1^x` is always 1 unless x is 0 (which it isn't). therefore, a user cookie of 1 does the trick.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/6c53e33a-5545-4fd7-91f3-5385efdbe7e4)
