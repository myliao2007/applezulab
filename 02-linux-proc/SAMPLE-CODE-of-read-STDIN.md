# 讀取 STDIN (Standard INPUT) 範例程式

```c
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>

/* return value:
 * END_OF_FILE: End of File
 * More than 0: read bytes from stdin
 * Less than 0: error
 * 0 : nodata
 */
int get_stdin(int block, char *buf, int buflen)
{
    /* man 2 select, for details */
    struct timeval tv;
    fd_set    rfds;
        int retval;
    char *ret;

    tv.tv_sec = TIME_OUT_S;
    tv.tv_usec = TIME_OUT_US; 

        /* Watch stdin (fd 0) to see when it has input. */
        FD_ZERO(&rfds);
        FD_SET( STDIN, &rfds);

    if(block) 
        retval = select( ( STDIN + 1), &rfds, NULL, NULL, &tv);
    else
            retval = select( ( STDIN + 1), &rfds, NULL, NULL, NULL);
        /* Don't rely on the value of tv now! */

    //} else if( FD_ISSET( STDIN, &rfds) ) {

    if( retval > 0 ) {

        bzero(buf, buflen);

        /* read() retval:0 endoffile, retval:-1 error*/

        /* gets() ignore the newline and ENDOFFILE */
        ret = gets( buf );

        if(ret == NULL)
            return END_OF_FILE;
    } 

    /* error: -1 , no data: 0, read bytes number */ 
    return retval;

}c
```
