# Perl & LWP



Perl and LWP 介紹的是以 LWP module 設計網頁相關的程式，可以用 Perl \[2] 設計程式自動填寫網頁表格，自動解析網頁內容、圖片等資料。而作者也提供了修正錯誤的線上版本 \[3] 供我們閱讀。Apache2 已經和 Perl 整合在一起: libapache2-mod-perl2。\
其他實用的工具：\
FastCGI \[5] 支援了 C/C++/Perl/Java 等語言的函式庫(library)。\
\
libcgi：用 C 寫 CGI 的函式庫。\
\
libwww-perl\[4] 也是用來寫網頁相關的函式庫。\
\
最好用的線上文件：man manual\


> \
> $ man LWP\
> $ man LWP::Simple\
> $ man LWP::Protocol\
> ...

\
Perl Net module 用來寫 FTP client 的範例，可以看的出來，用 Perl 寫程式很方便。\


>
>
> ```perl
> #!/usr/bin/perl -w
>
> require Net::FTP;
>
> my $host="FTP IP 位址";
> my $user="FTP 帳號";
> my $pass="FTP 密碼";
>
> $ftp = Net::FTP->new($host, Debug=> 0)|| die "can not connect to $host.";
>
> $ftp->login("$user", "$pass");
> print $ftp->message;
> $ftp->list;
> print $ftp->message;
> $ftp->quit;
> print $ftp->message;
>
> exit 0;
> ```

> \[1] Sean M. Burke, Perl and LWP, O'Reilly Inc., 2002.\
> \[2] Perl, http://www.perl.org/.\
> \[3] Perl and LWP Online 2007, http://lwp.interglacial.com/.\
> Mirror: http://lovezutto2.googlepages.com/lwpbook.zip.\
> \[4] libwww-perl, http://ftp.ics.uci.edu/pub/websoft/libwww-perl/.\
> \[5] FastCGI, http://www.fastcgi.com/drupal/.
