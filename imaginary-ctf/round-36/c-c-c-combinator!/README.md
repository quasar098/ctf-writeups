# c-c-c-combinator!

## problem

I don't know Y this is the only challenge I can come up with right now...

see `combinator` binary file

## solution

we can see that three values are being XOR'd together and that is being checked equals to 0 if it is then we win

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/aaf6a8db-dbc9-4fd9-8139-f2e1776038e7)

for each index from 0 to 0x2e, it is `var_c8[i] ^ flag_input[i] ^ weird_function(weird_thing, i+20)`

we don't know the output of weird_function, but we can just use gdb/binja/ghidra/whatever to find the answer by running the code with breakpoints and extracting register values

we know that `var_c8[i] ^ flag_input[i] ^ weird_function(weird_thing, i+20) = 0`, so we know that `var_c8[i] ^ weird_function(weird_thing, i+20) = flag_input[i]`

we can xor the var_c8 by the weird_function output like so:

```py
dat1 = """0x4f9b
0x56d7
0x9c6f
0x57d5
0x2286
0x4ec0
0x8585
0x6f83
0x6c7d
0x6d4d
0x57b9
0xfe7
0x1324
0x59e
0x2cf8
0x818a
0x3b7c
0x750b
0x342
0x7984
0x62ef
0x3c00
0x933e
0x7f5f
0x1a4f
0x3dc5
0x8172
0x156a
0x3909
0x69ad
0x2703
0x6640
0x38c2
0x86ce
0x69d8
0x52fa
0x739
0x6518
0x8e28
0x77f7
0x9ee
0x6891
0x8aee
0x7f92
0x41e7
0x78cc
0x591""".splitlines()
dat2 = """0x4ff2
0x56b4
0x9c1b
0x57b3
0x22fd
0x4ea2
0x85e0
0x6fe6
0x6c13
0x6d12
0x57d0
0x0f89
0x137b
0x05ff
0x2ca7
0x81e6
0x3b1d
0x7566
0x0320
0x79e0
0x628e
0x3c5f
0x935d
0x7f3e
0x1a23
0x3da6
0x812d
0x1519
0x3966
0x69df
0x2777
0x6621
0x389d
0x86a3
0x69b7
0x5295
0x075d
0x6547
0x8e5a
0x7792
0x098d
0x68f4
0x8a80
0x7fe6
0x418b
0x78b5
0x05ec""".splitlines()
for _ in range(47):
 a, b = dat1[_], dat2[_]
 a, b = int(a[2:], 16), int(b[2:], 16)
 print(chr(a^b))
```

the flag is `ictf{been_in_a_lambda_calc_sorta_mood_recently}`

i saw that by 24minutes after release of this chall, there were still no bloods, so i took it upon me to complete it and get a blood, which i did luckily
