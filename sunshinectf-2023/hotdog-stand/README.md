# hotdog stand

## problem

cannot access the website unfortunately as the ctf is over and the sites are down

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/19068284-78d9-48a6-ae4b-459563fdbf65)

## solution

looking in the `/robots.txt` file, there is a "disallow" for `/hotdog-database/` or something i dont remember

then, going to `/hotdog-database/database.db` downloaded an sqlite3 database which i peeked inside to find the user and password to log in and get the flag
