# zshfuck

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/afc8e1ca-2460-466c-8bb8-0d25be94ca0a)

solved this with "HalfInchPunisher" also on the CyberSpace team

## solution

```zsh
#!/bin/zsh
print -n -P "%F{green}Specify your charset: %f"
read -r charset
# get uniq characters in charset
charset=("${(us..)charset}")
banned=('*' '?' '`')

if [[ ${#charset} -gt 6 || ${#charset:|banned} -ne ${#charset} ]]; then
    print -P "\n%F{red}That's too easy. Sorry.%f\n"
    exit 1
fi
print -P "\n%F{green}OK! Got $charset.%f"
charset+=($'\n')

# start jail via coproc
coproc zsh -s
exec 3>&p 4<&p

# read chars from fd 4 (jail stdout), print to stdout
while IFS= read -u4 -r -k1 char; do
    print -u1 -n -- "$char"
done &
# read chars from stdin, send to jail stdin if valid
while IFS= read -u0 -r -k1 char; do
    if [[ ! ${#char:|charset} -eq 0 ]]; then
        print -P "\n%F{red}Nope.%f\n"
        exit 1
    fi
    # send to fd 3 (jail stdin)
    print -u3 -n -- "$char"
done
```

we are only allowed to use 6 characters of our choosing (including space and tab) and then we can pass them to the stdin of another process as many times as we want. the goal is to run the getflag binary somewhere on the system

the first thing we can do is to try finding the getflag binary by using `find /` as our charset and `find /` as the command also

first.txt
```
find /
find /
```

```console
quasar@quasar098:~/wasteland/dice$ cat first.txt | nc mc.ax 31774 | grep getflag
/app/y0u/w1ll/n3v3r_g3t/th1s/getflag
```

for the other payload, we can use `[^r]` to match any character that is not `r`, i think, and then we can just run the binary by using those instead of regular letters in our path

```
/[^r][^r][^r]/[^r][^r][^r]/[^r][^r][^r][^r]/[^r][^r][^r][^r]r[^r][^r][^r][^r]/[^r][^r][^r][^r]/[^r][^r][^r][^r][^r][^r][^r]
```

this works because each character is not r except for that one character which is r, which we can just use as r
