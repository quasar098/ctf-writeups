# screenshotguesser

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/71d0af3b-329e-4f06-a8ed-d0878537780e)

```py
#!/usr/bin/env python3
# modified from HSCTF 10 grader
import json
with open("locations.json") as f:
	locations = json.load(f)
wrong = False
for i, coords in enumerate(locations, start=1):
	x2, y2 = coords
	x, y = map(float, input(f"Please enter the long and lat of the location: ").replace(",","").split(" "))
	if abs(x2 - x) < 0.0025 and abs(y2 - y) < 0.0025:
		print("Correct!")
	else:
		print("Wrong!")
		wrong = True

if not wrong:
	with open("flag.txt") as f:
		print("Great job, the flag is ",f.read().strip())
else:
	print("Better luck next time, but here's a postcard. (hopefully your term can display it) ")
	with open("bear.bin") as f:
		print(f.read())
```

![screenshot](https://github.com/quasar098/ctf-writeups/assets/70716985/fcbbaea6-b4fa-4600-a19b-3c5fd8346a3f)

## solution

luckily i know about wigle.net from another ctf, so i was able to quickly find the location using the primavera 5g 2.4hz one

i saw that it's in Tucson arizona

i then found closeby places around it and then averaged the latlong positions and then got the flag
