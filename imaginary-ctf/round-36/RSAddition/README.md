# RSAddition

this was my first ever challenge i made for ictf

## problem

i put on my 'foil hat for this one

rsaddition.py
```py
from Crypto.Util.number import *

flag = b"ictf{REDACTED}"


e = 65537
p, q = getPrime(512), getPrime(512)
n = p * q

# there are only ~2**512 distinct integer pairs that add up to this value
# should be fine to show to the user, takes too long to brute force
summed = p+q

ct = pow(bytes_to_long(flag), e, n)

print(f"ct={ct}\ne={e}\nn={n}\nsummed={summed}")
```

output.txt
```
ct=26160450844298943504678972030472716712047794802763091939193132707048856890893390980361554185003039173393740811093711782751606081050596537476001245983808102501387180326986171845675612043507711123480129124669722351434100990576755222282726240666464794033619019191083073042945912852716204408306619811829163336755
e=65537
n=86338745966220371556239926612554414373579311619232575486418481568758210798212953404212355078736533033682842952740707065182044474164520461621679321897238868505524027414607448133372780812112565573173272288130472793683268593859506190978693486765429929952445994021592312274572512640282666895354782106869437954377
summed=18675822678246482576474042913680809027311647578626296267885137565966120131868892884853415575679630265881423713686562167071683681452092448296569017957106118
```

## solution

when making this challenge, i acknowledge that there are two intended solutions for this one

the first is to derive the decryption exponent from the `summed` and `n` values, like so:

```py
from Crypto.Util.number import *
# d = pow(65537, -1, (p-1)*(q-1))
d = pow(65537, -1, n - summed + 1)
print(long_to_bytes(pow(ct, d, n)))
```

the second intended solution was to use the quadratic formula to derive `p` and `q`, like so:

```py
from Crypto.Util.number import *
from math import isqrt
p = (summed-isqrt(summed**2-4*n))//2
q = (summed+isqrt(summed**2-4*n))//2
print(f"{p=}\n{q=}")
d = pow(65537, -1, (p-1)*(q-1))
print(long_to_bytes(pow(ct, d, n)))
```

the flag is `ictf{noooo!_my_evil_plans_were_FOIL'd_again!!!}`
