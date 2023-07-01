# encrypted-command-executor

## problem

A service that only executes encrypted commands. Regular users can only run echo or ls commands. The command execution output is encrypted too btw.

The server do not allow any outbound network connection, and the flag is located at /flag.

```py
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
from shlex import quote
from base64 import b64decode, b64encode
import os
from subprocess import Popen, PIPE, DEVNULL


def encrypt(key: bytes, ct: bytes) -> bytes:
    cipher = AES.new(key, AES.MODE_ECB)
    return cipher.encrypt(pad(ct, AES.block_size))


def decrypt(key: bytes, ct: bytes) -> bytes:
    cipher = AES.new(key, AES.MODE_ECB)
    return unpad(cipher.decrypt(ct), AES.block_size)


if __name__ == "__main__":
    print("=" * 40)
    print("Welcome to Encrypted Command Executor")
    print("=" * 40)
    key = os.urandom(AES.block_size)
    while True:
        print("1. Generate an echo command")
        print("2. Generate a ls command")
        print("3. Run an encrypted command")
        print("4. Exit")
        choice = input("> ")
        if choice == "1":
            msg = input("Message: ")
            cmd = "echo %s" % quote(msg)
            print("result:", b64encode(encrypt(key, cmd.encode())).decode())
        elif choice == "2":
            directory = input("Directory: ")
            cmd = "ls -- %s" % quote(directory)
            print("result:", b64encode(encrypt(key, cmd.encode())).decode())
        elif choice == "3":
            ct = input("Encrypted command: ")
            cmd = decrypt(key, b64decode(ct))
            proc = Popen(
                cmd.decode(), stdin=DEVNULL, stdout=PIPE, stderr=DEVNULL, shell=True
            )
            stdout, _ = proc.communicate()
            print("result:", b64encode(encrypt(key, stdout)).decode())
        elif choice == "4":
            exit()
```

## solution

because this uses ECB, we can perform a chosen plaintext attack because unlike CBC, ECB encrypts the 2nd block with exactly the same starting state as the first.

see [here](https://gchq.github.io/CyberChef/#recipe=AES_Encrypt(%7B'option':'Hex','string':'0000000000000000000009000000000'%7D,%7B'option':'Hex','string':''%7D,'ECB','Raw','Hex',%7B'option':'Hex','string':''%7D)&input=QUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFB)

if we can input any data we want to AES ECB, we can easily encrypt any data after removing the first 16 bytes

solve script:

```py
import socket
import string

HOST = "ictf2.maple3142.net"  # The server's hostname or IP address
PORT = 9999  # The port used by the server

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.connect((HOST, PORT))
    s.recv(1024)
    letters = "abcdefghijklmnopqrstuvwxyzQWERTYUIOPASDFGHJKLZXCVBNM1234567890!@#$%^&*()_{}"
    buffers = {}
    print(letters)
    newline = b"\n"
    for letter in letters:
        s.sendall(b"2\n")
        s.recv(1024)
        pload = b"abcdefghiqwertyuiopqwertyqwertyuiopqwer /" + letter.encode('ascii')
        # print(pload)
        s.sendall(pload + b"\n")
        buffers[s.recv(1024).split(newline)[0][8+64:].decode('ascii')] = letter
    for index in range(70):
        s.sendall(b"2\n")
        s.recv(1024)
        payload = b"abcdefghiqwertyuiopqwertyqwertyuiopqwertycat /flag | head -c " + str(index+1).encode('ascii') + b" | tail -c 1; echo Jw== | base64 -d #"
        # print(payload)
        s.sendall(payload + b"\n")
        cmd = s.recv(1024).split(newline)[0][8+64:]
        s.sendall(b"3\n")
        s.recv(1024)
        s.sendall(cmd + b"\n")
        thing = (s.recv(1024).split(newline)[0][8:].decode('ascii'))
        if thing in buffers:
            print(buffers[thing])
        else:
            print(f"MISSING ERROR AT INDEX {index+1} STARTING FROM 1")
```

this will print out the flag char by char

it is `ictf{maybe_ecb_is_not_a_good_idea}`

