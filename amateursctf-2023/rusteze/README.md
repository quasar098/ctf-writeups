# rusteze

## problem

download [here](https://amateurs-prod.storage.googleapis.com/uploads/c5baf81b72d18eb39c9e7eb627f738ba4ea7c0f7a2cd3f11a0f913f8502e6cd9/rusteze)

## solution

taking a look at the main function, we input a line from stdin

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/ac338c3a-045c-49fc-88da-53623714b2e9)

also, the flag is 38 chars long

then, we declare some stuff

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/53b3bcc7-8fc9-4f15-b537-6ac85620eee6)

then, we copy the input from the stdin'd line to the buffer, while xoring by the previously defined variables and then rotating the bits

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/94c6a3fa-aa97-4e4e-8098-bcb730632da3)

then we check to see if the output is correct

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/045f52a6-ef52-4534-b258-6186578d20d6)

final check code

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/d91d2a69-770a-482e-af93-1474ea420a71)

solve script

```py
dat1 = """000091b4          char buf2start = 0x19
000091bc          char var_c5_1 = 0xeb
000091c4          char var_c4_1 = 0xd8
000091cc          char var_c3_1 = 0x56
000091d4          char var_c2_1 = 0x33
000091dc          char var_c1_1 = 0x00
000091e4          char var_c0_1 = 0x50
000091ec          char var_bf_1 = 0x35
000091f4          char var_be_1 = 0x61
000091fc          char var_bd_1 = 0xdc
00009204          char var_bc_1 = 0x96
0000920c          char var_bb_1 = 0x6f
00009214          char var_ba_1 = 0xb5
0000921c          char var_b9_1 = 0x0d
00009224          char var_b8_1 = 0xa4
0000922c          char var_b7_1 = 0x7a
00009234          char var_b6_1 = 0x55
0000923c          char var_b5_1 = 0xe8
00009244          char var_b4_1 = 0xfe
0000924c          char var_b3_1 = 0x56
00009254          char var_b2_1 = 0x97
0000925c          char var_b1_1 = 0xde
00009264          char var_b0_1 = 0x9d
0000926c          char var_af_1 = 0xaf
00009274          char var_ae_1 = 0xd4
0000927c          char var_ad_1 = 0x47
00009284          char var_ac_1 = 0xaf
0000928c          char var_ab_1 = 0xc1
00009294          char var_aa_1 = 0xc2
0000929c          char var_a9_1 = 0x6a
000092a4          char var_a8_1 = 0x5a
000092ac          char var_a7_1 = 0xac
000092b4          char var_a6_1 = 0xb1
000092bc          char var_a5_1 = 0xa2
000092c4          char var_a4_1 = 0x8a
000092cc          char var_a3_1 = 0x59
000092d4          char var_a2_1 = 0x52
000092dc          char var_a1_1 = 0xe2"""
# we xor the flag by dat2 first
dat2 = """00009011          char buf1start = 0x27
00009019          char var_11b_1 = 0x97
00009021          char var_11a_1 = 0x57
00009029          char var_119_1 = 0xe1
00009031          char var_118_1 = 0xa9
00009039          char var_117_1 = 0x75
00009041          char var_116_1 = 0x66
00009049          char var_115_1 = 0x3e
00009051          char var_114_1 = 0x1b
00009059          char var_113_1 = 0x63
00009061          char var_112_1 = 0xe3
00009069          char var_111_1 = 0xa0
00009071          char var_110_1 = 0x05
00009079          char var_10f_1 = 0x73
00009081          char var_10e_1 = 0x59
00009089          char var_10d_1 = 0xfb
00009091          char var_10c_1 = 0xa
00009099          char var_10b_1 = 0x43
000090a1          char var_10a_1 = 0x8f
000090a9          char var_109_1 = 0xe0
000090b1          char var_108_1 = 0xba
000090b9          char var_107_1 = 0xc0
000090c1          char var_106_1 = 0x54
000090c9          char var_105_1 = 0x99
000090d1          char var_104_1 = 0x06
000090d9          char var_103_1 = 0xbf
000090e1          char var_102_1 = 0x9f
000090e9          char var_101_1 = 0x2f
000090f1          char var_100_1 = 0xc4
000090f9          char var_ff_1 = 0xaa
00009101          char var_fe_1 = 0xa6
00009109          char var_fd_1 = 0x74
00009111          char var_fc_1 = 0x1e
00009119          char var_fb_1 = 0xdd
00009121          char var_fa_1 = 0x97
00009129          char var_f9_1 = 0x22
00009131          char var_f8_1 = 0xed
00009139          char var_f7_1 = 0xc5"""

dat1 = dat1.splitlines(False)
dat2 = dat2.splitlines(False)
for _ in range(38):
    q = int(dat1[_][-4:], 16)
    q = q >> 2 | ((q & 3) << 6)
    print(chr(int(dat2[_][-4:], 16) ^ q))
```
