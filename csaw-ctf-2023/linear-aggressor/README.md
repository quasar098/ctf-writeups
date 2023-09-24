# linear-aggressor

guessy chall kinda but its fine because it's linear

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/fe766533-b863-46dc-82bf-33427df01d70)

the service is down i cant get screenshots

## solution

```py
from subprocess import check_output

check = lambda a, b: (int(check_output(f"""python3 -c "print(*([0] * {a} + [{b}] + [0] * {30-a}), sep='\\n')" | nc misc.csaw.io 3000""", shell=True).split(b"\r\n")[-2].decode('ascii')))

for _ in range(30):
    print(check(_, 1)-check(_, 0))
```

so what i did here is that the model is a 30-dimensional (i think that is the correct number) linear regression

at first i thought it was a pwn challenge because nothing important is really going on, but i tried inputting 1,0,0,0,etc, and got some number.
then i tried inputting 2,0,0,0,etc and got another number. Because the equation is linear, it looks something like `a*x_1+b*x_2...+z*x_2` etc, so the difference of the two numbers i got out was the `a` value

it turns out that the coefficients were the flag, so i just had to check the output of each `0,0,0,...,1,...,0,0,0` against each `0,0,0...0,0,0` and then i got the answer

the above solution is slightly inefficient because it uses `0,0,0...0,0,0` checks multiple times, and i think the y-intercept (if that exists for dimensions > 2) can be brute forced instead.
