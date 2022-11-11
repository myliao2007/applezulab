# Proxy ARP

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>Proxy ARP 示意圖</p></figcaption></figure>

ASCII 圖例: \[LAN0]---\[(eth0)-LinuxBox-(eth1)]---\[LAN1]\
\
Linux Advanced Routing & Traffic Control \[1] 有說明 Proxy ARP 可以做到類似橋接 (bridge) 的功能，連接 Linux box 左 (LAN0) 右 (LAN1) 端網路，並且可代替左右方區域網路(LAN) 的電腦回應 ARP response。\
\
參考 \[1] ，啟動 Linux proxy ARP：\
\
其中 ethR 與 ethL 以本文上方的圖例來看，對應 eth1 與 eth0，\
\
echo "1" > /proc/sys/net/ipv4/conf/ethR/proxy\_arp;\
echo "1" > /proc/sys/net/ipv4/conf/ethL/proxy\_arp;\
\
\# 啟動 packet forward\
echo "1" > /proc/sys/net/ipv4/conf/ip\_forward;\
\
如果 Linux Kernel 的版本是 2.4，還需要這項設定以能夠送出 unsolicited ARP messages.\
echo "1" > /proc/sys/net/ipv4/ip\_nonlocal\_bind\
\
ARP, Proxy ARP 原理觀念可以參考後列的任一 TCP/IP 書籍 \[2]\[3]\[4]。\
\
至於 Proxy ARP 是如何運作的，Linux Advanced Routing & Traffic Control 並沒有特別詳細說明，但作者提示可以透過 arping 以更新其他電腦的 ARP cache table，並且使用 arp -d ip 來刪除 Linux 的 ARP cache table 記錄，於是我推論 Linux 是依照本機網路卡上的 ARP cache table 記錄得知 MAC/IP 的對應（mapping），於是，若是在 eth0 接收到 ARP request，而該 MAC/IP 在 Linux Box 的 ARP cache table 中有這筆記錄，則會代為送出 ARP response.\
\
若是在 Linux Box的 ARP cache table 設定靜態的 MAC/IP，應就能夠重導流量方向了。\
\
**參考文獻**\
\[1] Linux Advanced Routing & Traffic Control, [http://lartc.org/](https://web.archive.org/web/20200306075530/http://lartc.org/).\
\[2] Douglas E. Comer, [Internetworking with TCP/IP](https://web.archive.org/web/20200306075530/http://www.amazon.com/gp/product/013608530X/ref=as\_li\_tf\_tl?ie=UTF8\&camp=1789\&creative=9325\&creativeASIN=013608530X\&linkCode=as2\&tag=apla0fb9-20), Vol. 1, 6th Edition, Prentice Hall, 2013.\
\[3] Kevin R. Fall and W. Richard Stevens, [TCP/IP Illustrated](https://web.archive.org/web/20200306075530/http://www.amazon.com/gp/product/0321336313/ref=as\_li\_tf\_tl?ie=UTF8\&camp=1789\&creative=9325\&creativeASIN=0321336313\&linkCode=as2\&tag=apla0fb9-20), Vol. 1, 2nd Edition, Addison-Wesley Professional, 2011. \[Hardcover]\
\[4] Behrouz A Forouzan, [TCP/IP Protocol Suite](https://web.archive.org/web/20200306075530/http://www.amazon.com/gp/product/0073376043/ref=as\_li\_tf\_tl?ie=UTF8\&camp=1789\&creative=9325\&creativeASIN=0073376043\&linkCode=as2\&tag=apla0fb9-20), McGraw-Hill Inc., 2009.
