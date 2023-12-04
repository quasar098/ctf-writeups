# myjail

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/91fe52c8-973b-4cc3-b683-57f4d56e9480)

chall.py
```py
#!/usr/bin/env python3
# Thanks CSAW2023 for the inspiration
import ast
import sys

BANNED = {
    #def not
    ast.Import,
    ast.ImportFrom,
    ast.With,
    ast.alias,
    ast.Attribute,
    ast.Constant,

    #should be fine?
    ast.Subscript,
    ast.Assign,
    ast.AnnAssign,
    ast.AugAssign,
    ast.For,
    ast.Try,
    ast.ExceptHandler,
    ast.With,
    ast.withitem,
    ast.FunctionDef,
    ast.Lambda,
    ast.ClassDef,


    #prob overkill but should be fine
    ast.If,
    ast.And,
    ast.comprehension,
    ast.In,
    ast.Await,
    ast.Global,
    ast.Gt,
    ast.ListComp,
    ast.Slice,
    ast.Return,
    ast.List,
    ast.Dict,
    ast.Lt,
    ast.AsyncFunctionDef,
    ast.Eq,
    ast.keyword,
    ast.Mult,
    ast.arguments,
    ast.FormattedValue,
    ast.Not,
    ast.BoolOp,
    ast.Or,
    ast.Compare,
    ast.GtE,
    ast.ImportFrom,
    ast.Tuple,
    ast.NotEq,
    ast.IfExp,
    ast.alias,
    ast.arg,
    ast.JoinedStr,

    # Patch some more >:)
    ast.Match, 
    ast.Del,
    ast.Starred,
    ast.Is,
    ast.NamedExpr,
}

def hook(event, args):
    if not hook.exec and 'exec' in event:
        hook.exec = True
        return

    strr = event + " ".join(f"{x}" for x in args)
    strr = strr.lower()
    if any(i in strr for i in [
        'exec',
        'print',
        'import', 
        'system', 
        'flag', 
        'spawn',
        'fork', 
        'open', 
        'subprocess', 
        'sys',
        'ast', 
        'os',
        'audit',
        'hook'
        'compile',
        '__new__',
        'frame']):
        print("BONK audit!", event + " " + " ".join(f"{x}" for x in args))
        exit()

    # print("audit!", event + " " + " ".join(f"{x}" for x in args))
    

hook.exec = False

def banner():
    print("Hello world! Please input your program here: ")

    code = input()

    for n in ast.walk(ast.parse(code)):
        if type(n) in BANNED:
            print("BAD CODE! BONK!: " + str(type(n)))
            exit()
    
    return code

code = banner()
code = compile(code, "<code>", "exec")

safer_builtins = __builtins__.__dict__.copy()
banned_builtins = [
    'exec',
    'eval',
    'compile',
    'type',
    'globals',
    'dir',
    'callable',
    'type',
    'all',
    'any',
    'int',
    'input',
    'breakpoint',
    'print',
    'quit',
    'exit',
    'copyright',
    'credits',
    'license',
    'help',
]
for banned in banned_builtins:
    safer_builtins.pop(banned, None)

# safer 
sys.addaudithook(hook)

exec(code, {"__builtins__": safer_builtins})
```

Dockerfile not needed to show here but the version is python 3.10

## solution

firstly, only a few things are allowed to pass through the ast parser.

i checked the `ast.*` attributes and filtered out the blocked ones and some misc ones

```
'Add', 'Assert', 'AsyncFor', 'AsyncWith', 'BinOp', 'BitAnd', 'BitOr', 'BitXor', 'Break', 'Bytes', 'Call', 'Continue', 'Div', 'ExtSlice', 'FloorDiv', 'GeneratorExp', 'Index', 'IntEnum',
'Invert', 'IsNot', 'LShift', 'Load', 'LtE', 'MatMult', 'MatchAs', 'MatchClass', 'MatchMapping', 'MatchOr', 'MatchSequence', 'MatchSingleton', 'MatchStar', 'MatchValue', 'Mod', 'Name',
'NameConstant','Nonlocal', 'NotIn', 'Param', 'Pass', 'Pow', 'RShift', 'Raise', 'Set', 'Store', 'Str', 'Sub', 'Suite', 'TypeIgnore', 'UAdd', 'USub', 'UnaryOp', 'While', 'Yield', 'YieldFrom'
```

so, function calls are allowed, and identifiers/names are allowed.

therefore, we can call functions like `chr`, `getattr`, `setattr`, `len`, etc.

notably, we also have access to unary tilde operator `~n`. also we have access to unary minus operator `-n`.

interestingly, we cant get any raw numbers because they are filtered as `ast.Constant`. but, we can get 0 from `len(tuple())` because it creates an empty tuple and gets the length of it

to get 1 from 0, we can use the tilde operator to transform `0` into `-1` because it flips all the bits in the number. then, we can negate it to get positive `1`. therefore, we can get arbitrary numbers by simply adding the 1s up

in theory this works, but i had this issue where part of the input would get cut off to 4095 characters.

it turns out that there is some problem where the stdin buffer to a python process from the terminal was limited to length 4096 including the null terminator.

so, instead i used a pipe command (`python3 gen.py && { cat out.txt; cat; } | nc chal.nbctf.com 3038`)

the `gen.py` is my final script.

however, because i thought that the 4095 byte limit was somehow a bug in the ast.parse (that's where i got a "statement not valid" error), i thought it was part of the challenge.

so, i tried compressing it down using bit shifting and addition

```py
from math import log2
one = "len({id})"  # alternative compact way to get one by using a set (it is not parsed as ast.Constant)

def get_num(num: int):
    raw_steps = []  # "shift" or "add"
    big = int(log2(num))
    for i in range(big, -1, -1):
        raw_steps.append("shift")
        if num & (1 << i):
            raw_steps.append("add")
    raw_steps = raw_steps[1:]
    steps = []
    shift_amt = 0
    for step in raw_steps:
        if step == "add":
            if shift_amt:
                steps.append(("shift", shift_amt))
                shift_amt = 0
            steps.append(("add", 1))
        else:
            shift_amt += 1
    if shift_amt:
        steps.append(("shift", shift_amt))
    steps = steps[1:]
    cmd = "-~len(tuple())"
    # print(steps)
    for name, amount in steps:
        if name == "shift":
            cmd = f"({cmd})<<({'+'.join([one]*amount)})"
        else:
            cmd = f"({cmd})+{one}"
    return cmd
```

this algorithm gets a number in a very compressed format by using bit shifting and `-~len(tuple())`.


