# picochip 1

## problem

```py
from itertools import groupby
from Crypto.Util.number import *
from enum import Enum
from base64 import b64encode
from secret import flag

import plotly.express as px
import random


class State(Enum):
    # PicoChip power states for hardware simulation.
    FALLBACK = 0x0
    SUCCESS = 0x1
    ERROR = 0x2


class PicoChip:
    def __init__(self, k: int, N: int, t: int):
        self.states = []
        self.k = k
        self.N = N
        self.t = t
        self.r = self.gen_op()

    def rec(self, s: State):
        self.states.append(s)

    def gen_op(self) -> list:
        rs = []
        i = 3
        while len(rs) < self.N:
            if isPrime(i):
                self.rec(State.SUCCESS)
                rs.append(i)
            i += 2
        return rs
    
    def gen_secure_prime(self) -> int:
        # More secure than getPrime() as indicated from my latest paper.
        while True:
            v0 = random.randint(2**(self.k-1), 2**self.k)
            v = v0 + 1 - v0 % 2
            while True:
                is_prime = True
                i = 0
                while i < self.N:
                    if v % self.r[i] == 0:
                        v = v + 2
                        self.rec(State.FALLBACK)
                        break
                    i += 1
                    self.rec(State.SUCCESS)

                if i == self.N:
                    for m in range(1, self.t+1):
                        if not miller_rabin(v):
                            is_prime = False
                            v = v + 2
                            self.rec(State.FALLBACK)
                            break
                        self.rec(State.SUCCESS)
                    if is_prime:
                        return v
    
    def plot(self):
        # For transparency, I will show you the internals of PicoChip.
        power_states(self.states)


def power_states(states: list):
    if State.ERROR in states:
        raise Exception("PicoChip is broken!")
    
    power = []
    for k, g in groupby(states):
        if k != State.FALLBACK:
            power.append(sum(1 for _ in g))
        else:
            power.extend([0 for _ in g])

    gx, gy = [], []
    for i in range(len(power)):
        gx.append(i)
        gy.append(power[i])
        gx.append(i + 1)
        gy.append(power[i])

    fig = px.line(x=gx, y=gy, title="PicoChip Power States")
    fig.write_html("power_states.html")


def miller_rabin(n: int) -> bool:
    if n <= 1 or n == 4:
        return False
    if n <= 3:
        return True
    
    d = n - 1
    while d % 2 == 0:
        d //= 2
    a = 2 + random.randint(1, n - 4)
    x = pow(a, d, n)
    if x == 1 or x == n - 1:
        return True
    
    while d != n-1:
        x = (x * x) % n
        d *= 2
        if x == 1:
            return False
        if x == n - 1:
            return True
    return False


if __name__ == "__main__":
    pico = PicoChip(36, 60, 1)
    p = pico.gen_secure_prime()
    q = pico.gen_secure_prime()
    assert p != q and isPrime(p) and isPrime(q)

    assert flag.startswith(b"cvctf{") and flag.endswith(b"}")
    flag = flag[6:-1]
    
    N = p * q
    e = 0x10001
    m = bytes_to_long(flag)
    ct = pow(m, e, N)

    print("[+] PicoChip Encryption Parameters:")
    # print(f"N = {N}")
    print(f"e = {e}")
    print(f"ct = {ct}")

    pico.plot()
    print("[+] PicoChip Power States saved.")
```

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/a2a411b0-78d7-4367-83d5-b7b5ae5a0b08)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/874c5522-a8dc-4ea5-95ab-2777e5031961)

output.txt:

```
[+] PicoChip Encryption Parameters:
e = 65537
ct = 3059648962482294740345
[+] PicoChip Power States saved.
```

## solution

We are provided a graph, which we can exploit.

The graph represents all of the states of the PicoChip, and the code that generates the graph is as follows:

```py
if State.ERROR in states:
    raise Exception("PicoChip is broken!")

power = []
for k, g in groupby(states):
    if k != State.FALLBACK:
        power.append(sum(1 for _ in g))
    else:
        power.extend([0 for _ in g])

gx, gy = [], []
for i in range(len(power)):
    gx.append(i)
    gy.append(power[i])
    gx.append(i + 1)
    gy.append(power[i])

fig = px.line(x=gx, y=gy, title="PicoChip Power States")
fig.write_html("power_states.html")
```

The graph is made of tall bars, and the height of the bars represent how many successes there are (1 * number of success)<br>
In the places where there are no bars but instead y=0 height, how many in a row there are of those are how many failures there are.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/dc503f24-bca3-430e-9978-93b59215ec7c)

In this example, there are 1 failure, 1 success, 1 failure, 6 success, and 2 failure.

One more thing you need to know is that when the PicoChip object is created, 60 successes are immediately added. One for each of the first 60 primes.

```py
def gen_op(self) -> list:
    rs = []
    i = 3
    while len(rs) < self.N:  # self.N is 60
        if isPrime(i):
            self.rec(State.SUCCESS)
            rs.append(i)
        i += 2
    return rs
```

We now have the power states of the program after it finished:

```
- picochip init
60x success
- generate p
6x success
1x fallback
10x success
2x fallback
1x success
1x fallback
60x success
2x fallback
2x success
1x fallback
3x success 
2x fallback
17x success 
1x fallback
60x success
2x fallback
12x success
1x fallback
1x success
2x fallback
4x success
1x fallback
41x success
2x fallback
1x success
1x fallback
6x success
2x fallback
61x success
- generate q
4x success
1x fallback
0x success
1x fallback
2x success
1x fallback
1x success
1x fallback
0x success
1x fallback
3x success
1x fallback
48x success
1x fallback
0x success
1x fallback
1x success
1x fallback
2x success
1x fallback
0x success
1x fallback
29x success
1x fallback
28x success
1x fallback
0x success
1x fallback
60x success
1x fallback
5x success
1x fallback
0x success
1x fallback
16x success
1x fallback
1x success
1x fallback
0x success
1x fallback
25x success
1x fallback
61x success
```

I replaced 2x failure with 1x failure, 0x success, 1x failure, and you will see why.

The states are generated in the program in the following way:

Firstly, generate first 60 primes on PicoChip initialization, +60 successes

When generating a prime number, we check each of the first 60 primes (3 to 283). We add one success for each one it is not divisible by.

If it divisible by one of the prime numbers, add a failure and "retry" (see below for explanation)

Else, check if it is a prime number.

  If is not a prime number, "retry" and add a failure

  If it is, add a success and return the number.

A successful generation of a prime number ends in 61x success, 60x for 3-283 checks, and 1x for prime number check

Also, a "retry" means adding 2 to the number and trying again.

Therefore, we know the numbers below p and q values and their lowest divisor (that is 3 or above).

Now we have the numbers below p and q and what check they failed at:

```py
===== gen primes =====
60x success

===== calculate p =====
6x success (19)
1x fallback
10x success (37)
2x fallback
1x success (5)
1x fallback
60x success (miller_rabin)
2x fallback
2x success (7)
1x fallback
3x success (11)
2x fallback
17x success (67)
1x fallback
60x success (miller_rabin)
2x fallback
12x success (43)
1x fallback
1x success (5)
2x fallback
4x success (13)
1x fallback
41x success (191)
2x fallback
1x success (5)
1x fallback
6x success (19)
2x fallback
61x success  # p calculated

===== calculate q =====
4x success (13)
1x fallback
0x success (3)
1x fallback
2x success (7)
1x fallback
1x success (5)
1x fallback
0x success (3)
1x fallback
3x success (11)
1x fallback
48x success (229)
1x fallback
0x success (3)
1x fallback
1x success (5)
1x fallback
2x success (7)
1x fallback
0x success (3)
1x fallback
29x success (127)
1x fallback
28x success (113)
1x fallback
0x success (3)
1x fallback
60x success (miller_rabin)
1x fallback
5x success (17)
1x fallback
0x success (3)
1x fallback
16x success (61)
1x fallback
1x success (5)
1x fallback
0x success (3)
1x fallback
25x success (103)
1x fallback
61x success  # q calculated
```

I seperated the 2x failure into 1x failure, 0x success, 1x failure because 0x success just means that it was divisible by 3.

TODO FINISH WRITEUP
