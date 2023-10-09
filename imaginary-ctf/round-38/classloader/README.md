# classloader

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/f00455be-ee03-41e3-bd76-c99280d00dda)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/9707bf25-16e2-4a36-b4ba-a36131fcc0ff)

source.py
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
    default_limits=["5000 per hour"],
    storage_uri="memory://",
)

@app.route('/', methods=["GET"])
@limiter.limit("5/second")
def index():
    if "source" in request.args:
        return Response(open(__file__).read(), mimetype='text/plain')
    if "dockerfile" in request.args:
        return Response(open('Dockerfile').read(), mimetype='text/plain')
    return open("index.html").read()

@app.route('/java', methods=["POST"])
@limiter.limit("1/second")
def java():
    java_class = request.form['class']
    proc = run(['java', '-jar', 'challenge.jar', java_class], capture_output=True, timeout=1)
    return {'stdout': proc.stdout.decode(), 'stderr': proc.stderr.decode()}

if __name__ == "__main__":
    app.run('0.0.0.0', 5000)
```

Dockerfile
```dockerfile
FROM amazoncorretto:20-alpine

ENV PYTHONUNBUFFERED=1
RUN apk add --update --no-cache python3 && ln -sf python3 /usr/bin/python
RUN python3 -m ensurepip
RUN pip3 install --no-cache --upgrade pip setuptools

WORKDIR /app 

COPY Dockerfile .
COPY requirements.txt .
RUN pip3 install -r requirements.txt 

COPY challenge.jar .

COPY index.html .
COPY app.py .

CMD python3 app.py
```

## solution

here, we dont know the challenge.jar source code, but we can side channel this (i think that is what it is called)

input: `2`

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/dfbfff5e-6db8-48c9-bf4c-cbc3de8d2cb2)

it says that the class name must contain a `.`, so i put in a `.`

input: `Test.2`

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/3379a163-b637-46a6-9d93-dca70ce535b6)

then, it says invalid base 64, so i put it some base64

input: `Test.dGVzdA==`

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/7d021b19-e397-43a5-a9b2-636451b5ea5e)

then it says truncated class file, so i assume that the base64 encoded thing has to be a class.

i then put in a compiled java class file which has an empty class

```java
public class Hack { }
```

input: `Hack.yv66vgAAADQADQoAAwAKBwALBwAMAQAGPGluaXQ+AQADKClWAQAEQ29kZQEAD0xpbmVOdW1iZXJUYWJsZQEAClNvdXJjZUZpbGUBAAlIYWNrLmphdmEMAAQABQEABEhhY2sBABBqYXZhL2xhbmcvT2JqZWN0ACEAAgADAAAAAAABAAEABAAFAAEABgAAAB0AAQABAAAABSq3AAGxAAAAAQAHAAAABgABAAAAAQABAAgAAAACAAk=`

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/14001a1f-233c-477d-9304-6704fe2073fb)

so it looks like we have to have a `printFlag(String flag)` method

now,

```java
public class Test {
    public static void printFlag(String test) {
        System.out.println(test);
    }
}
```

input: `Test.yv66vgAAADQAGgoABQAOCQAPABAKABEAEgcAEwcAFAEABjxpbml0PgEAAygpVgEABENvZGUBAA9MaW5lTnVtYmVyVGFibGUBAAlwcmludEZsYWcBABUoTGphdmEvbGFuZy9TdHJpbmc7KVYBAApTb3VyY2VGaWxlAQAJVGVzdC5qYXZhDAAGAAcHABUMABYAFwcAGAwAGQALAQAEVGVzdAEAEGphdmEvbGFuZy9PYmplY3QBABBqYXZhL2xhbmcvU3lzdGVtAQADb3V0AQAVTGphdmEvaW8vUHJpbnRTdHJlYW07AQATamF2YS9pby9QcmludFN0cmVhbQEAB3ByaW50bG4AIQAEAAUAAAAAAAIAAQAGAAcAAQAIAAAAHQABAAEAAAAFKrcAAbEAAAABAAkAAAAGAAEAAAABAAkACgALAAEACAAAACQAAgABAAAACLIAAiq2AAOxAAAAAQAJAAAACgACAAAAAwAHAAQAAQAMAAAAAgAN`

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/23eede61-c65b-459d-87a5-5bfe26ee4a9d)
