# you-cant-reverse-me

## problem

There are too many great reverse players who can always get my flag from a flag checker, but nothing can be done if you can't even read the binary?

```dockerfile
FROM alpine:3 as builder

RUN apk add --no-cache build-base
WORKDIR /app
COPY checker.c .
RUN gcc checker.c -o checker

FROM alpine:3 as app
WORKDIR /app
COPY --from=builder /app/checker .
RUN chmod 111 checker && \
    printf '#!/bin/sh\nsh\n' > run && \
    chmod +x run && \
    chown -R root:root /app

FROM pwn.red/jail
COPY --from=app / /srv
ENV JAIL_TIME=60 JAIL_TMP_SIZE=1M
```

## solution

we get a shell but we can only execute the challenge and not read or write to it

however, we can use the `LD_PRELOAD` to inject some stuff before running the binary

i found this LD_PRELOAD binary dumper online:

```c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

static void init(void) __attribute__((constructor));
static void init(void)
{
  FILE *maps, *dst;
  char exe[4096], path[4096];
  char *start, *end;
  off_t offset;
  int n;

  n = readlink("/proc/self/exe", exe, sizeof(exe));
  exe[n] = '\0';
  dst = fopen(getenv("DST"), "w");
  maps = fopen("/proc/self/maps", "r");

  while((n = fscanf(maps, "%p-%p%*s%lx%*s%*s%*[ ]%[^\n ]\n", &start, &end, &offset, path)) != EOF) {
    if (n == 4 && !strcmp(path, exe)) {
      printf("Dumping [%p - %p] from %s at offset %#lx\n", start, end, path, offset);
      fseek(dst, offset, SEEK_SET);
      fwrite(start, end - start, 1, dst);
    }
  }
  exit(0);
}
```

after that, i had the binary but it was missing some header chunks or something, so it was really hard to debug

anyways, i was able to step through it in binja and find the values that XOR with each other to get the flag

i did not enjoy this challenge because i spend around 4 hours trying to use the `/proc/<pid>/*` files to get the memory of the binary, but i don't have the permissions to do so apparently
