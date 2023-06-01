# type-length-value

## problem

I have this file that uses some custom encoding. Can you figure it out?

see `flag.tlv`

## solution

if we try to look at the file from notepad, it shows random garbage because it shows in unicode, not ascii.

we check the wikipedia article on type length value encoding [here](https://en.wikipedia.org/wiki/Type%E2%80%93length%E2%80%93value)

we now know there are a bunch of chunks made of types, lengths, and values

hexdump of flag.tlv:

```
00000000: 0104 0802 1305 0401 7b02 0102 0102 1412  ........{.......
00000010: 0201 1301 020e 0c04 015f 0101 1302 0118  ........._......
00000020: 0102 0f04 0401 5f01 060b 040d 0613 0704  ......_.........
00000030: 015f 0105 1500 0b14 0404 015f 0205 1318  ._........._....
00000040: 0f04 1204 015f 0303 0104 0101 0200 0104  ....._..........
00000050: 017d                                     .}
```

we can see that it looks like a flag, but nothing else is visible.

let's look at the hex. so we know that first few bits are a "type", but we don't know how long the type value is in bits (or the length value either).

we take a look at the left curly bracket.

if we see that, we can see that before is an 01 and before that is an 04. from this we can infer that the type and length values are 1 byte long each, and that type 04 is a string type.

we take a look at the first section, we can see that it is type 01 and length 4. the next four bytes are 08, 02, 13, 05.

it took me a while to realize, but these use a standard english alphabet where 00=a and 0x19=z. we can then get the words "ictf", so we know it is correct.

the section after the `{`, we can see it is type 2 and length 1. we assume this is the same as type 01 and the first letter is 02=`c`

we can split up the chunks like so for better readability:

```
type=01, length=04, 08,02,13,05 ictf
type=04, length=01, 7b {
type=02, length=01, 02 c
type=01, length=02, 14,12 us
type=02, length=01, 13 t
type=01, length=02, 0e,0c om
type=04, length=01, 5f _
type=01, length=01, 13 t
type=02, length=01, 18 y
type=01, length=02, 0f,04 pe
type=04, length=01, 5f _
type=01, length=06, 0b,04,0d,06,13,07 length
type=04, length=01, 5f _
type=01, length=05, 15,00,0b,14,04 value
type=04, length=01, 5f _
type=02, length=05, 13,18,0f,04,12 types
type=04, length=01, 5f _
type=03, length=03, 01,04,01 beb
type=01, length=02, 00,01 ab
type=04, length=01, 7d }
```

remember that these are in hex, so we need to convert it from hex to decimal and add 1 to get the letter index

however, submitting the flag `ictf{custom_type_length_value_types_bebab}` does not look right because `bebab` is not a word, so i guessed type 3 must be integers where 00 -> 0.
therefore the flag is `ictf{custom_type_length_value_types_141ab}`

types 1 and 2 were originally different uppercase and lowercase, but the flag in all lowercase worked for me
