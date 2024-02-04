# dicedicegoose

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/73d3b938-5b11-4cd7-9994-fd1b9e6dd602)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/61a76129-48b3-46cd-81b8-d157525e35b9)

pressing WASD moves the dice, and the goal is to get the black square (the "goose" ??) on the same square as the dice. the goose moves randomly.

## solution

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/2e8a476f-ec55-4bcf-8d99-61cfff5d0a55)

we need to make the goose move left always for all 9 turns while we move down

i found this code that moves the goose

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/0d10d2e4-a475-437d-8708-4d889eac9905)

i just overwrote `Math.random` with `() => 0.3` to make it so the goose always moves left

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/8d10063f-b87e-4ea4-9f25-fb88bdf07aaf)
