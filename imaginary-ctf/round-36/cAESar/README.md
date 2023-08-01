# cAESar

## problem

Has no one made this pun yet?

```py
#!/usr/bin/env python3

from Crypto.Cipher import AES
from random import randint

flag = b"ictf{REDACTED}"

key = b"_verysecurekey_!"
cipher = AES.new(key, AES.MODE_ECB)

out = []
s = randint(0, 2**64)
ct = cipher.encrypt(flag.ljust(64, b'\0'))
for n in ct:
  out.append((n + s) % 256)

print(bytes(out))

# b'\xe4\xda\xdbD\x82(\x0c\x9bR\xcc\t\xcb\xe6\x14\xbc\x1e\x87\xf5\x06\xc0-K\xe2A\xdc}\x03\xc7^e\xa0i\xed\xbc[*\x91yG\x90\xa6\xe6\xed\xbf4EY\xe3A\\\x8c\x86=V#u0\x8b\xe3\xb1\x91Q)D'
```

## solution

we know the flag is encrypted with AES and we also know the key. We don't know the `s` value, but we do know that the value out value is `(n + s) % 256`

we can use the addition property of modular arithmetic that `(n + s) % 256 = n % 256 + s % 256`, and since we know that `255 >= n >= 0` always, we can ignore that, so it is `n + (s % 256)`

we can brute force for each s from 0 to 255 inclusive.

```py
#!/usr/bin/env python3

from Crypto.Cipher import AES
from random import randint

flag = b"ictf{REDACTED}"

key = b"_verysecurekey_!"
cipher = AES.new(key, AES.MODE_ECB)

s = randint(0, 2**64)

og = b'\xe4\xda\xdbD\x82(\x0c\x9bR\xcc\t\xcb\xe6\x14\xbc\x1e\x87\xf5\x06\xc0-K\xe2A\xdc}\x03\xc7^e\xa0i\xed\xbc[*\x91yG' \
     b'\x90\xa6\xe6\xed\xbf4EY\xe3A\\\x8c\x86=V#u0\x8b\xe3\xb1\x91Q)D'

for _ in range(256):
    try:
        fixed = list(og)
        for _2 in range(len(fixed)):
            fixed[_2] = (fixed[_2]+_) % 256
        print(cipher.decrypt(bytes(fixed)))
    except Exception as e:
        print(e)
        quit()
```

I then run the following in the cmd:

```
quasar@quasar098:~/wasteland$ python3 cAESar.py | grep ictf
b'ictf{oops_forgot_to_redact_the_key}\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00'
```

the flag is `ictf{oops_forgot_to_redact_the_key}`
