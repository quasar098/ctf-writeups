# strange-flag-checker

yet another challenge i made for ictf

## problem

Made entirely on a phone using the "BrainHack" app

strange.txt
```
,>++++++++[-<------------->]+<-[[-]>-<]>[-,>+++++++++[-<----------->]+<[[-]>-<]>[-,>++++++++++[-<------------>]+<++++[[-]>-<]>[-,>++++++++++[-<---------->]+<--[[-]>-<]>[-,>++++++++++[-<------------>]+<---[[-]>-<]>[-,>+++++++++++[-<----------->]+<[[-]>-<]>[-,>+++++++++++[-<---------->]+<-[[-]>-<]>[-,>+++++++++++[-<----------->]+<++++[[-]>-<]>[-,>++++++++++[-<---------->]+<+++++[[-]>-<]>[-,>++++++++++[-<----------->]+<+[[-]>-<]>[-,>++++++++++[-<---------->]+<-----[[-]>-<]>[-,>++++++++++[-<---------->]+<---[[-]>-<]>[-,>++++++++++[-<---------->]+<----[[-]>-<]>[-,>++++++++++[-<------------>]+<++++[[-]>-<]>[-,>++++++++++[-<---------->]+<+++++[[-]>-<]>[-,>++++++++++[-<----------->]+<[[-]>-<]>[-,>++++++++++[-<---------->]+<-[[-]>-<]>[-,>++++++++++[-<---------->]+<-[[-]>-<]>[-,>++++++++++[-<---------->]+<[[-]>-<]>[-,>++++++++++[-<---------->]+<+++++[[-]>-<]>[-,>++++++++++[-<---------->]+<+++[[-]>-<]>[-,>++++++++++[-<---------->]+<+++++[[-]>-<]>[-,>++++++++++[-<---------->]+<[[-]>-<]>[-,>++++++++++[-<---------->]+<-[[-]>-<]>[-,>++++++++++[-<---------->]+<++[[-]>-<]>[-,>++++++++++[-<------------>]+<+++[[-]>-<]>[-,>++++++++++[-<---------->]+<---[[-]>-<]>[-,>++++++++++[-<---------->]+<---[[-]>-<]>[-,>++++++++++[-<---------->]+<-[[-]>-<]>[-,>++++++++++[-<----------->]+<----[[-]>-<]>[-,>++++++++++[-<---------->]+<+++++[[-]>-<]>[-,>++++++++++[-<---------->]+<--[[-]>-<]>[-,>+++++++++++[-<---------->]+<-[[-]>-<]>[-,>++++++++++[-<----------->]+<----[[-]>-<]>[-,>++++++++++[-<--------->]+<-----[[-]>-<]>[-,>++++++++++[-<------------>]+<++++[[-]>-<]>[-,>++++++++++[-<---------->]+<----[[-]>-<]>[-,>++++++++[-<------------->]+<-[[-]>-<]>[-,>++++++++++[-<----------->]+<-----[[-]>-<]>[-,>++++++++++[-<---------->]+<+++++[[-]>-<]>[-,>++++++++++[-<----------->]+<-[[-]>-<]>[-,>++++++++++[-<----------->]+<[[-]>-<]>[-,>++++++++++[-<---------->]+<-[[-]>-<]>[-,>++++++++++[-<---------->]+<+++++[[-]>-<]>[-,>++++++++++[-<---------->]+<++[[-]>-<]>[-,>++++++++++[-<------------>]+<++++[[-]>-<]>[-,>++++++++++[-<------------>]+<+[[-]>-<]>[-,>++++++++++[-<------------->]+<+++++[[-]>-<]>[-++++++++++[->++++++++++<]>+++..>++++[->++++++++<]>.<<-----.++++++++++++++++.---.>>+.-.<<-.+.>>.[-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]][-]]>[[-]>+<]>+[-<->]<[[-]++++++++++[->++++++++++<]>++.-----.++++++++.+++.[-]]<
```

## solution

notice that if an incorrect character is entered, the program skips to the end and halts. therefore, we can exploit this to figure out whether each character was correct

here is what my program tries:

```
...
a
b
c
d
e
f
g
h
i <- execution doesnt halt, we are prompted for another char
ia
ib
ic <- again, prompted for another character
ica
icb
...
(etc)
...
ictf{you_might_need_a_debugger_for_this_one_btw{
ictf{you_might_need_a_debugger_for_this_one_btw|
ictf{you_might_need_a_debugger_for_this_one_btw} <- accepts this input
...
```

```py
import sys


queue = ""


def nextchar():
    global queue
    next_c, queue = queue[0], queue[1:]
    return next_c


def evaluate(code, capture_callback):
    code = cleanup(list(code))
    bracemap = buildbracemap(code)
    incount = 0

    cells, codeptr, cellptr = [0], 0, 0

    while codeptr < len(code):
        command = code[codeptr]

        if command == ">":
            cellptr += 1
            if cellptr == len(cells):
                cells.append(0)

        if command == "<":
            cellptr = 0 if cellptr <= 0 else cellptr - 1

        if command == "+":
            cells[cellptr] = cells[cellptr] + 1 if cells[cellptr] < 255 else 0

        if command == "-":
            cells[cellptr] = cells[cellptr] - 1 if cells[cellptr] > 0 else 255

        if command == "[" and cells[cellptr] == 0:
            codeptr = bracemap[codeptr]
        if command == "]" and cells[cellptr] != 0:
            codeptr = bracemap[codeptr]
        if command == ".":
            capture_callback(chr(cells[cellptr]))
        if command == ",":
            incount += 1
            cells[cellptr] = ord(nextchar())

        codeptr += 1

    return incount


def cleanup(code):
    return ''.join(filter(lambda x: x in ['.', ',', '[', ']', '<', '>', '+', '-'], code))


def buildbracemap(code):
    temp_bracestack, bracemap = [], {}

    for position, command in enumerate(code):
        if command == "[":
            temp_bracestack.append(position)
        if command == "]":
            # noinspection PyUnresolvedReferences
            start = temp_bracestack.pop()
            bracemap[start] = position
            bracemap[position] = start
    return bracemap


def main():
    global queue

    with open("./strange.txt") as f:
        code = f.read()

    good = "ictf{"
    total = ""
    count = 0

    def add_to_total(c):
        nonlocal total
        total += c

    while True:
        for _ in range(256):
            total = ""
            queue = good + chr(_)
            try:
                count = evaluate(code, add_to_total)
                if total != "fail":
                    print(good + chr(_))
                    quit()
            except IndexError:
                print(good)
                good += chr(_)
                break


if __name__ == '__main__':
    main()
```
