# longer-hashing

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/2419fc93-45b4-4b43-af18-1a3cbe29e2d8)

```py
from hashlib import sha256
from Crypto.Util.number import long_to_bytes, getPrime, isPrime
from secret import flag, genPrime      # generating them per instance takes too long :(

def sign(m):
    return sha256(m).hexdigest()

"""
# for reference
def genPrime():
    while True:
        p = getPrime(1024)
        if isPrime(p % 2**512):
            break
    return p
"""

def auth(sig, msg):
    if isPrime(msg):
        return "No no no, composites only."
    msg = long_to_bytes(p>>512) + long_to_bytes(msg)
    if sig != sign(msg):
        return "Authentication Failed."
    if msg.startswith(long_to_bytes(p)):
        return f"Welcome admin! Flag is {flag}"
    return "Authentication Successful!"

p = 0
def menu():
    global p
    print("1. Authenticate")
    print("2. Get p")
    print("3. Show source")
    print("4. Quit")
    x = int(input(">>> "))
    if x == 1:
        if not p:
            print("Get your prime first!!")
            return
        m = int(input("Enter message as long: "))
        sig = str(input("Enter string as hexstr: "))
        print(auth(sig,m))
    elif x == 2:
        p = genPrime()
        print("The prime is", p % 2**512)
        print("The signature is", sha256(long_to_bytes(p)).hexdigest())
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

here, this is vulnerable to a hash length extension attack because there is a secret of a known length (the first 512 bits of `p`) and a known plaintext (the last 512 bits of `p`) so we can abuse that

```py
from Crypto.Util.number import *
from hlextend import sha256 as hl256
from pwn import *

p = remote("puzzler7.imaginaryctf.org", 15003)
# p = process(["python3", "serv.py"])

p.recvuntil(b'>>>')
p.sendline(b'2')
known_p_segment = long_to_bytes(int(p.recvline(keepends=False).split(b" ")[-1].decode('ascii')))
known_hash = p.recvline(keepends=False).split(b" ")[-1].decode('ascii')

print("known_p_segment", known_p_segment)
print("known_hash", known_hash)

h = hl256()
dat = h.extend(b'', known_p_segment, 8 * 8, known_hash)
digest = h.hexdigest()
print("dat", dat)
print("num", bytes_to_long(dat))
print("digest", digest)

p.sendline(b'1')
p.sendline(str(bytes_to_long(dat)).encode('ascii'))
p.sendline(digest.encode('ascii'))

p.interactive()
```

here i use the [hlextend](https://github.com/stephenbradshaw/hlextend) library in python

i am not going to go into how hash length extension attacks work but the attack works

```
[x] Opening connection to puzzler7.imaginaryctf.org on port 15003
[x] Opening connection to puzzler7.imaginaryctf.org on port 15003: Trying 137.184.207.224
[+] Opening connection to puzzler7.imaginaryctf.org on port 15003: Done
known_p_segment b'2\xca\xb6\xee\r6\\\xdb\xcf\x07"\x90\xb3V@\xf4X}\xe6\xd8\x10V\xf2\x85m<R\xdf"\n\xab(\xd9T*@lQ\x99\x96\xed\x98\xd4\xe5:\x06\\\xbd\xf0\xa35Qx\xfbI\xddzXO\xd6\xae\xf5\x8fs'
known_hash 222e7c1b01447d09b91b7025a0bfd72578fb58a0adeb6d4c3470444257cdbeb0
dat b'2\xca\xb6\xee\r6\\\xdb\xcf\x07"\x90\xb3V@\xf4X}\xe6\xd8\x10V\xf2\x85m<R\xdf"\n\xab(\xd9T*@lQ\x99\x96\xed\x98\xd4\xe5:\x06\\\xbd\xf0\xa35Qx\xfbI\xddzXO\xd6\xae\xf5\x8fs\x80\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x04\x00'
num 35667252680734641451531248294302947442027119729406586615720386076277342058784464662929961499400579970901567527654820847886492544106483768886557614584040722443152693314736232151171843760858912658680585924637481161569788451503292639177562867608418783170009229574113619458616795932015003488123134192940518212608
digest 31cff0e8c01ea0a46f81cdabcf45a2ee1ea702a30541dcb226c6df4a22785c90
[*] Switching to interactive mode
1. Authenticate
2. Get p
3. Show source
4. Quit
>>> Enter message as long: Enter string as hexstr: Welcome admin! Flag is ictf{loNg3R_f0r_tH3_leNGth_eXtens1on.......................looks_long_enuf}
```
