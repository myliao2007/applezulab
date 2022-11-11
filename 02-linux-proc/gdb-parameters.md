# GDB 參數

程式編譯時需啟動 debug flag: ex. gcc hello.c -o hello -g&#x20;

* file : 載入執行檔
* break : 設定中斷點 ( n 為正整數 )
  * break n (中斷於第 n 行)
  * break +n (中斷於目前之後的 n 行)
  * break func (中斷於 func function)
  * break file.c:n (中斷於 file.c 的第 n 行)
* info break : 查看已經設定多少個 break point
* clear : 清除中斷點
  * clear n
  * clar func
* delete : 清除中斷點 ( m 為正整數 )
  * delete m (刪除第 m 個中斷點)
* disable : 暫時關閉中斷點
* enable : 啟動 disable 的中斷點
* return : 從 function return
* attach pid : 指定 trace 的 child process
* detach pid : 反向 attach 的動作
* set args str : 設定 \*argv\[ ]
* run : 開始執行程式
* continue : 中斷點後繼續執行，直到下一個中斷點或程式節數
* next : 繼續執行下一行 (若是 function, 執行完整的 function )
  * ex. 設定開始一行一行追蹤的中斷點: break n; 然後執行程式 (run); 接著就可以使用 next 一行一行追蹤
* step : 類似 next, 只是在 function 中也是一行一行跑
* until : break 迴圈
* print :
  * print variable : 印出 variable 變數的值
  * print \&variable : 印出 variable 變數的位址
  * print \*variable : 印出 \*variable 指標變數所指的記憶體內容值
  * print /x variable : 指定 print 以十六進位格式輸出 variable (格式: x 十六進位, d 有號整數, u 無號整數, o 八進位 , t binary , a 位址 , c 字元, f: 浮點數 )
* diaplay variable : 每次 step/next 都會輸出 variable 的值

\
更多進階 GDB 操作 (ex. multithread, signal handler, remote debug ) 可參考  \[1] Chap. 6, 進階 GDB \
\
Reference

1. 黃郁熙, 用Open Source工具開發軟體新軟體開發關念, [http://www.study-area.org/cyril/opentools/opentools/book1.html](https://web.archive.org/web/20200306075330/http://www.google.com/url?q=http%3A%2F%2Fwww.study-area.org%2Fcyril%2Fopentools%2Fopentools%2Fbook1.html\&sa=D\&sntz=1\&usg=AFrqEzd0H40st2Fm6xpveiMsWYYT7EFk8w)
2. 除錯工具, [http://www.study-area.org/cyril/opentools/opentools/debug.html](https://web.archive.org/web/20200306075330/http://www.google.com/url?q=http%3A%2F%2Fwww.study-area.org%2Fcyril%2Fopentools%2Fopentools%2Fdebug.html\&sa=D\&sntz=1\&usg=AFrqEzcD7r8cl7NtFhBvMgQQ-fHIw779MA)
3. ckhung GDB, [http://people.ofset.org/\~ckhung/b/c/gdb.php](https://web.archive.org/web/20200306075330/http://www.google.com/url?q=http%3A%2F%2Fpeople.ofset.org%2F%7Eckhung%2Fb%2Fc%2Fgdb.php\&sa=D\&sntz=1\&usg=AFrqEzfOhcO2z8eWRS5I2eTiMV8JKpWgPw)
4. GDB Toturial, [http://www-2.cs.cmu.edu/\~gilpin/tutorial/](https://web.archive.org/web/20200306075330/http://www.google.com/url?q=http%3A%2F%2Fwww-2.cs.cmu.edu%2F%7Egilpin%2Ftutorial%2F\&sa=D\&sntz=1\&usg=AFrqEzdQwM9So274E0IPZUBzkIk0r22XYA)
5. GDB manual, [http://www.gnu.org/software/gdb/documentation/](https://web.archive.org/web/20200306075330/http://www.google.com/url?q=http%3A%2F%2Fwww.gnu.org%2Fsoftware%2Fgdb%2Fdocumentation%2F\&sa=D\&sntz=1\&usg=AFrqEzdxEljzQQfxSG9jJlTFgWS7hCEk\_A)
6. Debugging with GDB, [http://www.delorie.com/gnu/docs/gdb/gdb\_toc.html](https://web.archive.org/web/20200306075330/http://www.google.com/url?q=http%3A%2F%2Fwww.delorie.com%2Fgnu%2Fdocs%2Fgdb%2Fgdb\_toc.html\&sa=D\&sntz=1\&usg=AFrqEzcat\_PmPKV\_1BBOtQzAmLS\_zW6Wlw)
7. RMS's gdb Debugger Tutorial, [http://www.unknownroad.com/rtfm/gdbtut/gdbtoc.html](https://web.archive.org/web/20200306075330/http://www.google.com/url?q=http%3A%2F%2Fwww.unknownroad.com%2Frtfm%2Fgdbtut%2Fgdbtoc.html\&sa=D\&sntz=1\&usg=AFrqEzdEPcxTyZ9czcWiDP8EHb9YzkuF7A)
8. Guide to Faster, Less Frustrating Debugging,  [http://heather.cs.ucdavis.edu/\~matloff/UnixAndC/CLanguage/Debug.html](https://web.archive.org/web/20200306075330/http://www.google.com/url?q=http%3A%2F%2Fheather.cs.ucdavis.edu%2F%7Ematloff%2FUnixAndC%2FCLanguage%2FDebug.html\&sa=D\&sntz=1\&usg=AFrqEzeMY45ofzpLoPYEtTKtGc0PiNyOug)
