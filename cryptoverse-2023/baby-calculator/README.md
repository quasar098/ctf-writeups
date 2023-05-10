# baby calculator

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/c2a133a8-771e-4928-b303-014bea48eef3)

Sample of me doing it:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/79fa293d-f198-4fc6-b6af-61298f42901a)

## solution

```py
import socket
from time import sleep
from math import log, cos, sin, tan
import sympy
import re

HOST = '20.169.252.240'
PORT = 4200

# Create a socket object
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Connect to the server
client_socket.connect((HOST, PORT))

while True:
    data = client_socket.recv(6000)
    decoded = data.decode()
    print(decoded)

    searched = re.search(r"Evaluate the integral of: (\S+) from (\d+) to (\d+)\.", decoded)
    eq, least, most = searched.groups()

    x = sympy.Symbol("x")
    integrated = sympy.integrate(eq, (x, least, most))
    command = "%.5f" % eval(str(integrated)) + "\n"

    print(command.encode())
    sleep(0.3)
    client_socket.sendall(command.encode())
```

I used the sympy library to evaluate the integral and then used `eval()` and some imported functions to get really precise answers.

At the end of the 40 questions, it says:

`Congrats! Here's your flag: cvctf{B4by_m@7h_G14n7_5t3P}`
