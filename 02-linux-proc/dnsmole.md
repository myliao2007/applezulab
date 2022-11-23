# dnsmole

Last updated: 2011/02/22

**何為 dnsMole ?**

***

依據官網的說明，dnsMole 的目的是分析 DNS 流量，從 DNS 流量中探測 botnet 的 C\&C (Command and Control) 伺服器與受感染的主機。 這段是摘錄自官網的說明

| <p>dnsMole is designed to <em>analyse dns traffic</em>, and to potentionaly detect botnet C&#x26;C server and infected hosts. It can be used as passive sniffer, and it can analyse already sniffed network traffic dumped in .pcap file format. Algorithms implemented in this tool are based on research and can viewed in following papers:</p><p>1. Anomaly detection for DNS Servers using frequent host selection<br>2. Botnet detection by monitoring group activities in DNS traffic<br>3. Extending black domain name list by using co-occurrence relation between DNS queres<br></p><p>Since all this methods heavily depends on treshold parameters, you can define your own parameters in dnsMole configuration file and in that way increase ( or decrease :) ) chances of positive detection. dnsMole support storing black/white list in memory and in that way it can help classify hosts.</p> |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

***

**安裝**

***

1\. DNS-mole 的官網 : [Domain Name System - Mole](https://web.archive.org/web/20200306075309/http://code.google.com/p/dns-mole/) 2. 筆者的測試環境: Ubuntu Linux 10.04 3. 安裝 DNS-mole，系統需要有安裝 subversion

| svn checkout [http://dns-mole.googlecode.com/svn/trunk/](https://web.archive.org/web/20200306075309/http://dns-mole.googlecode.com/svn/trunk/) dns-mole-read-only |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |

4\. 筆者的系統在安裝過程缺少 sqlite 與 event 等函式庫，需要補安裝

| <p>sudo apt-get install libsqlite3-dev libsqlite3-0</p><p>sudo apt-get install libevent-core-1.4-2 libevent-dev libevent-1.4-2</p> |
| ---------------------------------------------------------------------------------------------------------------------------------- |

5\. 可閱讀程式碼中提供的文件獲得基本的安裝與使用說明 dns-mole-read-only/README\
\
dnsmole.conf \\

| <p><br><br>#detection based on group activity<br><br>aAnalyzeInterval 1200 -- interval for collecting dns packets for activity method<br>aDrop 5 -- remove fqdn from structure if it is queried less than n different hosts<br>aBlackSimilarity 0.8 -- report black if similarity is higher or equal<br>aWhiteSimilarity 0.1 -- report white if similarity is less or equal<br><br>#detection based on host co-occurrence<br><br>oAnalyzeInterval 600 -- interval for collecting dns packets for co-occurrence method<br>oBlackIpTreshold 0.5 -- report host is infected if ratio (queried_black/queried_all) is higher or equal<br>oWhite 0.1 -- report white if index is less or equal<br>oBlack 0.9 -- report black if index is higher or equal<br><br># Subnet where program is going to be run<br><br>nSubnet 16 -- define subnet where program is running, used for storing hosts ip in hash table<br><br>#detection based on statistics<br><br>sThresholdTotal 5 -- the threshold for the number of a host's queries/responses for a time period<br>sThresholdPTR 5 -- for the number of PTR queries<br>sThresholdMX 5 -- for the number of MX queries<br>sThresholdBalance 0.9 -- for the difference between the number of queries and responses<br>sThresholdPTRRate 0.9 -- for the rate of PTR queries<br>sThresholdMXRate 0.9 -- for the rate of MX queries<br>sClassifyInterval 30 -- period to classify queries into host's statistics<br>sAnalyzeInterval 1800 -- the interval to collect data before sending to analyze<br><br><br></p> |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |

\
\
dnsMole parameters\\

|

\
dnsMole program parameters\
\==========================\
\
\-b \<file> and -w \<file> - black/white list filename\
\-c \<file> - configuration filename\
\-l \<file> - define name of log file, if it is not used default dnsmole-log will be used\
\-i \<interface> - set interface for sniffing\
\-s - sniffer mode\
\-d - run programm in backgroun\
\-p \<file> - use .pcap file for analysis\
\-a \<interval> - it MUST be used with method 2 in pcap analysis mode, defines interval of .pcap file\
\-t <1|2|3> - analysis method\
\


\| | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

\
\
筆者註: analysis method 中的 1, 2, 3 應該對應到\
\
1\. Anomaly detection for DNS Servers using frequent host selection\
2\. Botnet detection by monitoring group activities in DNS traffic\
3\. Extending black domain name list by using co-occurrence relation between DNS queres\
\
\
\
How to use dnsmole\\

| <p><br>dnsMole requires that you must run program as root in sniffer mode, but otherwise for pcap analysis<br>it isn't required.<br><br>1. pcap analysis<br><br>~ example.pcap for analysis with method 1 and put in background use bl.txt as blacklist<br><br>$ dnsmole -c doc/dnsmole.conf -p example.pcap -b bl.txt -t 1 -d<br><br>~ example.pcap for analysis with method 2, pcap interval is n, and use bl.txt as blacklist and wl.txt as whitelist<br><br>$ dnsmole -c doc/dnsmole.conf -p example.pcap -b bl.txt -w wl.txt -t 2 -a n<br><br>2. sniffer mode ( root privileges )<br><br>~ use wlan0 as interface with wl.txt as whitelist, define log001 as output log and use method 3, put in background<br><br># dnsmole -i wlan0 -s -d -c doc/dnsmole.conf -w wl.txt -l log001 -t 3<br><br>3. combined ( root privileges )<br><br>~ first use analysis on example.pcap and than run sniffer in background<br><br># dnsmole -t 2 -a n -p example.pcap -w wl.txt -b bl.txt -s -d -i wlan0 -l dnsmoleLog<br><br><br></p> |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

\
**Port mirror 模式測試**\
\
啟動：\
\
1\. 建立設定檔 /etc/dnsmole/dnsmole.conf (可使用 source code 中的 doc/dnsmole.conf 範本)\
2\. 指定流量介面: -i eth1\
2\. 建立黑名單網域 bl.txt，白名單網域 wl.txt\
\
bl.txt 跟 wl.txt 的格式是一行一筆 domain name，如 www.ncku.edu.tw\
\
3\. mode 1 ( -t 1)。\
4\. 啟動 sniffer 模式：-s

| <p><br># dnsmole -c /etc/dnsmole/dnsmole.conf -i eth1 -b bl.txt -w wl.txt -t 1 -s<br></p> |
| ----------------------------------------------------------------------------------------- |

\
若是流量來自 .pcap 檔案，可使用 -p file\_name.pcap 指定檔案。
