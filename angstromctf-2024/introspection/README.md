# introspection

## challenge

challenge.py
```py
#!/usr/local/bin/python
import subprocess
import sys

pkl = raw_input("Enter your introspective pickle: ")

pickle_result = subprocess.check_output([sys.executable, "unpickle.py", pkl])
cpickle_result = subprocess.check_output([sys.executable, "cunpickle.py", pkl])

print open("flag.txt").read().strip() if "pickle" in pickle_result and "cPickle" not in pickle_result and "cPickle" in cpickle_result and "pickle" not in cpickle_result else "You have much to learn."
```

unpickle.py
```py
#!/usr/local/bin/python
import pickle
import io
import base64
import sys

WiseUnpickler = pickle.Unpickler(io.BytesIO(base64.b64decode(sys.argv[1])))
WiseUnpickler.find_class = None

try: print WiseUnpickler.load()
except: pass
```

cunpickle.py
```py
#!/usr/local/bin/python
import cPickle
import io
import base64
import sys

cWiseUnpickler = cPickle.Unpickler(io.BytesIO(base64.b64decode(sys.argv[1])))
cWiseUnpickler.find_global = None

try: print cWiseUnpickler.load()
except: pass
```

Dockerfile
```dockerfile
FROM pwn.red/jail

COPY --from=python:2-slim-buster / /srv
COPY --chmod=755 introspection.py /srv/app/run
COPY cunpickle.py unpickle.py flag.txt /srv/app/
```

## solution

first off, this challenge was done on python2 (hence the print statement with no parens)

essentially, we had to make a pickle that when unpickled using the python implementation, returns `"pickle"`, but when unpickled using the c implementation of pickle, returns `"cPickle"`

if we look at the python docs for pickle ([here](https://docs.python.org/2/library/pickle.html))

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/86384141-2d08-499e-8ce4-16fca269b488)

little does anyone know, their differences are NOT pointed out where necessary. there are two majors ones, which i will describe below

we can start by opening up the [c implementation of pickle in python2](https://github.com/python/cpython/blob/2.7/Modules/cPickle.c) and the [python implementation of pickle in python2](https://github.com/python/cpython/blob/2.7/Lib/pickle.py)

the unpickling class is a stack based virtual machine that has a bunch of opcodes listed on line 102 of pickle.py.

one such opcode is the `INT` opcode, which is implemented as below in python

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/5c9a20cc-d35e-4fa4-b06f-58fc590c8011)

the c implementation is below

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/c9e628c0-9dbf-4d21-8a98-aee836c1a087)

do you see any differences?

well, here, you can see that the python implementation uses `int()` and `long()` as backup, but the c implementation uses a sort of `long()` primarily

we can abuse this by putting in octals like `010` which is `8` in octal but `10` but in long

alternatively, we can exploit a discrepancy in the long_binget opcode, which is definitely the easiest way to solve this challenge

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/b54332c3-7508-49e0-8935-6e140ecd97e7)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/508ec5b6-08f4-40ec-a93c-71f14aec6050)

we can exploit the fact that the `mloads` will return a signed integer.

so, we can see below that it prints something different

```
#!/usr/bin/python2.7

import pickle
import io
from pickle import *
import cPickle

p = PROTO + '\x02'
p += STRING + '"cPickle"\n'
p += PUT + b'-1\n'
p += STRING + b'"pickle"\n'
# memo is {'-1': "cPickle"}, stack has "pickle" at the top

p += LONG_BINPUT + '\xff\xff\xff\xff'  # pickle will recognize this as -1, cPickle will recognize this as 4294967295
# cPickle will have memo as {'-1': "cPickle", '4294967295': "pickle"}
# pickle will have memo as {'-1': "pickle"}
p += GET + b'-1\n'
# we return what is at -1 in memo

p += STOP

print(p)

cWiseUnpickler = cPickle.Unpickler(io.BytesIO(p))
cWiseUnpickler.find_global = None
print(cWiseUnpickler.load(), cWiseUnpickler.memo)

WiseUnpickler = pickle.Unpickler(io.BytesIO(p))
WiseUnpickler.find_class = None
print(WiseUnpickler.load(), WiseUnpickler.memo)
```
