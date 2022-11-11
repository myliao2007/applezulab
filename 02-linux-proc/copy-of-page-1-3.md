# 取得時間

```c
#include <time.h>

/* Error: return NULL 
 * Or return the string of time
 */
char * get_time(void)
{
    time_t t;

    if( time(&t) < 0 )
        return NULL;

    return ctime( (const time_t *) &t );

}
```
