# elfcrafting-v1

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/164e221d-58e8-404b-ad5c-dc02b5fdbd72)

see Dockerfile, chall files in this folder

## solution

we know the flag is in `/app/flag.txt`, because of the Dockerfile

next, we take a look at the chall binary in binja

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/3f0cef33-74d3-43e0-8f2a-d1b0d3aebc0b)

notably, it looks like it copies our binary to memory and executes it

we can see that it does `fexecve`. i wonder what that is?

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/09b041ce-88bb-4c15-973c-ab0a7414b038)

if we look at [docs](https://man7.org/linux/man-pages/man3/fexecve.3.html), we see that it is the same as execve but with a file descriptor, so reading from memory

for execve, we see [here](https://man7.org/linux/man-pages/man2/execve.2.html)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/d8c9c2dd-6322-4646-97ff-2fd7513d1f0f)

hmm. what if we use an interpreter script instead?

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/9f9d1816-7edd-49a7-8002-43fdff8d8c93)

it looks like we can run any executable, so we input `#!/usr/bin/cat /app/flag.txt`

```
quasar@quasar098:~$ nc amt.rs 31178
I'm sure you all enjoy doing shellcode golf problems.
But have you ever tried ELF golfing?
Have fun!
#!/usr/bin/cat /app/flag.txt
read 29 bytes from stdin
wrote 29 bytes to file
amateursCTF{i_th1nk_i_f0rg0t_about_sh3bangs_aaaaaargh}
/usr/bin/cat: /dev/fd/3: No such file or directory
```

we get the flag

this was the easiest pwn chall i've ever seen, and probably one of the only ones i can complete
