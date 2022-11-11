# UPnP 與 UPnP AV

Aaron Liao

UPnP \[1]\[2] 為1999年Microsoft所提出的架構，目的在於使設備之間能夠透過UPnP通訊協定知道彼此的存在，以達到Zero Configuration的目標，因此只要設備具備UPnP協定，當新增該設備於網路環境中時，就能夠使用與控制。UPnP規格定義了三項主要的元件，分別是CP (control point)、裝置 (Device) 與服務 (Service)，CP能夠找尋到目前UPnP網路中的可用裝置，並透過UPnP協定進行控制。而一個裝置可以提供多個服務，並且提供服務的資訊給CP。而服務為UPnP 網路中最小的單位，CP透過發出動作(action)參數給服務，可修改服務的狀態變數，而服務會將修改後的狀態變數提供給所有訂閱該服務的CPs。

UPnP 裝置間溝通的運作流程可分六個步驟，依序為：(1)Addressing (2)Discovery (3) Description (4)Control (5)Eventing (6) Presentation，分別描述如下：

(1) Addressing：

UPnP裝置在加入網路時，會要求取得一組網路位址，因此會先透過DHCP client發出DHCP Discovery訊息探測網路上是否有DHCP伺服器，並且向DHCP伺服器請求一組位址，若網路上沒有DHCP伺服器，則裝置將利用Auto IP自行產生一組IP位址。

(2) Discovery：

當裝置取得位址，會透過週期性的群播 (Multicast) discovery訊息來自我介紹，訊息內容包含了裝置本身的資訊與所包含的服務，所以CP就能夠透過discovery訊息得知有新的裝置提供服務。若加入網路的裝置是CP，則是透過單次群播搜尋網路中有興趣的裝置，並且得知該裝置所提供的服務。如果CP需要使用該裝置的服務，則會利用discovery訊息的內容向裝置取得XML格式的Description。

(3) Description：

CP透過裝置的discovery訊息內容取得描述裝置資訊與服務的XML檔案。

(4) Control：

透過description階段取得裝置與服務之描述檔。

(5) Eventing：

當有CP控制某個裝置並改變了該裝置中的服務狀態，該裝置需要通知已與它註冊的CPs。

(6) Presentation：

若裝置提供控制的網頁介面，則CP能夠透過此介面控制裝置。

UPnP AV

UPnP AV \[3] 基於UPnP架構對於影音傳輸所規範的標準，UPnP AV定義了CP (Control Point) 與裝置(Device)之間的互動模式。UPnP AV架構如圖所示， UPnP AV的定義中包含三項元件，分別是(a) CP (b) MS (Media Server) (c) MR (Media Renderer)，以下分別對三者進行描述：

(a) CP為協調與設定MS及MR的運作，當設定完成時，MS與MR會依照CP的設定建立連線，CP通常具備使用者介面，使用者透過此介面來設定控制MS與MR。

(b) MS主要為提供影音內容，透過家庭網路分享影音資料，使用者可以透過CP得知MS內的多媒體資料，MS可以是錄放影機、DVD撥放器、衛星/電纜接收器、CD撥放器或MP3撥放器等，MS設備上包含三種服務：CDS (Content Directory Service)、CMS (Connection Manager Service) 與AVT (AV Transport Service)。CP透過CDS服務瀏覽或搜尋MS所提供的影音目錄，其中的browse( )動作 (action)可以讓 CP得到影音內容相關的詳細資料，包含檔名、作者、檔案大小、影音的內容、影音傳輸協定與內容格式。CP 透過CMS通知MS準備一個即將開始的傳輸，若MS具有影音傳輸的服務，則透過動作回覆CP一個影音傳輸服務的識別碼(Instance ID)，用以識別多個影音傳輸服務的instance，每一個影音傳輸服務的instance 代表一個由MS到MR之間連線 (connection)，當CP要結束一個連線時，可以透過CMS的Connection Complete()動作釋放連線。AVT提供CP可以控制影音資料播放的動作，如停止播放、暫停播放、取得影片播放時間、設定影片播放時間等。

(c) MR為播放影音資料的設備，CP透過RCS (Rendering Control Service) 提供的動作控制MR撥放這些影音內容，包含設定畫面明亮度、對比、及調整音量等。

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption><p>UPnP AV架構圖</p></figcaption></figure>

如上圖所示，影音資料的內容由MS所提供，CP透過CDS服務的browse() 動作取得MS影音目錄，並透過AVT 服務將影音的URI通知MR，而CP也是透過AVT服務控制MR影音播放、暫停、停止等動作，至於影音資料的傳輸則是由MR透過Out-of-Band的方式與MS傳輸，影音資料的傳輸不會透過CP轉送。

參考文獻

\[1] Universal Plug and Play, http://www.upnp.org/.

\[2] UPnP Forum, UPnP Device Architecture 1.0, 2003.

\[3] UPnP Forum, “UPnP AV Architecture v1.0”.
