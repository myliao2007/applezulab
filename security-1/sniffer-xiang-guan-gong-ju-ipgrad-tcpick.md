# sniffer 相關工具 - IPgrad, tcpick

[IPgrad](https://web.archive.org/web/20200306080003/http://sourceforge.net/projects/ipgrab) 可以解析到應用層的內容。\
\
一個能將每個讀到的封包進行解譯的軟體，能呈現相當詳細的資訊，包含應用層欄位。\
\
授權：GNU General Public License (GPL)\
作業系統需求：全部的 All BSD 平台 (FreeBSD/NetBSD/OpenBSD/、Apple MacOS X)、全部的 POSIX (Linux/BSD/UNIX-like OSes)\
程式語言：C、Unix Shell\
\
[tcpick](https://web.archive.org/web/20200306080003/http://sourceforge.net/projects/tcpick) 是個 security 的工具，可以重組所監聽到的 TCP 傳輸資料。\
\
細節請 man tcpick。\
\
Debian 安裝指令：\
\# apt-get install tcpick\
\
ㄧ些 man 中提供的範例:\
\
顯示連線狀態：# tcpick -i eth0 -C\
\
顯示 payload 與封包表頭： \
\# tcpick -i eth0 -C -yP -h -a\
\
只顯示 client 端第一個 SMTP 連線資料：\
\# tcpick -i eth0 -C -bCU -T1 "port 25"\
\
下載被動 FTP 連線的檔案：\
\# tcpick -i eth0 -wR "port ftp-data"\
\
以唯一的檔案記錄 http 資料（client 與 server 混在一起）：\
\# tcpick -i eth0 "port 80" -wRub\
\
重新導向第一次的連線到某個軟體：\
\# tcpick -i eth0 --pipe client "port 80" | gzip > http\_response.gz\
\# tcpick -i eth0 --pipe server "port 25" | nc foobar.net 25
