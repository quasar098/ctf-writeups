# mac and cheese

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/b076f497-8fc2-420b-bd27-4f46ff102ad6)

(also see macncheese binary file)

## solution

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/8227dff2-112a-4bb4-a778-0ee1dd4ac04f)

This is a Mac-OS executable, but we can use Binary Ninja to get the equivalent C code

```c
#include <stdio.h>
#include <stdint.h>
typedef unsigned int uint;
uint magic_func(void);

uint DAT_100008018 = 0x20b5d4ff;
uint DAT_10000801C = 0x32378fc7;
uint DAT_100008020 = -0x2aa07899;
uint DAT_100008024 = 0x104aa1ad;

int main(void) {
  uint magic;
  uint user_input;
  int count;
  uint flag;
  
  flag = 0;
  count = 0;
  
  while(1) {
    if (count > 4) {
      printf("Wow, you cheesed it. Flag: cvctf{%u}\n", flag);
      return 0;
    }
    
    printf("I want cheese: ");
    scanf("%u", &user_input);
    
    magic = magic_func();
    printf("%u", magic);
    if (user_input != magic) {
      break;
    }
    
    flag = user_input % 1337 + flag;
    count = count + 1;
  }
  
  printf("Sorry no cheese!\n");
  return 0;
}

uint magic_func(void) {

  uint uVar1;
  uVar1 = DAT_100008018 ^ DAT_100008018 << 11;
  DAT_100008018 = DAT_10000801C;
  DAT_10000801C = DAT_100008020;
  DAT_100008020 = DAT_100008024;
  DAT_100008024 = DAT_100008024 ^ DAT_100008024 >> 0x13 ^ uVar1 ^ uVar1 >> 8;
  return DAT_100008024;
}
```

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/bad1ca78-9711-435f-afd5-e7002764e081)

What I did here was print the next magic number and try the C program over and over again until I win

the flag was `cvctf{4666}`
