# baby aes

## problem

```py
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
from secret import flag

KEY_LEN = 2
BS = 16
key = pad(open("/dev/urandom","rb").read(KEY_LEN), BS)
iv =  open("/dev/urandom","rb").read(BS)

cipher = AES.new(key, AES.MODE_CBC, iv)
ct = cipher.encrypt(pad(flag, 16))

print(f"iv = {iv.hex()}")
print(f"ct = {ct.hex()}")

# Output:
# iv = 1df49bc50bc2432bd336b4609f2104f7
# ct = a40c6502436e3a21dd63c1553e4816967a75dfc0c7b90328f00af93f0094ed62
```

## solution

```py
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
from secret import flag

KEY_LEN = 2
BS = 16

# Output:
iv = bytes.fromhex("1df49bc50bc2432bd336b4609f2104f7")
ct = bytes.fromhex("a40c6502436e3a21dd63c1553e4816967a75dfc0c7b90328f00af93f0094ed62")

while True:
    with open('/dev/urandom', 'rb') as f:
        rbytes = f.read(KEY_LEN)
        # noinspection PyBroadException
        try:
            cp = AES.new(pad(rbytes, BS), AES.MODE_CBC, iv)
            decrypted = cp.decrypt(ct).decode()
            if "cvctf" in decrypted:
                print(decrypted)
        except Exception:
            pass
```

Here, we were given a two-byte encryption key and a sixteen-byte IV. Luckily, the IV is given.
There are only (2^8)^2 = 2^16 possibilities, which isn't much at all.
I tried solving it using a for loop and enumerating over possible bytes, but I found more success with reading /dev/urandom and hoping for the key to be correct

The flag is `cvctf{b4by_AES_s1mpL3}`, and the key is `a5cf` (represented as hex)
