# 6. SCTP API

目前於Linux平台SCTP API \[14] 之實作有LKSCTP （Linux kernel SCTP）\[16] 與SCTPLIB \[17]，如圖8的LKSCTP的堆疊架構所示，LKSCTP將SCTP協定實作於作業系統之kernel space，應用層之網路程式使用socket API或ULP（upper layer protocol）以使用SCTP協定進行傳輸，lksctp運作於Linux作業系統之kernel space。

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption><p>圖8、LKSCTP之堆疊架構圖</p></figcaption></figure>

SCTPLIB（圖9）在作業系統中是屬於user space的設計，利用raw socket的方式於user space運作SCTP協定，以服務應用層網路程式。SCTPLIB為了能夠達到於系統內部服務多個應用層網路程式，透過UDP協定 \[20] 與SCTP daemon協同運作，SCTP daemon是負責同步排程工作以及與底層溝通的任務。以作業系統層面比較LKSCTP與SCTPLIB之SCTP API實作，由於SCTPLIB位於user space，因此在kernel space與user space間需要額外付出資料複製的成本，因此預期LKSCTP效能會較SCTPLIB佳。

<figure><img src="../../.gitbook/assets/image (7) (2).png" alt=""><figcaption><p>圖9、SCTPLIB之堆疊架構圖</p></figcaption></figure>



在Siddiqui等人的研究中 \[13]，他們進行兩個實驗比較SCTP協定實作在kernel space與user space之效能，在第一個實驗中，他們比較了SCTP協定切換路徑所需耗費的時間延遲，經由實驗結果得到，LKSCTP所耗費之時間延遲遠小於SCTPLIB，作者認為這是因為SCTPLIB需要時間與SCTP Daemon協調，因此在傳送與處理ASCONF \[15] 訊息時會增加額外的負擔；在第二個實驗中，他們分別於LKSCTP與SCTPLIB之SCTP API環境中，以檔案傳輸程式（FTP）傳輸不同資料尺寸之檔案，經由結果得知LKSCTP之運作效能均較SCTPLIB優異。

Nurul Islam \[7] 等人使用SCTPLIB之SCTP API，實驗主要的目的在分析傳輸不同的data chunk大小對傳輸效能所造成的影響，因此他們設計了兩個實驗場景，分別是設定無封包遺失與5 % 封包遺失的實驗場景，實驗一是在無封包遺失的場景中進行，先後傳輸288 bytes與488 bytes的資料，兩次傳輸由於是各自獨立進行，因此不會互相競爭，透過實驗結果分析可以得到傳輸488 bytes的平均傳輸率比288 bytes高，也就是傳輸較大的data chunk可以獲得較高的傳輸效能。實驗二是在 5% 封包遺失率的場景進行，同樣是分別傳輸288 bytes與488 bytes的資料，結果與之前的實驗相似，傳輸較大的data chunk平均能夠獲得較高的傳輸率。他們認為這樣的結果是因為在SCTP中使用Appropriate Byte Counting \[4]、Limited Transmit \[2] 與SACK等機制。由於SCTP是計算已接收的SACK回應bytes數來增加壅塞視窗的大小，因此，即使SCTP使用SACK延遲也不會減緩壅塞視窗的成長。
