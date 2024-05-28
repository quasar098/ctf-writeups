# caasio-ce

## challenge

caasio.js
```js
#!/usr/local/bin/node

const readline = require("readline/promises");
const rl = readline.createInterface({input: process.stdin, output: process.stdout});

console.log("Welcome to CaaSio: Contrived Edition!");
console.log("What \"math\" expression would you like to evaluate?");
rl.question("> ")
    .then(inp => {
        if (inp.length > 200) {
            console.log("That's so much math; what do you think I am, a calculator???");
            throw new Error();
        }
        const banned = new Set(".,:;(){}<>`pxu");
        for (const char of inp) {
            const c = char.charCodeAt(0);
            if (c < 0x20 || c > 0x7e || banned.has(char)) {
                console.log("I have arbitrarily deemed your input to not be math.");
                throw new Error();
            }
        }
        return inp;
    })
    .then(inp => console.log(Function("return " + inp)()))
    .catch((e) => console.log("Bye!", e))
    .finally(() => rl.close());
```

Dockerfile
```dockerfile
FROM pwn.red/jail

COPY --from=node:22-bookworm-slim / /srv
COPY --chmod=755 caasio.js /srv/app/run
COPY flag.txt /srv/app/flag.txt
```

## solution

we cannot call functions, create objects, or create anonymous functions. or can we ? *(vsauce music starts)*

the first thing i noticed about this challenge is that we can get arbitrary strings, even one that have blocked chars, by using octals like `"console\056log\050var\051"` (this is equal to `"console.log(var)"`). so, if we can get eval, we can easily win.

first off, we can call some functions/classes like `Object` with `new Object` because they are constructors.

but, we cannot call eval or anything so easily. to call functions with one argument, we can overwrite the hasInstance attribute (see [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/hasInstance)) of an object and then use `instanceof` keyword with it

however, to do multiple statements, we can use a weird trick where setting attributes with `=` isnt considered a statement, but rather, an expression (i think).

so, `[a][[b][c]]` will evaluate expression `a`, then expression `b`, then expression `c`, and return `undefined`. this doesn't raise an error because invalid array indicies return undefined instead of throwing errors

we can create an object, modify the Symbol.hasInstance to be eval, and then call eval with our string, but then how do we write the hasInstance attributes without `.` ? because we cannot do `q=new Object` and `q.hasInstance = "test"`

well, object attributes can be set in two ways in js. you can either set it with `obj.attr = val` or with `obj["attr"] = val`

so, we can do `[ q=new Object ][[ q[Symbol.hasInstance] = eval ][ "string" instanceof q ]]`. now we can eval arbitrary strings.

now comes the hardest part of the challenge (imo), which is trying to escape to a shell or something.

if we try to use `require`, we will get that it is undefined. so, my first attempt was to use `process.create_binding` or something to get an internal module which we could use to escape. but that would be too many chars.

however, we can use `process.mainModule.require` and that exists for some reason even though process.mainModule appears as below:

```
Module {
  id: '.',
  path: '/mnt/c/Users/quasar/Downloads',
  exports: {},
  filename: '/mnt/c/Users/quasar/Downloads/test.js',
  loaded: false,
  children: [],
  paths: [
    '/mnt/c/Users/quasar/Downloads/node_modules',
    '/mnt/c/Users/quasar/node_modules',
    '/mnt/c/Users/node_modules',
    '/mnt/c/node_modules',
    '/mnt/node_modules',
    '/node_modules'
  ]
}
```

for some reason, require still exists there idk why

so you can easily just use `[q=new Object][[q[Symbol["hasInstance"]]=eval]["throw process.mainModule.require('fs').readFileSync('flag.txt')" instanceof q]]`, encoding the eval'd string with octals to bypass the restrictions
