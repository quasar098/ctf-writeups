# seesawr-2.0

## problem

Better late than never, right?

I started submitting this challenge only to find I've already done a Seesawr - apparently I'm not creative enough with my challenge names.

```py
#!/usr/bin/env python3

from random import randint

def seesawr(s, key):
    return bytes((c + (1-2*i)*key)%256 for i, c in enumerate(s))

flag = open("flag.txt", 'rb').read()

key = randint(0, 2**128)
print(seesawr(flag, key))

# Output:
# b'\xc3\tf\xa4\x058\x83\xd1%e\xc6\rI\x97\xe1;\x8f\xc9\x1a]\xb7\x05T\x93\xd9)s\xc3\x1dW\xb4\xbbF\x8f\xdd#i\xb9\x03S\xad\xe7D}\xcd\x1ec\xba\x06c'
```

## solution

we can see in the seesawr function that for each letter in the input string, a number is being added to the value of the character mod 256

we can just reverse it by subtracting the `(1-2*i)*key`

then brute force for each key 0-255

```py
#!/usr/bin/env python3

from random import randint


def seesawr(s, key2):
    total = b""
    return bytes((c + (1-2*i)*key2) % 256 for i, c in enumerate(s))


def deobf_seesawr(plaintext, key2):
    total = []
    key2 = key2 % 256
    for index, char in enumerate(plaintext):
        offset = 2 * index * key2
        total.append((char - offset + key2) % 256)
    print(key2)
    return bytes(total)


def brute_key(ct: bytes):
    for key_attempt in range(256):
        new = []
        for index, char in enumerate(ct):
            new.append((char + 2 * index * key_attempt - key_attempt) % 256)
        print(key_attempt, bytes(new))


flag = b"abcdefg"

key = randint(0, 2**128)
print(seesawr(flag, key))
print(deobf_seesawr(flag, key))
brute_key(b'\xc3\tf\xa4\x058\x83\xd1%e\xc6\rI\x97\xe1;\x8f\xc9\x1a]\xb7\x05T\x93\xd9)s\xc3\x1dW\xb4\xbbF\x8f\xdd#i\xb9\x03S\xad\xe7D}\xcd\x1ec\xba\x06c')

# Output:
# b'\xc3\tf\xa4\x058\x83\xd1%e\xc6\rI\x97\xe1;\x8f\xc9\x1a]\xb7\x05T\x93\xd9)s\xc3\x1dW\xb4\xbbF\x8f\xdd#i\xb9\x03S\xad\xe7D}\xcd\x1ec\xba\x06c'
```
