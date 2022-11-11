# strncpy

這是一個 strncpy 的範例：

```c
int safe_strncpy(char *dst, char *src, int len)
{
    int count=0;
    if(len <1) return 1;

     // Null pointer is dangerous.
    if( dst==NULL || src == NULL ) return (-1);
    
    for(count=0; count<len; count++) {
        dst[count]=src[count];
        if(src[count]=='\0') break; /* String end of str */
    }

     return 0;
}
```

