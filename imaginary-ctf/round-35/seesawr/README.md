# seesawr

## problem

A day late, but here's yet another not-caesar cipher for y'all.

```py
#!/usr/bin/env python3

from random import randint

alphabet = bytes(range(32, 127))

def seesawr(s, key):
    if len(s) == 0:
        return b''
    return bytes([alphabet[(alphabet.index(s[0])+key) % len(alphabet)]]) + seesawr(s[:0:-1], key)

flag = open("flag.txt", "rb").read()

print(seesawr(flag, randint(0, 2**128)))

# Output:
# b'~3x|*${~1}^*tt{I~K$tv/"w"t/*tz{)~{.{z%ytty*(}~zzt-'
```

## solution

this is a recursive function, and the way that it works is that the first byte is caesar'd with the key and rotated around through the `alphabet`

also, a character is added to the beginning and then the entire string is reversed and it is tried again

so, it will look something like this:<br>
`abcdefghijklmnopqrstuvwxyz` -> `azbycxdwevfugthsirjqkplomn`

i wrote a script to automatically do it (including finding the key, which is 21 or 0x15)

```py
#!/usr/bin/env python3

alphabet = bytes(range(32, 127))

def unseesaw(s2):
    new = b""
    new2 = b""
    for letter in range(0,len(s2),2):
        new += chr(s2[letter]).encode('ascii')
    for letter in range(len(s2)-1,0,-2):
        new2 += chr(s2[letter]).encode('ascii')
    old = new + new2
    for _ in range(len(alphabet)):
        newer = ""
        for _2 in old:
            newer += (chr(alphabet[(_2+_)%len(alphabet)]))
        if newer.startswith("ictf"):
            break
    return newer
    

print(unseesaw(b'~3x|*${~1}^*tt{I~K$tv/"w"t/*tz{)~{.{z%ytty*(}~zzt-'))
```

the flag is `ictf{I_finally_fixed_the_weird_offset_by_64_thing}` 
