# quadratic

## problem

```
from Crypto.Util.number import bytes_to_long

x = bytes_to_long(b'ictf{xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx}')
print(69*x^2 + 42*x + 314159265358)

# 11579616830010600399932641971351067137758799841099537906925885183536730818623058070093057619806810857320199981348668999197653583956890465217095149031820045015395
```

## solution

what appears to be a quadratic is actually an xor operation equivalent to `(69*x)^(314159265360 + 42*x)`

we can just brute force this one

```py
from Crypto.Util.number import bytes_to_long

stuffs = bytearray(b'h'*60)
n = 11579616830010600399932641971351067137758799841099537906925885183536730818623058070093057619806810857320199981348668999197653583956890465217095149031820045015395


def get_flag(inner: bytes):
    return bytes_to_long(b'ictf{' + inner + b"}")


def calc(x):
    return 69*x ^ 2 + 42*x + 314159265358


chars = bytearray(b"abcdefghijklmnopqrstuvwxyz0123456789_,[]{}-=_+,.<>/\\")


offset = -3
total = b""
while True:
    offset += 3
    lastbest = None
    minimum = None
    best = bytearray(3)
    for char1 in chars:
        for char2 in chars:
            for char3 in chars:
                stuffs[offset] = char1
                stuffs[offset+1] = char2
                stuffs[offset+2] = char3
                done = abs(calc(get_flag(stuffs))-n)
                if minimum is None or done < minimum:
                    lastbest = best.copy()
                    minimum = done
                    best[0] = char1
                    best[1] = char2
                    best[2] = char3
                    # print(minimum, chr(char1), chr(char2), chr(char3))
    # little nudge in the right direction
    if lastbest == b"xor":
        best = bytearray(b"xor")
    if lastbest == b"_th":
        best = bytearray(b"_th")
    total += best

    if offset == 57:
        print(b"ictf{" + total + b"}")
        quit()
    stuffs[offset] = best[0]
    stuffs[offset+1] = best[1]
    stuffs[offset+2] = best[2]
```

