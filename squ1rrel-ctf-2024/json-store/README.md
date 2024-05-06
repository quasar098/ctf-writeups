# json store

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/05620503-4f86-4615-bae5-f54a9c8d8397)

see [./json-store.zip](./json-store.zip)

## solution

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/29572fc3-d8d1-4921-8a91-8dcd8198547b)

we are presented with this web app

```js
const express = require("express");
const path = require("path");
const TAFFY = require("taffydb").taffy;

... etc
```

so this uses taffy in js.

package.json below

```json
{
  "name": "kv-store",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "AGPL-3.0",
  "description": "",
  "dependencies": {
    "body-parser": "^1.20.2",
    "express": "^4.19.2",
    "taffydb": "^2.7.3"
  }
}

```

notably, we can see from the package.json that this is a taffy version that has vulnerabilities. see [here](https://security.snyk.io/vuln/SNYK-JS-TAFFY-546521)

we can literally just put in the proof of concept, change the name to admin, and win

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/03ed56cd-5eb5-4099-b9c9-c9e2c09f6f97)

