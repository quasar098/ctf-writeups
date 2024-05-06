# bf golfing

i am mad fr that the challenge changed from 90 to 100 char instead of 90 to 91 char. mad mad mad asf

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/fdf2ecb5-d1b4-468f-a785-3b82ae7c312b)

```py
def run_brainfuck(code):
    if len(code) > 90:
        return "Error: Code length exceeds 90 characters."
 
    tape = [0] * 30000
    ptr = 0
    code_ptr = 0
    output = []
    brackets = []
    max_steps = 200000
    step_count = 0

    while code_ptr < len(code) and step_count < max_steps:
        command = code[code_ptr]
        if command == '>':
            ptr += 1
        elif command == '<':
            ptr -= 1
        elif command == '+':
            tape[ptr] = (tape[ptr] + 1) % 256
        elif command == '-':
            tape[ptr] = (tape[ptr] - 1) % 256
        elif command == '.':
            output.append(chr(tape[ptr]))
        elif command == '[':
            if tape[ptr] == 0:
                depth = 1
                while depth and code_ptr < len(code):
                    code_ptr += 1
                    if code[code_ptr] == '[':
                        depth += 1
                    elif code[code_ptr] == ']':
                        depth -= 1
            else:
                brackets.append(code_ptr)
        elif command == ']':
            if tape[ptr] != 0:
                code_ptr = brackets[-1]
            else:
                brackets.pop()
        code_ptr += 1
        step_count += 1

    if step_count >= max_steps:
        return None  # Indicate that the program was terminated due to too many steps

    return ''.join(output)


print("how well can you read and write code now?")
bf_code = input()
expected_output = 'squ1rrel{es0g01f}'
actual_output = run_brainfuck(bf_code)

if expected_output != actual_output:
    print("does your brain hurt yet?")
    exit(1)
else:
    print("squ1rrel{test_flag}")
```

## solution

the goal is to make a program that outputs `squ1rrel{es0g01f}` and then we get the real flag

i tried making one manually, but it was too big (117 chars)

`+++[[<+>>++<-]>]<<<<+++[-<<+<+<+<+>>>>>]<<<++++.--.++++.>>+.<<---..<<-.>+++.>>.<<<.>>+.>>-.<<<<++.>>>>.+.<<<<-.>>>++.` my manual solution

from [https://codegolf.stackexchange.com/questions/55422/hello-world](https://codegolf.stackexchange.com/questions/55422/hello-world)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/cf5f8614-47ec-465a-9255-4ef560da5973)

this 72 byte solution was found using brute force

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/7e6ae9d4-f838-45d2-baa4-1eb15d634b7c)

see the repo [here](https://github.com/ksabry/bfbrute/tree/master)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/620e9e7e-fd58-49b5-a8c6-c418c0c434ad)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/c87cd4bf-e82b-45e7-a3de-db87db0719c6)

i changed the output to the goal output, and also changed the below

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/77a2c834-e23d-4e95-a57d-961fa8a3e5a4)

i set the threads to be 12 (the amount that my cpu has), and the SIZE_START to 24 (this seemed to be the ideal number).

i noticed the program runs a lot faster if the SHOW_ALL_PROGRAMS_LENGTH is lower, so i set it to 100.

i generated a lot of below 100 ones, but the best one i generated was `+[[+<->>>]<--<-<<<+<-<-]<<<------.--.++++.<-.>---..<<<-.<.<-.>>.>>>+.<-.<<++.>>.+.<<-.<<++.`, which is 91 bytes.

unfortunately, they raised the limit to 100 which is wayyyy too easy.

```terminal
quasar@quasar098:~/Downloads$ nc 34.132.166.199 11111
how well can you read and write code now?
+[[+<->>>]<--<-<<<+<-<-]<<<------.--.++++.<-.>---..<<<-.<.<-.>>.>>>+.<-.<<++.>>.+.<<-.<<++.
squ1rrel{g3n1u5_0r_brut3f0rc3d??!?}
```
