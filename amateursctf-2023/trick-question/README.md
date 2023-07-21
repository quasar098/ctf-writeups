# trick-question

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/3e7dc4eb-88d1-4b4b-899e-f6f6dd06c344)

## solution

i have this modified version of pycdc that can decompile anything, even if there are missing opcodes, which was very useful here and so i was able to decompile the pyc into a python file

first.py
```py
# Source Generated with Decompyle++
# File: trick-question.pyc (Python 3.10)

from base64 import b64decode as dec

# noinspection PyShadowingNames
b64decode = lambda x: print(dec(x).decode('ascii')) or exec(dec(x))

r = [
    0,
    0,
    1,
    1,
    0,
    1,
    0,
... etc ...
    0,
    0,
    0]
x = [
    'gIHByaW50KCJJbmNvcnJlY3QuIik=',
    'gcHJpbnQoIkNvcnJlY3QhIikKZWxzZToKIC',
    'CIgogOpkiIgozZhxmZgUGa0BiclRnbFJCK0VHculGKrNWZoNGImlmCp0HZpBiOnQWansHIsUGZvNGKps2Ylh2YoUGc5RHI9',
    'yajVGajpgCpkCKgwSKo',
    'xXHgwY1x4MDInLC',
... etc ...
    'DecRmMwgHXqFDM4xlaw',
    'xa1x4MDNyVHRceD',
    'xXHgwMHE6fFx4MDFnXHgwMGRcblx4YTJceD',
    'yXHhhMVx4MDFceD',
    'Decx3MwgHXwEGecFDM4xFfHJnMwgHXrRHXkNDM4xFfz',
    'xRFx4MDBdXHJcXFx4MDJ9XHgwMn1ceD',
    '4XHgxOVx4MDB8XHgwMFx4ODNceD',
    'DecRmMwgHXqFDM4xlaw',
    'DecRXMwgHX9BDM4x1Zw',
    'DecNFMwgHX5EDecNDM4xFZy',
    'DecpWMwgHXqBDM4xFdwI3MwgHXrdDM4xFZx',
    '2XHgxOVx4MDB8XHgwMFx4ODNceD',
    'DecRmMwgHXqFDM4xlaw',
    'DecRHMwgHX9BDM4xVOxgHXy',
    'wZFx4MDFkXHgwNFx4ODVceD',
    'DecxHMwgHXTBDM4xVOxgHXz',
    'DecRmMwgHXqFDM4xlaw',
    'DecRXYxgHXyNDM4x1a1',
    'wZFx4MDRceDE5XHgwMGRceD',
    'zXHgxOVx4MDBTXHgwMHxceD',
    'DecRmMwgHXqFDM4xlaw',
    'ya1x4MDNyXHgwZXRceD',
    'xXHg4NVx4MDJceDE5XHgwMGRceD',
    'Y2hlY2sgPSBsYW1iZGE6Tm9uZQpjb2RlID0gdHlwZShjaGVjay5fX2NvZGVfXykoMSwgMCwgMCwgNiwgNSwgNjcsIGInfFx4MDBkXHgwMGRceD']
for i in range(len(r)):
    if r[i]:
        x[i] = x[i][::-1]
b64decode('A'.join(x[::-1]))
```

to make this readable, we can just print out what is going to be base64 decoded

second.py
```py
check = lambda: None
code = type(check.__code__)(
    1,  # argcount
    0,  # irrelevant
    0,  # irrelevant
    6,  # nlocals
    5,  # stacksize
    67,  # flags
    b"|\x00d\x00d\x01\x85\x02\x19\x00d\x02k\x03r\x0et\x00j\x01j\x02d\x03\x19\x00S\x00|\x00d\x04\x19\x00d\x05k\x03r\x1at\x00j\x01j\x02d\x03\x19\x00S\x00|\x00d\x01d\x04\x85\x02\x19\x00}\x00t\x00j\x01j\x02d\x06\x19\x00|\x00\x83\x01d\x07k\x03r0t\x00j\x01j\x02d\x03\x19\x00S\x00g\x00}\x01t\x00j\x01j\x02d\x08\x19\x00|\x00\x83\x01D\x00]\r\\\x02}\x02}\x03|\x03d\tk\x02rG|\x01\xa0\x03|\x02\xa1\x01\x01\x00q:|\x01g\x00d\n\xa2\x01k\x03rTt\x00j\x01j\x02d\x03\x19\x00S\x00|\x00\xa0\x04\xa1\x00\xa0\x05d\x0b\xa1\x01}\x00|\x00d\x0c\x19\x00d\x00d\x00d\x04\x85\x03\x19\x00d\rk\x03rlt\x00j\x01j\x02d\x03\x19\x00S\x00|\x00d\x0e\x19\x00d\x0c\x19\x00|\x00d\x0e\x19\x00d\x0e\x19\x00\x17\x00|\x00d\x0e\x19\x00d\x0f\x19\x00\x18\x00|\x00d\x0e\x19\x00d\x0e\x19\x00|\x00d\x0e\x19\x00d\x0f\x19\x00\x17\x00|\x00d\x0e\x19\x00d\x0c\x19\x00\x18\x00|\x00d\x0e\x19\x00d\x0f\x19\x00|\x00d\x0e\x19\x00d\x0c\x19\x00\x17\x00|\x00d\x0e\x19\x00d\x0e\x19\x00\x18\x00f\x03d\x10k\x03r\xa9t\x00j\x01j\x02d\x03\x19\x00S\x00t\x00j\x01j\x02d\x11\x19\x00d\x12\x83\x01\xa0\x06|\x00d\x0f\x19\x00\xa1\x01\xa0\x07\xa1\x00d\x13k\x03r\xc0t\x00j\x01j\x02d\x03\x19\x00S\x00t\x00j\x01j\x02d\x11\x19\x00d\x14\x83\x01}\x04|\x04\xa0\x08|\x00d\x0f\x19\x00\xa1\x01\x01\x00t\x00j\x01j\x02d\x15\x19\x00|\x00d\x16\x19\x00\x83\x01|\x00d\x16<\x00|\x04\xa0\t|\x00d\x16\x19\x00\xa1\x01\x01\x00|\x00d\x16\x19\x00g\x00d\x17\xa2\x01k\x03r\xf0t\x00j\x01j\x02d\x03\x19\x00S\x00|\x00d\x18\x19\x00d\x19\x17\x00d\x1ak\x03r\xfet\x00j\x01j\x02d\x03\x19\x00S\x00t\x00j\x01j\x02d\x1b\x19\x00\xa0\n|\x00d\x1c\x19\x00d\x0cd\x18\x85\x02\x19\x00d\x1d\xa1\x02|\x04\xa0\x0bd\x0cd\x1e\xa1\x02A\x00d\x1fk\x03\x90\x01r\x1dt\x00j\x01j\x02d\x03\x19\x00S\x00t\x00j\x01j\x02d\x1b\x19\x00\xa0\n|\x00d\x1c\x19\x00d\x18d \x85\x02\x19\x00d\x1d\xa1\x02|\x04\xa0\x0bd\x0cd\x1e\xa1\x02A\x00d!k\x03\x90\x01r<t\x00j\x01j\x02d\x03\x19\x00S\x00t\x00j\x01j\x02d\x1b\x19\x00\xa0\n|\x00d\x1c\x19\x00d d\x01\x85\x02\x19\x00d\"\x17\x00d\x1d\xa1\x02|\x04\xa0\x0bd\x0cd\x1e\xa1\x02A\x00d#k\x03\x90\x01r]t\x00j\x01j\x02d\x03\x19\x00S\x00d\x0c}\x05|\x00d$\x19\x00D\x00]\x0b}\x02|\x05d%9\x00}\x05|\x05|\x027\x00}\x05\x90\x01qct\x00j\x01j\x02d&\x19\x00|\x05\x83\x01d'k\x03\x90\x01r\x80t\x00j\x01j\x02d\x03\x19\x00S\x00t\x00j\x01j\x02d(\x19\x00S\x00",
    # codestring
    (  # constants
        None,
        12,
        "amateursCTF{",
        "False",
        -1,
        "}",
        "len",
        42,
        "enumerate",
        "_",
        (7, 11, 13, 20, 23, 35),
        b"_",
        0,
        b"sn0h7YP",
        1,
        2,
        (160, 68, 34),
        "__import__",
        "hashlib",
        "4b227777d4dd1fc61c6f884f48641d02b4d121d3fd328cb08b5531fcacdabf8a",
        "random",
        "list",
        3,
        (49, 89, 102, 109, 108, 52),
        4,
        b"freebie",
        b"0ffreebie",
        "int",
        5,
        "little",
        4294967295,
        4227810561,
        8,
        825199122,
        b"\x00",
        4277086886,
        6,
        128,
        "hex",
        "0x29ee69af2f3",
        "True",
        "Did you know? pycdc can decompile marshaled code objects. Just make sure you mention the right version!",
    ),
    (  # names
        "id",
        "__self__",
        "__dict__",
        "append",
        "encode",
        "split",
        "sha256",
        "hexdigest",
        "seed",
        "shuffle",
        "from_bytes",
        "randint",
    ),  # varnames
    ("input", "underscores", "i", "x", "r", "c"),
    "",  # filename
    "check",  # name
    3,  # firstlineno
    # lnotab
    b'\x10\x01\x0c\x01\x0c\x01\x0c\x01\x0c\x01\x14\x02\x0c\x01\x04\x02\x18\x01\x08\x01\n\x01\x02\x80\x0c\x01\x0c\x01\x0e\x02\x16\x01\x0c\x01n\x03\x0c\x01"\x02\x0c\x01\x10\x02\x0e\x01\x18\x01\x0e\x01\x10\x02\x0c\x01\x10\x02\x0c\x012\x02\x0c\x012\x02\x0c\x016\x02\x0c\x01\x04\x02\x0c\x01\x08\x01\x0c\x01\x16\x02\x0c\x01\x0c\x02',
    (),  # freevars
    (),  # cellvars
)

check = type(check)(code, {"id": id})

# diss(check)
if check(input("Enter the flag: ")):
    print("Correct!")
else:
    print("Incorrect.")
```

now we have something to work with

can't exactly remember what i did here, but i think i was able to turn the function into a bytecode dump of some sort

```py
Name:              check
Filename:
Argument count:    1
Positional-only arguments: 0
Kw-only arguments: 0
Number of locals:  6
Stack size:        5
Flags:             OPTIMIZED, NEWLOCALS, NOFREE
Constants:
   0: None
   1: 12
   2: 'amateursCTF{'
   3: 'False'
   4: -1
   5: '}'
   6: 'len'
   7: 42
   8: 'enumerate'
   9: '_'
  10: (7, 11, 13, 20, 23, 35)
  11: b'_'
  12: 0
  13: b'sn0h7YP'
  14: 1
  15: 2
  16: (160, 68, 34)
  17: '__import__'
  18: 'hashlib'
  19: '4b227777d4dd1fc61c6f884f48641d02b4d121d3fd328cb08b5531fcacdabf8a'
  20: 'random'
  21: 'list'
  22: 3
  23: (49, 89, 102, 109, 108, 52)
  24: 4
  25: b'freebie'
  26: b'0ffreebie'
  27: 'int'
  28: 5
  29: 'little'
  30: 4294967295
  31: 4227810561
  32: 8
  33: 825199122
  34: b'\x00'
  35: 4277086886
  36: 6
  37: 128
  38: 'hex'
  39: '0x29ee69af2f3'
  40: 'True'
  41: 'Did you know? pycdc can decompile marshaled code objects. Just make sure you mention the right version!'
Names:
   0: id
   1: __self__
   2: __dict__
   3: append
   4: encode
   5: split
   6: sha256
   7: hexdigest
   8: seed
   9: shuffle
  10: from_bytes
  11: randint
Variable names:
   0: input
   1: underscores
   2: i
   3: x
   4: r
   5: c

              # assert input[:12] == "amateursCTF{"
  3           0 LOAD_FAST                0 (input)
              2 LOAD_CONST               0 (None)
              4 LOAD_CONST               1 (12)
              6 BUILD_SLICE              2
              8 BINARY_SUBSCR
             10 LOAD_CONST               2 ('amateursCTF{')
             12 COMPARE_OP               3 (!=)
        >>   14 POP_JUMP_IF_FALSE       14

  4          16 LOAD_GLOBAL              0 (id)
             18 LOAD_ATTR                1 (__self__)
             20 LOAD_ATTR                2 (__dict__)
             22 LOAD_CONST               3 ('False')
             24 BINARY_SUBSCR
        >>   26 RETURN_VALUE

             # assert input[-1:] == "{"
  5          28 LOAD_FAST                0 (input)
             30 LOAD_CONST               4 (-1)
             32 BINARY_SUBSCR
             34 LOAD_CONST               5 ('}')
             36 COMPARE_OP               3 (!=)
             38 POP_JUMP_IF_FALSE       26

  6          40 LOAD_GLOBAL              0 (id)
             42 LOAD_ATTR                1 (__self__)
             44 LOAD_ATTR                2 (__dict__)
             46 LOAD_CONST               3 ('False')
        >>   48 BINARY_SUBSCR
             50 RETURN_VALUE

             # input = input[12:-1]
  7          52 LOAD_FAST                0 (input)
             54 LOAD_CONST               1 (12)
             56 LOAD_CONST               4 (-1)
        >>   58 BUILD_SLICE              2
             60 BINARY_SUBSCR
             62 STORE_FAST               0 (input)

             # assert len(input) == 42
  8          64 LOAD_GLOBAL              0 (id)
             66 LOAD_ATTR                1 (__self__)
             68 LOAD_ATTR                2 (__dict__)
             70 LOAD_CONST               6 ('len')
             72 BINARY_SUBSCR
             74 LOAD_FAST                0 (input)
             76 CALL_FUNCTION            1
             78 LOAD_CONST               7 (42)
             80 COMPARE_OP               3 (!=)
             82 POP_JUMP_IF_FALSE       48

 10     >>   84 LOAD_GLOBAL              0 (id)
             86 LOAD_ATTR                1 (__self__)
             88 LOAD_ATTR                2 (__dict__)
             90 LOAD_CONST               3 ('False')
             92 BINARY_SUBSCR
             94 RETURN_VALUE

             # underscores = []
 11          96 BUILD_LIST               0
             98 STORE_FAST               1 (underscores)

            # for i, x in enumerate(input):
 13         100 LOAD_GLOBAL              0 (id)
            102 LOAD_ATTR                1 (__self__)
            104 LOAD_ATTR                2 (__dict__)
            106 LOAD_CONST               8 ('enumerate')
        >>  108 BINARY_SUBSCR
            110 LOAD_FAST                0 (input)
            112 CALL_FUNCTION            1
            114 GET_ITER
            116 FOR_ITER                13 (to 131)
            118 UNPACK_SEQUENCE          2
            120 STORE_FAST               2 (i)
            122 STORE_FAST               3 (x)

            #     if x == "_":
 14         124 LOAD_FAST                3 (x)
            126 LOAD_CONST               9 ('_')
            128 COMPARE_OP               2 (==)
            130 POP_JUMP_IF_FALSE       71

            #         underscores.append(i)
 15         132 LOAD_FAST                1 (underscores)
            134 LOAD_METHOD              3 (append)
            136 LOAD_FAST                2 (i)
            138 CALL_METHOD              1
            140 POP_TOP

            # continue the loop
 16         142 JUMP_ABSOLUTE           58

            # underscores
-112        144 LOAD_FAST                1 (underscores)
            146 BUILD_LIST               0
            148 LOAD_CONST              10 ((7, 11, 13, 20, 23, 35))  # amateursCTF{......._..._._......_.._..........._......}
            150 LIST_EXTEND              1
            152 COMPARE_OP               3 (!=)
            154 POP_JUMP_IF_FALSE       84

-111        156 LOAD_GLOBAL              0 (id)
            158 LOAD_ATTR                1 (__self__)
            160 LOAD_ATTR                2 (__dict__)
            162 LOAD_CONST               3 ('False')
            164 BINARY_SUBSCR
            166 RETURN_VALUE

            # input = input.encode().split(b'_')
-110        168 LOAD_FAST                0 (input)
            170 LOAD_METHOD              4 (encode)
            172 CALL_METHOD              0
            174 LOAD_METHOD              5 (split)
            176 LOAD_CONST              11 (b'_')
            178 CALL_METHOD              1
            180 STORE_FAST               0 (input)

            # assert input[::-1] == "sn0h7YP"
-108        182 LOAD_FAST                0 (input)
            184 LOAD_CONST              12 (0)
            186 BINARY_SUBSCR
            188 LOAD_CONST               0 (None)
            190 LOAD_CONST               0 (None)
        >>  192 LOAD_CONST               4 (-1)
            194 BUILD_SLICE              3
            196 BINARY_SUBSCR
            198 LOAD_CONST              13 (b'sn0h7YP')
            200 COMPARE_OP               3 (!=)
            202 POP_JUMP_IF_FALSE      108

-107        204 LOAD_GLOBAL              0 (id)
            206 LOAD_ATTR                1 (__self__)
            208 LOAD_ATTR                2 (__dict__)
            210 LOAD_CONST               3 ('False')
            212 BINARY_SUBSCR
            214 RETURN_VALUE

            # (a+b-c, b+c-a, c+a-b) == (160, 68, 34)
            # (a,b,c) == tuple(b'ar3')
-106        216 LOAD_FAST                0 (input)
            218 LOAD_CONST              14 (1)
            220 BINARY_SUBSCR
            222 LOAD_CONST              12 (0)
            224 BINARY_SUBSCR
            226 LOAD_FAST                0 (input)
            228 LOAD_CONST              14 (1)
            230 BINARY_SUBSCR
            232 LOAD_CONST              14 (1)
            234 BINARY_SUBSCR
            236 BINARY_ADD
            238 LOAD_FAST                0 (input)
        >>  240 LOAD_CONST              14 (1)
            242 BINARY_SUBSCR
            244 LOAD_CONST              15 (2)
            246 BINARY_SUBSCR
            248 BINARY_SUBTRACT
            ==
            250 LOAD_FAST                0 (input)
            252 LOAD_CONST              14 (1)
        >>  254 BINARY_SUBSCR
            256 LOAD_CONST              14 (1)
            258 BINARY_SUBSCR
            260 LOAD_FAST                0 (input)
            262 LOAD_CONST              14 (1)
            264 BINARY_SUBSCR
            266 LOAD_CONST              15 (2)
            268 BINARY_SUBSCR
            270 BINARY_ADD
            272 LOAD_FAST                0 (input)
            274 LOAD_CONST              14 (1)
            276 BINARY_SUBSCR
            278 LOAD_CONST              12 (0)
            280 BINARY_SUBSCR
            282 BINARY_SUBTRACT
            ==
            284 LOAD_FAST                0 (input)
            286 LOAD_CONST              14 (1)
            288 BINARY_SUBSCR
            290 LOAD_CONST              15 (2)
            292 BINARY_SUBSCR
            294 LOAD_FAST                0 (input)
            296 LOAD_CONST              14 (1)
            298 BINARY_SUBSCR
            300 LOAD_CONST              12 (0)
            302 BINARY_SUBSCR
            304 BINARY_ADD
            306 LOAD_FAST                0 (input)
            308 LOAD_CONST              14 (1)
            310 BINARY_SUBSCR
            312 LOAD_CONST              14 (1)
            314 BINARY_SUBSCR
        >>  316 BINARY_SUBTRACT
            ==
            318 BUILD_TUPLE              3
            320 LOAD_CONST              16 ((160, 68, 34))
            322 COMPARE_OP               3 (!=)
            324 POP_JUMP_IF_FALSE      169

-103        326 LOAD_GLOBAL              0 (id)
            328 LOAD_ATTR                1 (__self__)
            330 LOAD_ATTR                2 (__dict__)
            332 LOAD_CONST               3 ('False')
            334 BINARY_SUBSCR
            336 RETURN_VALUE

            # assert sha256(input[2]) == "4b227777d4dd1fc61c6f884f48641d02b4d121d3fd328cb08b5531fcacdabf8a"
            # input[2] is "4" btw
-102        338 LOAD_GLOBAL              0 (id)
            340 LOAD_ATTR                1 (__self__)
            342 LOAD_ATTR                2 (__dict__)
            344 LOAD_CONST              17 ('__import__')
            346 BINARY_SUBSCR
            348 LOAD_CONST              18 ('hashlib')
            350 CALL_FUNCTION            1
            352 LOAD_METHOD              6 (sha256)
            354 LOAD_FAST                0 (input)
            356 LOAD_CONST              15 (2)
            358 BINARY_SUBSCR
            360 CALL_METHOD              1
            362 LOAD_METHOD              7 (hexdigest)
            364 CALL_METHOD              0
            366 LOAD_CONST              19 ('4b227777d4dd1fc61c6f884f48641d02b4d121d3fd328cb08b5531fcacdabf8a')
            368 COMPARE_OP               3 (!=)
            370 POP_JUMP_IF_FALSE      192

-100        372 LOAD_GLOBAL              0 (id)
            374 LOAD_ATTR                1 (__self__)
            376 LOAD_ATTR                2 (__dict__)
            378 LOAD_CONST               3 ('False')
            380 BINARY_SUBSCR
            382 RETURN_VALUE

            # import random
-99     >>  384 LOAD_GLOBAL              0 (id)
            386 LOAD_ATTR                1 (__self__)
            388 LOAD_ATTR                2 (__dict__)
            390 LOAD_CONST              17 ('__import__')
            392 BINARY_SUBSCR
            394 LOAD_CONST              20 ('random')
            396 CALL_FUNCTION            1
            398 STORE_FAST               4 (r)

            # random.seed(input[2])
-97         400 LOAD_FAST                4 (r)
            402 LOAD_METHOD              8 (seed)
            404 LOAD_FAST                0 (input)
            406 LOAD_CONST              15 (2)
            408 BINARY_SUBSCR
            410 CALL_METHOD              1
            412 POP_TOP

            # input[3] == list(input[3])
-96         414 LOAD_GLOBAL              0 (id)
            416 LOAD_ATTR                1 (__self__)
            418 LOAD_ATTR                2 (__dict__)
            420 LOAD_CONST              21 ('list')
            422 BINARY_SUBSCR
            424 LOAD_FAST                0 (input)
            426 LOAD_CONST              22 (3)
            428 BINARY_SUBSCR
            430 CALL_FUNCTION            1
            432 LOAD_FAST                0 (input)
            434 LOAD_CONST              22 (3)
            436 STORE_SUBSCR

            # random.shuffle(input[3])
            # [1,2,3,4,5,6] -> [4, 6, 1, 3, 5, 2]
-95         438 LOAD_FAST                4 (r)
            440 LOAD_METHOD              9 (shuffle)
            442 LOAD_FAST                0 (input)
            444 LOAD_CONST              22 (3)
            446 BINARY_SUBSCR
            448 CALL_METHOD              1
            450 POP_TOP

            # assert input[3] == (49, 89, 102, 109, 108, 52)
            # original input[3] is f4m1lY
-94         452 LOAD_FAST                0 (input)
            454 LOAD_CONST              22 (3)
            456 BINARY_SUBSCR
            458 BUILD_LIST               0
            460 LOAD_CONST              23 ((49, 89, 102, 109, 108, 52))
            462 LIST_EXTEND              1
            464 COMPARE_OP               3 (!=)
            466 POP_JUMP_IF_FALSE      240

-92         468 LOAD_GLOBAL              0 (id)
            470 LOAD_ATTR                1 (__self__)
            472 LOAD_ATTR                2 (__dict__)
            474 LOAD_CONST               3 ('False')
            476 BINARY_SUBSCR
            478 RETURN_VALUE

            # assert input(4) + b'freebie' == b"0ffreebie"
-91         480 LOAD_FAST                0 (input)
            482 LOAD_CONST              24 (4)
            484 BINARY_SUBSCR
            486 LOAD_CONST              25 (b'freebie')
            488 BINARY_ADD
            490 LOAD_CONST              26 (b'0ffreebie')
            492 COMPARE_OP               3 (!=)
            494 POP_JUMP_IF_FALSE      254

-89         496 LOAD_GLOBAL              0 (id)
            498 LOAD_ATTR                1 (__self__)
            500 LOAD_ATTR                2 (__dict__)
            502 LOAD_CONST               3 ('False')
            504 BINARY_SUBSCR
            506 RETURN_VALUE

            # assert random.randint(0, 4294967295) ^ int.from_bytes(input[5][0:4], 'little') == 4227810561
            # input[5][:4] is b'N0Nv' if my calculations are correct
            # dont forget that it's little endian!
-88         508 LOAD_GLOBAL              0 (id)
            510 LOAD_ATTR                1 (__self__)
            512 LOAD_ATTR                2 (__dict__)
            514 LOAD_CONST              27 ('int')
            516 BINARY_SUBSCR
            518 LOAD_METHOD             10 (from_bytes)
            520 LOAD_FAST                0 (input)
            522 LOAD_CONST              28 (5)
            524 BINARY_SUBSCR
            526 LOAD_CONST              12 (0)
            528 LOAD_CONST              24 (4)
            530 BUILD_SLICE              2
            532 BINARY_SUBSCR
            534 LOAD_CONST              29 ('little')
            536 CALL_METHOD              2
            538 LOAD_FAST                4 (r)
            540 LOAD_METHOD             11 (randint)
            542 LOAD_CONST              12 (0)
            544 LOAD_CONST              30 (4294967295)
            546 CALL_METHOD              2
            548 BINARY_XOR
            550 LOAD_CONST              31 (4227810561)
            552 COMPARE_OP               3 (!=)
            554 EXTENDED_ARG             1
            556 POP_JUMP_IF_FALSE      285

-86         558 LOAD_GLOBAL              0 (id)
            560 LOAD_ATTR                1 (__self__)
            562 LOAD_ATTR                2 (__dict__)
            564 LOAD_CONST               3 ('False')
            566 BINARY_SUBSCR
            568 RETURN_VALUE

            # same as before but different constants
            # input[5][4:8] is b'3nom'
-85         570 LOAD_GLOBAL              0 (id)
            572 LOAD_ATTR                1 (__self__)
            574 LOAD_ATTR                2 (__dict__)
            576 LOAD_CONST              27 ('int')
            578 BINARY_SUBSCR
            580 LOAD_METHOD             10 (from_bytes)
            582 LOAD_FAST                0 (input)
            584 LOAD_CONST              28 (5)
            586 BINARY_SUBSCR
            588 LOAD_CONST              24 (4)
            590 LOAD_CONST              32 (8)
            592 BUILD_SLICE              2
            594 BINARY_SUBSCR
            596 LOAD_CONST              29 ('little')
            598 CALL_METHOD              2
            600 LOAD_FAST                4 (r)
            602 LOAD_METHOD             11 (randint)
            604 LOAD_CONST              12 (0)
            606 LOAD_CONST              30 (4294967295)
            608 CALL_METHOD              2
            610 BINARY_XOR
            612 LOAD_CONST              33 (825199122)
            614 COMPARE_OP               3 (!=)
            616 EXTENDED_ARG             1
            618 POP_JUMP_IF_FALSE      316

-83         620 LOAD_GLOBAL              0 (id)
            622 LOAD_ATTR                1 (__self__)
            624 LOAD_ATTR                2 (__dict__)
            626 LOAD_CONST               3 ('False')
            628 BINARY_SUBSCR
            630 RETURN_VALUE

            # last part is b'ous'
-82         632 LOAD_GLOBAL              0 (id)
            634 LOAD_ATTR                1 (__self__)
            636 LOAD_ATTR                2 (__dict__)
            638 LOAD_CONST              27 ('int')
            640 BINARY_SUBSCR
            642 LOAD_METHOD             10 (from_bytes)
            644 LOAD_FAST                0 (input)
            646 LOAD_CONST              28 (5)
            648 BINARY_SUBSCR
            650 LOAD_CONST              32 (8)
            652 LOAD_CONST               1 (12)
            654 BUILD_SLICE              2
            656 BINARY_SUBSCR
            658 LOAD_CONST              34 (b'\x00')
            660 BINARY_ADD
            662 LOAD_CONST              29 ('little')
            664 CALL_METHOD              2
            666 LOAD_FAST                4 (r)
            668 LOAD_METHOD             11 (randint)
            670 LOAD_CONST              12 (0)
            672 LOAD_CONST              30 (4294967295)
            674 CALL_METHOD              2
            676 BINARY_XOR
            678 LOAD_CONST              35 (4277086886)
            680 COMPARE_OP               3 (!=)
            682 EXTENDED_ARG             1
            684 POP_JUMP_IF_FALSE      349

-80         686 LOAD_GLOBAL              0 (id)
            688 LOAD_ATTR                1 (__self__)
            690 LOAD_ATTR                2 (__dict__)
            692 LOAD_CONST               3 ('False')
            694 BINARY_SUBSCR
            696 RETURN_VALUE

            # c = 0
-79         698 LOAD_CONST              12 (0)
            700 STORE_FAST               5 (c)

            # for i in input[6]:
-77         702 LOAD_FAST                0 (input)
            704 LOAD_CONST              36 (6)
            706 BINARY_SUBSCR
            708 GET_ITER
            710 FOR_ITER                11 (to 723)
            712 STORE_FAST               2 (i)

            #     c = 128 * c
-76         714 LOAD_FAST                5 (c)
            716 LOAD_CONST              37 (128)
            718 INPLACE_MULTIPLY
            720 STORE_FAST               5 (c)

            #     c = i+c
-75         722 LOAD_FAST                5 (c)
            724 LOAD_FAST                2 (i)
            726 INPLACE_ADD
            728 STORE_FAST               5 (c)
            730 EXTENDED_ARG             1
            732 JUMP_ABSOLUTE          355

            # assert hex(c) == '0x29ee69af2f3'
            # this means it must be b'Sn4kes'
-74         734 LOAD_GLOBAL              0 (id)
            736 LOAD_ATTR                1 (__self__)
            738 LOAD_ATTR                2 (__dict__)
            740 LOAD_CONST              38 ('hex')
            742 BINARY_SUBSCR
            744 LOAD_FAST                5 (c)
            746 CALL_FUNCTION            1
            748 LOAD_CONST              39 ('0x29ee69af2f3')
            750 COMPARE_OP               3 (!=)
            752 EXTENDED_ARG             1
            754 POP_JUMP_IF_FALSE      384

-72         756 LOAD_GLOBAL              0 (id)
            758 LOAD_ATTR                1 (__self__)
            760 LOAD_ATTR                2 (__dict__)
            762 LOAD_CONST               3 ('False')
            764 BINARY_SUBSCR
            766 RETURN_VALUE

-71         768 LOAD_GLOBAL              0 (id)
            770 LOAD_ATTR                1 (__self__)
            772 LOAD_ATTR                2 (__dict__)
            774 LOAD_CONST              40 ('True')
            776 BINARY_SUBSCR
            778 RETURN_VALUE
```

we can see that different functions are being used here to check different parts of the flag

we can take a look at [this](http://vega.lpl.arizona.edu/python/lib/bytecodes.html) for more bytecode specifications

then, we manually rev each part of the flag and get `amateursCTF{PY7h0ns_4re_4_f4m1lY_0f_N0Nv3nom0us_Sn4kes}`
