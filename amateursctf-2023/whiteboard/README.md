# whiteboard

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/e0e531ed-bd29-4494-bea7-b864737919db)

main.py
```py
#!/usr/local/bin/python
from flag import flag
import random


def my_very_cool_function(x):
    return pow(x, 2141, 998244353)


successes = 0
prefixes = ["1", "4", "1", "2"]
suffixes = ["0533", "0708", "1133"]  # skittles1412 birthday Orz... May has 30 days ofc (he claims 12/03 but i call cap)


def gen_goal():
    goal = random.choice(prefixes)
    for i in range(140):
        while (x := random.choice(prefixes)) == goal[-1] and random.randint(1, 5) != 1:
            pass
        goal += x
    goal += random.choice(suffixes)
    return int(goal)


def run_testcase():
    goal = gen_goal()
    print(f"Goal: {goal}")

    whiteboard = {my_very_cool_function(167289653), my_very_cool_function(68041722)}  # stO HBD BRYAN GAO Orz
    print(whiteboard)

    for _ in range(1412):
        try:
            a, b = map(int, input("Numbers to combine? ").split())
            assert a in whiteboard and b in whiteboard

            x = 2 * a - b
            whiteboard.add(x)

            if x == goal:
                print("Good job")
                return True

        except:
            print("smh bad input *die*")
            exit("Exiting...")

    return False


for i in range(5):
    ok = run_testcase()
    assert ok

print("happy birthday to you too!")
print(flag)
```

## solution

we start with the numbers 8 and 33, and add numbers until we reach the goal

it took me a long time to find the solution, but we can figure out the two numbers `a,b` that construct `n=2a-b` easily

i first used this script here to get various random numbers `2a-b` assuming the board starts with 8 and 33:
```py
from random import choice
from json import dumps


wb = [(8, 1, 0), (33, 0, 1)]


for _ in range(200):
    a = choice(wb)
    b = choice(wb)
    if a[0] != b[0]:
        n = 2*a[0] - b[0]
        wb.append((n, 2*a[0], -b[0]))

print(wb)
```

one key thing to realize is that it's possible to get to any number that is `25k+8`, k being an integer<br>
another key thing to realize is that `|a+b| == 1` always, where a and b are the numbers from `n=8a+33b` for a given number n. we can make a systems of equations in z3 and solve for `a` and `b` given a number `n`

```py
def get_a_b(goal: int):
    solver = z3.Solver()

    z3a = z3.Int("a")
    z3b = z3.Int("b")

    solver.add(50*z3a - 25*z3b + 8 == goal)

    solver.add(z3.And(1 >= z3a + z3b, z3a + z3b >= -1))

    if "unsat" == str(solver.check()):
        print("equation not satisfied")
        quit()

    model = solver.model()
    a = int(str(model[z3a]))
    b = int(str(model[z3b]))
    if a == b:
        b -= 1
    return a*25+8, b*25+8
```

here, we can get the `a`, `b` values that can construct any given integer (e.g. `58` -> `33,8`)<br>
the `solver.add(50*z3a - 25*z3b + 8 == goal)` came from `n=2a-b`, and if `a` and `b` are guarenteed to be `25k+8`, we can replace them with `25k+8` to make z3 give us possible integers that can be on the whiteboard potentially<br>
now, we need to find the chain that brings us from 8 and 33 to the goal integer

this function constructs a set of numbers that make a "path" to get to a given goal number
```py
def get_combos(n: int):
    redirects = {}

    def get_tree(goal: int):
        if goal in redirects:
            return redirects[goal]
        atoms = {-17, 33, 8, -42, 58}
        total = []
        for _ in get_a_b(goal):
            if _ in atoms:
                total.append(_)
                continue
            tree = get_tree(_)
            if _ not in redirects:
                redirects[_] = tree
            total.append(_)
        return tuple(total)

    # 8, 33 are included by default
    # 58 <- 33 8
    # -17 <- 8 33
    # -42 <- 8 58
    q = get_tree(n)

    for redirect in redirects:
        r = redirects[redirect]
        yield r[0], r[1]
    yield q[0], q[1]
```

the function is a [generator function](https://wiki.python.org/moin/Generators) that [yields](https://www.geeksforgeeks.org/python-yield-keyword/) the numbers in the correct order to get new numbers so 
that every number that we morph will have already been written on the whiteboard once before, with the exception of -17, -42, 58, 33, and 8, because those are easy to get to by hand

we can use this to solve the thing:
```py
def main():
    # p = process(["python3", "./main.py"])
    p = remote("amt.rs", 31694)

    for _ in range(5):
        g = int(p.recvline(False).decode('ascii').lstrip("Goal: "))
        p.recvline()
        sleep(0.3)
        print(g)
        p.sendline(b"33 8")
        p.sendline(b"8 33")
        p.sendline(b"8 58")
        for comb in get_combos(g):
            p.recvuntil(b"e? ")
            inp = f"{comb[0]} {comb[1]}".encode("ascii")
            p.sendline(inp)
        print(p.recvline())
        sleep(0.3)
    p.interactive()


if __name__ == '__main__':
    main()
```
takes a while, but it gets the flag, i think it was like `amateursCTF{hbd_bryan_gao!!!}` or smth cant remember
