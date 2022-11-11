# 為什麼需要 & 0377

在 Linux 系統中，我在許多 C 的程式碼中發現到有使用 &0377 的用法，這是什麼樣的目的呢？\
\
0377 是個八進位的數值，當我們將 0377 以二進制呈現，則是 11111111，而十進制是 255； \
\
在 C 語言中，unsigned char 型別的值域是 0 \~ 255；而 signed char 則是 -128 \~ 127。\
\
那麼，在下列的這個範例，這麼做有什麼意義呢？\
\
unsigned char a=128; /\* 1000 0000 \*/\
a= a &0377;\
\
1000 0000 & 1111 1111 的運算結果是 1000 0000。\
\
一開始，我不曉得這麼做的目的。\
\
於是，我發現 net-tools 的程式碼也出現了 &0377，如下：\
\


```c
net-tools-1.60/lib/ether.c
37 /* Display an Ethernet address in readable format. */
38 static char *pr_ether(unsigned char *ptr)
39 {
40     static char buff[64];
41
42     snprintf(buff, sizeof(buff), "%02X:%02X:%02X:%02X:%02X:%02X",
43              (ptr[0] & 0377), (ptr[1] & 0377), (ptr[2] & 0377),
44              (ptr[3] & 0377), (ptr[4] & 0377), (ptr[5] & 0377)
45         );
46     return (buff);
47 }
```

\
\
我在猜想著，還是 &0377 會讓編譯器做了什麼樣的型別轉換嗎？&#x20;

> 最後，我從[Sflam 的說法](https://web.archive.org/web/20200306075455/http://www.programmer-club.com/pc2020v5/forum/ShowSameTitleN.asp?%20URL=N\&board\_pc2020=c\&id=30709)找到一個比較合理的解釋，例如：在 Motorola DSP56300 中，一個 byte 的長度是 24 個 bits，因此，&0377 (1111 1111) 的目的是確保對其它的位元都有清為零（mask）。

\
在 C 語言中，char\_bit 是 8 個 bits，然而，並非每個硬體裝置都將一個 byte 以 8 個 bits 為單位。\
\
如果您有較好的理由，請告訴我，謝謝。
