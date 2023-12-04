# itchy scratchy

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/901bbbde-bb20-4eae-9592-0c2d98dce34c)

see [itchyscratchy.sb3](./itchyscratchy.sb3)

## solution

easier than the scratch in amateursCTF at least

inside of the body of aatrox, there is this code

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/858fa918-1dc8-4ce4-b6f0-ab730b3d769f)

obviously, the first answer is `isaac newton`

then, it uses a substitution cipher but with the numbers where each number is coordinating to a letter. this letter is stored in alpha, it looks like

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/9862c67b-90e8-4c4f-b1c7-c517b3762312)

so we can get alpha by enabling debugging it in the lists section

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/834afad1-c63d-4018-b068-5fb811d1e2f7)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/5cbd296a-bd4e-4396-afda-7bd70420f60a)

we can export this list fast by using right click and export

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/6928cbdb-df0d-41eb-9085-5b020c1f75f1)

i also exported the enc list because clearly that's the encrypted flag

alpha list
```z,v,t,w,r,c,a,5,7,n,4,9,u,2,b,y,1,j,d,q,o,6,g,0,k,s,x,f,i,8,p,e,l,m,h,3```

enc list
```902,764,141,454,207,51,532,1013,496,181,562,342```

clearly there is a `check` broadcast so we must also check other objects because the broadcast will trigger code to run in other places

looking at the backdrop, we can see more code

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/d7abbed9-57d0-4942-8f07-3a5ea19f6fbc)

fortunately, it is pretty short.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/451add86-ab64-42b6-af94-2d5c70a11651)

in the above picture, we can see that scratch starts lists and strings at index 1 instead of 0. this is important later.

so, the first part of the flag is `nbctf{` and the 19th char is `}`

therefore, the flag is 12 chars long

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/83bbefea-764d-4c48-bedd-6c352a5a13ae)

the inside of the flag is encoded using the `alpha` list and added to the `input` list

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/2e72ec69-91f6-4627-af59-e76d3fcfc8cc)

it encodes each character of the flag using the name and other characters in the flag

```py
name = " isaac newton"
flag = "nbctf{............}"
alpha = [" ", "z", "v", "t", "w", "r", "c", "a", "5", "7", "n", "4", "9", "u", "2", "b", "y", "1",
         "j", "d", "q", "o", "6",
         "g", "0", "k", "s", "x", "f", "i", "8", "p", "e", "l", "m", "h", "3"]
enc = [902, 764, 141, 454, 207, 51, 532, 1013, 496, 181, 562, 342]

assert len(flag) == 19

input = [" "] + list(flag[6:-1])

for i in range(1, 13):
    j = (i * i + alpha.index(name[i])) % (len(name)-1) + 1
    print(i, "=====================", j)
    for char1 in alpha:
        for char2 in alpha:
            tmp = (alpha.index(char1) * alpha.index(char2)) + alpha.index(name[i])*alpha.index(name[j])
            if tmp == enc[i-1]:
                print(f"flag[{i}] = {char1}, flag[{j}] = {char2}")
```

this is a python equivalent that brute forces different characters for `i` and `j` indices based on which index is being checked

this outputs

```
1 ===================== 7
flag[1] = 1, flag[7] = 3
flag[1] = j, flag[7] = m
flag[1] = m, flag[7] = j
flag[1] = 3, flag[7] = 1
2 ===================== 7
flag[2] = 2, flag[7] = 3
flag[2] = j, flag[7] = f
flag[2] = o, flag[7] = 0
flag[2] = 0, flag[7] = o
flag[2] = f, flag[7] = j
flag[2] = 3, flag[7] = 2
3 ===================== 5
flag[3] = t, flag[5] = l
flag[3] = 7, flag[5] = 4
flag[3] = 4, flag[5] = 7
flag[3] = l, flag[5] = t
4 ===================== 12
flag[4] = 9, flag[12] = e
flag[4] = y, flag[12] = 0
flag[4] = 0, flag[12] = y
flag[4] = e, flag[12] = 9
5 ===================== 8
flag[5] = z, flag[8] = b
flag[5] = t, flag[8] = r
flag[5] = r, flag[8] = t
flag[5] = b, flag[8] = z
6 ===================== 1
flag[6] = t, flag[1] = 1
flag[6] = 1, flag[1] = t
7 ===================== 12
flag[7] = 9, flag[12] = 3
flag[7] = y, flag[12] = x
flag[7] = j, flag[12] = 0
flag[7] = 0, flag[12] = j
flag[7] = x, flag[12] = y
flag[7] = 3, flag[12] = 9
8 ===================== 1
flag[8] = r, flag[1] = 1
flag[8] = 1, flag[1] = r
9 ===================== 2
flag[9] = 2, flag[2] = f
flag[9] = f, flag[2] = 2
10 ===================== 8
flag[10] = r, flag[8] = 1
flag[10] = 1, flag[8] = r
11 ===================== 11
flag[11] = 4, flag[11] = 4
12 ===================== 11
flag[12] = w, flag[11] = l
flag[12] = c, flag[11] = 6
flag[12] = 4, flag[11] = 9
flag[12] = 9, flag[11] = 4
flag[12] = 6, flag[11] = c
flag[12] = l, flag[11] = w
```

so `flag[12] = 1, flag[11] = w` means that the 12th char of the flag is `1` if the 11th char of the flag is `w`.

notably, the 11th char is guarenteed to be 4, so the 12th char is 9 for sure. this is because of the line `flag[12] = 9, flag[11] = 4`

this cause-effect reverse engineering is easily done by hand to get the flag
