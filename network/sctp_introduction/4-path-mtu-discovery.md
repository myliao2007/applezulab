# 4 Path MTU Discovery

PMTU探索（Path MTU discovery）\[9, 10] 能夠讓傳輸層得知路徑之MTU限制，因而可以確保傳輸層之segment 在經過IP層時不需要分割。在IPv4網路環境中，endpoint將IP表頭之Don’t fragment 欄位標示，當路由器接收到IP表頭標示 Don’t fragment時，將不能夠對該封包進行分割，因此，當該封包的長度超過路由器下一步 (next hop) 路徑之MTU時，路由器將會回傳ICMP訊息告知該封包的傳送端，多數的路由器會在ICMP訊息中存放可傳送的IP資料長度告知傳送端，當傳送端接收到路由器的ICMP訊息時，就會再次發送一個適當長度的封包，endpoint透過週期性的不斷探測，直到封包到達接收端為止，透過這樣的方式達成path MTU的探索。而在IPv6網路環境中，由於IPv6表頭並沒有don’t fragment欄位，路由器並不負責IP層的分割工作，而是由endpoint處理。

SCTP通訊協定在使用PMTU探索與TCP通訊協定之不同點在於，SCTP具備路徑多宿的特性，因此SCTP透過PMTU之探索，在每個路徑都會得到一個PMTU，SCTP 的方式是採用所有路徑之PMTU中最小的PMTU，稱之為sPMTU。以圖7所示，Endpoint Joe進行PMTU探索後將會發現路徑 Joe-A-C-Mary 之 PMTU 值為 512 bytes，而Joe-B-D-Mary之PMTU值為1024 bytes，因此，SCTP 通訊協定會選擇最小的512 bytes作為 sPMTU 之值。當應用層要傳送4096 bytes的資料時，則會先由SCTP進行分割的動作，將資料分割為512 bytes，若是SCTP傳輸之主要路徑已經分換為Joe-B-D-Mary時，由於該路徑之PMTU為1500 bytes，因此SCTP會將已經分割過的data chunk再集合為適當大小之長度，以達到最佳效能。

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption><p>圖7、The Path MTU discovery</p></figcaption></figure>

他們的實驗比較IP分割與SCTP協定分割在封包遺失率的環境中之傳輸率（throughput）；結果以SCTP協定分割傳輸的傳輸率皆比IP分割高，原因在於當傳輸層的segment在IP層被分割成數個小碎片（fragmentations）之後，只要其中的任何一個小碎片遺失，都會導致傳輸層的整個segment需要重新傳送。
