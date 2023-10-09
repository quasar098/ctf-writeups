# carry-out

more like a 100pts challenge, defo not 75

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/3e2941c3-8a64-47ac-b3e5-9b1db0abe797)

see [CarryOut](./CarryOut)

## solution

looking at the binary in binja

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/b21ed45e-85ec-4571-b5e3-dc00cd337581)

there is this crazy instruction from the intel carryless multiplication instruction set

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/499c7861-d8ca-4778-969b-9bc85a9cbd34)

it is pclmulqdq

this means that when adding the binary at the end, the carry is discarded (so like an XOR instead of an add)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/e6ecf844-b54d-4627-9de6-0842c52b6552)

looking more closely at the assembly instruction, it multiplies the xmm0 with itself at first glance

however, there is a `0x1` as the third argument to the instruction, so the first 64 bits are being multiplied with the second 64 bits

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/a4faa032-8c2c-4e39-b701-ec9c636ec4bf)

therefore, we need to get the two numbers that are less than 64 bits long that multiply to equal our answer, which is below

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/1624b074-7439-4d47-95c6-827de91e7032)

note that the ictf is not part of the multiplication, but the `{` and then 15 bytes are part of the multiplication

at first i tried making a brute force solution for this but it didnt work because the search space is much too large

the real solution is that this can be expressed as division of polynomials across finite fields

so for example, `1011` could be represented as x^3 + x^1 + x

and multiplying two of these polynomials looks something like this: 

(x^3 + x^1 + x^0) * (x^2 + x^0)

= (x^3 + x^1 + x^0) * x^2 + (x^3 + x^1 + x^0) * x^0

= x^5 + x^3 + x^2 + x^3 + x^1 + x^0

= x^5 + 2x^3 + x^2 + x^1 + x^0

now normally at this step the 2x^3 would equal x^4 because `1000` + `1000` = `10000` in binary, but this is carryless, so the carry is discarded

= x^5 + x^2 + x^1 + x^0

that is our final answer

so then, i found some library to divide the polynomials over finite fields called "flint" (docs [here](https://fredrikj.net/python-flint/nmod_poly.html))

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/0877120a-0d55-4e97-af8b-a00e631ec063)

then, i wrote a small program to assign each factor to either number one or number two, which are the two numbers that multiply to the "encrypted" text

```py
#!/usr/bin/env python3

from string import printable
def clmul(a: int, b: int):
    total = 0
    index = 0
    while a:
        if a & 1:
            total ^= (b << index)
        index += 1
        a >>= 1
    return total

from flint import nmod_poly
from Crypto.Util.number import *

# enter "a b": 1289 32819
# Result: 42237611


n = input("n: ")
n = int(n) if n != "" else 22791552864349647022332247016330384994

l = []
while n:
    l.insert(0, n & 1)
    n = n >> 1

fs = nmod_poly(l, 2).factor()[1]
rfs = []

for a, b in fs:
    for _ in range(b):
        rfs.append(a)

for _ in range(2**len(rfs)):
    n_a = []
    n_b = []
    for index, _2 in enumerate(rfs):
        if _ & (1 << index):
            n_a.append(rfs[index])
        else:
            n_b.append(rfs[index])
    if len(n_a) == 0 or len(n_b) == 0:
        continue
    s_a = n_a.pop(0)
    s_b = n_b.pop(0)
    while len(n_a):
        s_a *= n_a.pop(0)
    while len(n_b):
        s_b *= n_b.pop(0)
    c_a = s_a.coeffs()
    c_b = s_b.coeffs()
    p_a = int("".join([str(bit) for bit in s_a]), 2)
    p_b = int("".join([str(bit) for bit in s_b]), 2)
    sa = long_to_bytes(p_a)
    sb = long_to_bytes(p_b)
    if b'{' in sa or b'{' in sb:
        print(sa, sb, p_a, p_b)
```

then, i got some output

```
n: 
b'\xa0O\xb7' b"\x14'.\x8e;)m\x01\xbf\x9d\xa5Z{" 10506167 1596689447923404994382177589883
b'\x04\x07\xd6\xd4\x93\x1f\xc6\xa7' b"\x02'\x7f\x88\xfam\xf4{+" 290436909478430375 39739632246108486443
b'\x01\xec;t\xb7' b'\x0cg\xcdT\xe5\n\x8b\xac\x85\xe9\xdd{' 8258286775 3839309128436095456113188219
b'\x01_rZu\xabM\x1e\xb3\xdeB]' b'\n\xe5{\xea\xbd' 424872981149704131453010525 46799776445
b'f\xd6_\x11({' b'?\xfav\x0f\xc7\xc9\x00\xe25\xb7' 113070903994491 302129281677102279701943
b'\x03\xa5;\xe8\xbd\xbd{\xa5\x9f' b'\x06\xe1I\x81\x0e` \xb3' 67246598162638153119 495758252628254899
b'Irotc4F{' b'&*\xb6\xb6!\xaf\xa3\xb7' 5292415057785013883 2750211415828571063
b'\x01\x83H\x9ec\x03{T\x03-IK1\x99\xcbc' b'\x0f' 2010891766437343616881986654055287651 15
b'/{\x86\xdc\xfe!' b'_uq\r\x87|\x98\x90\xd7\x11' 52207590112801 450791231245510501259025
b'\x0b\xeeu' b'\x01{\xc7\x16\x9c"\x00\xbcG<%\x8aL\xd5' 781941 30089088443545076760261285268693
b'L7+\x91\xb3\x90\x05' b'$\x8b\xbeUFl{\xcd\xa5' 21452758497595397 674152366231346990501
b'\x0c\x08{}\xb5 K\xe9' b'\x01\xe2\xd5xV$\xac)\x19' 867078708108741609 34791846807698417945
b'\x021\xac\x93' b'\x04)\xc8\x8b\xa2Ow\x0eWR{A?' 36809875 329843980026695144067898753343
b'\x07Y\xdf\xe4\xb8G' b'\x03{\xb5\xd5\xaf{\xeee\x9d\xd0K' 8082589792327 4210982794576110154534987
b'\x05>\xd1=\x0f\x1d' b'\x02\x9a\x05\xa2\xdf{\x06\xe8\x1c\x8f\xfd' 5767356550941 3145200047545975805087741
b'\x02\x8b\xc7{\xc3' b'\x05\x1cD\x1e;ai\xf7\x81\xbd\xda/' 10935040963 1581596650657909940409981487
b',ET\xca{\xf09\xa9\xa6\xbd\x94Y\x11' b'YP!' 3507496122278440406619612535057 5853217
b'1\xc2\xadYw\xb0\x84\x8fL\xe93\xe3o' b'{\x9b\x83' 3942429621769585785241439822703 8100739
b'1\xb4~\xc3\x8fT\x95\xe0\xd6ib\xcc\xc8\xad' b'{M' 1008138371360130479291807034034349 31565
b'/{\x86\xdc\xfe!' b'_uq\r\x87|\x98\x90\xd7\x11' 52207590112801 450791231245510501259025
b'\x0b\xeeu' b'\x01{\xc7\x16\x9c"\x00\xbcG<%\x8aL\xd5' 781941 30089088443545076760261285268693
b'L7+\x91\xb3\x90\x05' b'$\x8b\xbeUFl{\xcd\xa5' 21452758497595397 674152366231346990501
b'\x0c\x08{}\xb5 K\xe9' b'\x01\xe2\xd5xV$\xac)\x19' 867078708108741609 34791846807698417945
b'\x021\xac\x93' b'\x04)\xc8\x8b\xa2Ow\x0eWR{A?' 36809875 329843980026695144067898753343
b'\x07Y\xdf\xe4\xb8G' b'\x03{\xb5\xd5\xaf{\xeee\x9d\xd0K' 8082589792327 4210982794576110154534987
b'\x05>\xd1=\x0f\x1d' b'\x02\x9a\x05\xa2\xdf{\x06\xe8\x1c\x8f\xfd' 5767356550941 3145200047545975805087741
b'\x02\x8b\xc7{\xc3' b'\x05\x1cD\x1e;ai\xf7\x81\xbd\xda/' 10935040963 1581596650657909940409981487
b',ET\xca{\xf09\xa9\xa6\xbd\x94Y\x11' b'YP!' 3507496122278440406619612535057 5853217
b'1\xc2\xadYw\xb0\x84\x8fL\xe93\xe3o' b'{\x9b\x83' 3942429621769585785241439822703 8100739
b'1\xb4~\xc3\x8fT\x95\xe0\xd6ib\xcc\xc8\xad' b'{M' 1008138371360130479291807034034349 31565
b'{M' b'1\xb4~\xc3\x8fT\x95\xe0\xd6ib\xcc\xc8\xad' 31565 1008138371360130479291807034034349
b'{\x9b\x83' b'1\xc2\xadYw\xb0\x84\x8fL\xe93\xe3o' 8100739 3942429621769585785241439822703
b'YP!' b',ET\xca{\xf09\xa9\xa6\xbd\x94Y\x11' 5853217 3507496122278440406619612535057
b'\x05\x1cD\x1e;ai\xf7\x81\xbd\xda/' b'\x02\x8b\xc7{\xc3' 1581596650657909940409981487 10935040963
b'\x02\x9a\x05\xa2\xdf{\x06\xe8\x1c\x8f\xfd' b'\x05>\xd1=\x0f\x1d' 3145200047545975805087741 5767356550941
b'\x03{\xb5\xd5\xaf{\xeee\x9d\xd0K' b'\x07Y\xdf\xe4\xb8G' 4210982794576110154534987 8082589792327
b'\x04)\xc8\x8b\xa2Ow\x0eWR{A?' b'\x021\xac\x93' 329843980026695144067898753343 36809875
b'\x01\xe2\xd5xV$\xac)\x19' b'\x0c\x08{}\xb5 K\xe9' 34791846807698417945 867078708108741609
b'$\x8b\xbeUFl{\xcd\xa5' b'L7+\x91\xb3\x90\x05' 674152366231346990501 21452758497595397
b'\x01{\xc7\x16\x9c"\x00\xbcG<%\x8aL\xd5' b'\x0b\xeeu' 30089088443545076760261285268693 781941
b'_uq\r\x87|\x98\x90\xd7\x11' b'/{\x86\xdc\xfe!' 450791231245510501259025 52207590112801
b'/{\x86\xdc\xfe!' b'_uq\r\x87|\x98\x90\xd7\x11' 52207590112801 450791231245510501259025
b'\x0b\xeeu' b'\x01{\xc7\x16\x9c"\x00\xbcG<%\x8aL\xd5' 781941 30089088443545076760261285268693
b'L7+\x91\xb3\x90\x05' b'$\x8b\xbeUFl{\xcd\xa5' 21452758497595397 674152366231346990501
b'\x0c\x08{}\xb5 K\xe9' b'\x01\xe2\xd5xV$\xac)\x19' 867078708108741609 34791846807698417945
b'\x021\xac\x93' b'\x04)\xc8\x8b\xa2Ow\x0eWR{A?' 36809875 329843980026695144067898753343
b'\x07Y\xdf\xe4\xb8G' b'\x03{\xb5\xd5\xaf{\xeee\x9d\xd0K' 8082589792327 4210982794576110154534987
b'\x05>\xd1=\x0f\x1d' b'\x02\x9a\x05\xa2\xdf{\x06\xe8\x1c\x8f\xfd' 5767356550941 3145200047545975805087741
b'\x02\x8b\xc7{\xc3' b'\x05\x1cD\x1e;ai\xf7\x81\xbd\xda/' 10935040963 1581596650657909940409981487
b',ET\xca{\xf09\xa9\xa6\xbd\x94Y\x11' b'YP!' 3507496122278440406619612535057 5853217
b'1\xc2\xadYw\xb0\x84\x8fL\xe93\xe3o' b'{\x9b\x83' 3942429621769585785241439822703 8100739
b'1\xb4~\xc3\x8fT\x95\xe0\xd6ib\xcc\xc8\xad' b'{M' 1008138371360130479291807034034349 31565
b'{M' b'1\xb4~\xc3\x8fT\x95\xe0\xd6ib\xcc\xc8\xad' 31565 1008138371360130479291807034034349
b'{\x9b\x83' b'1\xc2\xadYw\xb0\x84\x8fL\xe93\xe3o' 8100739 3942429621769585785241439822703
b'YP!' b',ET\xca{\xf09\xa9\xa6\xbd\x94Y\x11' 5853217 3507496122278440406619612535057
b'\x05\x1cD\x1e;ai\xf7\x81\xbd\xda/' b'\x02\x8b\xc7{\xc3' 1581596650657909940409981487 10935040963
b'\x02\x9a\x05\xa2\xdf{\x06\xe8\x1c\x8f\xfd' b'\x05>\xd1=\x0f\x1d' 3145200047545975805087741 5767356550941
b'\x03{\xb5\xd5\xaf{\xeee\x9d\xd0K' b'\x07Y\xdf\xe4\xb8G' 4210982794576110154534987 8082589792327
b'\x04)\xc8\x8b\xa2Ow\x0eWR{A?' b'\x021\xac\x93' 329843980026695144067898753343 36809875
b'\x01\xe2\xd5xV$\xac)\x19' b'\x0c\x08{}\xb5 K\xe9' 34791846807698417945 867078708108741609
b'$\x8b\xbeUFl{\xcd\xa5' b'L7+\x91\xb3\x90\x05' 674152366231346990501 21452758497595397
b'\x01{\xc7\x16\x9c"\x00\xbcG<%\x8aL\xd5' b'\x0b\xeeu' 30089088443545076760261285268693 781941
b'_uq\r\x87|\x98\x90\xd7\x11' b'/{\x86\xdc\xfe!' 450791231245510501259025 52207590112801
b'{M' b'1\xb4~\xc3\x8fT\x95\xe0\xd6ib\xcc\xc8\xad' 31565 1008138371360130479291807034034349
b'{\x9b\x83' b'1\xc2\xadYw\xb0\x84\x8fL\xe93\xe3o' 8100739 3942429621769585785241439822703
b'YP!' b',ET\xca{\xf09\xa9\xa6\xbd\x94Y\x11' 5853217 3507496122278440406619612535057
b'\x05\x1cD\x1e;ai\xf7\x81\xbd\xda/' b'\x02\x8b\xc7{\xc3' 1581596650657909940409981487 10935040963
b'\x02\x9a\x05\xa2\xdf{\x06\xe8\x1c\x8f\xfd' b'\x05>\xd1=\x0f\x1d' 3145200047545975805087741 5767356550941
b'\x03{\xb5\xd5\xaf{\xeee\x9d\xd0K' b'\x07Y\xdf\xe4\xb8G' 4210982794576110154534987 8082589792327
b'\x04)\xc8\x8b\xa2Ow\x0eWR{A?' b'\x021\xac\x93' 329843980026695144067898753343 36809875
b'\x01\xe2\xd5xV$\xac)\x19' b'\x0c\x08{}\xb5 K\xe9' 34791846807698417945 867078708108741609
b'$\x8b\xbeUFl{\xcd\xa5' b'L7+\x91\xb3\x90\x05' 674152366231346990501 21452758497595397
b'\x01{\xc7\x16\x9c"\x00\xbcG<%\x8aL\xd5' b'\x0b\xeeu' 30089088443545076760261285268693 781941
b'_uq\r\x87|\x98\x90\xd7\x11' b'/{\x86\xdc\xfe!' 450791231245510501259025 52207590112801
b'\x0f' b'\x01\x83H\x9ec\x03{T\x03-IK1\x99\xcbc' 15 2010891766437343616881986654055287651
b'&*\xb6\xb6!\xaf\xa3\xb7' b'Irotc4F{' 2750211415828571063 5292415057785013883
b'\x06\xe1I\x81\x0e` \xb3' b'\x03\xa5;\xe8\xbd\xbd{\xa5\x9f' 495758252628254899 67246598162638153119
b'?\xfav\x0f\xc7\xc9\x00\xe25\xb7' b'f\xd6_\x11({' 302129281677102279701943 113070903994491
b'\n\xe5{\xea\xbd' b'\x01_rZu\xabM\x1e\xb3\xdeB]' 46799776445 424872981149704131453010525
b'\x0cg\xcdT\xe5\n\x8b\xac\x85\xe9\xdd{' b'\x01\xec;t\xb7' 3839309128436095456113188219 8258286775
b"\x02'\x7f\x88\xfam\xf4{+" b'\x04\x07\xd6\xd4\x93\x1f\xc6\xa7' 39739632246108486443 290436909478430375
b"\x14'.\x8e;)m\x01\xbf\x9d\xa5Z{" b'\xa0O\xb7' 1596689447923404994382177589883 10506167
```

notice the line `b'Irotc4F{' b'&*\xb6\xb6!\xaf\xa3\xb7' 5292415057785013883 2750211415828571063`

the first part is correct and looks like english (it is weird endianness so actually it is `{F4ctorI`), but the second part is gibberish

i converted the bytes into a number and looked at it in windows calculator

```
>>> from Crypto.Util.number import *
>>> bytes_to_long(b'&*\xb6\xb6!\xaf\xa3\xb7')
2750211415828571063
```

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/3c26fe0f-4362-4c2e-ac40-184b7108c2e4)

in theory, the first bit of each byte would be a 0 because all ascii characters have that property, so i looked for columns with zeros all around

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/f964cb52-8982-420a-b6fd-87d1c9d12567)

this means that i have to bit shift it left by one to get the actual answer, because then the firstmost bit of each byte will be a 0

```
>>> from Crypto.Util.number import *
>>> long_to_bytes(bytes_to_long(b'&*\xb6\xb6!\xaf\xa3\xb7')<<1)
b'LUmlC_Gn'
```

then, the flag is `ictf{F4ctorInG_ClmUL}`
