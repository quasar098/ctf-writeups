# gcd-query-v2

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/0206b2fe-2b9b-42bd-833b-7321d400315b)

main.py
```py
#!/usr/local/bin/python
from flag import flag
from math import gcd
from Crypto.Util.number import getRandomInteger

x = getRandomInteger(128)
for i in range(16):
    try:
        n, m = map(int, input("n m: ").split())
        assert m > 0
        print(gcd(x + n, m))
    except:
        print("bad input. you *die*")
        exit(0)
if int(input("x: ")) == x:
    print(flag)
else:
    print("get better lol")
```

## solution

same thing as gcd-query-v1 but smaller numbers, fewer queries

solve.py
```py
from pwn import *
from Crypto.Util.number import isPrime
from sympy.ntheory.modular import crt

# p = process(["python3", "gcdquery2.py"])
p = remote("amt.rs", 31693)


def query(n, m):
    p.sendline(f"{n} {m}".encode('ascii'))
    return int(p.recvline()[5:-1].decode('ascii'))


nbits = 128
killer = 1
for _ in range(2, 300):
    if isPrime(_):
        killer *= _
killer = killer ** 7
thing = 1

apple = 0
for _ in range(16):
    r = query(apple, killer)
    before_apple = apple
    apple += r
    # print(f"{_+1}\t{before_apple}\t{apple}\t{r}\t{len(bin(apple))} bits")
    if len(bin(apple))-2 == nbits:
        print(apple-before_apple*2)
p.interactive()
```

naming variables is hard, ok?
