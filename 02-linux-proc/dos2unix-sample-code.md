# Page 1

格式 DOS(windows) 與 UNIX 的差異是，\
\
DOS 以 '\r' 作為換行符號，而 UNIX 系統以 '\n' 作為換行符號，\
\
一般 Linux 系統都會有 dos2unix 與 unix2dos 的指令，\
\
如果要自行安裝的話，該指令是位於 sysutils 套件中。\
\
因為只有更換 '\r' -> '\n' 或 '\n' -> '\r' 而已，\
\
以前就練習一下，寫一個 dos2unix 的 sample.\


```c
/* Author : AppleZu Lab (http://applezu.netdpi.net)
 * Only translate '\r'(ASCII code: 13) to '\n'.
 * License: GNU
 */

#include<stdio.h>
#include<string.h>
#include<unistd.h>
#include<stdio.h>

int main(int argc, char *argv[])
{
  FILE *fp;
  int i;
  char buf[1024];
  char buf2[65535];

  if (argc <2) {
    printf("%s filename\n", argv[0]);
    return -2;
  }

  memset(buf, '\0', sizeof(buf));
  memset(buf2, '\0', sizeof(buf2));

  fp=fopen(argv[1], "r");

  if(fp == NULL){
    printf("Can't open %s\n", argv[1]);
      return -1;
  }

  while( fgets( buf, sizeof(buf), fp) != NULL ) {

    for(i=0; i < sizeof (buf); i++) {
      if(buf[i] == 13)
        buf[i] = '\n';
    }

    strncat(buf2, buf, strlen(buf));
  }

  fclose(fp);

  fp=fopen(argv[1], "w+");
  fputs(buf2, fp);
  fclose(fp);
  return 0;
}
```
