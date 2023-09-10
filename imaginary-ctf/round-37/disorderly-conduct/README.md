# disorderly-conduct

## problem

see [out.zip](./out.zip)

## solution

ok so this was like the easiest forensics challenge i've ever done, free 75pts in ictf

first i wanted to see what files were inside of the zip, so i took a look inside

```
quasar@quasar098:~/Downloads$ zipinfo out.zip
Archive:  out.zip
Zip file size: 8086 bytes, number of entries: 42
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/i
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/c
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/t
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/f
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/{
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/T
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/h
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/e
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/_
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/F
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/I
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/v
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/3
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/~
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/b
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/o
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/x
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/!
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/n
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/g
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/-
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/w
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/1
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/z
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/a
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/r
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/d
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/s
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/^
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/j
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/u
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/m
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/p
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/*
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/q
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/U
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/|
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/(
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/k
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/l
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/y
-rwxrwxrwx  3.0 unx       56 tx defX 23-Aug-13 23:52 files/}
42 files, 2352 bytes uncompressed, 2100 bytes compressed:  10.7%
```

and would you look at that, it's literally right there

we can use text format wizard to get the flag from this zipinfo 

see [here](https://quasar.name/text-format-wizard/#eyJyZWNpcGUiOlt7Im1vZHVsZVR5cGUiOiJlNWNjMWM4Yi02MjA3LTVjZjQtOTMzZS1jYzA2YjdlNjBiNmQiLCJhcmdzIjp7ImZvcm1hdCI6IiVvcmlnaW5hbCUiLCJyZWdleCI6Ii4oJHxcXG4pIn19LHsibW9kdWxlVHlwZSI6IjRkOTdkNjQ2LWM5MjYtNWQ3Yy04MDZlLTg3MGIzMjhlNmIxZCIsImFyZ3MiOnsicmVtb3ZlIjoiXFxzIn19XSwiaW5wdXQiOiItcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzL2lcbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMvY1xuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy90XG4tcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzL2Zcbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMve1xuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy9UXG4tcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzL2hcbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMvZVxuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy9fXG4tcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzL0Zcbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMvSVxuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy92XG4tcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzLzNcbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMvflxuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy9iXG4tcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzL29cbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMveFxuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy8hXG4tcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzL25cbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMvZ1xuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy8tXG4tcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzL3dcbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMvMVxuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy96XG4tcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzL2Fcbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMvclxuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy9kXG4tcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzL3Ncbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMvXlxuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy9qXG4tcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzL3Vcbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMvbVxuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy9wXG4tcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzLypcbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMvcVxuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy9VXG4tcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzL3xcbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMvKFxuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy9rXG4tcnd4cnd4cnd4ICAzLjAgdW54ICAgICAgIDU2IHR4IGRlZlggMjMtQXVnLTEzIDIzOjUyIGZpbGVzL2xcbi1yd3hyd3hyd3ggIDMuMCB1bnggICAgICAgNTYgdHggZGVmWCAyMy1BdWctMTMgMjM6NTIgZmlsZXMveVxuLXJ3eHJ3eHJ3eCAgMy4wIHVueCAgICAgICA1NiB0eCBkZWZYIDIzLUF1Zy0xMyAyMzo1MiBmaWxlcy99In0=)
