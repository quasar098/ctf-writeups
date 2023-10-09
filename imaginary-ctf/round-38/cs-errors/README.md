# c's errors

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/00bc64ed-bc0a-4824-b362-5d042ed355e8)

see [error](./error)

## solution

opening this up in binja

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/66bea0ac-d7f1-419b-b667-40a9c6bacb92)

hence the name c's errors

notice that each signal raised is going to be handled by check function

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/861ccdf6-9702-4010-b17a-500bd03b3230)

the signal has to be `0x0b`

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/9f351aeb-6b8e-4a59-90b6-f39a0a2a2471)

so looping over each character, the xor of the input and the encoded text has to equal `0x0b` for each character

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/e2e8aaf6-bb4a-4acd-92e1-a6703ccd1398)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/b7b83320-e58c-411a-b248-130625ffd7e1)

so we can just xor the string

[tfw solution](https://quasar.name/text-format-wizard/#eyJyZWNpcGUiOlt7Im1vZHVsZVR5cGUiOiJlN2Y2OTdhOC1hMGViLTU1YzktYTM1Ni01NzMzMDhhOTJmMTgiLCJhcmdzIjp7Im1ldGhvZCI6ImRlY29kZSJ9fSx7Im1vZHVsZVR5cGUiOiJhYTgwYjEyMy1mOTljLTViNGEtOGZiNi04ZmNjNWU0YTM3ZmMiLCJhcmdzIjp7ImtleSI6IjBiIn19XSwiaW5wdXQiOiI2MjY4N2Y2ZDcwNjE3ZTc4N2Y1NDY0NjU2ZTU0Njg2YTZlNzg2YTc5NTQ2NDZkNTQ2NjZhNjU3MjU0N2Y2NDU0Njg2NDY2NmU1NDQyNTQ2MzY0N2I2ZTc2In0=)
