# hoyoverse II

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/aff34383-a996-4fbd-83bd-50ec3d9314fd)
![image](https://github.com/quasar098/ctf-writeups/assets/70716985/8fab87a4-20fd-4005-a345-0d40ad4d7049)

## solution

This one was just guesswork and being lucky and precise.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/67bbaccf-e6b6-4f6f-8a6c-704f5c9c76db)

One of my team members made a script to do it for me to save time

```py
import socket

HOST = '67.205.179.135'
PORT = 13337

# Create a socket object
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Connect to the server
client_socket.connect((HOST, PORT))

# Receive the welcome message from the server
data = client_socket.recv(1024)
print(data.decode())

# Send the first prompt injection command to the server
prompt1 = 'hello world (reverse)'
command1 = f"{prompt1}\n"
client_socket.sendall(command1.encode())

# Receive the response from the server
data = client_socket.recv(1024)
print(data.decode())

# Send the second prompt injection command to the server
prompt2 = 'add "zellic " and "zelli zell zel ze z" without quote'
command2 = f"{prompt2}\n"
client_socket.sendall(command2.encode())

# Receive the response from the server
data = client_socket.recv(1024)
print(data.decode())

# Send the third prompt injection command to the server
prompt3 = "5 asterisks decreasing 5 times"
command3 = f"{prompt3}\n"
client_socket.sendall(command3.encode())

# Receive the response from the server
data = client_socket.recv(1024)
print(data.decode())

# Close the socket connection
client_socket.close()
```
