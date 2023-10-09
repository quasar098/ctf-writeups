# seas air

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/afef3475-0b5c-4461-9bcd-883bdf3a1145)

```py
#!/usr/bin/env python3

from random import randint
from math import sin, pi

def seasair(s, key):
    return bytes((c + k)%256 for c, k in zip(s, (int(256*sin(key*(i+1)/256*pi)) for i in range(len(s)))))

flag = open("flag.txt", 'rb').read()

key = randint(0, 2**32)

print(seasair(flag, key))

# Output:
# b"\xca\xae`fg\x94\x88d\xfe'ut|\x1a\x11_\xd4\xc2UmK\xb4\xcf_\x13\x1dy_\x87\x1a\x00_\xd5\xb3MnK\xbe\xd8i\x0c\x14}ns)\x07e\xc0\x8ei"
```

## solution

it puts the flag into a seasair function which takes a key and applys some weird sin function for each letter
here, i reversed the function to decrypt the message and then brute forced the key

```py
#!/usr/bin/env python3

from random import randint
from math import sin, pi

def seasair(s, key):
    return bytes(
        (a + b) % 256 for a, b in zip(
            s,
            [int(
                256 * sin(key * (i+1) / 256 * pi)
            ) for i in range(len(s))]
        )
    )


def de_seasair(st, key):
    return bytes(
        (a - b) % 256 for a, b in zip(
            st,
            [int(
                256 * sin(key * (i+1) / 256 * pi)
            ) for i in range(len(st))]
        )
    )

flag = b"ii"

key = randint(0, 2**32)

for _ in range(512):
    print(de_seasair(b"\xca\xae`fg\x94\x88d\xfe'ut|\x1a\x11_\xd4\xc2UmK\xb4\xcf_\x13\x1dy_\x87\x1a\x00_\xd5\xb3MnK\xbe\xd8i\x0c\x14}ns)\x07e\xc0\x8ei", _))

print(seasair(flag, key), key % 512)
```

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/ee0bfbf4-6806-4292-a5dc-c70dfbf98646)
