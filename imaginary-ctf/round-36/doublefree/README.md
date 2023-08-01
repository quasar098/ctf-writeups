# doublefree

## problem

Free me once, a coding routine. Free me twice, disaster unforeseen. Put the flag in `ictf{string_here_with_no_spaces}` format.

see libc.so.6

## solution

i took my copy of libc.so.6 on my wsl2 instance, and compared it to the new version using `cmp` command:

```
quasar@quasar098:~/wasteland/doublefree$ cmp /lib/x86_64-linux-gnu/libc.so.6 libc.so.6
/lib/x86_64-linux-gnu/libc.so.6 libc.so.6 differ: byte 1959705, line 3687
```

i knew the difference would be at address `0x1DE719` (that's 1959705 in hex), so i went there in binja to see what changed

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/337b0666-3ec1-4113-aa1a-3d31896d2075)

here, we get the flag as `ictf{fr33_str1ngs}`
