# hoothoot-flow

## problem

That Noctowl challenge last month was such a doozy, here's a hoothoot one instead!

see `hoot` binary (in this folder)

## solution

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/714ae303-03a1-416b-b552-3847a2f2d7b7)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/7e02c1de-9cf1-4aab-934a-64dc37d0e5fd)

here, we have to get `var_10` in the `sub_1169` function to be 0, and to do that, we have to overflow the sum of `arg_1` (8 chars of the input) and `data_4020`

to get the number `arg_1` that will overflow `data_4020`, causing `var_10` to be 0, we can use the calculator app to subtract 8 bytes from the `data_4020` buffer from 0

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/e3f9e92f-c122-4f83-8245-861041239cff)

we do this 4 times and get

```
6863 7466 7B68 6F70
7368 6F6F 745F 7574
6473 5F7A 6572 6F70
7565 7266 6C6F 777E
```

`hctf{hopshoot_utds_zeropuerflow~`

this is obviously incorrect, but we can see that because of overflow error (i think it is anyways), the first character is one less and the last character is one more, so we can correct for that

```
6963 7466 7B68 6F6f
7468 6F6F 745F 7573
6573 5F7A 6572 6F6f
7665 7266 6C6F 777d
```

the flag is `ictf{hoothoot_uses_zerooverflow}`
