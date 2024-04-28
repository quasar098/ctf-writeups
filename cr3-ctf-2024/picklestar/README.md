# picklestar

only 6 solves for some reason even though this was a super easy pickle chal

## problem

challenge.py
```py
import pickle
import pickletools

from blacklist import BLACKLISTED_OPCODES
from typing import List


p = bytes.fromhex(input('Gimme some pickles in hex: '))
if len(p) > 25:
    print("That's too much!")
    exit()


def get_ops() -> List[str]:
    try:
        for x in pickletools.genops(p):
            yield x[0].name
    except:
        pass


try:
    if any(p.count(x.encode()) > 0 for x in [
        'os',
        'system',
        'sys',
        'exec',
        'subprocess',
    ]):
        print('Are you trying to hack me? I thought we were friends!')
        exit()

    opcodes = list(get_ops())

    if len(opcodes) > 3:
        print("That's too much!")
        exit()

    if any(x in BLACKLISTED_OPCODES for x in opcodes):
        print('Forbidden pickle!!')
        exit()
except Exception as e:
    print("That doesn't look like a pickle to me!")
    exit()

print('Here you go:', pickle.loads(p))
```

blacklist.py
```py
BLACKLISTED_OPCODES = [
    'POP',
    'POP_MARK',
    'DUP',
    'FLOAT',
    'INT',
    'BININT',
    'BININT1',
    'LONG',
    'BININT2',
    'NONE',
    'PERSID',
    'BINPERSID',
    'REDUCE',
    'STRING',
    'BINSTRING',
    'BINUNICODE',
    'APPEND',
    'BUILD',
    'DICT',
    'EMPTY_DICT',
    'APPENDS',
    'GET',
    'BINGET',
    'LONG_BINGET',
    'LIST',
    'EMPTY_LIST',
    'PUT',
    'BINPUT',
    'LONG_BINPUT',
    'SETITEM',
    'TUPLE',
    'EMPTY_TUPLE',
    'SETITEMS',
    'BINFLOAT',
    'TRUE',
    'FALSE',
    'PROTO',
    'NEWOBJ',
    'EXT1',
    'EXT2',
    'EXT4',
    'TUPLE1',
    'TUPLE2',
    'TUPLE3',
    'NEWTRUE',
    'NEWFALSE',
    'LONG1',
    'LONG4',
    '_tuplesize2code',
    'BINBYTES',
    'SHORT_BINBYTES',
    'BINUNICODE8',
    'BINBYTES8',
    'EMPTY_SET',
    'ADDITEMS',
    'FROZENSET',
    'NEWOBJ_EX',
    'STACK_GLOBAL',
    'MEMOIZE',
    'FRAME',
    'BYTEARRAY8',
    'NEXT_BUFFER',
    'READONLY_BUFFER',
    'SHORT_BINSTRING',
    'SHORT_BINUNICODE',
    'UNICODE',
]
```

## solution

there are some restrictions on what we can pass into `pickle.loads`

1) less than 25 bytes payload
2) cannot contain certain strings like `"os"` and `"exec"`
3) max 3 opcodes
4) no use of blacklisted opcode names allowed

i checked what opcodes were allowed by diffing the list of all opcodes in pickle against the blacklist and found that the only opcodes that are allowed are `GLOBAL`, `INST`, `MARK`, `OBJ`, and `STOP`

obviously, we don't need `STOP` if we achieve RCE before the end of the pickle is reached

what we can do is use `INST`. `load_inst` is shown below.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/21aa1ee8-bfda-40ee-bb9b-a4fd64523bd6)

we can just use the builtins module to call `builtins.breakpoint`

we need a mark before that though (there are no arguments for breakpoint btw)

so we can just do `pickle.MARK + pickle.INST + b'builtins\nbreakpoint\n'` as out payload and then win
