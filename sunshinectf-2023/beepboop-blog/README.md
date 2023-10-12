# beepboop-blog

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/54da7391-18db-4cd8-af6e-3cd6d18f7ecf)

cant access the website unforunately

## solution

looking at the post data (`/post/<postid>` iirc), the json data has the "hidden" tag, so enumerating posts works

alternative solution is to get the request that gets the post ids to display (in json format), so at which point the index of the item in the list and the post id is out of sync, that is the hidden post.
