# 2 路徑多宿 (Multi-homing)

SCTP通訊協定路徑多宿之特性允許端點與端點間的連線可透過多個網路裝置達到路徑備援的能力，當具備多路徑時，SCTP會選擇某一路徑做為資料傳輸的主要路徑（primary path），而其它的路徑則作為備援路徑（backup path），備援路徑的用途是當主要路徑發生故障或斷線時，SCTP會利用heartbeat週期性偵測各線路是否存活，因此當主要路徑發生中斷時，SCTP之端點可自動由多個備援路徑中選取其中一路做為主要路徑之替代，進而達到快速恢復（failover）的能力。

以圖4為例，host A與host B設備均具備了兩張網路介面卡，每張介面卡都能夠經由網路服務供應商（ISP）之路由器或閘道器(gateway)連結至網際網路，因此host A或host B都能夠透過各自擁有的任何一張網路介面卡傳送資料。該圖例呈現host A使用A1網路介面作為主要路徑與host B進行網路語音電話之通話，而對於host A而言，A2網路介面僅是備援路徑。當主要路徑（primary path）發生問題時，SCTP則會自動從主要路徑切換至備援路徑（backup path），避免host A與host B的通話因單一線路之故障而被迫通話中斷，增加了連線通訊的可靠度。

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>圖4、SCTP路徑多宿示意圖</p></figcaption></figure>

