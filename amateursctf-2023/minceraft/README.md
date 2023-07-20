# minceraft

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/5b0cc08e-90d9-42e7-94e1-d1309ace0996)

## solution

i did not solve this chall

it is really dumb that i didnt

but here are my steps anyways:

my first order of business was to open the minecraft world by replacing region files in an existing world with these ones.

i also know from experience that each region file is 32x32 chunks, and each chunk is 16x16, so we can just teleport to the coords in-game

we see that it's nether chunks

i checked the fortress near the centre to try to find something, but i didn't

i saw some suspicious cows on the nether roof, but i didn't think much of it because i was in the overworld looking at nether chunks, but that was the answer (i did not know during ctf)

my second order of business was to use NBTExplorer, which i am already very familiar with, to try to find the flag

seeing how many solves (95) there were, i would expect that it would be easy and not obscure, but for some reason NBTExplorer search didnt work and i did not find the flag by searching

the flag is `amateursCTF{cow_wear_thing_lmao_0f9183ca}` by the way

then, i made this script to try to find it in chunk data

do.py
```py
import anvil
import anvil.errors
from os import listdir
from nbtest import dump_zlibd_chunk


cnks = []
for fname in [<names>]:
    with open(fname, 'rb') as f:
        data = f.read()[0x2000:]
        while len(data):
            chunk_length = int.from_bytes(data[:4], 'big')
            chnk, data = data[0x5:0x5+chunk_length], data[((chunk_length//0x1000)+1)*0x1000:]

            print(chunk_length, fname)

            chnk_dump = dump_zlibd_chunk(chnk)
            if "amateurs" in chnk_dump:
                print(chnk_dump)
                quit()
```

nbtest.py
```py
import zlib
from io import BytesIO
import nbt.nbt


def dump_zlibd_chunk(chnk):
    data = zlib.decompress(chnk)

    root = nbt.nbt.NBTFile(buffer=BytesIO(data))

    def recurs(tag):
        if isinstance(tag, nbt.nbt.TAG_Compound):
            total = {}
            for name in tag.keys():
                total[name] = recurs(tag.get(name))
            return total
        if isinstance(tag, nbt.nbt.TAG_List):
            total = []
            for _ in tag.tags:
                total.append(recurs(_))
            return total
        if isinstance(tag, nbt.nbt.TAG_Byte_Array):
            return eval(str(tag))
        return tag.value

    return recurs(root.get("Level"))
```

i did not end up finding it somehow, i do not know how

i am very angry about it because i spent way too long on this one as well, and i have over 4k hours in the 'craft
