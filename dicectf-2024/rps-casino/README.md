# rps-casino

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/ae0eadd1-ae20-47c1-aa25-9f9346a4e480)

```py
#!/usr/local/bin/python

import os
from Crypto.Util.number import bytes_to_long

def LFSR():
	state = bytes_to_long(os.urandom(8))
	while 1:
		yield state & 0xf
		for i in range(4):
			bit = (state ^ (state >> 1) ^ (state >> 3) ^ (state >> 4)) & 1
			state = (state >> 1) | (bit << 63)

rng = LFSR()

n = 56

print(f"Let's play rock-paper-scissors! We'll give you {n} free games, but after that you'll have to beat me 50 times in a row to win. Good luck!")
rps = ["rock", "paper", "scissors", "rock"]

nums = []
for i in range(n):
	choice = next(rng) % 3
	inp = input("Choose rock, paper, or scissors: ")
	if inp not in rps:
		print("Invalid choice")
		exit(0)
	if inp == rps[choice]:
		print("Tie!")
	elif rps.index(inp, 1) - 1 == choice:
		print("You win!")
	else:
		print("You lose!")

for i in range(50):
	choice = next(rng) % 3
	inp = input("Choose rock, paper, or scissors: ")
	if inp not in rps:
		print("Invalid choice")
		break
	if rps.index(inp, 1) - 1 != choice:
		print("Better luck next time!")
		break
	else:
		print("You win!")
else:
	print(open("flag.txt").read())
```

## solution

this is an LFSR where each rock paper scissors game gives the information of what the last 4 bits mod 3 of the state of the lfsr is

after each game, the LFSR is shifted right by 4 every time, and each time it shifts the new left most bit is the xor of the 0th, 1st, 3rd, and 4th right most bits

i just put it into z3

i for some reason couldn't use a bitvec idk why it just wasn't working but the booleans seemed to work

```
from z3 import *
from pwn import *

# p = process(["python3", "rps-casino.py"])
p = remote("mc.ax", 31234)

p.recvline()

s = Solver()
state = [Bool(f"b{_}") for _ in range(424)]

for a in range(424-64):
    s.add(state[a+64] == Xor(state[a], Xor(state[a+1], Xor(state[a+3], state[a+4]))))

print("xors added")

for i in range(56):
    p.sendline(b'rock')
    line = p.recvline()
    mod3 = 0
    lsb0 = state[i*4]
    lsb1 = state[i*4+1]
    lsb2 = state[i*4+2]
    lsb3 = state[i*4+3]
    if b'win' in line:  # mod 3 == 2
        mod3 = 2
        is_two = And(lsb1, And(Not(lsb0), And(Not(lsb2), Not(lsb3))))
        is_five = And(lsb0, And(lsb2, And(Not(lsb1), Not(lsb3))))
        is_eight = And(lsb3, And(Not(lsb0), And(Not(lsb1), Not(lsb2))))
        is_eleven = And(lsb1, And(lsb0, And(lsb3, Not(lsb2))))
        is_fourteen = And(lsb1, And(lsb2, And(lsb3, Not(lsb0))))
        s.add(Or(is_two, Or(is_five, Or(is_eight, Or(is_eleven, is_fourteen)))))
    elif b'lose' in line:  # mod 3 == 1
        mod3 = 1
        is_one = And(Not(lsb1), And(lsb0, And(Not(lsb2), Not(lsb3))))
        is_four = And(lsb2, And(Not(lsb0), And(Not(lsb1), Not(lsb3))))
        is_seven = And(lsb0, And(lsb1, And(lsb2, Not(lsb3))))
        is_ten = And(lsb3, And(lsb1, And(Not(lsb0), Not(lsb2))))
        is_thirteen = And(lsb3, And(lsb2, And(Not(lsb1), lsb0)))
        s.add(Or(is_one, Or(is_four, Or(is_seven, Or(is_ten, is_thirteen)))))
    elif b'Tie' in line:  # mod 3 == 0
        mod3 = 0
        is_zero = And(And(Not(lsb0), Not(lsb1)), And(Not(lsb2), Not(lsb3)))
        is_three = And(And(lsb0, lsb1), And(Not(lsb2), Not(lsb3)))
        is_six = And(And(lsb1, lsb2), And(Not(lsb0), Not(lsb3)))
        is_nine = And(And(lsb0, lsb3), And(Not(lsb1), Not(lsb2)))
        is_twelve = And(And(lsb3, lsb2), And(Not(lsb1), Not(lsb0)))
        is_fifteen = And(And(lsb0, lsb1), And(lsb2, lsb3))
        s.add(Or(is_zero, Or(is_three, Or(is_six, Or(is_nine, Or(is_twelve, is_fifteen))))))
    print(f"#{i}, mod 3 == {mod3}, {line}")


print(s.check())
total = ""
solved = s.model()
for i in range(424):
    total = ("1" if str(s.model()[state[i]]) == "True" else "0") + total
print(total)

def LFSR():
    state = int(total[-64:], 2)
    while 1:
        yield state & 0xf
        for i in range(4):
            bit = (state ^ (state >> 1) ^ (state >> 3) ^ (state >> 4)) & 1
            state = (state >> 1) | (bit << 63)

rng = LFSR()
for _ in range(56):
    selected = next(rng) % 3
    print(["rock", "paper", "scissors"][(selected+1) % 3])
for _ in range(50):
    selected = next(rng) % 3
    choice = ["rock", "paper", "scissors"][(selected+1) % 3]
    p.sendline(choice.encode('ascii'))
p.interactive()
```

also making the `is_one`, `is_two` was so boring
