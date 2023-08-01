# geoguessr

yet another geoguessr-style chall...

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/e2393ce9-8c2c-4b51-80e6-68b7ad873968)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/b508efeb-7130-4d87-a235-e0ef939252bb)

## solution

google reverse image search shows it's the four corners monument in the United States

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/6a5add27-41e0-48fc-bad3-4eff3c6e8589)

we can find the exact spot the image was taken in by trying all possible street view locations on google maps

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/ecf0de9b-ec54-4ab4-84ad-98a3d422ce30)

we find the coordinates to be `36.9986877,-109.0451584`, so we know the flag is `ictf{36.999,-109.045}` because of rounding of numbers
