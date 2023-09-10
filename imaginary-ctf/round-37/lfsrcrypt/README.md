# lfsrcrypt

another chall i made for ictf, kinda meh tbh but sequel soon

## problem

tried to make the title sound cool, failed

gen.py
```py
#!/usr/bin/env python3

from random import randint

LFSR_SIZE = 8
flag = open("flag.txt", "rb").read()

def paddedbin(n: int, padding: int = LFSR_SIZE):
    return f"0b{bin(n)[2:].rjust(padding, '0')}"


def xor_all(things: list[int]):
    if len(things) == 0:
        return 0
    return things.pop(0) ^ xor_all(things)


def next_val(current_state: int, taps: list[int]) -> int:
    new_msb = xor_all([(current_state >> LFSR_SIZE-tap) & 1 for tap in taps])
    return (current_state >> 1) | (new_msb << LFSR_SIZE-1)


if __name__ == '__main__':
    current = 1 << LFSR_SIZE-1
    for _ in range(randint(2**8, 2**10)):
        current = next_val(current, [8, 5, 6, 4])
    flag_encryption_taps = [7, 8]
    total = []
    for char in flag:
        total.append(str(next_val(char, flag_encryption_taps) ^ current))
    print(" ".join(total))
```

output.txt
```
40 173 166 47 161 170 47 165 37 165 179 44 37 46 179 44 170 165 171 179 38 165 46 174 179 40 43 179 166 46 166 37 40 165 179 135 46 47 4 0 4 135 0 44 132 7 34
```

## solution

here we can see that each character's ASCII value is being changed using an LFSR with taps 7 and 8, which means that the new MSB (most significant bit) is based upon the XOR of the 7th and 8th leftmost bits
therefore, we can deduce the missing bit's value by XORing the new MSB with the leftover tap bit

lastly, we can brute force the `current` value and then xor it with the output to get the flag

```py
LFSR_SIZE = 8

def paddedbin(n: int, padding: int = LFSR_SIZE):
    return f"0b{bin(n)[2:].rjust(padding, '0')}"


def xor_all(things: list[int]):
    if len(things) == 0:
        return 0
    return things.pop(0) ^ xor_all(things)


def next_val(current_state: int, taps: list[int]) -> int:
    new_msb = xor_all([(current_state >> LFSR_SIZE-tap) & 1 for tap in taps])
    return (current_state >> 1) | (new_msb << LFSR_SIZE-1)


data = open("output.txt").read()
data = [int(datum) for datum in data.split(" ")]

for _ in range(256):
    output = ""
    for index, datum in enumerate(data):
        datum = datum ^ _
        find_bit = xor_all([datum >> 7 & 1, datum & 1])
        charv = (((datum << 1) & 0b11111111) | find_bit)
        output += chr(charv)
    if "ictf{" in output:
        print(f"XOR Key: {_}\nFlag: {output}")
```
