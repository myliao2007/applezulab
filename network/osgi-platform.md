# OSGi 平台

Aaron Liao\
OSGi \[1]\[2]\[3]\[4] 標準是1999年由Open Service Gateway Initiative協會制定的標準，在OSGi架構圖中，Execution Environment為JVM虛擬平台，OSGi規格是依據Java平台所制定的，而Bundle由一個Java的JAR 檔案所組成，JAR主要是由Java class檔所構成，Bundle與Bundle之間可以透過Import使用其他Bundle的Export資源，而透過Export可釋出資源與其他Bundle分享。因此，只要是基於Bundle所設計的服務就能夠達到服務的資源分享。Security是基於Java2 security所制定，Module在Bundle與Bundle間使用彼此制定的Java Packages規則。而Life cycle定義了Bundle的生命週期，包括已安裝、等待啟動、啟動、執行、停止、取消安裝。Service是定義動態的合作模式，Bundle可以將本身的Service註冊於Service Registry，因此，其他的Bundle就能夠經由Service Registry獲知所需要的服務。\


<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption><p>OSGi 架構圖 </p></figcaption></figure>

\
在Bundle狀態轉換時，INSTALL狀態表示已經成功載入Bundle，RESOLVED狀態表示Bundle所需要的class皆能夠使用，代表已經載入的Bundle準備好能夠進入STARTING狀態或是剛從STOPPING狀態轉換到RESOLVED狀態。在STARTING狀態中，Bundle已經是啟動的，並且會呼叫BundleActivator.start method。在ACTIVE狀態的Bundle處於正在運作中的情況，並且已呼叫過BundleActivator.start method。STOPING狀態表示Bundle正在停止，並且已經呼叫過BundleActivator.stop method。最後UNINSTALLED狀態代表Bundle已經卸載，於是Bundle的狀態也已經無法繼續轉移。\


![](<../.gitbook/assets/image (6).png>)

bundle 狀態轉換\
&#x20;**** \
\
參考文獻\
\[1] D. Valtchev, and I. Frankov, “Service gateway architecture for a smart home”, IEEE Magazine on Communications, Vol. 40, Issue 4, pp. 126-132, Apr. 2002.\
\[2] OSGi Alliance, “OSGi Service Platform Release 3”, IOS Press, 2003.\
\[3] OSGi Service Platform, Core Specification r4, The OSGi Alliance, 2005, http://www.osgi.org.\
\[4] P. Dobrev, D. Famolari, C. Kurzke, and B.A. Miller, “Device and service discovery in home networks with OSGi”, IEEE Magazine on Communication, Vol. 40, Issue 8, pp. 86-92, Aug. 2002.
