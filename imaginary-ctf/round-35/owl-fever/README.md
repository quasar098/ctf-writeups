# owl fever

## problem

The past tense of the hint is an anagram of the title.

see `owlfever_new_new` for the file

## solution

again, we can use binja on wsl2 to have full linux binary debugging capabilities

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/d3b9e4af-387c-4812-9a40-165b720a2c79)


we can see here that eight letter chunks are being added to constants and the result of each of the 4 addition operations's product has to be `0x52d93ead8ed7e32d`, or `5969871696203080493`

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/10c84583-9857-4c8c-9a7f-5d570e5c5d96)

we can see that there are four chunks and also that `5969871696203080493` has four factors, `45263, 45433, 51991, 55837`.

therefore, each of the addition of the constants to the reverse of the chunk of the flag has to be in `[45263, 45433, 51991, 55837]`, and there can only be one of each.

here is my notes file to take notes on the constants and such:

```
45263, 45433, 51991, 55837 are the factors of var_20
B0CF , B179 , CB17 , DA1D

flag is 32 chars long like
ictf{ABCDEFGHIJKLMNOPQRSTUVWXYZ}

decoded flag writeout
ictf{ov3rfl0win_to_n0nz3roEsszz}

0xcc899084998c67ae+0x??????7b66746369=CB17
0xa0919688cf9473ab+0x??????????6c6671=DA1D
0xcc8591cf91a14205+0x????????????????=B179
0x8285858c8cbb415d+0x????????????????=B0CF
0xcc899084998c67ae+(first eight chars reversed hex value)
```

the flag `ictf{ov3rfl0win_to_n0nz3roEsszz}` appears to be the answer
