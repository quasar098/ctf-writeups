# warmup 3

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/ae2dd77f-d6f5-4254-8581-2f059e577539)

## solution

Here, I first saw that there was a "abacd" pattern before the curly brace at the end, which made me think it was the flag.
Because all of the flags in this ctf start with "cvctf", I could easily replace those characters.

Now I have `女川弓c己廾川 t己 t大川 口川c己广巛 川巛飞t飞己广 己f c巾山彐t己v川巾口川 ctf. 弓艹口t 山川艹巾, t川艹廾 廾艹彐弓川 屮艹c己广 f巾己廾 c艹广艹巛艹 女己广 t大川 c己廾彐川t飞t飞己广 屮山 口己弓v飞广寸 艹弓弓 c大艹弓弓川广寸川口. 飞f 山己门 门广巛川巾口t己己巛 t大川 艹屮己v川 c大飞广川口川, 大川巾川 飞口 t大川 f弓艹寸: cvctf{飞广口川c门巾川c大广c飞彐大川巾}. 口门屮廾飞t t大川 f弓艹寸 飞广 弓己女川巾 c艹口川.`

I guessed that this four letter word might be flag, because it's right before the flag, so I replaced more characters

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/07023740-a6b1-4bda-bda3-70ed26bd499b)

Now I have `女川lc己廾川 t己 t大川 口川c己广巛 川巛飞t飞己广 己f c巾山彐t己v川巾口川 ctf. la口t 山川a巾, t川a廾 廾a彐l川 屮ac己广 f巾己廾 ca广a巛a 女己广 t大川 c己廾彐川t飞t飞己广 屮山 口己lv飞广g all c大all川广g川口. 飞f 山己门 门广巛川巾口t己己巛 t大川 a屮己v川 c大飞广川口川, 大川巾川 飞口 t大川 flag: cvctf{飞广口川c门巾川c大广c飞彐大川巾}. 口门屮廾飞t t大川 flag 飞广 l己女川巾 ca口川.`

I saw this:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/2993fbc8-5523-472b-a52f-8c968c2d1956)

and I immediately thought: canada

I replace more letters

I now have `女川lc己廾川 t己 t大川 口川c己nd 川d飞t飞己n 己f c巾山彐t己v川巾口川 ctf. la口t 山川a巾, t川a廾 廾a彐l川 屮ac己n f巾己廾 canada 女己n t大川 c己廾彐川t飞t飞己n 屮山 口己lv飞ng all c大all川ng川口. 飞f 山己门 门nd川巾口t己己d t大川 a屮己v川 c大飞n川口川, 大川巾川 飞口 t大川 flag: cvctf{飞n口川c门巾川c大nc飞彐大川巾}. 口门屮廾飞t t大川 flag 飞n l己女川巾 ca口川.`

I replace for letter "S" here:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/c10d66b7-c458-4954-9084-2f85aff937b6)

I replace for "second" here:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/11e66b1b-3d5c-4a90-bf9c-f1c55cc2ce9f)

I replace for "the" here:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/6edc3c50-23e6-454a-99d3-36df831562db)

Now I have `女elco廾e to the second ed飞t飞on of c巾山彐tove巾se ctf. last 山ea巾, tea廾 廾a彐le 屮acon f巾o廾 canada 女on the co廾彐et飞t飞on 屮山 solv飞ng all challenges. 飞f 山o门 门nde巾stood the a屮ove ch飞nese, he巾e 飞s the flag: cvctf{飞nsec门巾echnc飞彐he巾}. s门屮廾飞t the flag 飞n lo女e巾 case.`

It is pretty easy to read right now, and so I replace the rest of the letters I can.

We get the decrypted:

`welcome to the second edition of cryptoverse ctf. last year, team maple bacon from canada won the competition by solving all challenges. if you understood the above chinese, here is the flag: cvctf{insecurechncipher}. submit the flag in lower case.`

If you tried to solve this but do not know English too well, you would have a very hard time. You can use this however: 

- https://www.crosswordsolver.org/solve/ to find words that satisfy a specific pattern which you do not know letters
- https://quasar.name/text-format-wizard/ to replace all occurences of char
