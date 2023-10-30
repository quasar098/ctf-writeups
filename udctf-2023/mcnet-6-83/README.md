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

basically, the original impartial game "21" is to count to 21 with another person, and whoever says 21 (or above) loses. however, you can increment the current number by 1, 2, or 3.

so for example, the current number starts at 0 and the first turn can result in the current number being 1, 2, or 3. for the next turn, the other player can bring it to 2, 3, 4, 5, or 6.

to get to 6, it is required that both players increment the current number by 3, so there is some strategy to this game

the winning strategy for 21 is to bring the current number to 20, because that will force the other player to say 21.

so, how do you get to 20 reliably?

well, if the opponent goes first, no matter what they say, 1, 2, or 3, it is always possible for you to get the number to 4.

here are some examples:

```
0 -> +1 -> +3 = 4
0 -> +2 -> +2 = 4
0 -> +3 -> +1 = 4
```

so no matter what the first player does, the second player can always bring it to 4 from the start.

now think about the next turn, the opponent goes next when the current number is 4, and so no matter what they say, it is possible for the next player to bring the current number to 8.

so like:

```
4 -> +1 -> +3 = 8
4 -> +2 -> +2 = 8
4 -> +3 -> +1 = 8
```

this works for all multiples of 4, and so it is possible to win (if you play as the second player) no matter what in the game 21, because the second player can get it to 20, which is a multiple of 4.

however, in the 6-83 game, the player (me) goes first. however, the number can be incremented by 1, 2, 3, 4, 5, or 6, and the goal is to not say 83.

to win in that game, the goal is to say 82, so the other player says 83.

that means that any integer n where 82 = n (mod 7) is a good number because it will allow you to forcibly say 82.

so, the lowest positive integer n is 5.

therefore, it is best to start with 5, and then say that number plus 7 because it will always be possible no matter what the opponent says.

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
