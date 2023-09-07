# cyberchef

another interesting chall i made for ictf

## problem

Mirror, Mirror on the wall, who's the most miscellaneous of them all? all letters in flag are lowercase btw

```
https://gchq.github.io/CyberChef/#recipe=Register('(%5B%5C%5Cs%5C%5CS%5D*)',true,false,false)Jump('start',100)Label('yep')Find_/_Replace(%7B'option':'Regex','string':'.%2B'%7D,'yep,%20the%20flag%20is%20$R0',true,false,true,false)Return()Label('nope')Find_/_Replace(%7B'option':'Regex','string':'.%2B'%7D,'',true,false,true,false)Find_/_Replace(%7B'option':'Regex','string':'.?'%7D,'nope!',true,false,true,false)Return()Label('start')Conditional_Jump('%5E%5C%5Cx69%5C%5C143%5C%5Cx74%5C%5C146%5C%5C173(?:%5B%5E%5C%5C172%5D)%2B%7D$',true,'nope',10)To_Binary('Space',8)Sum('Space')Conditional_Jump('38.2.556(two%20hidden%20characters%20add%20to%20nine%20btw)?',true,'nope',10)Find_/_Replace(%7B'option':'Regex','string':'.%2B'%7D,'',true,false,true,false)Find_/_Replace(%7B'option':'Regex','string':'.*'%7D,'$R0',true,false,true,false)Count_occurrences(%7B'option':'Regex','string':'.'%7D)ADD(%7B'option':'UTF8','string':'3%3D'%7D)To_Morse_Code('-/.','Space','Line%20feed')Conditional_Jump('--.%20---',true,'nope',10)Find_/_Replace(%7B'option':'Regex','string':'.%2B'%7D,'$R0',true,false,true,false)Take_bytes(5,9,false)Register('(%5B%5C%5Cs%5C%5CS%5D*)',true,false,false)HTTP_request('GET','https://gchq.github.io/CyberChef/','','Cross-Origin%20Resource%20Sharing',false)Take_bytes(9,9,false)Register('(%5B%5C%5Cs%5C%5CS%5D*)',true,false,false)To_Lower_case()Conditional_Jump('$R1',true,'nope',10)Find_/_Replace(%7B'option':'Regex','string':'.%2B'%7D,'$R0',true,false,true,false)Take_bytes(14,3,false)MD5()Conditional_Jump('e5e02580f0673815f7ce515fabe658e0',true,'nope',10)Find_/_Replace(%7B'option':'Regex','string':'.%2B'%7D,'$R0',true,false,true,false)Drop_bytes(0,17,false)Drop_bytes(3,1337,false)To_Octal('Space')To_Base32('A-Z2-7%3D')To_Base45('0-9A-Z%20$%25*%2B%5C%5C-./:')To_Base58('123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz')To_Base62('0-9A-Za-z')To_Base64('A-Za-z0-9%2B/%3D')To_Base85('0-9A-Za-z!%23$%25%26()*%2B%5C%5C-;%3C%3D%3E?@%5E_%60%7B%7C%7D~',false)Conditional_Jump('V%5C%5C?%7BAFR!BlZRxo%26SWmQEpS5;1%5C%5Cx49%5C%5Cx52%5C%5Cx63%5C%5Cx6c%5C%5Cx67%5C%5Cx46%5C%5Cx50%5C%5Cx48%5C%5Cx74%5C%5Cx39%5C%5Cx34%5C%5Cx52%5C%5Cx61%5C%5Cx4a%5C%5Cx4f%5C%5Cx74%5C%5Cx51%5C%5Cx24%5C%5Cx3d%5C%5Cx56%5C%5Cx3e%5C%5Cx57HW3%60Wk%5C%5C*UxPHRpuQ%5C%5C$%5C%5C%7Cu%25Vlh@%25QB_er',true,'nope',10)Find_/_Replace(%7B'option':'Regex','string':'.%2B'%7D,'$R0',true,false,true,false)Convert_to_NATO_alphabet()Find_/_Replace(%7B'option':'Regex','string':'%5E%5B%5E?%5D%2B%5C%5C?_India%5C%5Cs(?:%5C%5Cw%2B%5C%5Cs)%7B3%7D'%7D,'_',true,false,true,false)Conditional_Jump('%5E__One%20Hotel%20Four%20Tango%20_',true,'nope',10)Find_/_Replace(%7B'option':'Regex','string':'.%2B'%7D,'$R0',true,false,true,false)Atbash_Cipher()Label('the%20second%20to%20last%20char%20of%20the%20flag%20is%20%22?%22')To_Decimal('Space',false)Jump('the%20second%20to%20last%20char%20of%20the%20flag%20is%20%22?%22',2)Multiply('Space')Conditional_Jump('71642507476992000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000$',true,'nope',10)Count_occurrences(%7B'option':'Regex','string':'%5B92604%5D%7B2%7D'%7D)Conditional_Jump('163',true,'nope',10)Find_/_Replace(%7B'option':'Regex','string':'.%2B'%7D,'%5B%7B%22flag%22:%20%22$R0%22%7D%5D',true,false,true,false)JPath_expression('$%5B?(@.flag%5B26%5D%3D%3D%22a%22%26@.flag%5B27%5D%3D%3D%22_%22)%5D','%5C%5Cn',false)Conditional_Jump('flag',true,'nope',10)Drop_bytes(0,37,false)Drop_bytes(5,6,false)Drop_bytes(6,5,false)To_Decimal('Space',false)Register('(%5B%5C%5Cs%5C%5CS%5D*)',true,false,false)Power_Set('%20')Fork('%5C%5Cn','%5C%5Cn',false)Sum('Space')Merge(true)Find_/_Replace(%7B'option':'Regex','string':'(%5C%5CnNaN%7CNaN%5C%5Cn)'%7D,'',true,false,true,false)Sum('Line%20feed')Conditional_Jump('%5E18368$',true,'nope',10)Find_/_Replace(%7B'option':'Regex','string':'.%2B'%7D,'$R3',true,false,true,false)Conditional_Jump('%5E102%20108%2052%20103%2095%20%5C%5Cd%2B$',true,'nope',10)Find_/_Replace(%7B'option':'Regex','string':'.%2B'%7D,'$R0',true,false,true,false)Take_bytes(33,6,false)Generate_Image('RGB',8,2)Extract_RGBA('%20',true)Conditional_Jump('%5E%5C%5C71%5C%5Cx39%5C%5Cx2010%5C%5C64%5C%5C%20101(?:(?:%5C%5Cs%5C%5Cd%2B))%7B29%7D%5C%5Cs99%5C%5Cs10%5C%5C67%5C%5Cs%5C%5Cx351%5C%5Cs2%5C%5Cx35%5C%5Cx35.*$',true,'nope',10)Find_/_Replace(%7B'option':'Regex','string':'.%2B'%7D,'$R0',true,false,true,false)SHA3('384')Conditional_Jump('9a86e4757b07123b5edfcdc9e7a3abb0ff34b5a48df694f15f4361d16b10e8768c661e4d7b9171fbc46dad1baa578491',false,'yep',10)Jump('nope',10)
```

## solution

the writeup on the ictf website explains it pretty well

```
Each part of the cyberchef program checks a certain part of the flag, and then either continues or halts and says "nope!". You can pause execution at each step to see the output. You can also disable steps that are impossible to reverse. The parts, in order, are (all byte numbers are 0 indexed):

Storing the flag in a register $R0 and jumping to the start of the program
The bit that prints if you win
The bit that prints if you lose
A check that the flag is in flag format
A check that the sums of the binary of the characters matches a certain value (seems difficult/impossible to cleanly reverse, can be disabled)
A check for the length of the flag (42) by counting the characters with regex
A check that bytes 5 - 13 of the flag are cyberchef
A check that bytes 14 - 16 have the correct md5 hash
A check that bytes 17 - 19 have the correct value after being encoded in several bases
A check that bytes 20 - 26 have the correct value of NATO alphabet
A check that takes the atbash cipher of the flag, converts to decimal twice, multiplies the values together, and compares it against a value (seems difficult/impossible to cleanly reverse, can be disabled)
A check that bytes 26 and 27 are a_ with a JPath expression
A check that bytes 28 - 32 and 39 and satisfy a bunch of math
A check that bytes 28 - 32 are fl4g_ (lets you brute force char 39 with the prev step)
A check that bytes 33 - 38 are certain values by converting them to pixels and checking the output with regex
A check for the sha3 of the flag
Reversing each step will yield the flag, and brute forcing against the sha3 hash will help check against any characters that might be missing (or if you didn't want to rev one of the checks).
```
