# 陣列名稱與指標

定義一個 ptr pointer, point to 1024 個 char size 的記憶體區塊，  \
char \*ptr = malloc( sizeof(char) \* 1024);sizeof(ptr) 所得到的是該指標本身的大小，而 sizeof (\*ptr) 所得到的是，指標指向的記憶體區塊之第一個 item 之型態大小。char array\[1024];sizeof(array) 為 1024 個 char 的大小，\
\
sizeof(\*array) 為一個 char 的大小，因為 \*array 會被 compiler 視為 \*(array+0)，等同於 array\[0]，代表 array 中的第一個 item 的 size，ex. array\[2] = \*(array+2) = \*(2+array) = 2\[array]\
如果要將 array 每個 item 內容設定為 0, 可以 memset(array, 0, sizeof(array));而若要將 pointer 所指向的整塊 memory 都設定為 0，就要自己記錄 memory size:buf\_size\*=sizeof(char);\
char \*ptr = malloc( buf\_size);\
memset(ptr, 0, buf\_size);sizeof 取到 (\*pointer) 是 1 個 element 的 size\


```c
#include<stdio.h>
struct co {
  int a; int b;  int c;  int d;  int e;
  int f;  int g;  int h;  int i;  int j;
};

int main(void)
{
  struct co *a=NULL;
  int *b;
  printf("sizeof(struct co): %d\n", sizeof(struct co));
  printf("sizeof(a): %d\n", sizeof(a));
  a = malloc(sizeof(struct co)*2);
  b = malloc(sizeof(int)*2);
  printf("sizeof(*a): %d\n", sizeof(*a));
  printf("sizeof(*b): %d\n", sizeof(*b));
  return 0;
}
```

\
ming@mp:/tmp$ gcc test.c -o test\
ming@mp:/tmp$ ./test\
sizeof(struct co): 40\
sizeof(a): 4 ( pointer a 的 size)\
sizeof(\*a): 40 ( 1 個 struct co 的 size )\
sizeof(\*b): 4 ( 1 個 int 的 size)
