# 使用 nameif 修改 Linux 網路卡名稱

話說 net-tools::nameif 可以更改網卡的名稱，\
\
例如，以下是在eth1網路卡狀態為 down 的環境情況下處理：\
\
假設 eth1 MAC 為 00:30:48:84:76:07\
\
修改 /etc/mactab\
\# cat /etc/mactab\
abc200 00:30:48:84:76:07\
\
\# nameif\
\# ifconfig -a\
\
發現 eth1 被改成 abc200\
\
這樣可以用來作啥呢？...\
\
引述 OuTian 是這麼用的\
\


> \--# OuTian::Start #------------------------------------------\
> \
> ip link set eth0 down (因為在 up 的情況下會噴 resource busy ... )\
> ip link set eth0 name NEWNAME\
> ip link set eth0 up\
> \
> 以前管了一台機器接七條 isp 時\
> 通通把網卡 rename 掉 .... hinet seednet sparq ... 都設 isp 名字\
> 才不用每次要 ping/traceroute 時 , 想半天到底 ethx 是哪條線 XD\
> \
> \--# OuTian::END #------------------------------------------

\
OuTian 兄說得真是不錯的想法，我之前看 linux kernel source code 得知一開始在命名時 eth0\
\
以 eth 是指該裝置為 ethernet 才會以 eth 開頭，而 eth0, 1, 2, ... 則是以偵測到的順序，\
\
而用 nameif 改名稱，對 kernel 內部的改變是如何的？(ex. kernel 中紀錄 Network 裝置的 link-list 是否有什麼改變？)\
\
我檢視程式碼 nameif.c，發現關鍵下列，並順便寫個小程式測試玩玩\
\---------------------------------------------------------\
ioctl(sk, SIOCSIFNAME, \&ifr);\
// sk = socket(PF\_INET, SOCK\_DGRAM, 0);\
\---------------------------------------------------------\


> ```c
> #include <unistd.h>
> #include <stdlib.h>
> #include <string.h>
> #include <sys/ioctl.h>
> #include <net/if.h>
> #include <sys/socket.h>
> #include <linux/sockios.h>
>
> int main(void)
> {
>   char *mac="00:30:48:84:76:07";
>   char *old="lalala001";
>   char *new="NBA2000";
>   int sk;
>   struct ifreq ifr;
>
>   sk = socket(PF_INET, SOCK_DGRAM, 0);
>
>   if(sk<0)
>     exit;
>
>   memset( &ifr, 0, sizeof(struct ifreq));
>
>   strcpy(ifr.ifr_name, old);
>   strcpy(ifr.ifr_newname, new);
>
>   ioctl(sk, SIOCSIFNAME, &ifr);
>
>   close(sk);
>
>   return 0;
> }
> ```

\
下列我追蹤 Linux Kernel (linux-2.6.14.7) 的程式碼： \
\


> ```c
> LinuxKernel::net/core/dev.c   
>
> 2361
> 2362 case SIOCSIFNAME:
> 2363   ifr->ifr_newname[IFNAMSIZ-1] = '\0';
> 2364   return dev_change_name(dev, ifr->ifr_newname);
> 2365
> ```

\


> ```c
> 711 int dev_change_name(struct net_device *dev,
>         char *newname)
> 712 {
> 713   int err = 0;
> 714
> 715   ASSERT_RTNL();
> 716
> 717   if (dev->flags & IFF_UP)
> 718     return -EBUSY;
> 719
> 720   if (!dev_valid_name(newname))
> 721     return -EINVAL;
> 722
> 723 if (strchr(newname, '%')) {
> 724     err = dev_alloc_name(dev, newname);
> ```

\
也可以用 eth%d 當作新名稱，至於 %d 是多少跟原本取得名稱一樣是給 kernel 計算 我在 /etc/mactab 玩看看，下列是測試結果：\
\


> ```
> 在 /etc/mactab 填入此行內容：eth%d 00:30:48:84:76:07
>
> 執行指令測試
>
> # nameif
> # ifconfig -a
> # nameif
> # ifconfig -a
> # nameif
> # ifconfig -a
> # nameif
> # ifconfig -a
> ```

\
上面的指令將可以看到，名字會跳來跳去的，如：eth1, eth2, eth1, eth2 .. \
(因為這邊有兩張網路卡，我沒有動 eth0 ，而是動 eth1，所以才會是在 eth1 , eth2 之間跳) \
\


> ```
> 725 if (err < 0)
> 726     return err;
> 727     strcpy(newname, dev->name);
> 728 }
> ```

\
檢查該裝置(名稱)是否存在 \
\


> ```
> 729 else if (__dev_get_by_name(newname))
> 730     return -EEXIST;
> 731 else
> 732     strlcpy(dev->name, newname, IFNAMSIZ);
> 733
> 734 err = class_device_rename(&dev->class_dev, dev->name);
> 735   if (!err) {
> 736     hlist_del(&dev->name_hlist);
> 737     hlist_add_head(&dev->name_hlist,
>         dev_name_hash(dev->name));
> 738     notifier_call_chain(&netdev_chain, NETDEV_CHANGENAME,dev);
> 739   }
> 740
> 741   return err;
> 742 }
> 743
> ```

\
初步得到的結論是 nameif 直接改了 kernel 裡的裝置名稱，而沒有其他的別名（alias name）。
