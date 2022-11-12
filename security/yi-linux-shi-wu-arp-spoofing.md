# 以 Linux 實務 ARP Spoofing

Step 1. 透過 ARP spoofing 偽裝為 gateway, 致使所有流量都必須經過此 Linux PC.

Step 2. Linux PC 啟動 forward 功能

Step 3. 可於 Linux PC 進行流量管制。

防治方法：

1. 只要記得真正 gw 的 MAC 是多少？查 ARP cache 觀察是否變更即可。
2. 將 ARP table 設定 static gateway MAC.

ARP Spoofing 的威脅：

1. 偽裝成 gateway，可以考慮是否要幫忙轉送封包，不轉送則會導致 LAN 中斷。

由此延伸，bot 可以讓流量正常運作，但是潛伏在此網路中，再暗中擷取一般未加密協定的個人敏感資料，如帳號與密碼等，甚至再進行 hijack connection。

1. 偽裝成某台 host，IP 與 MAC 都一樣，所以抓不到，但這樣沒有好處，只是讓這個 IP 的 TCP connection 不斷的 reset (TCP RST) 而已。
2. 如果有對外線路，甚至可以考慮中斷真正的 gw 而偽裝與 GW 同樣的 IP 與 MAC，難以避免。

ARP spoofing 的方式避免方式？或許要嚴密從 switch port 去設定 policy。

Reference:

1. ettercap, dsniff
2. Security power tools, Oreilly Inc., 2007-2008.
