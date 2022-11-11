# kernel 中的網路卡名稱與命名順序關係

有天，WalkingIce 於 Linux 版發問了下列的問題：\
\


> 看板: Linux\
> \
> 標 題: \[問題] 關於eth0與網路卡的關係\
> \
> 發信站: 批踢踢實業 (Sat Apr 8 17:52:48 2006)\
> 轉信站: SayYa!ctu-reader!news.nctu!ptt\
> Origin: sally.csie.ntu.edu.tw\
> \
> 不知道標題這樣下有沒有問題:P\
> \
> 就我的認知，假設網路卡都沒問題可以讓kernel抓到之後\
> \
> 設定/etc/network/interfaces (以debian為例)\
> \
> 把這一類的設定填入\
> \
> auto eth0\
> iface eth0 inet static\
> address 192.168.1.1\
> \
> 再執行ifup eth0就可以啟動網路卡，並且有192.168.1.1這個ip\
> \
> 我很好奇的是，系統如何決定eth0這個代號會跟哪一個硬體扯上關係？\
> \
> 如果我插的是另外一張莫名其妙的卡而非網路卡，系統這時候又會怎麼應對呢？\
> \
> 此外，如果我還有另一張網路卡。這個代號的順序又是如何決定呢？\
> \
> 謝謝 <(\_ \_)>\
> \
>

\
\
這使得我那天熱血沸騰起來，直接翻出 Linux kernel source code 來讀：\
\
me="個人推敲"\
linux="/usr/src/linux-2.6.15.6"\
\


> ```c
> $linux/drivers/net/Space.c:360
>
>             /*  Statically configured drivers -- order matters here. */
>             static int __init net_olddevs_init(void)
>             {
>
>
>                 int num;
>
>
>                 if (loopback_init()) {
>                         printk(KERN_ERR "Network loopback device setup failed\n");
>                 }
>
>
>                 ...
>
>
>                  /*  me: NETDEV_BOOT_SETUP_MAX 預設值 8
>                   *      意謂預設開機最多偵測的網卡數 == 8？
>                  */
>                 for (num = 0; num < 8; ++num)
>                     etherif_probe2(num);    <----------
>
>                 ...
>
>             }
> ```

> ```c
> $linux/drivers/net/Space.c:300
>
>         /*
>          * Unified ethernet device probe, segmented per architecture and
>          * per bus interface. This drives the legacy devices only for now.
>          */
>
>
>         static void __init ethif_probe2(int unit)
>         {
>
>
>                 /*
>                   *  me: netdev_boot_base 檢查此裝置是否已經 register
>                  */
>                 unsigned long base_addr = netdev_boot_base("eth", unit);
>
>
>                 if (base_addr == 1)
>                         return;
>
>
>                 /* 偵測網卡 */
>                 (void)( probe_list2(unit, m68k_probes, base_addr == 0) &&
>                         probe_list2(unit, eisa_probes, base_addr == 0) &&
>                         probe_list2(unit, mca_probes, base_addr == 0) &&
>                         probe_list2(unit, isa_probes, base_addr == 0) &&
>                         probe_list2(unit, parport_probes, base_addr == 0));
>         }
> ```

Space.c 在開機時偵測 ISA, MCA, EISA, .. 這幾種類型的網卡，而 eth\[0,1,2,3,...] 這樣的順序是去查 net device list， 如果還沒有註冊，則使用 register\_netdev(dev) 來將該裝置 register 加入 list。

> ```c
> $linux/net/ethernet/eth.c
>
> /* me: alloc_etherdev 配置了一個 ethernet 的 net_device 空間
> * 並且由 ether_setup 設定 ethernet device.
> * 經由程式碼可看出，其實又呼叫了 alloc_netdev()，
> * 而在這邊，裝置的名稱直接指定為 "eth%d"，代表為 ethernet device
> * 而這裡還沒有處理 eth? 的 '?' 該是多少
> */
>
>         298 struct net_device *alloc_etherdev(int sizeof_priv)
>         299 {
>         300         return alloc_netdev(sizeof_priv, "eth%d", ether_setup);
>         301 }
> ```

alloc\_netdev 可參考 $linux/core/dev.c /\* 決定 eth? 的 '?' 是多少是在下面處理的 \*/

> ```c
> 在 register_netdev() {
>
>           /*
>            會用 dev_alloc_name(dev, dev_name) 來設定裝置的名稱 ethx，
>             而 'x' 則經由計算在該 net device list 中的位置取得。
>             該 list 的資料型態為 struct net_device。
>
>
>             等知道該裝置的名稱為何時 ex. (dev->name="eth1")
>            最後才 call register_netdevice(dev) 將該裝置加入 list 中。
>           */
>         }
> ```

\
而 PCI 網卡，如 8139too.c 也是一樣，\
在 device driver 中也是要有 call register\_netdev() 的動作。\
\
以目前的了解，核心會將網路裝置用 link list 串起來，而編號順序應只是被放入 list 中的順序而已。\
\
如果有三張同樣的網卡呢？我在 VMware 直接 dmesg 觀察三張 pcnet32 的結果，I/O address 比較小的會在前面。\
\
ex. eth0 at 0x1080\
eth1 at 0x1400\
eth2 at 0x1480\
\
後記：在我 trace 完上列這段流程之後，我才發現到，市面上已經有好幾本講 Linux kernel 網路的書，都已經有談到我上列的內容，而且內容更詳盡，於是我又去讀了這幾本書 =.=\
\
參考文獻\
1\. Understanding Linux Kernel Internals\
2\. The Linux TCP/IP Stack: Networking Embedded System\
3\. Linux Network Architecture
