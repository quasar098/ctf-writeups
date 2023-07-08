# suction

## problem

The easy suction cryptosystem is designed with a primary focus on simplicity and user-friendliness, employing streamlined algorithms that make encryption straightforward and accessible even for individuals without extensive technical knowledge.

```py
#!/usr/bin/env python3

from Crypto.Util.number import *
from flag import flag

def keygen(nbit, r):
	while True:
		p, q = [getPrime(nbit) for _ in '__']
		e, n = getPrime(16), p * q
		phi = (p - 1) * (q - 1)
		if GCD(e, phi) == 1:
			N = bin(n)[2:-r]
			E = bin(e)[2:-r]
			PKEY = N + E
			pkey = (n, e)
			return PKEY, pkey

def encrypt(msg, pkey, r):
	m = bytes_to_long(msg)
	n, e = pkey
	c = pow(m, e, n)
	C = bin(c)[2:-r]
	return C

r, nbit = 8, 128
PKEY, pkey = keygen(nbit, r)
print(f'PKEY = {int(PKEY, 2)}')
FLAG = flag.lstrip(b'CCTF{').rstrip(b'}')
enc = encrypt(FLAG, pkey, r)
print(f'enc = {int(enc, 2)}')
```

output.txt
```
PKEY = 55208723145458976481271800608918815438075571763947979755496510859604544396672
ENC = 127194641882350916936065994389482700479720132804140137082316257506737630761
```

## solution

we can see that binary version of PKEY is the concatenation of the bits (except first 8) of `N` and the bits (except first 8) of `e`.

additionally, the `enc` value last eight bits are removed, so we have to brute force them.

to crack this RSA, we can exploit the fact that the key size is 128 bits, which is very low (crack in a minute tops iirc)

to get possible `e` values, because we know `e` is prime, we do this:

```py
from Crypto.Util.number import *
potential_e = []
for _ in range(256):
    pot_e = 0b10000000_00000000 + _
    if isPrime(pot_e):
        potential_e.append(pot_e)
print(potential_e)
```

this results in `[32771, 32779, 32783, 32789, 32797, 32801, 32803, 32831, 32833, 32839, 32843, 32869, 32887, 32909, 32911, 32917, 32933, 32939, 32941, 32957, 32969, 32971, 32983, 32987, 32993, 32999, 33013, 33023]`

to get potential N values, we know that N is not prime, and that the factors of N are above `20,000` for sure

```py
from Crypto.Util.number import *
potential_n = []
for _ in range(256):
    pot_n = (215659074786949126879967971128589122804982702202921795919908245545330251549 << 8) + _
    if isPrime(pot_n):
        continue
    bad = False
    for _ in range(2, 20_000):
        if pot_n % _ == 0:
            bad = True
    if bad:
        continue
    print(pot_n)
```

this outputs

```
55208723145458976481271800608918815438075571763947979755496510859604544396571
55208723145458976481271800608918815438075571763947979755496510859604544396573
55208723145458976481271800608918815438075571763947979755496510859604544396577
55208723145458976481271800608918815438075571763947979755496510859604544396583
55208723145458976481271800608918815438075571763947979755496510859604544396589
55208723145458976481271800608918815438075571763947979755496510859604544396603
55208723145458976481271800608918815438075571763947979755496510859604544396613
55208723145458976481271800608918815438075571763947979755496510859604544396633
55208723145458976481271800608918815438075571763947979755496510859604544396643
55208723145458976481271800608918815438075571763947979755496510859604544396667
55208723145458976481271800608918815438075571763947979755496510859604544396673
55208723145458976481271800608918815438075571763947979755496510859604544396703
55208723145458976481271800608918815438075571763947979755496510859604544396717
```

because these are so low values to check for primes, we can use [factordb](http://factordb.com/) to check each one by hand for two prime factors of equal length in binary representation.

we see that the only one where the two factors are close is `55208723145458976481271800608918815438075571763947979755496510859604544396613`, with factors of `292926085409388790329114797826820624883` and `188473222069998143349386719941755726311`

now, we can just do the RSA decryption and get the flag

```py
from Crypto.Util.number import *
from sympy import factorint
from string import printable

potential_e = []
for _ in range(256):
    pot_e = 0b10000000_00000000 + _
    if isPrime(pot_e):
        potential_e.append(pot_e)

p = 292926085409388790329114797826820624883
q = 188473222069998143349386719941755726311
n = 55208723145458976481271800608918815438075571763947979755496510859604544396613
phi = (p-1)*(q-1)

for _ in range(256):
    ct = (127194641882350916936065994389482700479720132804140137082316257506737630761 << 8) + _
    for pot_e in potential_e:
        d = inverse(pot_e, phi)
        outflag = long_to_bytes(pow(ct, d, n))
        if all([chr(_) in printable for _ in outflag]):
            print(outflag.decode('ascii'))
```

the flag is `CCTF{6oRYGy&Dc$G2ZS}`
