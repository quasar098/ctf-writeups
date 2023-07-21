# flagchecker

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/3c7de0ff-8e86-4cb1-a006-bb0290763e5a)

## solution

we can click to see the scratch code

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/67cb7d88-6a2f-4928-a1af-3b3151ba5132)

it looks like a nightmare, but we can clean things up by renaming variables via right click and dragging the top of code block chains (not blockchains!!!) around.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/201f81c6-b05d-4159-9c2d-39aca56e11d6)

i already have a few years of experience with scratch and it was good to see scratch once more

i don't have screenshots of me solving this, but it took a few hours and a lot of skill

anyways, we find that most of the code is just bloat and prerequisites for binary conversion, which doesn't exist in scratch by default, so custom implementations in pink are there

we can see that the scratch code is equivalent to 

```py
def obfuscate(f, z, r):
    f += 2654435769
    f %= 4294967296
    g = (((r << 4) + 69420) % 4294967296) ^ ((r+f) % 4294967296)
    z += g ^ ((r // 32) + 1412141)
    z %= 4294967296
    g = (((z << 4) + 1936419188) % 4294967296) ^ ((z+f) % 4294967296)
    r_diff = g ^ ((z // 32) + 1953260915)
    r += r_diff
    r %= 4294967296
    return f, z, r


def deobfuscate(after_f, after_z, after_r):
    before_f = after_f - 2654435769
    before_f %= 4294967296
    r_xor_left = ((after_z << 4) + 1936419188) % 4294967296
    r_xor_right = (after_f+after_z) % 4294967296
    r_g = r_xor_left ^ r_xor_right
    r_diff = r_g ^ ((after_z // 32) + 1953260915)
    before_r = after_r - r_diff
    before_r %= 4294967296
    z_xored = (((before_r << 4) + 69420) % 4294967296) ^ ((before_r+after_f) % 4294967296)
    before_z = after_z - (z_xored ^ ((before_r // 32) + 1412141))
    before_z %= 4294967296
    return before_f, before_z, before_r


def modify(block: bytes):
    z = int.from_bytes(block[:4], 'big')
    r = int.from_bytes(block[4:], 'big')
    f = 0
    for _ in range(32):
        f, z, r = obfuscate(f, z, r)
    print(f)
    return [list(int.to_bytes(_, 4, 'big')) for _ in [z, r]]


def demod(outchunk: list[int]):
    outchunk = bytes(outchunk)
    z = int.from_bytes(outchunk[:4], 'big')
    r = int.from_bytes(outchunk[4:], 'big')
    f = 3337565984
    for _ in range(32):
        f, z, r = deobfuscate(f, z, r)
    return [list(int.to_bytes(_, 4, 'big')) for _ in [z, r]]


def main():
    with open("flag.txt") as f:
        flag = [int(_) for _ in f.read().splitlines(False)]

    # encode
    text = b"amateursCTF{"
    encoded = []
    while len(text) % 8:
        text += b'\x00'
    for _ in range(len(text)//8):
        encoded.extend(modify(text[_*8:8+_*8]))
    encoded, _ = [], encoded
    for _2 in _:
        encoded.extend(_2)
    print(encoded)

    # decode
    for _ in range(len(flag)//8):
        print(demod(flag[_*8:8+_*8]))


if __name__ == '__main__':
    main()
```

the above file also includes the deobfuscation function to decode the thing, but that was made by me.

this is apparently the [TEA](https://en.wikipedia.org/wiki/Tiny_Encryption_Algorithm), which i did not know existed or what it was

anyways the flag is `amateursCTF{screw_scratch_llvm_we_code_by_hand_1a89c87b}`
