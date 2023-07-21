# waiting-an-eternity

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/5267c069-5d7c-49a5-a919-f209587598a3)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/501c27b7-6d45-4b5a-ba3e-cbf0c4b1ec92)

??? i had a stroke reading that

on the site:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/5f0f6229-9908-40df-9ccf-70af5a24c002)

## solution

taking a look at the response to the request for the website:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/a7ff5e75-7c13-4875-97d5-a567d76e1f45)

so, we go to `/secret-site?secretcode=5770011ff65738feaf0c1d009caffb035651bb8a7e16799a433a301c0756003a`

we see this:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/56fd3d81-c9c5-4ed2-99c1-06c53f5c34d8)

taking a look at the cookies now, we see that a new cookie for time is set

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/3e3fefdc-2517-4e0d-a7fc-c56a594c2f9d)

we set the time value to two less than it currently is, and it says that we have been waiting for some seconds

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/1369b37a-16ed-4ebb-8757-757ea0a24872)

so, we set the time value really low to make it appear like we waited

we need to set it really really low, so we just set it to `-Infinity`

then, we get the flag 

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/bd4eaf4d-01ad-4d40-a99c-c6f611b72455)
