# yafc

## problem

You know the drill. Input the flag, the binary will tell you if it's correct. Unless the rng decides it isn't, who knows.

see `chall` file

## solution

if we take a look at it in binja

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/4df63ccb-d78a-4ae3-84bd-e0de434334e7)

this looks overwhelming, but we know the first few characters of the flag is `ictf{` and so we can try to guess what is happening from looking at the registers on runtime

it looks like for every character in the flag, the loop runs once with the new character appended to the `data_4140` buffer

also, the flag is 31 characters long (`var_c <= 0x1e`)

this was my learning experience to use the debugging utilities of binja in wsl2

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/182b2661-1b7c-4814-ab0a-687d14e7fe1e)

we can see that when we run the program a different function is called every time, and that function has to return 1 or else the main function's `var_10` is set to 0

so, we can just brute force the program by running a different flag each time in the debugger and seeing if the `var_10` is getting set to 0, and if not, a letter of the flag passed inspection.

this took me about 5 hours to brute force while running the debugger by hand. i now know, however, that binja has an api that you can access to automate the process.
