# 於 Linux 上撰寫 daemon server 要注意的

要撰寫一個網路伺服器 Deamon，一般要注意掌控 fork() 所產生的子行程 (child process) 數目，因此程式中需要記錄以對子行程的數目進行限制，以預防有人惡意的不斷送出僅設有 SYN flag 的 TCP SYN 封包(packet)，導致 TCP 三向交握(Three-way handshaking)無法正常完成，而 Daemon 只能不斷停留在等待 SYN-ACK，最後耗盡系統資源，導致系統完全當機(crash)。\
\
ulimit -Ha 與 ulimit -Sa 來檢視目前系統的硬限制(Hard limit)與軟限制(Soft limit) 的值：

> \# ulimit -Sa\
> core file size (blocks, -c) 0\
> data seg size (kbytes, -d) unlimited\
> file size (blocks, -f) unlimited\
> max locked memory (kbytes, -l) unlimited\
> max memory size (kbytes, -m) unlimited\
> open files (-n) 1024\
> pipe size (512 bytes, -p) 8\
> stack size (kbytes, -s) 8192\
> cpu time (seconds, -t) unlimited\
> max user processes (-u) unlimited\
> virtual memory (kbytes, -v) unlimited\
> \
> \# ulimit -Ha\
> core file size (blocks, -c) unlimited\
> data seg size (kbytes, -d) unlimited\
> file size (blocks, -f) unlimited\
> max locked memory (kbytes, -l) unlimited\
> max memory size (kbytes, -m) unlimited\
> open files (-n) 1024\
> pipe size (512 bytes, -p) 8\
> stack size (kbytes, -s) unlimited\
> cpu time (seconds, -t) unlimited\
> max user processes (-u) unlimited\
> virtual memory (kbytes, -v) unlimited\
>

\
根據 man bash 對 ulimit hard limit 與 soft limit 的描述，他們的功用在於，hard limit 一旦設定之後就無法在增加該參數的值，而 soft limit 則是最多可以增加到與 hard limit 的值一樣多，還有就是 hard limit 是需要管理員權限(super user)才能更動的。\
\
像上例，就有個很明顯的 open files，這個項目的值就表示 Linux 預設一個行程可開啟的 file descriptor 數目是 1024 個，如果我們沒有更改這個值，那我們的程式最多只能開啟 1024 個檔案而已。\
\
除了檔案，由於 Linux 中開啟某裝置都是抽象化分層了，於是像開啟某個檔案一般，開啟的 socket 連線數也是受制於檔案的開啟數目，所以如果要讓 server 可以接受大於 1024 的連線，則需要修改這個值。\
\
有幾個系統呼叫 (system call)，可以寫在程式中更動這些預設值：\
\
ulimit()、getrlimit()/setrlimit()，其中後兩者是用來取代 ulimit() 的。\
\
下列是 getrlimit/setrlimit 系統呼叫的函式原型(prototype)：

> int getrlimit(int resource, struct rlimit);\
> int setrlimit(int resource, const struct rlimit \*rlim);\
>

\
其中 resource 是所要更動的項目選項，如我們想更動可開啟檔案的個數，則可修改 RLIMIT\_NOFILE，如 setrlimit( RLIMIT\_NOFILE, \&rlim);\
\
Man manual for details:\
$ man 2 getrlimit\
$ man 2 setrlimit\
\
細節可參考 manual:\
$ man 2 getrlimit\
$ man 2 setrlimit\
\
下列為 rlimit 的 structure：\
struct rlimit{\
&#x20;   rlimit\_t rlimit\_cur; // Soft-limit\
&#x20;   rlimit\_t rlim\_max; // Hard-limit\
};\
\
下面用一個簡單的範例，來呈現如何使用 getrlimit/setrlimit 取得與設定，這個範例，首先會用 getrlimit 取得目前可開啟檔案的 soft limit 與 hard limit，然後，再用 setrlimit 設定希望的值，最後再用 getrlimit 重新取得一次確認修改結果。

```c
// test.cpp
#include <sys/time.h>
#include <sys/resource.h>
#include <unistd.h>
#include <iostream>

using namespace std;

int main(void)
{
  struct rlimit limit;
  const struct rlimit mit = { 2048, 600000 };

  getrlimit(RLIMIT_NOFILE, &limit);
  cout << "Current: " << limit.rlim_cur << endl;
  cout << "Max: " << limit.rlim_max << endl;

  setrlimit(RLIMIT_NOFILE, &mit);

  getrlimit(RLIMIT_NOFILE, &limit);
  cout << "Current: " << limit.rlim_cur << endl;
  cout << "Max: " << limit.rlim_max << endl;

  return 0;
}
```

\
編譯:&#x20;

> g++ test.cpp -o test\
>

\
執行結果：

> \# ./test\
> Current: 1024\
> Max: 1024\
> Current: 2048\
> Max: 600000\
>

\
除了 RLIMIT\_NOFILE，其他還有如 RLIMIT\_NPROC (最大行程數目)、RLIMIT\_FSIZE (最大檔案大小)等都是需要注意的項目，關於 resource 的完整選項可以在 Linux /usr/include/bits/resource.h 找到。\
\
還有一個可查詢目前系統限制的函式，sysconf(int name)\
\
比如可以使用 sysconf( \_SC\_OPEN\_MAX ) 來查詢目前可開啟的最大檔案個數，細節請 man 3 sysconf，完整的參數有上百個，一樣是可以在 /usr/include/bits/confname.h 找到。\
\
Client 的部份則是要注意 connect timeout 問題，TCP sender 要注意 sned() 之傳回值，send 與 recv 可配合 select() 使用。\
\
PS: 設定 limit 雖然可以避免資源被耗盡，但是，該 sshd 服務等同於受到阻斷式攻擊，導致其他使用者無法連線，該怎麼辦呢?\
\
參考文獻：\
John Shapley Gray, [Interprocess Communications in Linux: The Nooks and Crannies](https://web.archive.org/web/20200306075425/http://www.amazon.com/gp/product/0130460427/ref=as\_li\_tf\_tl?ie=UTF8\&camp=1789\&creative=9325\&creativeASIN=0130460427\&linkCode=as2\&tag=apla0fb9-20), Chap 2.9, Pearson Education, 2003.
