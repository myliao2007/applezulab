# 設定 Debian 預設啟動服務

自動啟動服務的土砲方式是自己寫在 /etc/rc.local 或 /sbin/setup.sh 之類的地方，\
\
或者自己手動去做 symlink（從 /etc/init.d/\* 至 /etc/rc?.d/ ），以前我都手動這樣做。\
\
不過，這樣太辛苦了，應該都會有正規方法，所以下列是參考資料使用系統內建指令處理的方式，\
\
利用 update-rc.d 或 insserv 加入需要開機自動啟動的程式、反之移除之，\
\
即為將 init.d 的 script 做 S 開頭加上號碼的 soft link 到 rc\*.d 中 (\* = runlevel)，\
\
Debian 6.0 之前是使用 update-rc.d，而 6.0 以後則是使用 insserv，至於 Ubuntu 是使用 update-rc.d。\
\
啟動服務\
insserv service 或\
update-rc.d servic defaults\
\
例如，啟動 bind9， insserv bind9\
\
關閉服務\
insserv -r service 或\
update-rc.d service remove\
\
參考資料\
Peter Dave Hello's Blog, [Debian / Ubuntu 系列 GNU/Linux 服務開機自動啟動調整](https://web.archive.org/web/20200306075545/https://www.peterdavehello.org/2014/02/debian-ubuntu-startup-service-control/)
