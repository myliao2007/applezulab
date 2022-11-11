# Function pointer in C

Function pointer is useful to do abstraction; it is a general implementation of immediate interface; it is also used to implement the plug-in interface.

Here is a example:



```c
#include <stdio.h> 
#include <strings.h>

typedef struct _dev{ 
    char name[16]; 
    void (*open)(); 
} dev;

double init (dev *dev) 
{ 
    bzero(dev->name, sizeof(dev->name)); 
    return 0.0; 
}

int dev_a_open(void) 
{ 
    printf("device a open\n"); 
    return 0; 
}

int dev_b_open(void) 
{ 
    printf("device b open\n"); 
    return 0; 
}

int main(int argc, char *argv) {

    dev dev_a, dev_b, *dev;
    init(&dev_a);
    init(&dev_b);

    dev = &dev_a;
    dev->open = dev_a_open;
    dev->open();

    dev = &dev_b;
    dev->open = dev_b_open;
    dev->open();

    return 0;
}

```
