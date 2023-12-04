# flotsam

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/34cafb11-b5a1-45af-9993-5bddf26341af)

sample image of webpage

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/6cd45e76-6139-4e4b-8dce-7ca9f68bb1de)

## solution

so the goal is to move the ship to the right spot and in theory we will get the flag is my thinking

but, where is the right spot (the `x`?)

i didn't know but i ran the boat into a mine and it said "you died" and reloaded the page

well, we can just look at the javascript of the webpage to do some analysis

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/fbbb16f8-ba92-4c26-9ca9-4eb4e91a02d8)

here you can see that when wasd is pressed the ship moves, and when x is pressed then the xy position of the boat is checked.

easily, we can just brute force the checks by declaring `u.check_x` in the global scope.

to put `u.check_x` in the global scope, we can make an override in the `Sources` tab of browser developer tools and add the line to put stuff as attribute of the window.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/636b4fe6-66c9-4166-9c2f-1173f3364abd)

then we can easily run a brute force operation on window.u_check_x to get the flag (we have to move the ship first to trigger our code though)

```js
for (let x=0; x<100; x++) {
  for (let y=0; y<100; y++) {
    if (window.u_check_x(x, y)) { console.log(x, y) }
  }
}
```

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/a7a4a063-0a19-4701-b5f0-7f5cada3d6c7)

yay flag

didn't even have to reverse engineer the wasm
