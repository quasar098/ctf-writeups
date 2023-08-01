# based

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/6af6d8dd-a568-4af0-8dfc-d72d76280832)

see `based` binary in this folder

## solution

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/67033bc8-c24c-4eb7-8355-053b146eea14)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/08836a37-5ea2-4e04-ab05-708236d309d9)

this looks overwhelming but all that we have to know its doing is that there is a table of 4096 3-char combos (this is base 4096), 
and that three characters of the flag are used as two characters in base-4096 because 1 digit in base-4096 is three hex digits, and 3 hex digits is 1.5 ascii chars, so 6 ascii -> 2 base-4096

too bored to do rest of writeup, the flag is `ictf{I_h0pe_th!5_cha1l_!s_n0rm@l_en0ugh..}`
