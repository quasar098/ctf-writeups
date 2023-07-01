# base64

## problem

Decode this base64 to get the flag. Easy, right?

```
UEsDBBQAAAAIAMSkwVazwQokLAAAACQAAAAAAAAABUABCoAgDHyTrP8cuaYOFMSNosK/D2Uv/5emYdEtZ5cLj3rDOKoy8utkOwBQSwECFAAUAAAACADEpMFWs8EKJCwAAAAkAAAAAAAAAAAAAAAAAAAAAAAAAAAAUEsFBgAAAAABAAEALgAAAEoAAAAAAA==
```

## solution

if we decode the base64, it looks like we get garbage, but it's actually a zip file

we know this because the start of the header of the file are PK, which is standard for zip files

if we did not know that it was a zip file, we could search up common headers for files (there is a page on wikipedia)

looking at the zip file, we don't see any file names, which are usually present in the central directory section of a zip file

unfortunately, this means that the file has an empty name, so most zip decoders wont recognize and be able to unzip the file

however, we can use cyberchef to extract the zipdata anyways. see [here](https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true,false)Unzip('',false)To_Hexdump(16,false,false,false)From_Hexdump()&input=VUVzREJCUUFBQUFJQU1Ta3dWYXp3UW9rTEFBQUFDUUFBQUFBQUFBQUJVQUJDb0FnREh5VHJQOGN1YVlPRk1TTm9zSy9EMlV2LzVlbVlkRXRaNWNMajNyRE9Lb3k4dXRrT3dCUVN3RUNGQUFVQUFBQUNBREVwTUZXczhFS0pDd0FBQUFrQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQVVFc0ZCZ0FBQUFBQkFBRUFMZ0FBQUVvQUFBQUFBQT09)

the flag is `ictf{z1ps_r3vealed_with_m4gic_byt3s}`

