# 使用 xhost

參考 [X remote HOWTO](https://web.archive.org/web/20200306075600/http://www.faqs.org/docs/Linux-mini/Remote-X-Apps.html)\
\
假設的場景 (Secnario):\
\
X server主機 IP：192.168.10.20\
Client 主機：192.168.30.10\
\
設定要將畫面呈現到何處： DISPLAY=192.168.30.10:0.0\
\
0.0 之意義:\
第一個 0 為 port, ex. 6000\
第二個 0 螢幕或顯卡之數目 0 代表第一個\
\
xhost + 192.168.10.20 允許遠端 X server 送資料過來\~\~\
\
OK, 接著在 X server 執行之程式都會顯示到 client 的螢幕上。\
\
此為 X client/server 架構之優點\
\
安全性問題語與細節可參考 [X remote HOWTO](https://web.archive.org/web/20200306075600/http://www.faqs.org/docs/Linux-mini/Remote-X-Apps.html)
