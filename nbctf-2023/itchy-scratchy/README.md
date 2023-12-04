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

clearly there is a `check` broadcast so we must also check other objects because the (todo do this)
