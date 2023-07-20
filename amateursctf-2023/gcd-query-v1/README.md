# gcd-query-v1

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/f8537f51-0135-4bd3-98da-766ecaa33a24)

main.py
```py
#!/usr/local/bin/python
from flag import flag
from math import gcd
from Crypto.Util.number import getRandomInteger


for i in range(10):
    x = getRandomInteger(2048)
    for i in range(1412):
        try:
            n, m = map(int, input("n m: ").split())
            assert m > 0
            print(gcd(x + n, m))
        except:
            print("bad input. you *die*")
            exit(0)
    if int(input("x: ")) != x:
        print("get better lol")
        exit(0)

print(flag)
```

## solution

we can use a janky version of Chinese Remainder Theorem to figure out the flag

this took me a while to figure out, and was mostly based on long time of thinking that i cannot explain

```py
from pwn import *
from Crypto.Util.number import isPrime
from sympy.ntheory.modular import crt

# p = process(["python3", "gcdquery.py"])
p = remote("amt.rs", 31692)


def query(boffer3, other):
    p.sendline(f"{boffer3} {other}".encode('ascii'))
    return int(p.recvline(False).decode('ascii')[5:].replace("m: ", ""))


killer = 1
for prime in range(2, 204):
    if isPrime(prime):
        killer *= prime
killer **= 10

sleep(2)
for _ in range(10):
    a = []
    b = []
    q = 0
    for _2 in range(1412):
        print(_, _2)
        b.append(q)
        w = query(q, killer)
        a.append(w)
        q += w
    res = crt(a, b)
    x = res[1]-res[0]
    print(x)
    sleep(0.1)
    p.sendline(str(x).encode('ascii'))
    sleep(0.1)
p.interactive()
```

this gets us the flag pretty slowly, but it works nonetheless
