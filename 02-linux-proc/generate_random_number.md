# \[C] 產生亂數

傳回一個 0 \~ RAND\_MAX 的整數, 以 nano second 為 seed, 需要 librt (-lrt)

```c
#include <stdlib.h>
#include <time.h>

long gen_rand(void)
{
    struct timespec tv;
    clock_gettime(CLOCK_MONOTONIC, &tv);
    srandom( tv.tv_nsec );

    return random();
}
```

傳回一個 0 \~ 1 的小數

```c
double rand(void)
{
    return ( (double) gen_rand() / (double) RAND_MAX );
}
```

