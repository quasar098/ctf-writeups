# c-sar

## problem

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    unsigned char flag[128];
    FILE* file = fopen("./flag.txt", "r");
    fscanf(file, "%s", flag);
    unsigned char *ptr = flag;
    while(*ptr) {
        printf("%u, ", rand()^(*(ptr++)));
    }

    // output:
    // 1804289294, 846930853, 1681692701, 1714636821, 1957747754, 424238262, 719885333, 1649760399, 596516680, 1189641379, 1025202333, 1350490079, 783368621, 1102519961, 2044897670, 1967513898, 1365180437, 1540383399, 304089122, 1303455645, 35005252, 521595297, 294702520, 1726956537, 336465694, 861021493, 278722907, 233665028, 2145174107, 468703211, 1101513878, 1801979893, 1315633920, 635723117, 1369133182, 1125898194, 1059961428, 2089018428, 628175056, 1656478011, 1131176279, 1653377282, 859484519, 1914545010, 608413758, 756898438, 1734575148, 1973594289, 149798388, 2038664390, 1129566373, 184803503, 412776168, 1424269001,
}
```

## solution

the file reads the flag and xors each letter by the output of the rand function

we can exploit this because the rand function is deterministic.<br>
therefore, we now have the first rand call in the sequence because we know the flag format is `ictf{.+}`<br>
the first integer is 1804289383<br>
if we search this up on google, we can get [this](https://stackoverflow.com/questions/40686961/how-can-i-find-the-exact-rand-used-in-a-c-library) stackoverflow post<br>
we now can run the c program in the question and modify it like so:
```cpp
#include <stdlib.h>     /* rand */
#include <iostream>
using namespace std;

int main ()
{
    int arr[54] = {1804289294, 846930853, 1681692701, 1714636821, 1957747754, 424238262, 719885333, 1649760399, 596516680, 1189641379, 1025202333, 1350490079, 783368621, 1102519961, 2044897670, 1967513898, 1365180437, 1540383399, 304089122, 1303455645, 35005252, 521595297, 294702520, 1726956537, 336465694, 861021493, 278722907, 233665028, 2145174107, 468703211, 1101513878, 1801979893, 1315633920, 635723117, 1369133182, 1125898194, 1059961428, 2089018428, 628175056, 1656478011, 1131176279, 1653377282, 859484519, 1914545010, 608413758, 756898438, 1734575148, 1973594289, 149798388, 2038664390, 1129566373, 184803503, 412776168, 1424269001};

    for (int i = 0 ; i< 54 ; i++) {
        cout << (rand()^arr[i]) << " ";
    }

    return 0;
}
```
We compile this with g++

now we have `105 99 116 102 123 73 95 99 97 110 39 116 95 98 101 108 105 101 118 101 95 73 95 116 104 111 117 103 104 116 95 111 102 95 115 101 101 100 115 97 114 95 98 101 102 111 114 101 95 116 104 105 115 125`

We decode the decimal using (https://cryptii.com/pipes/decimal-text)[https://cryptii.com/pipes/decimal-text] to get `ictf{I_can't_believe_I_thought_of_seedsar_before_this}`
