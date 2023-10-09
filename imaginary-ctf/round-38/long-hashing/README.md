# long-hashing

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/e54aee5d-5a33-4364-9e2e-f744da720615)

```py
#!/usr/bin/env python3

from Crypto.Util.number import long_to_bytes, getPrime, isPrime
from hashlib import sha256

flag = open("flag.txt").read()

def sign(m):
    return sha256(m).hexdigest()

p = getPrime(256)

def auth(sig, msg):
    if isPrime(msg):
        return "No no no, composites only."
    msg = long_to_bytes(msg)
    if sig != sign(msg):
        return "Authentication Failed."
    if msg.startswith(long_to_bytes(p)):
        return f"Welcome admin! Flag is {flag}"
    return "Authentication Successful!"

def menu():
    print("1. Authenticate")
    print("2. Get p")
    print("3. Show source")
    print("4. Quit")
    x = int(input(">>> "))
    if x == 1:
        m = int(input("Enter message as long: "))
        sig = str(input("Enter string as hexstr: "))
        print(auth(sig,m))
    elif x == 2:
        print("The prime is", p)
        print("The signature is", sign(long_to_bytes(p)))
    elif x == 3:
        print("="*80)
        print(open(__file__).read())
        print("="*80)
    elif x == 4:
        exit()

if __name__ == "__main__":
    while True:
        menu()
```

## solution

here the vulnerability is the "startswith" because we can just append a `\x00` byte to the end of the p (equivalent to p*256) and it will be both composite and start with the flag

in the main window
```
1. Authenticate
2. Get p
3. Show source
4. Quit
>>> 2
The prime is 109009618883972309948887404534525956656316477932361687539264517929194570707173
The signature is b02b4d28e373295ce1c65a1650055227fd28258890976690395908e2443b1d0e
```

then, in a seperate window
```
>>> from hashlib import sha256
>>> def sign(m):
...     return sha256(m).hexdigest()
...
>>> 109009618883972309948887404534525956656316477932361687539264517929194570707173*256
27906462434296911346915175560838644904017018350684592010051716589873810101036288
>>> from Crypto.Util.number import *
>>> long_to_bytes(27906462434296911346915175560838644904017018350684592010051716589873810101036288)
b'\xf1\x01B\x00|7:t\xad\xd2k}\x1b\xf4\x1e\xd4\x19:@\x000JU\xa1i\xb0x\x85N0\xf0\xe5\x00'
>>> sign(_)
'9600f5fd0f40d4823d4fecc6d140dbf8a6b58724b782c0bf31e98a40fd0d14f2'
```

then, back in the main window
```
1. Authenticate
2. Get p
3. Show source
4. Quit
>>> 1
Enter message as long: 27906462434296911346915175560838644904017018350684592010051716589873810101036288
Enter string as hexstr: 9600f5fd0f40d4823d4fecc6d140dbf8a6b58724b782c0bf31e98a40fd0d14f2
Welcome admin! Flag is ictf{jUst_g0ttA_m4k3_iT_a_teEny_b1T_l0ngER...}
```
