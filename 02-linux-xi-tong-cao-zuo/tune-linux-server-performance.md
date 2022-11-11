# Linux 伺服器效能調校

**網路效能測試軟體**

* Netperf (http://www.netperf.org): 支援 TCP, UDP, DLPI (Data Link Provider Interface), UNIX Domain Sockets.
* SPEC SFS (http://www.spec.org/)
* Volanomark (http://www.volano.com/benchmarks.html)
* Web server: SPECweb, SPECweb SSL, TPC-W, SPECjAPPServer and ECPerf
* Oracle: http://www.oracle.com/apps\_benchmark/index.html)
* SAP Standard (http://www50.sap.com/benchmark/)

\
**Linux 網路參數設定**\


> echo "30000" > /proc/sys/net/ipv4/tcp\_max\_syn\_backlog\
> 預設為 1024，可增加 Server queue TCP SYN 的數量\
> \
> echo "2000000" > /proc/sys/net/ipv4/tcp\_max\_tw\_buckets\
> 預設為 180000，增加 TIME-WAIT state 的數量\
> \
> echo "50000" > /proc/sys/net/core/netdev\_max\_backlog\
> 設定可以 queue 在IP層之下的 network core 之封包數目，可以使用較多的記憶體儲存進入的封包，預設 1000。\
>

\
最好用的文件仍然是 Kernel source 中的 Documentation，設定 Kernel 參數可透過 sysctl, procfs\
範例：

> \
> \# sysctl -w kernel.shmmax=32\
> \# sysctl -p /etc/sysctl.conf\
> \
> \# echo "32" > /proc/sys/kernel/shmmax\
> \
> Linux kernel 2.6\
> \
> /proc/sys/kernel/\
> thread-max: 系統中可允許的最大 thread 數目。\
> domainname\
> hostname\
> ostype\
> osrelease\
> version：Kernel version\
> real-root-dev：real root device partition\
> rtsig-max：最大的 real time signals\
> rtsig-nr：現在 queue 中的 real-time signals 數量\
> pid\_max\
> \
> /proc/sys/fs/\
> file-max: file descriptors 的最大數目\
> file-nr: file handles 的個數\
> super-max: 檔案系統的最大數目\
> \
> /proc/sys/net/ipv4/\
> ip\_no\_pmtu\_disc: 關閉 Path MTU discovery\
> \
> /proc/sys/net/route/\
> max\_size: routing cache 的最大 size\
> max\_delay: flushing routing cache 的最大延遲時間\
> \
> /proc/sys/net/conf/interface/\
> proxy\_arp:\
> rp\_filter: 需要驗證來源位址\
> \
> /proc/sys/net/core\
> rmem\_default: socket 的接收 buffer size\
> rmem\_max: socket 的最大接收 buffer size\
> wmem\_default: socket 的傳送 buffer size\
> wmem\_max: socket 的最大傳送 buffer size\
> netdev\_max\_backlog: queued 封包的最大數目\
> \
>

\
**參考文獻**\
1\. Sandra K. Johnson, [Performance Tuning for Linux Servers](https://web.archive.org/web/20200306075615/http://www.amazon.com/gp/product/0137136285/ref=as\_li\_tf\_tl?ie=UTF8\&camp=1789\&creative=9325\&creativeASIN=0137136285\&linkCode=as2\&tag=apla0fb9-20), IBM PRESS, 2005.\
2\. 劉怡芳、劉育銘，[伺服器效能調校專家─以 Linux 為例](https://web.archive.org/web/20200306075615/http://www.books.com.tw/exep/assp.php/vip--af000078707/exep/prod/booksfile.php?item=0010318534)，學貫，2005。
