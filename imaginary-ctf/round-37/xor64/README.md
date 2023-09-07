# xor64

## problem

It was base64ed, but also XORed with... something?

```
AAAAABIFGFIOPBJWGw4VEjYFRBQ2FxwDNhRFCEg8VTlIPFU5SDxVOUg8VTlIPFU5SDxVGw==
```

## solution

if we b64 decode it and then hexdump it, we can see that the flag is xored with `ictf` because `ictf.*` ^ `(ictf)+` first four bytes are null bytes always due to the properties of xor

```
00000000: 0000 0000 1205 1852 0e3c 1256 1b0e 1512  .......R.<.V....
00000010: 3605 4414 3617 1c03 3614 4508 483c 5539  6.D.6...6.E.H<U9
00000020: 483c 5539 483c 5539 483c 5539 483c 5539  H<U9H<U9H<U9H<U9
00000030: 483c 551b                                H<U.
```

[see it in tfw here](https://quasar.name/text-format-wizard/#eyJyZWNpcGUiOlt7Im1vZHVsZVR5cGUiOiI0ZDM0YTU5ZS05MDU3LTU0MDktYmM1YS04ZWI3M2YwYjBiZjIiLCJhcmdzIjp7Im1ldGhvZCI6ImRlY3J5cHQifX0seyJtb2R1bGVUeXBlIjoiNGQ1MmM0ZGQtMjllMy01NzUyLWI4YmUtZTFkOTA5ODc4YjFmIiwiYXJncyI6eyJrZXkiOiJpY3RmIn19XSwiaW5wdXQiOiJBQUFBQUJJRkdGSU9QQkpXR3c0VkVqWUZSQlEyRnh3RE5oUkZDRWc4VlRsSVBGVTVTRHhWT1VnOFZUbElQRlU1U0R4Vkd3PT0ifQ==)
