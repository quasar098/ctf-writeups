# ojail

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/38d6776d-c813-4576-a7be-5801c4573a06)

## solution

This netcat just opens up an OCaml shell

In DamCTF, I saw that the solution to one of the challenges was to execute commands from inside of ruby programming language.

Here, I used `Sys.command "ls";;` to get the `ls` command output.

Then, I saw a folder named system.

I did `Sys.command "ls system"` and saw a flag file

I used `cat` command on flag file and got the flag.
