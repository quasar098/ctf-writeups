# babyfunge

personally it was like a 4/10 on the difficulty scale because i am the esolang enjoyer

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/f54b4ef0-9e13-40f4-a343-0b4ad171ce49)

see [babyfunge.zip](./babyfunge.zip)

```py
from interpreter import befunge


""" Befunge grid:
[Line 1 input here]
[Line 2 input here]
[Line 3 input here]
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
vsctf{??????????????????????????????}
"""

unneeded_commands = "pg&~"  # idk why anyone would need these
FLAG = "vsctf{??????????????????????????????}"  # real flag will be the same length
line_size = len(FLAG)

# get the input code
payload = ""
for i in range(3):
    this_line = input(f"Line {i+1}: ")
    if len(this_line) > line_size:
        print("Jeez, you don't need all that space!")
        quit()
    payload += f'{this_line:<{line_size}}\n'  # fill the line

# make sure your code is optimal!
num_of_commands = len(payload.replace(" ", "").replace("\n", ""))
any_unneeded_commands = any(command in unneeded_commands for command in payload)
if any_unneeded_commands or num_of_commands > 7:
    print("You don't need all that overhead!")
    quit()

befunge(f'{payload}{"@"*line_size}\n{FLAG}')
```

## solution

befunge is a programming language where the instruction pointer / pointer cursor moves along a 2 dimensional plane, instead of just top of the file to the bottom of the file.

we can redirect the instruction pointer direction using `><^v`

taking a look at the esolang wiki page for befunge ([here](https://esolangs.org/wiki/Befunge)), we can see two interesting and useful instructions that aren't blocked:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/00f06a27-f62a-497d-81a5-9aa5e0e936db)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/e51275bf-e153-4e37-9fc5-b57d0bddf829)

note that the grid looks something like this (`X` is instruction ptr)
```
-----------------------------------------
| X                                     |
|                                       |
|                                       |
| @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ |
| vsctf{??????????????????????????????} |
-----------------------------------------
```

note that the `@` are "stop" characters, meaning that there is no easy way for the program to move execution to the bottom of the screen.

also, the payload has to be 7 instructions total, not including whitespace.

the key here is that the instruction pointer can "roll over" the boundary and go back to the start

here, we can toggle the string mode and then roll over the boundary from the top of the screen to the bottom of the screen, capturing a flag character inside of the string.

like so:

```
-----------------------------------------
| v"                                    |
| >^                                    |
|  X                                    |
| @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ |
| vsctf{??????????????????????????????} |
-----------------------------------------
```

then the instruction pointer will end up at the `X` shown above

following that, we have to print out the two characters that were just placed onto the stack (that's what the string does, places chars onto the stack)

however, now it will be an infinite loop because it will continue upwards and then fail.

the clever trick is that we can have two redirections in the same grid space using the `?` character. 
with `?`, there is a 1/4 chance that the first move will go upwards, and another 1/4 chance it will go to the right after that

```
-----------------------------------------
| v"                                    |
| >?X                                   |
|  "                                    |
| @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ |
| vsctf{??????????????????????????????} |
-----------------------------------------
```

so after the cursor rolls over the boundary, it gets sent to the right side by the `?` an average of 1/16 of the time

then, we can just print the two characters using the `,` instruction to output ascii characters

```
-----------------------------------------
| v"                                    |
| >?,,                                  |
|  "                                    |
| @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ |
| vsctf{??????????????????????????????} |
-----------------------------------------
```

this is 7 bytes, and 1/16 chance to get a letter of the flag

but what about the other flag characters?

well, we can just add whitespace because that doesnt count as instructions by the 7 instruction limit

```
-----------------------------------------
|  v"                                   |
|  >?,,                                 |
|   "                                   |
| @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ |
| vsctf{??????????????????????????????} |
-----------------------------------------
```

keep in mind that the cursor starts moving to the right

anyways, that gets every single letter of the flag except the first, but the first is known anyways.

from the script, i got `ssctf{Wh0_R3m3mber5_Yellsatbefunge5`, so i just guess the flag was `vsctf{Wh0_R3m3mber5_Yellsatbefunge5?}` because that's 37 chars
