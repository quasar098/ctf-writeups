# pickled-rolls

## problem

```py
from pickle import loads

flag = loads(bytes.fromhex('80047d284b2a4b754b124b374b264b674b154b5f4b284b724b1e4b5f4b054b704b194b334b224b5f4b074b634b2c4b7d4b114b5f4b134b684b104b6d4b0f4b304b0a4b334b244b344b204b724b254b6e4b1a4b724b234b644b1b4b6e636f730a72656d6f76650a635f5f6d61696e5f5f0a5f5f66696c655f5f0a8552304b084b6b4b034b664b0b4b354b0e4b724b1c4b334b214b334b294b304b044b7b4b184b374b144b334b2b4b354b1f4b344b0d4b664b174b6e4b014b634b004b694b0c4b5f4b274b334b1d4b374b024b744b064b314b094b314b164b317530636261736536340a6236346465636f64650a566148523063484d364c7939336433637565573931644856695a53356a62323076643246305932672f646a316b55586330647a6c585a31686a55513d3d0a85522e'))

print(flag)
```

## solution

the pickle data prints the rickroll link and deletes the file we saved the program to, so we have to figure out a way to extract the pickle data.

here, we can use the `pickletools` python module to analyse the code:

```py
import pickletools
pickletools.dis(bytes.fromhex('80047d284b2a4b754b124b374b264b674b154b5f4b284b724b1e4b5f4b054b704b194b334b224b5f4b074
b634b2c4b7d4b114b5f4b134b684b104b6d4b0f4b304b0a4b334b244b344b204b724b254b6e4b1a4b724b234b644b1b4b6e636f730a72656d6f76650
a635f5f6d61696e5f5f0a5f5f66696c655f5f0a8552304b084b6b4b034b664b0b4b354b0e4b724b1c4b334b214b334b294b304b044b7b4b184b374b1
44b334b2b4b354b1f4b344b0d4b664b174b6e4b014b634b004b694b0c4b5f4b274b334b1d4b374b024b744b064b314b094b314b164b3175306362617
36536340a6236346465636f64650a566148523063484d364c7939336433637565573931644856695a53356a62323076643246305932672f646a316b5
5586330647a6c585a31686a55513d3d0a85522e'))
```

output:

```
    0: \x80 PROTO      4
    2: }    EMPTY_DICT
    3: (    MARK
    4: K        BININT1    42
    6: K        BININT1    117
    8: K        BININT1    18
   10: K        BININT1    55
   12: K        BININT1    38
   14: K        BININT1    103
   16: K        BININT1    21
   18: K        BININT1    95
   20: K        BININT1    40
   22: K        BININT1    114
   24: K        BININT1    30
   26: K        BININT1    95
   28: K        BININT1    5
   30: K        BININT1    112
   32: K        BININT1    25
   34: K        BININT1    51
   36: K        BININT1    34
   38: K        BININT1    95
   40: K        BININT1    7
   42: K        BININT1    99
   44: K        BININT1    44
   46: K        BININT1    125
   48: K        BININT1    17
   50: K        BININT1    95
   52: K        BININT1    19
   54: K        BININT1    104
   56: K        BININT1    16
   58: K        BININT1    109
   60: K        BININT1    15
   62: K        BININT1    48
   64: K        BININT1    10
   66: K        BININT1    51
   68: K        BININT1    36
   70: K        BININT1    52
   72: K        BININT1    32
   74: K        BININT1    114
   76: K        BININT1    37
   78: K        BININT1    110
   80: K        BININT1    26
   82: K        BININT1    114
   84: K        BININT1    35
   86: K        BININT1    100
   88: K        BININT1    27
   90: K        BININT1    110
   92: c        GLOBAL     'os remove'
  103: c        GLOBAL     '__main__ __file__'
  122: \x85     TUPLE1
  123: R        REDUCE
  124: 0        POP
  125: K        BININT1    8
  127: K        BININT1    107
  129: K        BININT1    3
  131: K        BININT1    102
  133: K        BININT1    11
  135: K        BININT1    53
  137: K        BININT1    14
  139: K        BININT1    114
  141: K        BININT1    28
  143: K        BININT1    51
  145: K        BININT1    33
  147: K        BININT1    51
  149: K        BININT1    41
  151: K        BININT1    48
  153: K        BININT1    4
  155: K        BININT1    123
  157: K        BININT1    24
  159: K        BININT1    55
  161: K        BININT1    20
  163: K        BININT1    51
  165: K        BININT1    43
  167: K        BININT1    53
  169: K        BININT1    31
  171: K        BININT1    52
  173: K        BININT1    13
  175: K        BININT1    102
  177: K        BININT1    23
  179: K        BININT1    110
  181: K        BININT1    1
  183: K        BININT1    99
  185: K        BININT1    0
  187: K        BININT1    105
  189: K        BININT1    12
  191: K        BININT1    95
  193: K        BININT1    39
  195: K        BININT1    51
  197: K        BININT1    29
  199: K        BININT1    55
  201: K        BININT1    2
  203: K        BININT1    116
  205: K        BININT1    6
  207: K        BININT1    49
  209: K        BININT1    9
  211: K        BININT1    49
  213: K        BININT1    22
  215: K        BININT1    49
  217: u        SETITEMS   (MARK at 3)
  218: 0    POP
  219: c    GLOBAL     'base64 b64decode'
  237: V    UNICODE    'aHR0cHM6Ly93d3cueW91dHViZS5jb20vd2F0Y2g/dj1kUXc0dzlXZ1hjUQ=='
  299: \x85 TUPLE1
  300: R    REDUCE
  301: .    STOP
```
here, we can see a base64 decode for the rickroll link

take a look at the documentation [here](https://github.com/python/cpython/blob/main/Lib/pickle.py) and see the different opcodes that pickle uses.

we can see that there is a lot of BININT1 opcodes here, and also that there is an empty dict created at the beginning of the opcodes.

from this, we can infer that the integers are dictionary item data, and so we can reassemble the dictionary using [text format wizard](https://quasar.name/text-format-wizard/)

```py
{42: 117
18: 55
38: 103
21: 95
40: 114
30: 95
5: 112
25: 51
34: 95
7: 99
44: 125
17: 95
19: 104
16: 109
15: 48
10: 51
36: 52
32: 114
37: 110
26: 114
35: 100
27: 110
8: 107
3: 102
11: 53
14: 114
28: 51
33: 51
41: 48
4: 123
24: 55
20: 51
43: 53
31: 52
13: 102
23: 110
1: 99
0: 105
12: 95
39: 51
29: 55
2: 116
6: 49
9: 49
22: 49}
```

these are just decimal codes for ascii chars, we can just sort them with python or decode by hand (which i did)

the flag is `ictf{p1ck135_fr0m_7h3_1n73rn37_4r3_d4ng3r0u5}`
