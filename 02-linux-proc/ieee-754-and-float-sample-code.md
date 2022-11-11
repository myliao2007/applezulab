# IEEE 754 and float 範例程式

功能：互轉浮點數與二進制表示法\


```c

/* ieee754.c  
 * Author: Aaron Liao (http://applezulab.netdpi.net)
 * Last updated: 2010/12/29 
 * IEEE754 sample code
 * LICENSE: GPL
 * 32 bits:  [1][8][23]
 * 64 bits:  [1][11][52]
 */

#include <strings.h>
#include <string.h>
#include <stdio.h>

int ieee754(char *data, double real)
{
    int exp=0, i=0, exp_tmp;
    double cal, frag_tmp;
    char buf[32];

    bzero(buf, sizeof(buf));

    cal = real;
    /* get sign */
    if( cal < 0 ){

        buf[0] = 1;
        cal *= (double) -1;

    } else {

        buf[0] = 0;
    }

    /* get exp */
    while( cal >= 1.0 ) {
        cal /= 2;
        exp++;
    }

    for( i=8, exp_tmp=1; i>0; i--){

        if( (exp & exp_tmp) ) {

            buf[i] = 1;

        } else {

            buf[i] = 0;

        }

        exp_tmp *=2 ;
    }

    /* get fraction */
    for(i=9, frag_tmp = (double) 1/2; i< 32; i++) {

        if( (cal - frag_tmp) >= 0 ){
            buf[i] = 1;
            cal = (double) cal - (double) frag_tmp;
        } else {
            buf[i] = 0;
        }
        frag_tmp /= (double) 2;

    }

    /* To ascii */
    for(i=0; i<32; i++) {

        buf[i] += '0';
    }

    strncpy(data, buf, 32);

    return 0;
}

double rev_ieee754(char *buf)
{
    int exp=0, i=0, exp_tmp, sign;
    double real, frag_tmp;
    int len = 32;

    /* get sign */
    if( buf[0] == '1' ){

        sign = -1;

    } else {

        sign = 1;
    }

    /* get exp */
    for( exp=0, i=8, exp_tmp=1; i>0; i--){

        if( buf[i] == '1') {
            exp += exp_tmp;
        }

        exp_tmp*=2;
    }

    /* get fraction */
    for(i=9, real=(double)0, frag_tmp = (double) 1/2; i< len; i++) {

        if( buf[i] == '1') {
            real += frag_tmp;
        }
        
        frag_tmp /= (double) 2;

    }

    while(exp>0) {

        real *= 2;
        exp--;
    }

    real *= (double) sign;

    return real;
}

int ieee754_64(char *data, double real)
{
    int exp=0, i=0, exp_tmp;
    double cal, frag_tmp;
    static char buf[64];

    bzero(buf, sizeof(buf));

    cal = real;
    /* get sign */
    if( cal < 0 ){

        cal *= (double) -1;
        buf[0] = 1;

    } else {

        buf[0] = 0;
    }

    /* get exp */
    while( cal >= 1.0 ) {
        cal /= 2;
        exp++;
    }

    for( i=11, exp_tmp=1; i>0; i--){

        if( (exp & exp_tmp) ) {

            buf[i] = 1;

        } else {

            buf[i] = 0;

        }

        exp_tmp *=2 ;
    }

    /* get fraction */
    for(i=12, frag_tmp = (double) 1/2; i< 64; i++) {

        if( (cal - frag_tmp) >= 0 ){
            buf[i] = 1;
            cal = (double) cal - (double) frag_tmp;
        } else {
            buf[i] = 0;
        }
        frag_tmp /= (double) 2;

    }

    /* To ascii */
    for(i=0; i<64; i++) {

        buf[i] += '0';
    }

    strncpy(data, buf, 64);

    return 0;
}

double rev_ieee754_64(char *buf)
{
    int exp=0, i=0, exp_tmp, sign;
    double real, frag_tmp;
    int len=64;

    /* get sign */
    if( buf[0] == '1' ){

        sign = -1;

    } else {

        sign = 1;
    }

    /* get exp */
    for( exp=0, i=11, exp_tmp=1; i>0; i--){

        if( buf[i] == '1') {
            exp += exp_tmp;
        }

        exp_tmp*=2;
    }

    /* get fraction */
    for(i=12, real=(double)0, frag_tmp = (double) 1/2; i< len; i++) {

        if( buf[i] == '1') {
            real += frag_tmp;
        }
        
        frag_tmp /= (double) 2;

    }

    while(exp>0) {

        real *= 2;
        exp--;
    }

    real *= (double) sign;

    return real;
}

/* test */
int main(void)
{
    int i, j;
    char buf[32], buf2[64];

    for(i=1; i<=1000000; i*=2) {
        
        bzero(buf, sizeof(buf));
        bzero(buf2, sizeof(buf2));

        ieee754( (char*)buf, (double) i);
        printf("[32bit] %20f ->\t", rev_ieee754( (char*)buf));;

        for(j=0; j<sizeof(buf); j++) {
            printf("%c ", buf[j]);
        }
        printf("\n");

        ieee754_64( buf2, (double) i);
        printf("[64bit] %20f ->\t", rev_ieee754_64( (char*)buf2));;

        for(j=0; j<sizeof(buf2);j++) {
            printf("%c ", buf2[j]);
        }
        printf("\n");
    }

    return 0;
}
```

\
