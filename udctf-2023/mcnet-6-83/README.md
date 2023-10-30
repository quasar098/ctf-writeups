# mcNET 6-83

## problem

```
In our udctf.mc.gg 1.19.2 minecraft server you can play the game 83 against our bot. Win to get your flag. Here's how you play:

Start at 0, each player adds 1 to 6 to the current total, whoever says 83 loses! Start (or reset) by saying !game N where N is 1 to 6. Then continue with higher and higher numbers until someone is forced to say 83.

For instance:

!game 1

bot says 7

!game 13

bot says 14

etc.

If you solve this and mcNET warm-up you will unlock a 3rd final boss.

udctf.mc.gg
```

## solution

i am not sure if anyone has ever played the impartial game called "21" in their childhood

if you have, this challenge is free

basically, the original impartial game "21" is to count to 21 with another person, and whoever says 21 loses. however, you can increment the current number by 1, 2, or 3.

so basically that challenge would be mcNET 3-21.

the solution for the game 21 is to bring the number to 20, so that the opponent has to say 21.

notably, no matter what the first player says, 1, 2, or 3, it is always possible for the opponent to get the current number to four

then, no matter what the first player says, 1, 2, or 3, it is always possible to bring the number to a multiple of four.

likewise, it is always possible to bring the number to a multiple of 7 in 6-83. just try it yourself

```
82
75
68
61
54
47
40
33
26
19
12
5
```

i wrote a quick little script to get the numbers above. the goal is to get the number to 82, so the opponent is forced to say 83

therefore, the first optimal move is to start at the number 5, because no matter what the opponent says, it is always possible to bring it to 12, and so on

after saying these numbers in order from least to greatest, i got the flag.
