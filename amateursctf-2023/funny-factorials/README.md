# funny factorials

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/8f9b3212-0cbb-44b3-9fd9-2cdf9813d7fb)

see other files in this folder

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/f306fbab-0850-4a0e-8aa3-465c73ef96de)

## solution

taking a look at 

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/cfedcde7-7633-43f9-8a29-b7a5a69dc841)

(yes, i'm using notepad)

we can see it is possible to maybe do path traversal to read any file in the system. however:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/f552b16b-ad53-4cf6-832a-fa7689c4d55a)

we can see that there is path filtering for the themes files paths

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/7ba53d74-b99d-4333-8a79-e229f8787c94)

we know the flag is in `/flag.txt`, so we just need to access the root directory.

the main idea here is that the recursion error is only caught once (by the outermost function i think?), so we just need to put `%2F%2F` (equivalent to `//`), to bypass this.

once we do `?theme=%2F%2Fflag.txt`, we dont see any change in the main window, but we can take a look at the requests to see the flag which is interpreted as CSS code.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/e359612d-1745-4500-bc26-6b5090e5e728)
