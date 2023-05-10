# simple checkin

## problem

(see checkin binary file)

## solution

I opened up the checkin file in my assembly reader tool, Binary Ninja.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/886560ab-9d88-42f8-985f-22e0a798dad3)

Here, I saw that there were two large sections of information being written.

My first thought was to XOR the two chunks of data, and so to get the data by itself, I used my tool text-format-wizard to cleanse the data.
I then used a short python script to XOR each chunk with the other and get the flag.

```py
data = """0x0b
0x68
0x7d

... (etc) ...

0xb0
0x99
0x5f
0x7e
0x22
0x5f""".splitlines()


total = ""
for _ in range(int(len(data)/2)):
    first = data[_*2]
    second = data[_*2+1]
    if len(first) == 2:
        first = f"0x{first}"
    total += chr(int(first[2:], 16) ^ int(second[2:], 16))
print(total)
```

The flag here is very long, it is `cvctf{i_apologize_for_such_a_long_string_in_this_checkin_challenge,but_it_might_be_a_good_time_to_learn_about_automating_this_process?You_might_need_to_do_it_because_here_is_a_painful_hex:32a16b3a7eef8de1263812.Enjoy(or_not)!}`.
