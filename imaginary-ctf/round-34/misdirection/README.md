# misdirection

## problem

see flag.zip

## solution

it is password protected, but we can just use a hexdump on it to find the flag

```
00000000: 504b 0304 0a00 0900 0000 8fa5 a256 b42d  PK...........V.-
00000010: 9f15 2600 0000 2600 0000 0800 1c00 666c  ..&...&.......fl
00000020: 6167 2e74 7874 5554 0900 039d d851 649e  ag.txtUT.....Qd.
00000030: d851 6475 780b 0001 04e8 0300 0004 e803  .Qdux...........
00000040: 0000 6963 7466 7b68 6964 696e 675f 696e  ..ictf{hiding_in
00000050: 5f70 6c61 696e 5f73 7472 696e 6773 5f63  _plain_strings_c
00000060: 3364 3435 3136 347d 504b 0102 1e03 0a00  3d45164}PK......
00000070: 0900 0000 8fa5 a256 b42d 9f15 2600 0000  .......V.-..&...
00000080: 2600 0000 0800 1800 0000 0000 0000 0000  &...............
00000090: ff81 0000 0000 666c 6167 2e74 7874 5554  ......flag.txtUT
000000a0: 0500 039d d851 6475 780b 0001 04e8 0300  .....Qdux.......
000000b0: 0004 e803 0000 504b 0506 0000 0000 0100  ......PK........
000000c0: 0100 4e00 0000 6800 0000 0000            ..N...h.....
```

flag is `ictf{hiding_in_plain_strings_c3d45164}`
