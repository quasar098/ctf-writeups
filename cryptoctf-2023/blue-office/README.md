# blue-office

## problem

The Blue Office's ingenious cipher, meticulously crafted for the prestigious CCTF, became an impenetrable enigma that left even the most seasoned cryptanalysts baffled.

```py
#!/usr/bin/enc python3

import binascii
from secret import seed, flag

def gen_seed(s):
	i, j, k = 0, len(s), 0
	while i < j:
		k = k + ord(s[i])
		i += 1
	i = 0
	while i < j:
		if (i % 2) != 0:
			k = k - (ord(s[i]) * (j - i + 1))            
		else:
			k = k + (ord(s[i]) * (j - i + 1))
	
		k = k % 2147483647
		i += 1

	k = (k * j) % 2147483647
	return k

def reseed(s):
	return s * 214013 + 2531011

def encrypt(s, msg):
	assert s <= 2**32
	c, d = 0, s
	enc, l = b'', len(msg)
	while c < l:
		d = reseed(d)
		enc += (msg[c] ^ ((d >> 16) & 0xff)).to_bytes(1, 'big')
		c += 1
	return enc

enc = encrypt(seed, flag)
print(f'enc = {binascii.hexlify(enc)}')
```

## solution

here, we can see that a secret integer between 0 and 2**32 inclusive is used as the seed, and it is reseeded and then XORed by the original string to get the final string

the key element here is that the only bits that matter are the `1 << 16` to `1 << 23` bits because of the bit mask of `0b0000_0000_1111_1111_0000_0000_0000_0000` or `0x00ff0000`.

so, we can just brute force this from 0 to 2**24

```py
#!/usr/bin/enc python3

import binascii
from secret import seed, flag


def gen_seed(s):
    i, j, k = 0, len(s), 0
    while i < j:
        k = k + ord(s[i])
        i += 1
    # k is currently the ascii sum of the string
    i = 0
    while i < j:
        print(j - i + 1)
        if (i % 2) != 0:
            k = k - (ord(s[i]) * (j - i + 1))
        else:
            k = k + (ord(s[i]) * (j - i + 1))

        k = k % 2147483647
        i += 1

    k = (k * j) % 2147483647
    return k


def reseed(s):
    return s * 214013 + 2531011


def encrypt(s, msg):
    assert s <= 2 ** 32
    index, d = 0, s
    enc, length = b'', len(msg)
    #  C, C, T, F, {
    # f3,f9,
    print(d)
    while index < length:
        d = reseed(d)
        enc += (msg[index] ^ ((d >> 16) & 0xff)).to_bytes(1, 'big')
        index += 1
    return enc


def decrypt(s, enc):
    assert s <= 2 ** 32
    index, d = 0, s
    msg, length = b'', len(enc)
    while index < length:
        d = reseed(d)
        msg += (enc[index] ^ ((d >> 16) & 0xff)).to_bytes(1, 'big')
        index += 1
    return msg


def main():
    enc2 = encrypt(seed, flag)
    print(f'enc = {binascii.hexlify(enc2)}')
    ltb = binascii.unhexlify('b0cb631639f8a5ab20ff7385926383f89a71bbc4ed2d57142e05f39d434fce')
    for tryseed in range(2**24):
        if tryseed % 2**20 == 0:
            print(tryseed//(2**24/100))
        oted = decrypt(tryseed, ltb)
        if oted.startswith(b"CCTF"):
            print(oted, tryseed)
            quit()


if __name__ == '__main__':
    main()
```

this takes some time but does print out the flag `CCTF{__B4ck_0r!F1c3__C1pHeR_!!}` and seed `10364460`
