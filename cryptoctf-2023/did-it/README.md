# did it

## problem

Finding the intersection among subsets can sometimes be a challenging endeavor, as it requires meticulous comparison and analysis of overlapping elements within each set. But she did it! Try doing it yourself too.

```py
#!/usr/bin/env python3

from random import randint
import sys
from flag import flag

def die(*args):
	pr(*args)
	quit()

def pr(*args):
	s = " ".join(map(str, args))
	sys.stdout.write(s + "\n")
	sys.stdout.flush()

def sc():
	return sys.stdin.buffer.readline()

def did(n, l, K, A):
	A, K = set(A), set(K)
	R = [pow(_, 2, n) + randint(0, 1) for _ in A - K]
	return R

def main():
	border = "+"
	pr(border*72)
	pr(border, ".::   Hi all, she DID it, you should do it too! Are you ready? ::.  ", border)
	pr(border*72)

	_flag = False
	n, l = 127, 20
	N = set(list(range(0, n)))
	K = [randint(0, n-1) for _ in range(l)]
	cnt, STEP = 0, 2 * n // l - 1
	
	while True:
		ans = sc().decode().strip()
		try:
			_A = [int(_) for _  in ans.split(',')]
			if len(_A) <= l and set(_A).issubset(N):
				DID = did(n, l, K, _A)
				pr(border, f'DID = {DID}')
				if set(_A) == set(K):
					_flag = True
			else:
				die(border, 'Exception! Bye!!')
		except:
			die(border, 'Your input is not valid! Bye!!')
		if _flag:
			die(border, f'Congrats! the flag: {flag}')
		if cnt > STEP:
			die(border, f'Too many tries, bye!')
		cnt += 1

if __name__ == '__main__':
	main()
```

## solution

here, we can see that there is a hidden set of 20 (or fewer if there are duplicates) integers between 0 and 126 inclusive both ways

the goal here is to figure out which integers are in the hidden set by inputting your own set of 20 integers (e.g. `1,2,3,9,100`) and receiving back some output

the output is constructed as follows:

- initialize an empty output list
- for each integer in the input array, check if the integer is in the hidden set
- if it is, then skip the step below
- put inside of the output list an integer that is `n**2+randint(0,1) % 127`
- return the output list

we are presented with a problem because of the `randint(0,1)`. if we input `0,1` and the output is `1`, 
we do not know whether 0 or 1 is in the hidden set because `0+(randint(0,1)=1) -> 1`, and `1+(randint(0,1)=0) -> 1`.

we have at most 12 "questions" (input set of 20 or fewer integers -> get output) to determine the hidden set

here is my solve script:

```py
from json import dumps
from pwn import *
import random


known = []


def get_conflicts(tried, did_input):
    inp = tried
    mappa = {}
    for _ in inp:
        s, s1 = _ ** 2 % 127, (_ ** 2 + 1) % 127
        if s not in mappa:
            mappa[s] = []
        if s1 not in mappa:
            mappa[s1] = []
        mappa[s].append(_)
        mappa[s1].append(_)

    known_fs = inp.copy()
    inp2 = did_input
    conflict = []
    for _ in inp2:
        assert _ in mappa
        if len(mappa[_]) == 1:
            q = mappa[_][0]
            for _2 in mappa:
                if q in mappa[_2]:
                    mappa[_2].remove(q)
            if q in known_fs:
                known_fs.remove(q)
        else:
            for _2 in mappa[_]:
                conflict.append(_2)
                if _2 in known_fs:
                    known_fs.remove(_2)
    conflicting = list(set(conflict))
    return known_fs, conflicting


def main():
    p = remote("00.cr.yp.toc.tf", 11337)
    queue = [_ for _ in list(range(127))]
    times = 0
    while len(queue):
        tried, queue = queue[:20], queue[20:]
        print(f"Sending: {str(tried).encode('ascii')[1:-1]}")
        p.sendline(str(tried).encode('ascii')[1:-1])
        p.recvuntil(b"DID = ")
        did_result = eval(p.recvuntil(b']').decode('ascii'))
        print(f"Received: {did_result}")
        known_add, conflicts = get_conflicts(tried, did_result)
        known.extend(known_add)
        print(f"Known ({len(known)}): {known}")
        while len(conflicts):
            queue.insert(random.randint(0, len(queue)), conflicts.pop(0))
        print(f"Length of queue: {len(queue)}")
        if times == 10:
            break
        times += 1
    p.interactive()


if __name__ == '__main__':
    main()
```

here is a sample run:

```
[x] Opening connection to 00.cr.yp.toc.tf on port 11337
[x] Opening connection to 00.cr.yp.toc.tf on port 11337: Trying 135.181.80.21
[+] Opening connection to 00.cr.yp.toc.tf on port 11337: Done
Sending: b'2, 37, 40, 83, 47, 52, 34, 114, 50, 22, 3, 56, 8, 21, 65, 17, 68, 32, 67, 0'
Received: [0, 5, 10, 65, 35, 60, 104, 9, 14, 100, 77, 50, 87, 38, 88, 34, 53, 32, 42]
Known (1): [67]
Length of queue: 109
Sending: b'18, 6, 1, 13, 39, 112, 81, 118, 69, 58, 25, 98, 91, 113, 20, 59, 80, 92, 120, 5'
Received: [2, 79, 25, 63, 125, 99, 85, 70, 70, 20, 81, 49, 52, 82]
Known (6): [67, 6, 13, 25, 91, 80]
Length of queue: 93
Sending: b'10, 113, 95, 111, 126, 49, 16, 119, 15, 51, 45, 7, 117, 57, 64, 38, 55, 84, 86, 115'
Received: [49, 100, 99, 48, 120, 115, 61, 104, 75, 32, 71, 30, 8, 3, 69, 18, 101, 65, 2]
Known (6): [67, 6, 13, 25, 91, 80]
Length of queue: 78
Sending: b'116, 122, 111, 23, 76, 58, 48, 71, 18, 121, 44, 109, 35, 17, 27, 36, 102, 46, 117, 61'
Received: [36, 70, 21, 95, 83, 27, 32, 85, 19, 38, 89, 61, 118, 70, 3, 122, 100, 36, 25]
Known (7): [67, 6, 13, 25, 91, 80, 58]
Length of queue: 62
Sending: b'78, 121, 82, 106, 63, 123, 108, 16, 87, 93, 79, 74, 72, 103, 33, 104, 19, 26, 53, 89'
Received: [73, 36, 69, 105, 22, 61, 107, 116, 18, 121, 107, 76, 48, 16, 33]
Known (10): [67, 6, 13, 25, 91, 80, 58, 16, 93, 26]
Length of queue: 47
Sending: b'105, 109, 96, 75, 28, 11, 110, 54, 69, 24, 97, 100, 62, 31, 107, 53, 12, 4, 74, 43'
Received: [73, 11, 16, 62, 95, 103, 121, 18, 72, 38, 20, 70, 123, 23, 73]
Known (13): [67, 6, 13, 25, 91, 80, 58, 16, 93, 26, 110, 24, 62]
Length of queue: 33
Sending: b'94, 108, 85, 66, 125, 41, 60, 43, 126, 10, 99, 74, 73, 70, 88, 30, 9, 29, 14, 123'
Received: [39, 75, 81, 100, 30, 71, 122, 69, 107, 114, 4, 124, 1, 17, 44, 79, 74]
Known (16): [67, 6, 13, 25, 91, 80, 58, 16, 93, 26, 110, 24, 62, 99, 74, 30]
Length of queue: 13
Sending: b'53, 96, 42, 17, 90, 18, 65, 19, 31, 101, 77, 4, 124'
Received: [73, 34, 17, 41, 113, 88, 36, 70, 108, 10, 73]
Known (18): [67, 6, 13, 25, 91, 80, 58, 16, 93, 26, 110, 24, 62, 99, 74, 30, 53, 90]
Length of queue: 2
Sending: b'31, 96'
Received: [73, 73]
Known (18): [67, 6, 13, 25, 91, 80, 58, 16, 93, 26, 110, 24, 62, 99, 74, 30, 53, 90]
Length of queue: 2
Sending: b'31, 96'
Received: [72, 73]
Known (18): [67, 6, 13, 25, 91, 80, 58, 16, 93, 26, 110, 24, 62, 99, 74, 30, 53, 90]
Length of queue: 2
Sending: b'96, 31'
Received: [73, 73]
Known (18): [67, 6, 13, 25, 91, 80, 58, 16, 93, 26, 110, 24, 62, 99, 74, 30, 53, 90]
Length of queue: 2
[*] Switching to interactive mode

$ 67, 6, 13, 25, 91, 80, 58, 16, 93, 26, 110, 24, 62, 99, 74, 30, 53, 90
+ DID = []
+ Congrats! the flag: CCTF{W4rM_Up_CrYpt0_Ch4Ll3n9e!!}
[*] Got EOF while reading in interactive
```

the flag here is `CCTF{W4rM_Up_CrYpt0_Ch4Ll3n9e!!}`
