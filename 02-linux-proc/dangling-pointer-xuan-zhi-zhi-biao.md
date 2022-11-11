# Dangling pointer（懸置指標）

在C語言中，如果一個指標不再使用了，那麼我們應該要在釋放指標所指的記憶體之後，將指標指定為空值（NULL）。

```c
#include <iostream>
#include <string.h>
#include <stdio.h>
#include <stdlib.h> 
 
using namespace std;
int main(int argc, char * argv[])
{
     char *cmd = NULL;

     cmd = new char[16];
     printf("cmd: %p\n", cmd);

     if(cmd) {
         delete [] cmd;
     }

     printf("delete cmd: %p\n", cmd);

     cmd = (char*)malloc(sizeof(char)*16);

     printf("cmd: %p\n", cmd);
     if( cmd ) {
         free(cmd);
     }
     printf("free cmd: %p\n", cmd);

     return 0;
}
```

\
myliao@my-thinkpad:/tmp$ g++ play.cpp -o play\
myliao@my-thinkpad:/tmp$ ./play\
cmd: 0x9943008\
delete cmd: 0x9943008\
cmd: 0x9943008\
free cmd: 0x9943008\
\
從程式的結果來看，可以知道，我們 free cmd 之後，cmd 指標並不會自動改為NULL。\
\
這會有什麼問題呢？\
\
如果說你的指標是要重複使用的，如果沒有在 free() 之後，手動將指標修改為 NULL，則之後的程式碼會無法得知該指標所指向的記憶體已經被釋放了。
