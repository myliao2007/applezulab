# Linux 系統安全管理

取消 Linux Ctrl + Alt + Del 功能：

將 /etc/inittab 中的 ca::ctrlaltdel:/sbin/shutdown -t3 -r now 註解

使 Single user mode 需要輸入密碼： 將「\~\~:S:wait:/sbin/sulogin」 增加到 si::sysint ... 之後。

限制 root 能夠登入的 TTY interface： 修改 /etc/securetty

限制登入 FTP 的 user： /etc/ftpusers 中的使用者是拒絕使用 FTP 登入的。

設定登入的帳號參數： /etc/security/access.conf 如 -:ALL EXCEPT root admin :console 表示只有 root 與 admin 可以經由 console 登入。

/etc/security/group.conf 定義了該群組的使用者能夠存取的資源

/etc/security/limits.conf 可自行定義群組的 system resource limits， 使用者可透過 ulimit 查詢。

/etc/security/times.conf 定義指定的使用者每天可以存取系統的時間

/etc/syslog.conf

lsof 可以查看所有開啟的檔案。

Psionic PortSentry 可以偵測到有人在進行掃描主機並可立即 block 以防止該人有進一步的攻擊。

Nessus (http://www.nessus.org) open source, 為 client server model, 可以進行安全掃描與 DoS 攻擊測試。

nmap: 常用的 service 掃描程式

相關的商業產品： ISS (Internet Security System: http://www.iss.net) CyberCop Scanner, NAI (Network Associates, Inc. : http://www.nai.com) http://www.sans.org/

Reference: A Survival Guide for Linux Security, Securiting Linux Step by Step, Version 1.0, http://www.sansstore.org/
