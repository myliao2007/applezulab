# \[C] Pointer and array

Here, we define a pointer named ptr to point to a memory block whose size is (1024\*sizeof(char)).

char \*ptr = malloc( sizeof(char) \* 1024);

The value of sizeof(ptr) is the size of ptr, and the value of sizeof(\*ptr) is the size of the first character in the memory block pointed by ptr.

If we define an array whose size is 1024 characters:

char array\[1024];

The value of sizeof(array) is (1024 \* sizeof(char)).

However, sizeof(\*array) is sizeof(char).

Because \*array means \*(array+0) which is equivalent to array\[0], we get the size of the first item in array.

For example, array\[2] = \*(array+2) = \*(2+array) = 2\[array]

We use memset(array, 0, sizeof(array)) to clean the array.

However, if we want to clean the memory pointed by ptr, we have to record the size of the memory ourselves.

For example, we use buf\_size to record the size of memory pointed by ptr.

buf\_size = ( sizeof(char) \* 1024 ); ptr = malloc(buf\_size); memset(ptr, 0, buf\_size);
