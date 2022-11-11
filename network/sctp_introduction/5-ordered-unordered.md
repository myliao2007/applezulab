# 5 Ordered/Unordered 傳送機制

SCTP通訊協定支援ordered與unordered傳輸機制，除了在同一個串流（stream）中有限制必須要嚴格遵守ordered傳遞的規則外，SCTP能夠透過將data chunk中的U旗標設定為1以Unordered模式傳輸資料。因此，當SCTP端點接收到U旗標為1的data chunk時，會跳過（bypass）依序機制 （Ordering mechanism），同時也立刻將資料傳送至上層，除非是分割過的片段（fragment）資料才需要先將資料重組完成後傳送至上層。當data chunk中的U旗標設定為1時，也就使用Unordered機制傳送資料時，傳送端並不會增加串流序號（SSN, stream sequence number）的計算，因此傳送時，在data chunk的串流欄位中並不會被指定串流序號，而接收端在收到U旗標設定為1之data chunk時，則不需要使用該data chunk的串流序號，而且需要將串流序號欄位的內容忽略。

在Grinnem \[8] 等人的研究中，他們比較SCTP通訊協定分別以非依序（unordered）傳輸與依序（ordered）傳輸模式傳輸時，觀察平均傳輸延遲的變化，根據他們實驗所得結果，非依序傳輸會比依序傳輸減少0 % 到18 % 的平均傳輸延遲時間，因此，可以表示SCTP通訊協定之非依序傳輸功能的確有助於降低HoL Blocking產生的延遲。
