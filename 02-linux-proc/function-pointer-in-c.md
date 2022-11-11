# Function pointer in C

Function pointer is useful to do abstraction; it is a general implementation of immediate interface; it is also used to implement the plug-in interface.

Here is a example:



<pre class="language-c"><code class="lang-c">#include &#x3C;stdio.h> 
#include &#x3C;strings.h>

typedef struct _dev{ 
    char name[16]; 
    void (*open)(); 
}dev;

double init (dev *dev) { bzero(dev->name, sizeof(dev->name)); return 0.0; }
int dev_a_open(void) { printf("device a open\n"); return 0; }
int dev_b_open(void) { printf("device b open\n"); return 0; }
int main(int argc, char *argv) {
<strong>
</strong><strong>    dev dev_a, dev_b, *dev;
</strong>
    init(&#x26;dev_a);
    init(&#x26;dev_b);

    dev = &#x26;dev_a;
    dev->open = dev_a_open;
    dev->open();

    dev = &#x26;dev_b;
    dev->open = dev_b_open;
    dev->open();

    return 0;
}
</code></pre>
