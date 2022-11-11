# GCC header limits.h

在 trace vsftpd 時，看到裡面有個變數 INT\_MAX，

但是 vim + tags 或 grep 完全找不到 INT\_MAX 的定義，

因此發現 limits.h 裡頭有定義了，而且也有之前談過的 CHAR\_BIT。

細節請參考 GCC /usr/include/limits.h 的內容。
