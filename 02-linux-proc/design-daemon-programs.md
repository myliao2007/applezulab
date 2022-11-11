# 設計在背景執行的 Linux 程式

下面這段 vsftpd 的 source code ，就是使 vsftpd 啟動後，在背景執行的方法：

```c
54
55   /*
56    * tracing date : 2006-10-17
57    */
58   if (tunable_background)
59   {
60       /*
61        * fork()
62        */
63     int forkret = vsf_sysutil_fork();
64     if (forkret > 0)
65     {
66       /* Parent, just exit */
67       vsf_sysutil_exit(0);
68     }
69
70     /*
71      * 2006-10-17 - so.. setsid()
72      */
73     vsf_sysutil_make_session_leader();
74   }
75
```

\
方法是，透過 fork() child process，以 setsid() 讓 child process 成為 process leader，有自己的 process group，\
\
parent process 單純結束，下面是 vsf\_sysutil\_make\_session\_leader() \
\
vsftpd-2.0.3/sysutil.c\


```c
2463
2464 void
2465 vsf_sysutil_make_session_leader(void)
2466 {
2467   /* This makes us the leader if we are not already */
2468   (void) setsid();
2469   /* Check we're the leader */
2470   if (getpid() != getpgrp())
2471   {
2472     die("not session leader");
2473   }
2474 }
2475
```

\
**參考文獻**\
Douglas E. Comer, David L. Stevens, [Internetworking with TCP/IP, Vol. III: Client-Server Programming and Applications, Linux/Posix Sockets Version](https://web.archive.org/web/20200306075259/http://www.amazon.com/gp/product/0130320714/ref=as\_li\_tf\_tl?ie=UTF8\&camp=1789\&creative=9325\&creativeASIN=0130320714\&linkCode=as2\&tag=apla0fb9-20), Vol. 3, Chap. 30, 2000.
