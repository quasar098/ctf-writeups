# mcNET warm up

## problem

```
OK, so we built a working minecraft internet... but it's clunky and you all could very well crash things and it's tough to install. But honestly, the real internet was actually also tough to connect in the early days. To make it easy on you we set it up on a server: udctf.mc.gg Our server is set to version 1.19.2 (Java edition).

It can hold 70 people at a time so if it's getting overwhelmed just have some patience and try to be decent to each other when you're on there. Once you are on the server (in survival mode) you can gain access to a few things (all vanilla):

!ping to know that it's working (you should hear pong)
!get_computer will build you a personal computer in front of you (Don't move while it builds!!!! This will mess up your computer!!!) Once you have a computer you can fetch anyone's hosted builds using our first internet protocol:
!get_build profninja/flag will get you this first flag (my daughter made it for us, thanks hon)
!game N where N is an integer 1 to 6 will start the 6-83 game (another flag if you win)
If you solve this one and mcNET 6-83 it will unlock a 3rd mcNET problem.

Alright, so this one is a mechanical intro to our mcNET. But we made it easy enough that it's not educational yet. So here is the architecture:

The minecraft server connects to a private discord server using essentialX, the private discord server acts like the Network Interface Card.

Our private discord has another bot which implements various protocols, so far our protocols are pretty simple. This second bot is basically an ethernet cable plugged into an ISP.

Your personal computer in the server acts like the running process at a port which can act on inputs from the rest of the world.

Our !get_build domain/path protocol is our minecraft-y HTTP. If you want to host a site/build we can upload any .nbt files (in Minecraft you can put your builds into structure blocks which saves them as .nbt). We wrote a beautiful .nbt to .mc86 (sequences of minecraft commands) system that then saves the builds in a firebase. Our nodeJS "file-server" parses the commands and packetizes them (there are maximum packet sizes to the various discord bots) and gets them down to your "port" (minecraft storage commands) which your CPU then runs to create the build in front of you.

If you're interested in connecting your own servers to each other and some sort of minecraft internet you can checkout the github project. They made a docker, various scripts, and some setup guides. I went through the process from their instructions and it was pretty tough, a CTFer could do it, but I decided not to make you.

-ProfNinja and JD and a Capstone Team last year
```

## solution

i logged onto the minecraft server on version 1.19.2

then, i typed into chat the command `!get_build profninja/flag`

then, i typed into chat the command `!get_computer`

the get_computer command placed command blocks and gave me a button, which i could use to start the computer. the command block computer was using [mc86](https://www.youtube.com/watch?v=mqOSgJ0NM_Q), but i don't know where the chest with the commands was.

anyways, clicking the button in game made the server build a greenhouse building which contained the flag on a sign
