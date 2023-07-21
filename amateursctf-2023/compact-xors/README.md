# compact xors

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/de0731a4-7f3a-45ef-bfd7-fec4c5642243)

fleg
`610c6115651072014317463d73127613732c73036102653a6217742b701c61086e1a651d742b69075f2f6c0d69075f2c690e681c5f673604650364023944`

## solution

we know the first few characters of the flag are going to be `amateursCTF`, or `616d617465757273435446` in hex

looking at the first 6 hex chars, we see that it is `610c61`, which follows the `ama` pattern

we can see that two input chars xored outputs one char (`61^6d` -> `0c`) for each character pair `ab` in `abababab`

we can use a script to solve this

```py
data = ["610c6115651072014317463d73127613732c73036102653a6217742b701c61086e1a651d742b69075f2f6c0d69075f2c690e681c5f673604650364023944"[_*2:_*2+2] for _ in range(62)]
#        a m a t e u r s C T F
#        616d617465757273435446
new = ""
for _ in range(len(data)//2):
    a = int(data[_*2], 16)
    b = int(data[_*2+1], 16)
    new += chr(a)
    new += chr(a ^ b)
print(new)
```
