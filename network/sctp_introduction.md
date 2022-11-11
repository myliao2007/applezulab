# SCTP 通訊協定簡介

Aaron Liao (aaron@netdpi.net)

SCTP不僅在設計層面能夠改善傳統 TCP 連線安全的弱點，SCTP 之多重串流特性能夠改善 HoL Blocking 問題，並且路徑多宿之特性更是能夠增強資料傳輸之可靠度。

IETF組織於2000年十月制定了SCTP（Stream Control Transmission Protocol）通訊協定，SCTP透過四向交握（four-way handshake）的方式（圖1）建立一個初始連線（association），並經由Cookie機制作為端點之間的認證，如此的設計可避免傳統TCP三向交握（圖2）過程中易遭受服務阻斷攻擊之弱點。在連線中斷方面，TCP採用四向交握方式結束連線（圖2），當TCP連線之一方已經關閉了並且無法繼續傳送新的資料，而另一方仍保持開啟狀態並可以繼續傳送資料，即所謂之半關閉狀態（half-closed state）；而SCTP是以三向交握的方式結束連線（圖1），不提供半關閉狀態，接收端在接收由傳送端送出SHUTDOWN訊息的chunk之後，會回傳一個SHOUTDOWN-ACK封包進行回覆，等待結束連線的傳送端確認完成後便結束連線，同時傳送SHUTDOWN-COMPLETE的訊息至接收端。在一個SCTP封包中能夠包含不同的資料區塊（data chunk）與控制區塊（control chunk），而SCTP通訊協定以heartbeat封包對路徑進行偵測與回復。



<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>圖1、SCTP association之建立與終止流程</p></figcaption></figure>



<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>圖2、TCP session之建立與終止流程</p></figcaption></figure>
