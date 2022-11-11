# 在 bash 中設定 Proxy server

bash 要環境的設定如下

export http\_proxy="http://your.proxy.net:port" export ftp\_proxy="http://your.proxy.net:port"

依 shell 環境 加在 \~/ 目錄下的 .bashrc or .cshrc

適合 sh, bash, ksh, zsh ex .

ftp\_proxy="http://proxy.hinet.net:80/" gopher\_proxy="http://proxy.hinet.net:80/" http\_proxy="http://proxy.hinet.net:80/" no\_proxy="aa.bb.tw" export ftp\_proxy gopher\_proxy http\_proxy no\_proxy

適合 csh, tcsh ex: setenv http\_proxy "http://proxy.hinet.net:80/" setenv no\_proxy "aa.bb.tw"

需要帳號密碼 $ http\_proxy="http://username:password@host:port" $ export http\_proxy
