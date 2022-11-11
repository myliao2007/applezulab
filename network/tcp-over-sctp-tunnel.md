# TCP over SCTP tunnel

Aaron Liao

為了使SCTP通訊協定能用應用於目前之網路，SCTP tunnel proxy之設計將SCTP tunnel proxy設置於兩端TCP網路環境間，並以SCTP通訊協定傳輸資料，可將SCTP通訊協定之優點應用於現有之TCP網路環境中。

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>圖1、SCTP tunnel傳輸資料示意圖</p></figcaption></figure>



如圖1，Joe傳送之TCP封包在經過SCTP tunnel proxy時，會被封裝於SCTP chunk內傳送，直到抵達另一端SCTP tunnel proxy再封裝為TCP格式傳送給Mary。因此，兩端TCP node使用者則透過SCTP tunnel proxy之間的SCTP連線，不需更改網路設定就能夠以SCTP tunnel 傳輸，當兩端之網路環境間具備兩路以上（含）之傳輸路徑時，SCTP 路徑多宿之功能可在主要通訊線路發生中斷時，能夠以備援路徑進行資料傳輸，利用SCTP路徑多宿的功能可避免單一線路的故障而導致通訊中斷的發生。

然而，透過SCTP tunnel方式傳送封包會增加額外的header redundancy，於圖2中，當SCTP tunnel proxy轉送TCP node之封包時，轉送的資料內容包含IP header、TCP header與payload，而IP header與TCP header的基本長度總共有40 bytes。因此，為了能夠降低header redundancy，本研究於轉送封包之前，會先將封包進行拆解並擷取IP header與TCP header之重要資訊（圖10），再將這些重要資訊與payload透過SCTP chunk傳送，待送達目的端SCTP tunnel proxy時，會依據這些重要資訊將封包重新封裝還原，並透過raw socket將還原的TCP封包送至目的端TCP node。

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption><p>圖2、Redundancy of tunnel</p></figcaption></figure>

在圖3中，IP header與TCP header之Source IP、Source Port、Destination IP與Destination port於同一TCP連線過程中斷前為固定之資訊，因此SCTP tunnel proxy可於建立tunnel連線時先彼此交換資訊並記錄該連線的上述資訊。著色欄位為每次傳輸時經常使用與更動可能性的欄位，因此會將這些資訊與payload封裝於SCTP data chunk中傳輸。透過這樣的方式，在IP header的部份能夠降低75 % redundancy，TCP header的部份則能夠降低30 % redundancy，整體SCTP tunnel payload的header redundancy則平均能夠降低50 % 以上的header redundancy。

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>圖3、Header資訊</p></figcaption></figure>

在SCTP tunnel的建立流程方面，如圖4所示，TCP node（A）先送出一個設定SYN 旗標的TCP封包至TCP node（B），以開始進行建立連線的三向交握流程。而當SCTP tunnel proxy（C）偵測到TCP的SYN封包時，首先會先檢查是否已經存在tunnel可傳輸該封包至目的端點，若已經有建立完成之tunnel，則直接轉送該封包；若是沒有可使用之tunnel，則SCTP tunnel proxy將初始一個新的SCTP連線作為tunnel使用，並且記錄IP位址與連接埠號之對映（mapping）。而當SCTP tunnel proxy偵測到兩端TCP節點送出的FIN訊息時，則會終止tunnel。

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption><p>圖4、SCTP tunnel之建立與結束</p></figcaption></figure>

在TCP端初次連線時，需要等待TCP三向交握與SCTP四向交握之步驟，為了降低使用者等待時間，如圖5所示，本研究以預先建立tunnel pool的方式改善，類似作業系統中Pre-fork的概念 \[1]。在SCTP tunnel proxy之間預先建立tunnel連線，當TCP node要建立連線時，SCTP tunnel proxy則由預先建立的tunnel pool中選取未使用的tunnel給予該節點使用，如此只有當可配置的tunnel都正在使用時，才需即時建立SCTP tunnel連線。

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>圖5、預先建立SCTP tunnel</p></figcaption></figure>

SCTP tunnel proxy透過tunnel代理可使兩端TCP網路端在proxy之間以SCTP協定傳輸，而且使用SCTP tunnel proxy與現有TCP網路通訊並沒有衝突，在SCTP tunnel proxy可指定需要代理的TCP目的端，而對於無須代理的TCP目的端，SCTP tunnel proxy僅會bypass並單純路由繞送該流量封包，不作tunnel代理的處理。

**參考文獻**

\[1] W. Richard Stevens, Bill Fenner, and Andrew M. Rudoff, UNIX Network Programming The sockets networking API, Vol. 1, Adisson Wesley, Inc. ,third edition, 2004.
