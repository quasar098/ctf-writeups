# simonprogrammer 1

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/463e5eb6-9297-4a43-8b98-6606e266de00)

cant access the website anymore because it is down idk why

## solution

everything is stored client side, so click "PLAY" and then run the code in console:

```js
current_frequencies_used_global = global_frequencies.map(_ => _.replace(/.*\/([-A-Za-z0-9_=]+).wav/, "$1"));
submitFrequencies(current_frequencies_used_global);
```
