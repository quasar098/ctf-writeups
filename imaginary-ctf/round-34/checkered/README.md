# checkered

## problem

see out.pcap

## solution

this is a wireshark dump

if we take a look at the data section and use the arrow down key to scroll through the packets, we can see the flag is in the checksum

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/914554ae-824c-4a06-9ad0-46d3031dda43)
![image](https://github.com/quasar098/ctf-writeups/assets/70716985/ff84b979-69d5-4913-b8a8-e8e2e952b228)
![image](https://github.com/quasar098/ctf-writeups/assets/70716985/210a8219-2108-4335-9990-1a076fb2b633)
![image](https://github.com/quasar098/ctf-writeups/assets/70716985/17883e01-376f-4108-b2a7-2cdadf79aacc)

etc.

the flag is `ictf{playing_4D_chess_with_checksums}`
