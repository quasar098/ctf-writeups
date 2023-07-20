# gitint 7d

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/b54cba54-52ba-4889-8981-e301edf8869f)

## solution

i spent waaaay too long trying to solve this one (~10 hrs)

i knew the repo was more-CTFd-mods, but didn't know where the password was

i looked inside of the `*.md` and was able to get the bonus flag of `bonusFlag{good job you guessed hard enough. did you enjoy manually adding the 0s back?}` by writing a script

```py
allowed = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ `~1234567890!@#$%^&*()-=_+[]{};':\",./?\\|"

n = "binary of <br> and newlines here"
known = ""


def reduce(n_str: str, depth: int) -> list[str]:
    if len(n_str) == 0 or depth == 100:
        return ['']
    if n_str[0] == "0":
        return []
    possible = []
    for index in range(1, len(n_str)+1):
        substr = n_str[:index]
        if int(substr, 2) >= 256:
            break
        if chr(int(substr, 2)) in allowed:
            for _ in reduce(n_str[index:], depth+1):
                possible.append(chr(int(substr, 2)) + _)
    return possible


print(reduce(n, 0))
```

i also tried looking at github api to see if anything was being hidden, which nothing was (see [here](https://api.github.com/repos/les-amateurs/more-CTFd-mods/pulls/2))

i did not end up solving this one

the actual solution was to see that the pull request message has been edited

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/c25db516-5518-4b77-b103-d4b2661b5b12)

i never knew this was a feature, and i've always had to write a new message any time there is more info i have to say on github issues or a pull request

this challenge makes me very very angry

do not ever do github osint again pls
