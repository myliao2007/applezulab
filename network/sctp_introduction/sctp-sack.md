# Page 3

SCTP通訊協定預設採用選擇回覆（SACK, selective acknowledgement）之回報機制，SACK之功能主要協助接收端以確認封包資料之接收或通知傳送端之封包資料傳輸產生的區段（gaps）, 接收端將尚未接收到的TSN值記錄於SACK chunk並回送至傳送端，以通知傳送端進行新資料之傳輸或已傳送資料之重傳的動作，圖5為SCTP通訊協定之SACK chunk。

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption><p>圖5、SCTP SACK Chunk格式</p></figcaption></figure>

下列是SACK chunk中各參數的定義：

(a) Chunk Flags: 8 bits

當傳送時，全部的bits都設定為零，接收時，忽略這個欄位。

(b) Cumulative TSN ACK: 32 bits (unsigned integer)

此參數是最後一個收到的依序data chunk的TSN值。

(c) Advertised receiver window credit: 32 bits (unsigned integer)

告知傳送端更新接收端的緩衝區空間（單位：bytes）。

(d) Number of Gap ACK block: 16 bits (unsigned integer)

說明此SACK中有幾個Gap ACK blocks。

(e) Number of Duplicate TSNs: 16 bits

收到的重複TSN數目。

(f) Gap ACK block start: 16 bits (unsigned integer)

說明此Gap ACK block開始的TSN offset。

(g) Gap ACK block end: 16 bits (unsigned integer)

說明此Gap ACK block結束的TSN offset。

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption><p>圖6、SACK範例</p></figcaption></figure>

圖6是一個SCTP通訊協定的SACK chunk範例，根據SACK chunk的欄位格式，可以得知最後所收到的data chunk TSN序號值為29，而number of block=2是代表區塊（block）之數目為2，如圖中之著色區塊數目。在區塊一的offset範圍是3 \~ 4，如圖中之著色區塊TSN = 32 \~ 33，TSN start offset為3 （29 + 3 = 32）即代表 TSN = 32 為該區塊的起始位置，而TSN end offset是4（29 + 4 = 33）即代表 TSN = 33 為該區塊之結束位置。同理，位於SACK chunk第五列之區塊二，TSN start offset與TSN end offset均相同為6，代表該區塊之範圍起始與結束位置均於TSN=35（29 + 6 = 35）。透過這樣的方式，傳送端可以從SACK回覆訊息中計算出需要重送的data chunk之TSN序號值。
