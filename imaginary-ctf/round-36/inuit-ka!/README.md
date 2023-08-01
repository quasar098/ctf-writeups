# inuit-ka!

not sure what the title of this chall means but whatever

## problem

Decompile this compiled python, if you can! Note that the contents of the dist folder, other than checker.exe, are not to be reversed or looked into. Run checker.exe like one would an ordinary executable on the command line.

[checker.dist.zip](https://github.com/quasar098/ctf-writeups/files/12233170/checker.dist.zip)

## solution

taking a look at the checker.exe in binja, it is a nightmare

we can observe the behavior of the binary to solve this challenge

```
Enter flag: ic
Wrong length.
```

there is a check for length in this program, and so we can grep for that string.

```
quasar@quasar098:~/Downloads/checker.dist/checker.dist$ grep "Wrong length" checker.exe --text | xxd
00000000: 0132 f01e 2001 5301 f000 2001 5301 f000  .2.. .S... .S...
00000010: 2001 5301 f000 2001 5301 f000 2001 5301   .S... .S... .S.
00000020: 720d 0000 002e 00f8 0200 0059 0054 0000  r..........Y.T..
00000030: 0000 4400 0000 006c 0000 0000 6c01 0000  ..D....l....l...
00000040: 006c ffff ffff 5a00 5a01 6600 0000 0000  .l....Z.Z.f.....
00000050: 00f0 3f66 0000 0000 0000 f0bf 7500 6300  ..?f........u.c.
00000060: 615f 5f6d 6f64 756c 655f 5f00 615f 5f63  a__module__.a__c
00000070: 6c61 7373 5f5f 0061 5f5f 6e61 6d65 5f5f  lass__.a__name__
00000080: 0061 5f5f 7061 636b 6167 655f 5f00 615f  .a__package__.a_
00000090: 5f6d 6574 6163 6c61 7373 5f5f 0061 5f5f  _metaclass__.a__
000000a0: 6162 7374 7261 6374 6d65 7468 6f64 735f  abstractmethods_
000000b0: 5f00 615f 5f64 6963 745f 5f00 615f 5f64  _.a__dict__.a__d
000000c0: 6f63 5f5f 0061 5f5f 6669 6c65 5f5f 0061  oc__.a__file__.a
000000d0: 5f5f 7061 7468 5f5f 0061 5f5f 656e 7465  __path__.a__ente
000000e0: 725f 5f00 615f 5f65 7869 745f 5f00 615f  r__.a__exit__.a_
000000f0: 5f62 7569 6c74 696e 735f 5f00 615f 5f61  _builtins__.a__a
00000100: 6c6c 5f5f 0061 5f5f 696e 6974 5f5f 0061  ll__.a__init__.a
00000110: 5f5f 636d 705f 5f00 615f 5f69 7465 725f  __cmp__.a__iter_
00000120: 5f00 615f 5f6c 6f61 6465 725f 5f00 615f  _.a__loader__.a_
00000130: 5f63 6f6d 7069 6c65 645f 5f00 615f 5f6e  _compiled__.a__n
00000140: 7569 746b 615f 5f00 6169 6e73 7065 6374  uitka__.ainspect
00000150: 0061 636f 6d70 696c 6500 6172 616e 6765  .acompile.arange
00000160: 0061 6f70 656e 0061 7375 7065 7200 6173  .aopen.asuper.as
00000170: 756d 0061 666f 726d 6174 0061 5f5f 696d  um.aformat.a__im
00000180: 706f 7274 5f5f 0061 6279 7465 6172 7261  port__.abytearra
00000190: 7900 6173 7461 7469 636d 6574 686f 6400  y.astaticmethod.
000001a0: 6163 6c61 7373 6d65 7468 6f64 0061 6b65  aclassmethod.ake
000001b0: 7973 0061 6e61 6d65 0061 676c 6f62 616c  ys.aname.aglobal
000001c0: 7300 616c 6f63 616c 7300 6166 726f 6d6c  s.alocals.afroml
000001d0: 6973 7400 616c 6576 656c 0061 7265 6164  ist.alevel.aread
000001e0: 0061 7262 0077 2f77 5c61 7061 7468 0061  .arb.w/w\apath.a
000001f0: 6261 7365 6e61 6d65 0061 6162 7370 6174  basename.aabspat
00000200: 6800 6169 7361 6273 0061 6578 6973 7473  h.aisabs.aexists
00000210: 0061 6973 6469 7200 6169 7366 696c 6500  .aisdir.aisfile.
00000220: 616c 6973 7464 6972 0061 6765 7461 7474  alistdir.agetatt
00000230: 7200 615f 5f63 6163 6865 645f 5f00 6170  r.a__cached__.ap
00000240: 7269 6e74 0061 656e 6400 6166 696c 6500  rint.aend.afile.
00000250: 6162 7974 6573 0077 2e61 7365 6e64 0061  abytes.w.asend.a
00000260: 7468 726f 7700 6163 6c6f 7365 0061 7369  throw.aclose.asi
00000270: 7465 0061 7479 7065 0061 6c65 6e00 6172  te.atype.alen.ar
00000280: 6570 7200 6169 6e74 0061 6974 6572 0061  epr.aint.aiter.a
00000290: 5f5f 7370 6563 5f5f 0061 5f69 6e69 7469  __spec__.a_initi
000002a0: 616c 697a 696e 6700 6170 6172 656e 7400  alizing.aparent.
000002b0: 6174 7970 6573 0061 5f5f 6d61 696e 5f5f  atypes.a__main__
000002c0: 0061 6173 5f66 696c 6500 6172 6567 6973  .aas_file.aregis
000002d0: 7465 7200 615f 5f63 6c61 7373 5f67 6574  ter.a__class_get
000002e0: 6974 656d 5f5f 0061 5f5f 6d61 7463 685f  item__.a__match_
000002f0: 6172 6773 5f5f 0061 5f5f 6165 6e74 6572  args__.a__aenter
00000300: 5f5f 0061 5f5f 6165 7869 745f 5f00 6166  __.a__aexit__.af
00000310: 696c 656e 6f00 7570 7974 686f 6e2e 6578  ileno.upython.ex
00000320: 6500 2e5f 5f6d 6169 6e5f 5f00 3e01 0000  e..__main__.>...
00000330: 1d00 7762 6169 6e70 7574 0054 0100 0000  ..wbainput.T....
00000340: 7545 6e74 6572 2066 6c61 673a 2000 6165  uEnter flag: .ae
00000350: 6e63 6f64 6500 6173 786f 7200 5402 0000  ncode.asxor.T...
00000360: 0063 2c26 3123 3e21 7421 1a30 2b26 2a28  .c,&1#>!t!.0+&*(
00000370: 353c 2920 1a32 372e 7a38 006c 4500 0000  5<) .27.z8.lE...
00000380: 6170 7269 6e74 0054 0100 0000 7557 726f  aprint.T....uWro
00000390: 6e67 206c 656e 6774 682e 006c 0100 0000  ng length..l....
000003a0: 7773 6173 7461 7274 7377 6974 6800 5401  wsastartswith.T.
000003b0: 0000 0075 5772 6f6e 672e 0061 736c 6565  ...uWrong..aslee
000003c0: 7000 5401 0000 0066 9a99 9999 9999 c93f  p.T....f.......?
000003d0: 5401 0000 0075 436f 7272 6563 7421 0061  T....uCorrect!.a
000003e0: 5f5f 646f 635f 5f00 615f 5f66 696c 655f  __doc__.a__file_
000003f0: 5f00 615f 5f63 6163 6865 645f 5f00 615f  _.a__cached__.a_
00000400: 5f61 6e6e 6f74 6174 696f 6e73 5f5f 0061  _annotations__.a
00000410: 7469 6d65 0054 0100 0000 6173 6c65 6570  time.T....asleep
00000420: 006c 0000 0000 616d 6169 6e00 7563 6865  .l....amain.uche
00000430: 636b 6572 2e70 7900 753c 6c69 7374 636f  cker.py.u<listco
00000440: 6d70 3e00 5402 0000 0077 6977 6275 3c6d  mp>.T....wiwbu<m
00000450: 6f64 756c 653e 0054 0300 0000 7773 6173  odule>.T....wsas
00000460: 7300 7769 5402 0000 0077 6177 622e 5041  s.wiT....wawb.PA
00000470: 443c 6e73 303a 6173 7365 6d62 6c79 2078  D<ns0:assembly x
00000480: 6d6c 6e73 3a6e 7330 3d22 7572 6e3a 7363  mlns:ns0="urn:sc
00000490: 6865 6d61 732d 6d69 6372 6f73 6f66 742d  hemas-microsoft-
000004a0: 636f 6d3a 6173 6d2e 7631 2220 786d 6c6e  com:asm.v1" xmln
000004b0: 733a 6e73 313d 2275 726e 3a73 6368 656d  s:ns1="urn:schem
000004c0: 6173 2d6d 6963 726f 736f 6674 2d63 6f6d  as-microsoft-com
000004d0: 3a63 6f6d 7061 7469 6269 6c69 7479 2e76  :compatibility.v
000004e0: 3122 2078 6d6c 6e73 3a6e 7332 3d22 7572  1" xmlns:ns2="ur
000004f0: 6e3a 7363 6865 6d61 732d 6d69 6372 6f73  n:schemas-micros
00000500: 6f66 742d 636f 6d3a 6173 6d2e 7633 2220  oft-com:asm.v3"
00000510: 6d61 6e69 6665 7374 5665 7273 696f 6e3d  manifestVersion=
00000520: 2231 2e30 223e 3c6e 7330 3a61 7373 656d  "1.0"><ns0:assem
00000530: 626c 7949 6465 6e74 6974 7920 7479 7065  blyIdentity type
00000540: 3d22 7769 6e33 3222 206e 616d 653d 224d  ="win32" name="M
00000550: 696e 6922 2076 6572 7369 6f6e 3d22 312e  ini" version="1.
00000560: 302e 302e 3022 202f 3e3c 6e73 313a 636f  0.0.0" /><ns1:co
00000570: 6d70 6174 6962 696c 6974 793e 3c6e 7331  mpatibility><ns1
00000580: 3a61 7070 6c69 6361 7469 6f6e 3e3c 6e73  :application><ns
00000590: 313a 7375 7070 6f72 7465 644f 5320 4964  1:supportedOS Id
000005a0: 3d22 7b65 3230 3131 3435 372d 3135 3436  ="{e2011457-1546
000005b0: 2d34 3363 352d 6135 6665 2d30 3038 6465  -43c5-a5fe-008de
000005c0: 6565 3364 3366 307d 2220 2f3e 3c6e 7331  ee3d3f0}" /><ns1
000005d0: 3a73 7570 706f 7274 6564 4f53 2049 643d  :supportedOS Id=
000005e0: 227b 3335 3133 3862 3961 2d35 6439 362d  "{35138b9a-5d96-
000005f0: 3466 6264 2d38 6532 642d 6132 3434 3032  4fbd-8e2d-a24402
00000600: 3235 6639 3361 7d22 202f 3e3c 6e73 313a  25f93a}" /><ns1:
00000610: 7375 7070 6f72 7465 644f 5320 4964 3d22  supportedOS Id="
00000620: 7b34 6132 6632 3865 332d 3533 6239 2d34  {4a2f28e3-53b9-4
00000630: 3434 312d 6261 3963 2d64 3639 6434 6134  441-ba9c-d69d4a4
00000640: 6136 6533 387d 2220 2f3e 3c6e 7331 3a73  a6e38}" /><ns1:s
00000650: 7570 706f 7274 6564 4f53 2049 643d 227b  upportedOS Id="{
00000660: 3166 3637 3663 3736 2d38 3065 312d 3432  1f676c76-80e1-42
00000670: 3339 2d39 3562 622d 3833 6430 6636 6430  39-95bb-83d0f6d0
00000680: 6461 3738 7d22 202f 3e3c 6e73 313a 7375  da78}" /><ns1:su
00000690: 7070 6f72 7465 644f 5320 4964 3d22 7b38  pportedOS Id="{8
000006a0: 6530 6637 6131 322d 6266 6233 2d34 6665  e0f7a12-bfb3-4fe
000006b0: 382d 6239 6135 2d34 3866 6435 3061 3135  8-b9a5-48fd50a15
000006c0: 6139 617d 2220 2f3e 3c2f 6e73 313a 6170  a9a}" /></ns1:ap
000006d0: 706c 6963 6174 696f 6e3e 3c2f 6e73 313a  plication></ns1:
000006e0: 636f 6d70 6174 6962 696c 6974 793e 3c6e  compatibility><n
000006f0: 7332 3a74 7275 7374 496e 666f 3e3c 6e73  s2:trustInfo><ns
00000700: 323a 7365 6375 7269 7479 3e3c 6e73 323a  2:security><ns2:
00000710: 7265 7175 6573 7465 6450 7269 7669 6c65  requestedPrivile
00000720: 6765 733e 3c6e 7332 3a72 6571 7565 7374  ges><ns2:request
00000730: 6564 4578 6563 7574 696f 6e4c 6576 656c  edExecutionLevel
00000740: 206c 6576 656c 3d22 6173 496e 766f 6b65   level="asInvoke
00000750: 7222 2075 6941 6363 6573 733d 2266 616c  r" uiAccess="fal
00000760: 7365 2220 2f3e 3c2f 6e73 323a 7265 7175  se" /></ns2:requ
00000770: 6573 7465 6450 7269 7669 6c65 6765 733e  estedPrivileges>
00000780: 3c2f 6e73 323a 7365 6375 7269 7479 3e3c  </ns2:security><
00000790: 2f6e 7332 3a74 7275 7374 496e 666f 3e3c  /ns2:trustInfo><
000007a0: 2f6e 7330 3a61 7373 656d 626c 793e 5041  /ns0:assembly>PA
000007b0: 4450 4144 4449 4e47 5858 5041 4444 494e  DPADDINGXXPADDIN
000007c0: 4750 4144 4449 4e47 5858 5041 4444 494e  GPADDINGXXPADDIN
```

here, we can take a look at the important section

```
00000330: 1d00 7762 6169 6e70 7574 0054 0100 0000  ..wbainput.T....
00000340: 7545 6e74 6572 2066 6c61 673a 2000 6165  uEnter flag: .ae
00000350: 6e63 6f64 6500 6173 786f 7200 5402 0000  ncode.asxor.T...
00000360: 0063 2c26 3123 3e21 7421 1a30 2b26 2a28  .c,&1#>!t!.0+&*(
00000370: 353c 2920 1a32 372e 7a38 006c 4500 0000  5<) .27.z8.lE...
00000380: 6170 7269 6e74 0054 0100 0000 7557 726f  aprint.T....uWro
00000390: 6e67 206c 656e 6774 682e 006c 0100 0000  ng length..l....
000003a0: 7773 6173 7461 7274 7377 6974 6800 5401  wsastartswith.T.
000003b0: 0000 0075 5772 6f6e 672e 0061 736c 6565  ...uWrong..aslee
000003c0: 7000 5401 0000 0066 9a99 9999 9999 c93f  p.T....f.......?
000003d0: 5401 0000 0075 436f 7272 6563 7421 0061  T....uCorrect!.a
000003e0: 5f5f 646f 635f 5f00 615f 5f66 696c 655f  __doc__.a__file_
000003f0: 5f00 615f 5f63 6163 6865 645f 5f00 615f  _.a__cached__.a_
00000400: 5f61 6e6e 6f74 6174 696f 6e73 5f5f 0061  _annotations__.a
00000410: 7469 6d65 0054 0100 0000 6173 6c65 6570  time.T....asleep
00000420: 006c 0000 0000 616d 6169 6e00 7563 6865  .l....amain.uche
00000430: 636b 6572 2e70 7900 753c 6c69 7374 636f  cker.py.u<listco
00000440: 6d70 3e00 5402 0000 0077 6977 6275 3c6d  mp>.T....wiwbu<m
00000450: 6f64 756c 653e 0054 0300 0000 7773 6173  odule>.T....wsas
00000460: 7300 7769 5402 0000 0077 6177 622e 5041  s.wiT....wawb.PA
```

notably, you can see that there is a `sleep` string next to some other functions and strings used in the program. hmm

also, we can easily get the correct length of the flag by trying a bunch of different lengths

```
quasar@quasar098:~/Downloads/checker.dist/checker.dist$ ./checker.exe
Enter flag: ictf{................}
Wrong length.
quasar@quasar098:~/Downloads/checker.dist/checker.dist$ ./checker.exe
Enter flag: ictf{.................}
Wrong length.
quasar@quasar098:~/Downloads/checker.dist/checker.dist$ ./checker.exe
Enter flag: ictf{..................}
Wrong.
quasar@quasar098:~/Downloads/checker.dist/checker.dist$
```

here, i tried different lengths manually, but i'm sure you could automate this

anyways, we know the flag is 24 characters long, but i notice some other behavior...

```
quasar@quasar098:~/Downloads/checker.dist/checker.dist$ echo "ictf{..................}" | time ./checker.exe
Enter flag: Wrong.
0.00user 0.00system 0:01.12elapsed 0%CPU (0avgtext+0avgdata 1560maxresident)k
0inputs+0outputs (0major+68minor)pagefaults 0swaps
quasar@quasar098:~/Downloads/checker.dist/checker.dist$ echo "jjjjjjjj...............j" | time ./checker.exe
Enter flag: Wrong.
0.00user 0.00system 0:00.12elapsed 0%CPU (0avgtext+0avgdata 1560maxresident)k
0inputs+0outputs (0major+68minor)pagefaults 0swaps
quasar@quasar098:~/Downloads/checker.dist/checker.dist$
```

notably, there is a side-channel vulnerability where we can execute a timing attack to retrieve the flag.

i dont know where my solve script went, but i was able to use multithreading to have all of the `checker.exe` processes running in parallel, so the script takes only about 2 minutes

anyways the flag is `ictf{d1d_uncompyle_wrk?}`

uncompyle did not indeed work
