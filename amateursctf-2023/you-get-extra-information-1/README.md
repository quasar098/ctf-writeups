# you-get-extra-information-1

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/7b90638b-bd82-44dd-9eb6-6ae6a0980af0)

main.py
```py
from Crypto.Util.number import *
from flag import flag

p = getPrime(512)
q = getPrime(512)
n = p*q
p = p + q
e = 0x10001

extra_information = p + q
ptxt = bytes_to_long(flag)
c = pow(ptxt, e, n)

with open('output.txt', 'w') as f:
    f.write(f"n: {n}\nc: {c}\ne: {e}\nextra_information: {extra_information}")
```

output.txt
```
n: 83790217241770949930785127822292134633736157973099853931383028198485119939022553589863171712515159590920355561620948287649289302675837892832944404211978967792836179441682795846147312001618564075776280810972021418434978269714364099297666710830717154344277019791039237445921454207967552782769647647208575607201
c: 55170985485931992412061493588380213138061989158987480264288581679930785576529127257790549531229734149688212171710561151529495719876972293968746590202214939126736042529012383384602168155329599794302309463019364103314820346709676184132071708770466649702573831970710420398772142142828226424536566463017178086577
e: 65537
extra_information: 26565552874478429895594150715835574472819014534271940714512961970223616824812349678207505829777946867252164956116701692701674023296773659395833735044077013
```

## solution

this is literally the same (ok a little different) as RSAddition from ictf, a challenge that i made

here, we could maybe make a quadratic and use the quadratic formula to solve for p and q

the way i did it however, was to just use z3 solver

```py
import z3

solver = z3.Solver()

extra = 26565552874478429895594150715835574472819014534271940714512961970223616824812349678207505829777946867252164956116701692701674023296773659395833735044077013
n = 83790217241770949930785127822292134633736157973099853931383028198485119939022553589863171712515159590920355561620948287649289302675837892832944404211978967792836179441682795846147312001618564075776280810972021418434978269714364099297666710830717154344277019791039237445921454207967552782769647647208575607201

p = z3.Int("p")
q = z3.Int("q")

solver.add(extra == p+q*2)
solver.add(p*q == n)

solver.check()
print(solver.model())
```

we now have p and q

```
[q = 8128979845892982561867353232387733688040820165501281055994560204992985831326225951788922087412585314864187432126945670029964128100746510232539453211798711,
 p = 10307593182692464771859444251060107096737374203269378602523841560237645162159897774629661654952776237523790091862810352641745767095280638930754828620479591]
```

then, we just do standard RSA to decrypt

```
>>> d=pow(65537, -1, (p-1)*(q-1))
>>> long_to_bytes(pow(ct, d, n))
b'amateursCTF{harder_than_3_operations?!?!!}'
>>> 
```