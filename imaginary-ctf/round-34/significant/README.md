# significant

## problem

```py
#!/usr/bin/env python3

from Crypto.Util.number import *
from threading import Thread
from time import time, sleep

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


admin_users_init = ["Astro", "Eth007", "Ciaran", "maple3142", "Robin_Jadoul", "tirefire"]
users_db_init = ["boringuser"] + admin_users_init
RESET_DELAY = 600

def reset():
    global users_db, admin_users, p, q, n, phi, e, d
    users_db = list(users_db_init)
    admin_users = set(admin_users_init)
    p = getPrime(1024)
    q = getPrime(1024)
    n = p * q
    phi = (p - 1) * (q - 1)
    e = 17
    if phi % e == 0:
        return
    d = pow(e, -1, phi)

class Restart(Thread):
    def run(self):
        global reset_time
        while True:
            reset()
            while phi % e == 0:
                reset()
            reset_time = time() + RESET_DELAY
            sleep(RESET_DELAY)
        
Restart().start()

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

@app.route('/reset_time', methods=["GET"])
@limiter.limit("2/second")
def reset_time_endpoint():
    return f"Resetting in {int(reset_time - time())} seconds"

@app.route('/user/<path:user_id>', methods=["GET"])
@limiter.limit("2/second")
def view_user(user_id):
    global users_db
    if int(user_id) > len(users_db):
        return "This user doesn't exist!"
    username = users_db[int(user_id) - 1]
    ret = f'''<h1>{username}</h1>
    You are {"not " if username != current_user() else ""}this user.'''
    if username == current_user() and username in admin_users:
        ret += f" {open('flag.txt').read()}"
    return ret

if __name__ == "__main__":
    app.run('0.0.0.0', 13003)
```

## solution

see the insignificant challenge before this one to understand

here, there is no vulnerability of `1^x=1` given `x!=0`. however, what we can do is set the user id to 0 because `0^x=0` given `x!=0`<br>
now, `users_db[0 - 1] == users_db[-1]` which is tirefire assuming no other users have been created. luckily, the users_db resets every 600 seconds, so it is very easy.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/8f1f4fcc-9d4f-49a4-91b9-a2506ddfa1bc)
