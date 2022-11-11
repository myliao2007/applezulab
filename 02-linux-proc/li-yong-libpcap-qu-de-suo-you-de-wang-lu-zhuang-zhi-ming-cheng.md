# 利用 libpcap 取得所有的網路裝置名稱

利用 libpcap 的 function 來尋找目前 Linux 系統上所有的網路卡裝置名稱，\
\
1\. 需要安裝 libpcap 才能使用 libpcap，下面是編譯的方法：\


> \# gcc search\_dev.c -o search\_dev -lpcap

執行結果：\


> \# ./search\_dev\
> eth0\
> any\
> lo

```c
/* libpcap */
/* Author: Aaron Liao */

#include<stdio.h>
#include<pcap.h>

int main(void)
{
        pcap_if_t       *dev, *ptr;
        char errbuf[PCAP_ERRBUF_SIZE];

        pcap_findalldevs(&dev, errbuf);

        for(ptr=dev; ptr != NULL; ptr = ptr->next){
                printf("%s\n", ptr->name);
        }

        pcap_freealldevs(dev);

        return 0;

}
```



其 實用 libpcap 有一些特別的好處，就是寫出來的程式可以在 libpcap 有支援的平台上直接使用 \[1]，例如可以在 linux 跟 freebsd 上通用，因為若是在 Linux 寫 link layer socket，是使用 PF\_PACKET, 例如：\
\
新寫法：\


> socket(PF\_PACKET, SOCK\_RAW, ... )；

舊寫法:\


> socket(AF\_INET, SOCK\_PACKET, ... );

例如要接收所有 datalink layer frames 可以這麼寫：\


> socket(PF\_PACKET, SOCK\_RAW, htons(ETH\_P\_ALL));\
> socket(AF\_INET, SOCK\_PACKET, htons(ETH\_P\_ALL));

若是只接收 IPv4 frames,\


> socket(PF\_PACKET, SOCK\_RAW, htons(ETH\_P\_IP));\
> socket(AF\_INET, SOCK\_RAW, htons(ETH\_P\_IP));

而在 bsd 上面則是使用 bsd packet filter (BPF)\
\
而使用 libpcap 寫程式，好處就是只要使用 libpcap 提供的 function，\
而不需要在意各系統平台的 socket 呼叫方法。\
\
\[1] Richard Steven, UNIX Network Programming, Volume 2, Second Edition: Interprocess Communications, Prentice Hall, 1999, ISBN 0-13-081081-9.

