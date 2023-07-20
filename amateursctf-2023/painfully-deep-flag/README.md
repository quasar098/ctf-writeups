# painfully deep flag

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/ef98c357-0547-47af-9757-84fc246a4cd0)

see flag.pdf

## solution

forensics is always a little guessy, and a lot of attempts are required to do it

i used `pdfimages` to extract the images from the pdf, and it turns out there was one extra

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/83856c1c-7ae4-4845-a7b7-7b7549153269)

```
quasar@quasar098:~/wasteland/painful$ pdfimages flag.pdf 1 -all
```
