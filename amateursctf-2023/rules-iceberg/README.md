# rules-iceberg

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/5a1c7b43-7dbe-4eab-81cb-10511376177f)

## solution

this one was really really easy because i knew python pillow library and it told me that it was 2nd lsb stego anyways

solve.py
```py
from PIL import Image


def decode_lsb(og_p, new_p):
    image = Image.open(og_p)
    pixels = image.load()
    image_new = Image.open(new_p)
    pixels_new = image_new.load()
    binary_decoded = []

    char_index = 0
    for y in range(image.height):
        for x in range(image.width):
            r, g, b, a = pixels[x, y]

            if r > g and r > b:
                binary_decoded.append((pixels_new[x, y][0] & 2) >> 1)
                char_index += 1

    # Save the modified image
    print(binary_decoded)


# Example usage
ogpath = "rules-iceberg.png"

newpath = "new-rules-iceberg.png"

decode_lsb(ogpath, newpath)
```

then we take the output and put it into text format wizard to strip out any chars that arent 0 or 1

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/d9049043-651f-41c2-8b62-2bae79cc19a7)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/776f4ac1-a3cd-4833-ae74-c4cd0343b919)
