# GNU C 函式庫常見問題（FAQ）

#### 本文試著回答使用者在安裝與使用 glibc 時可能遭遇的問題，請您在發問或回報 bug（程式錯誤）給維護者之前，先確定您已經讀過這份文件。 <a href="#h.fyxd9ygum0rw_l" id="h.fyxd9ygum0rw_l"></a>

GNU C 函式庫很複雜，安裝的過程還沒有完全的自動化；有太多變數，沒有正確的安裝函式庫會對系統造成實質的傷害，在您開始之前先確定您已經了解你能承擔這一切。

**\[編譯 glibc]**

**GNU C 函式庫可以在哪些系統上面運作呢？**

細節請參考 [README](http://www.google.com/url?q=http%3A%2F%2Fsourceware.org%2Fgit%2F%3Fp%3Dglibc.git%3Ba%3Dblob\_plain%3Bf%3DREADME%3Bhb%3DHEAD\&sa=D\&sntz=1\&usg=AOvVaw317Od\_yIt0XnECP6-bYMp6) 檔案。

GNU C 函式庫能支援使用 Linux kernel 的這些組態（configurations）：

i\[4567]86-\*-linux-gnu

x86\_64-\*-linux-gnu

powerpc-\*-linux-gnu Hardware floating point required

powerpc64-\*-linux-gnu

s390-\*-linux-gnu

s390x-\*-linux-gnu

sh\[34]-\*-linux-gnu Requires Linux 2.6.11 or newer

sparc\*-\*-linux-gnu

sparc64\*-\*-linux-gnu

另外的組態是 ports 目錄的一部分，細節請參考 [README](http://www.google.com/url?q=http%3A%2F%2Fsourceware.org%2Fgit%2F%3Fp%3Dglibc.git%3Ba%3Dblob\_plain%3Bf%3DREADME%3Bhb%3DHEAD\&sa=D\&sntz=1\&usg=AOvVaw317Od\_yIt0XnECP6-bYMp6)。

**我需要什麼工具來建立 GNU libc 呢？**

您會需要：

GCC, C 與 C++ 編譯器都要［供 testsuite 用途］

GNU binutils

GNU make

Perl

GNU awk

GNU sed

在 Linux 上：Linux kernel header（表頭）檔

修改 glibc 的開發者可能會另外需要：

gperf

GNU autoconf

GNU gettext

GNU texinfo

更多細節請參考使用手冊的 “[Tools for Compilation](http://www.google.com/url?q=http%3A%2F%2Fwww.gnu.org%2Fsoftware%2Flibc%2Fmanual%2Fhtml\_node%2FTools-for-Compilation.html\&sa=D\&sntz=1\&usg=AOvVaw2t7C2zg9IuVHIjRTMfcVuG)” 章節，或者閱讀 glibc 原始碼中的 INSTALL 檔案。

**會用到哪些版本的 Linux kernel header 呢？**

Linux kernel 最近的 header 都應該用的到。在編譯 GNU C 函式庫所用到的 header 版本，與使用函式庫所需的 kernel binary（核心執行檔）版本不用一樣。在 kernel 版本比所用的 kernel header 版本還要舊時，GNU C 函式庫是可以正常運作的。反之［用比較舊版的 kernel header 來編譯，並跑較新版的 kernel］則不會如所預期的運作。比如：假設你使用舊版的 kernel header 來編譯 GNU C 函式庫時，你就不能使用新的 kernel 功能。

即使你的系統是使用比較舊的 kernel，我們還是建議你用最新的 kernel header 來編譯 GNU libc。這樣如果你平時想要升級 kernel 版本的話，你就不用重新編譯 libc 了。為了要讓 libc 知道要使用哪一個 header，需要設定 –with-headers 進行切換［例如：–with-headers=/usr/src/linux-3.3/include］。

為了安裝 Linux kernel header，在 kernel source 的樹狀目錄中執行 make header\_install，這在 [kernel 文件](http://www.google.com/url?q=http%3A%2F%2Fgit.kernel.org%2F%3Fp%3Dlinux%2Fkernel%2Fgit%2Ftorvalds%2Flinux.git%3Ba%3Dblob\_plain%3Bf%3DDocumentation%2Fmake%2Fheaders\_install.txt%3Bhb%3DHEAD\&sa=D\&sntz=1\&usg=AOvVaw2FjDKd\_AW4FnP5trwzf6yQ)中會有說明。

**當我在所產生的函式庫上執行 \`nm -u libc.so’ 時，我仍然會有 unresolved symbols（無法解析的符號），這樣正常嗎？**

是的，這是正常的。會有很多種無法解析的符號：

由 linker（連結器）自動產生的 magic symbols（魔術符號）。他們的名稱會類似 \_\_start\_\* 與 \_\_stop\_\*

從 dynamic linker（動態連結器）產生的是以 \_dl\_\* 開頭的符號

weak symbols（弱符號），不需要全部解析（例如 fabs）

一般而言，你要保證你有找到一個真實的程式，它在認定有問題之前就進行 linking（連結）會產生錯誤。

**什麼是所謂的 \`add-ons’（附加元件）？**

為了延伸 glibc，因而有了以不同 packages（套件）發佈的額外 add-ons（附加元件）。目前 nptl、libidn add-ons 是 glibc 的一部分，而 ports add-on 則是另一個 package。

如果要將這些 packages 當作 GNU libc 的一部分來使用的話，只要解開 libc 原始碼目錄中的 tar 檔，並使用 –enable-add-ons 選項來設定組態檔 script。若你只有給 –enable-add-ons 組態，則會試著找出你原始碼目錄中的全部 add-on packages。若你只是想要選擇 add-ons 的子集合時，請用逗號隔開所要啟用的 add-ons 清單：

例如：configure –enable-add-ons=nptl,libidn

Add-ons 可以新增功能［包含全部新的共享函式庫］、覆蓋檔案、對額外的架構提供支援、以及任何相關的事情。現有的 makefiles 處理了多數的工作；只需要重寫一些少數的 stub rules，就能讓全部的事情都能正常運作。

多數的 add-ons 是對特定的 GNU libc 版本緊耦合（tightly coupled），請檢查你用來與 add-ons 一起運作的 GNU libc 版本。

在 glibc 2.2 的 crypt add-on 以及 glibc 2.1 的 localedata add-on 都已經被整合到一般的 glibc distribution 了。crypt 與 localedata 因此不再出 add-ons。還有，linuxthreads 的 add-on 已經過期了，請改用 nptl。

**我的 kernel 幫我模擬了一顆浮點數（floating-point）的 coprocessor，那我還需要啟用 –with-fp 嗎？**

只有某些平台會有相關的問題，如 PowerPC 或 MIPS。GNU libc 的組態必須與你的編譯器所用的 ABI 一致：兩者都要使用同樣的方式設定。

一顆模擬的 FPU 跟真的一樣好，就我所知，C 函式庫與編譯器是會在意的，如果你的機器沒有辦法執行浮點數指令的話，你只需要設定 –without-fp，並依此設定你的編譯器。

喜歡榨乾機器效能的人會想要避免因這麼做而產生的 trap overhead（負荷）。

**為什麼我在使用 librt 時出現了 missing thread functions（找不到執行緒函式）的訊息呢？我根本沒有用 threads。**

這個時候你可能已經搞爛你的系統了，librt 會在內部使用 threads，因而間接隱含的參考到 thread 函式庫。通常這些條件都會自動滿足，不過如果 thread 函式庫不在預期的地方時，你就必須要跟 linker 說 thread 函式庫在哪裡，使用 GNU ld 執行的樣子會類似這樣：

gcc -o foo foo.c -Wl,-rpath-link=/some/other/dir -lrt

/some/other/dir 應該要包含 thread 函式庫，在沒有任何其它的 link 路徑時，ld 會使用所給的路徑（path）去找出隱含參考的函式庫。

**我在 \`make check’ 的過程失敗了，我應該怎麼辦呢？**

testsuite 應該要在你的系統上乾淨地編譯與執行；每次的執行失敗都應該要找出來。如果測試失敗了，你或許一點都不該安裝這個函式庫。

你應該要在 [bugzilla](http://www.google.com/url?q=http%3A%2F%2Fsourceware.org%2Fbugzilla%2F\&sa=D\&sntz=1\&usg=AOvVaw0V90GbUO2E207mmNQXG24h) 中提出研究報告，並盡可能提供詳細的資料。若你要直接進行一個測試，請記得正確地設定環境。如果你想要測試已編譯好的函式庫，而不是你安裝的函式庫，最好的方式是準確複製失敗的命令列指令，並從原始碼中的子目錄執行測試。

有一些沒有與 GNU libc 直接相關的失敗原因：

有些編譯器會產出 buggy（充滿 bugs）的程式碼，在 Alpha 平台上，沒有編譯器能正確得到單精度的複數。然而，gcc-3.2 應該沒問題。

kernel 可能會有 bugs，比如 tst-cpuclock2 test 需要修正為 Linux 3.1（[patch](http://www.google.com/url?q=http%3A%2F%2Fsourceware.org%2Fml%2Flibc-alpha%2F2012-02%2Fmsg00468.html\&sa=D\&sntz=1\&usg=AOvVaw1pToQsvejlZWgeyT\_rTOc2)）。

**什麼是 symbol versioning（符號版本）的好處呢？我會需要它嗎？**

Symbol versioning（符號版本）解決的問題是與 interface（介面）的改變有關。一個 interface 的某個版本可以在先前的 GNU C 函式庫版本中引導，但是 interface 或函式的 semantics（語意） 在這期間已經改變了。為了二進位檔案能與舊版相容，新版的函式庫仍然需要幫舊版程式保留原本的 interface。另一方面，新版程式應該使用新的 interface。Symbol versioning 解決了這個問題，GNU libc 預設使用 symbol versioning，除非透過 configure switch（組態切換）將它關閉。

我們奉勸大家要使用 symbol versioning，不然你會發生二進位檔案的不相容－會永遠不相容！你不僅會與之前 GNU libc 版本的二進位檔案不相容，也會與之後全部的版本不相容。這表示你不能執行別人編譯的程式。

**我該如何在我的快速 ix86 電腦上編譯一個 libc 給較慢的 ix86 電腦使用呢? 在安裝 libc 以後，程式會出現 “Illegal Instruction”（不合法的指令）訊息並結束。**

glibc 與 gcc 會在你的電腦上產生一些舊電腦沒有的指令，你需要告訴 glibc 你正在設定的電腦類型，比如你想要設定給 i586 的電腦時，新增 i586。例如：

../configure –prefix=/usr i586-pc-linux-gnu

而且如果你要告訴 gcc 只產生 i586 的程式，只能增加 \`-mcpu=i586′ 到你的 CFLAGS 中（只用 -m586 沒有效果）。

要注意的是，i486 是最舊的支援架構，因為 nptl 需要 atomic（原子）指令，而這些指令是從 i486 開始支援。

**當第一次執行 rpcgen 時，\`make’ 失敗，是發生什麼事呢？我該怎麼修正呢？**

第一次呼叫 rpcgen，即第一次使用最近編譯的 dynamic loader（動態載入器）。若 dynamic loader 有任何問題，比較像是執行 rpcgen 失敗，這裡有許多問題。

唯一的真實解法就是對 loader 進行除錯，並由你自行定義問題。請記得，對於每個架構而言，需要各種 patches（補丁）讓 glibc HEAD 能轉為可執行狀態，最好的行動方針就是定義你是否具有全部所需的 patches。

**為什麼我試著用 GNU CC 編譯 GNU libc 時會得到錯誤訊息：\`#error “glibc cannot be compiled without optimization”‘（＃錯誤 glibc 不能在沒有最佳化之下編譯）？**

有幾個理由可以說明為什麼 GNU C 函式庫在非最佳化編譯之下，無法正確地工作。

在 dynamic loader (\_dl\_start) 啟動初期時，還沒有進行 PLT relocation（重新定位）以前，你不能進行函式呼叫，你必須 inline 你在初期啟動期間會用到的函式，或者呼叫 ［compiler builtins（編譯器內建）］（\_\_builtin\_\*）。

沒有啟用最佳化的 GNU CC 將不能 inline 函式，dynamic loader 在啟動初期時會透過 unrelocated（尚未重新定位的）PLT 與 crash 進行函式呼叫。

很難在未經查核 dynamic linker 程式碼的情況移除這項需求。

其它的理由是因為，在許多案例中的巢狀函式必須 inline，以避免 executable stacks。

在實務上，在編譯時沒有理由不做最佳化，因此，我們要求 GNU libc 在編譯時都要啟用最佳化。

**安裝與設定議題**

**我該如何設定 GNU libc，讓必須的函式庫，如 libc.so 可以放在 /lib，而其它的放到 /usr/lib 呢？**

如同其它全部的 GNU packages，GNU libc 的設計就是使用基底目錄（base directory），並將全部的檔案都安裝到這裡。預設是 /usr/local，因為這樣是安全的（如果安裝在那裡，它不會危及系統）。若你想要安裝 GNU libc 做為系統上的主要 C 函式庫，只要將基底目錄設定為 /usr（如：執行 configure –prefix=/usr ）。

有些系統，像 Linux 有一個檔案系統標準，它在基本的函式庫與其它函式庫間會產生差異。基本的函式庫放置於 /lib，因為這個目錄是要用來在如 / 的同一個磁碟分割區上定位。/usr 子樹可以在其它的分割區/磁碟上找到。若你在 Linux 設定了 –prefix=/usr，那麼就會自動進行。

想要在非 Linux 的系統上將 GNU libc 基本函式庫安裝在系統上的 /lib 時，必須要明確的要求。這裡的 Autoconf 沒有參數，所以你必須使用 configparam’ 檔案（細節請參考 INSTALL’ 檔案）。它包含了：

slibdir=/lib

sysconfdir=/etc

第一行指定基本函式庫的目錄，而第二行指定系統組態檔的目錄。

**我需要使用 GNU CC 來編譯用到 GNU C 函式庫的程式嗎？**

理論上不用，linker 不會管它，但是在使用 GNU CC 的 C 語言擴充之前，要檢查 GNU CC 所需的 header。

然而，目前 glibc 並沒有對預設使用其它編譯器的系統提供移植，所以沒有人能用其它編譯器測試 headers，因此你會遇到困難，如果你遇到困難了，請以 bugs 的方式回報。

還有，在一些地方，GNU 擴充在程式碼的品質上提供了極大的好處，例如：函式庫已經人工最佳化，對某些字串函式的組合語言版本做了 inline，這些都只有 GCC 可以用。

**我透過查詢共用的 libc 檔案，沒有找到這些函式：\`stat’、\`lstat’、\`fstat’、以及 \`mknod’，且在我的 Linux 系統 linking 時有錯誤訊息。要怎麼樣才能正常運作呢？**

\


你信不信，stat 與 lstat（以及 fstat 跟 mknod）在 libc.so.6 中都能預期是 undefined references（未定義的參考）！你的問題或許是因為 /usr/lib/libc.so 遺失或不正確；要注意的是，現在這是一個小的文字檔，而不是 libc.so.6 的 symlink（符號連結）。它看起來像這樣：

GROUP ( libc.so.6 libc\_nonshared.a )

**使用 libc 的程式會有翻譯過的訊息，但是其它的行為就沒有本土化（localized）［如：collating order（核對順序）］；這是為什麼呢？**

會自動安裝已翻譯的訊息，但是控制其它行為的 locale database（本土化資料庫）則不會翻譯，在你執行\`make install’以後，你需要執行 localedef 來安裝這個資料庫。例如：設定French Canadian locale（法裔加拿大本土化）只要簡單的執行這個指令：

localedef -i fr\_CA -f ISO-8859-1 fr\_CA

細節請參考程式碼目錄中的 localedata/README。

**我要怎麼幫 NSS 建立資料庫呢？**

若你在 /etc/nsswitch.conf 裡有一筆 “db” 的資料，你應該也要建立資料庫檔案，glibc 原始碼包含一個 Makefile，可以處理所需的轉換與呼叫以建立那些檔案，檔案位在 nss 子目錄中的 db-Makefile，而你能用 \`make -f db-Makefile’ 來呼叫它。請注意，不是全部的服務都能使用資料庫。

**我無法接受連靜態連結的程式都需要用到一些共享函式庫。我能做什麼呢？**

NSS（關於細節只需輸入\`info libc “Name Service Switch”‘）沒有共享函式庫就無法正常運作，NSS 可以只改變一個組態檔就能使用不同服務（如：NIS、檔案、db、hesiod），而不用重新連結任何程式。但是唯一的缺點是目前的靜態函式庫會需要存取共享函式庫，這個會由 GNU C 函式庫透明化處理。

一個解決方案是以 –enable-static-nss 設定 glibc，在這個例子中，你可以建立一個靜態的執行檔，只能用 dns 與檔案服務（這麼做需修改 /etc/nsswitch.conf），你需要對全部的服務進行明確地連結。例如：

gcc -static test-netdb.c -o test-netdb \\

\-Wl,--start-group -lc -lnss\_files -lnss\_dns -lresolv -Wl,--end-group

這個方法的問題在於，你必須使用全部的那些函式來連結每個使用 NSS routines（常式）的靜態程式。

事實上，不要再說用這個選項所編譯的 libc 是使用 NSS 了，不會再有任何的 switch（切換）。因此，強烈建議不要使用 –enable-static-nss，因為這會讓程式在系統中的行為不一致。

**我需要開啟很多檔案，我必須做什麼事呢？**

首先，這是個 kernel 議題，kernel 在 OPEN\_MAX 定義同時開啟檔案的數目，並用 FD\_SETSIZE 定義使用的 file descriptors（檔案描述子）數目。你需要改變 kernel 中的這些值，並重新編譯 kernel，如此一來 kernel 才會允許開啟更多檔案。你不用重新編譯 GNU C 函式庫，因為在函式庫本身中，唯一真正需要 OPEN\_MAX 與 FD\_SETSIZE 的地方是 fd\_set 的大小，這個只有 select 會用到。

GNU C 函式庫現在是 select free（免用 select）的，這表示它的內部不會受限於 fd\_set 型別，將每個需要這個功能的地方改用 poll 函式。

若你在 kernel 中增加 file descriptors 的數目，你就不需要重新編譯 C 函式庫。

你可以在任何時候取得一個 process 允許開啟的 file descriptors 最大數量，使用：

number = sysconf (\_SC\_OPEN\_MAX);

即使 kernel 的限制改變了，這個方法也能正常運作。

**為什麼從不將 glibc 安裝在 GNU/Linux 系統的 /usr/local 資料夾中呢？**

GNU C 編譯器用特殊的方式來看待目錄 /usr/local/include 與 /usr/local/lib，會在系統目錄之前先搜尋這些目錄，因為在 GNU/Linux，系統目錄 /usr/include 與 /usr/lib 包含一個 — 可能不一樣的 — glibc 版本，且混合了其它不受支援及將中止的 glibc 版本某些檔案，這將帶來損壞你整個系統的風險。若你想要測試所安裝的 glibc，以 –prefix 並使用其它的目錄做為參數；若你想要將這個 glibc 版本安裝為預設的版本，使用 –prefix=/usr 覆蓋掉現有的檔案，而全部的東西都會安裝到對的地方。

**原始碼與二進位檔案不相容**

**\`connect’, \`accept’, \`getsockopt’, \`setsockopt’, \`getsockname’, \`getpeername’, \`send’, \`sendto’, and \`recvfrom’ 的原型在 GNU libc 與我在其它系統所見到的不一樣。這是 bug，不是嗎？**

不是的，這不是 bug。GNU libc 已遵循 Single Unix 規格（而我認為 POSIX.1g 草案採用了這個解決方案），描述大小的參數型別是 socklen\_t。

**為什麼不再呼叫訊號中斷系統（signals interrupt system）呢？**

GNU libc 在 signal() 預設是使用 BSD semantics，不像 Linux libc 5，它使用 System V semantics。這一部分相容於其它的系統，而另一部分是由於 BSD semantics 有助於讓 signal 程式設計比較簡單。

有三個差異之處：

在系統呼叫中發生的 BSD 風格 signal 不會影響到系統呼叫；而 System V signals 會讓系統呼叫失敗，並將 errno 設定為 EINTR。BSD signal handlers（訊號處理常式）在觸發之後依然還裝載著，而 System V signal handlers 確只會執行一次，所以每次執行過後都必須要重新安裝。BSD signal 在執行本身的 handler 期間會發生 block，換句話說，不用擔心其它的 SIGCHLD 會中斷 SIGCHLD 的 handler［舉例］，然而，這樣會被其它的 signals 中斷。在使用 signals 進行完好程式設計（casual programming）的一般共識是傾向 BSD semantics 的。你不用擔心系統呼叫會傳回 EINTR，而且也不用擔心與一次性 signal handlers 有關的 race conditions （競速狀況）。

若你正在移植使用舊 semantics 的程式，你可以將全部的 signal() 改變為 sysv\_signal()，以快速修正問題。此外，在引用 \<signal.h> 之前要先定義 \_XOPEN\_SOURCE。

對於新版的程式，sigaction() 函式可以讓你精確地指定要讓 signals 如何運作。上面所述的三個差異點，可以使用這個函式以基於個別 signal 分別獨立切換。

若你只是需要一個特定 signal，可以讓系統呼叫失敗並傳回 EINTR［例如：實作一個 timeout］，那麼你可以用 siginterrupt() 來做。

**我在編譯使用特定字串函式的程式時收到錯誤，為什麼？**

glibc 有特殊的字串函式，可以比一般的函式庫函式還快。部分函式另外以 inline 函式實作，而有些用 macros（巨集）實作，這樣可能會導致現有的程式碼出問題，但是這是由 ISO C 明確許可的。

最佳化過的字串函式只有用在以最佳化編譯時（-O1 或更高），行為能用兩個 feature macros（功能巨集）來改變：

\_\_NO\_STRING\_INLINES：不要進行任何的字串最佳化。

\_\_USE\_STRING\_INLINES：使用組合語言 inline 函式（可能會明顯地增加程式碼的大小）。

因為這些字串函式有一些現在已經另外定義為 macros，類似 “char \*strncpy();” 的程式碼不再能正常運作（而且不需要，因為 \<string.h> 有所需的宣告），只能選擇改變你的程式碼或定義 \_\_NO\_STRING\_INLINES。

在這裡的另一個問題是 gcc 在 registers（暫存器）很少的機器上（如：ix86）仍然會有一些問題，inline 組譯碼幾乎會用到全部的 registers，且 register allocator（暫存器配置者）不能一直處理這種情況。

一種方法是選擇性的關閉字串最佳化，或者是改寫成：

cp = strcpy (foo, "lkj");

一種寫法是：

cp = (strcpy) (foo, "lkj");

這會關閉特定呼叫的最佳化。

**我在使用 stdin/stdout/stderr 時收到編譯器的訊息 “Initializer element not constant” （初始者成員不是常數）。為什麼呢？**

類似這樣的寫法:

static FILE \*InPtr = stdin;

會導致發生這個訊息，在 glibc 這是正確的行為，因為 stdin 不是常數表示，請注意，ISO C 的嚴格讀取並不接受上述的寫法。

這樣的其中一個好處是你能指定給 stdin、stdout 與 stderr，就像任何其它的全域變數（global variable）［例如：stdout = my\_stream;］，這樣會很有用，你可以用 libio 來寫自訂的 streams。［但是要注意，這個不必是可移植的］。以這個方式實作的理由是 FILE structure 大小的版本問題。

為了修正那些問題，你要在執行期就初始化變數，比如說可以這麼做，例如：在 main 中，像這樣：

static FILE \*InPtr;

int main(void)

{

InPtr = stdin;

}

或者以 constructors［要注意這是 gcc 才有的］：

static FILE \*InPtr;

static void inPtr\_construct (void) \_\_attribute\_\_((constructor));

static void inPtr\_construct (void) { InPtr = stdin; }

**我在 \`gcc -ansi’ 時出現一些錯誤，glibc ANSI 不相容嗎？**

GNU C 函式庫相容於 ANSI/ISO C 標準，若你使用 \`gcc -ansi’，glibc 會遵循標準 incldue 標準所規範的。ANSI/ISO C 標準定義了要 include 的檔案，並也說明在 include 檔案中不該

有任何東西［順便提一下，你仍然可以使用某些功能旗標來啟用額外的標準］。

GNU C 函式庫符合 ANSI/ISO C，若且唯若你只使用標準的 headers 與函式庫函式。

**我都不能存取某些函數了，nm 顯示它們存在，不過連結失敗。**

在 glibc 2.1 的版本介紹中，可能只匯出這些應用程式與 glibc 其它部分真正正需要的 identifiers［函式、變數］，許多 internal interface（內部的介面）目前都隱藏起來了。nm 仍然會顯示這些 identifiers，但是會將它們標示為 internal。ISO C 談到，以底線開頭的 identifiers 對 libc 而言都是 internal。應用程式一般應該不會使用這些 internal interface［有些例外，比如：\_\_ivaliduser］。若程式用到這些 interfaces，它會發生 broken（損壞）。這些 internal interface 在 glibc 釋出的版本間可以改變，或者完全捨棄。

**sys/sem.h 檔案缺少 \`union semun’ 的定義。**

不是的，這個 union 必須由使用者的程式提供。以前的 glibc 版本定義了這件事，但是它是個錯誤，因為想一想其實它很不合理，描述 System V IPC 函式的標準定義了它，因此程式必須要採用。

**當我對 setmntent() 傳回的 FILE\* 呼叫 fclose()時，我的程式發生 segfaults（記憶體區段錯誤），這是 glibc 的 bug 嗎？**

不，別這麼做。使用 endmntent()，這就是它的用途。

一般而言，你應該使用正確的解除配置機制（deallocation routine），例如：若你使用 fopen() 開啟檔案，你應該使用 fclose() 解除配置 FILE\*，而不是 free()，即使是 FILE \* 也是個指標。

在 setmntent() 案例中，它可以在多數案例中運作，但是它不一定都能正常運作。無疑地，為了相容性的理由，我們不能將 setmntent() 的回傳型別改為 FILE \* 以外的東西。

**我收到 “undefined reference to \`atexit'”。**

這表示你的安裝幾乎失敗了，這種情況與 stat()、fstat()等一樣［參考問題 2.7］。研究為什麼 linker 不會使用 libc\_nonshared.a。

若在執行期產生了類似的訊息，這表示應用程式或 DSO 沒有連結到 libc。這樣會產生問題，因為 atexit() 不再匯出了。

**雜項**

**我該如何正確地設定 timezone 呢？**

首先你要自己安裝 timezone 資料庫，它架設在 [http://www.iana.org/time-zones](http://www.google.com/url?q=http%3A%2F%2Fwww.iana.org%2Ftime-zones\&sa=D\&sntz=1\&usg=AOvVaw1QKjoooPjbuVTRkUvI3ZGQ)。

然後，直接執行 tzselect shell script，建立一個 /etc/localtime symlink（符號連結）指向 /usr/share/zoneinfo/NAME［NAME 是 tzselect 的傳回值］，回答問題並使用後面印出的名字。就這樣，你不用再擔心了。取代系統全部的 /etc/localtime\}} 設定，你也可以設定 \{{{TZ 環境變數。

GNU C 函式庫支援擴充的 POSIX method，用來設定 TZ 變數，這個記錄在 [使用手冊](http://www.google.com/url?q=http%3A%2F%2Fwww.gnu.org%2Fsoftware%2Flibc%2Fmanual%2Fhtml\_node%2FTZ-Variable.html%23TZ-Variable\&sa=D\&sntz=1\&usg=AOvVaw067GIMV1tillEoexU76YoM)。

**有哪些關於 glibc 的文件來源呢？**

glibc 手冊是 glibc 的一部分，它能由此下載：[線上](http://www.google.com/url?q=http%3A%2F%2Fwww.gnu.org%2Fsoftware%2Flibc%2Fmanual%2F\&sa=D\&sntz=1\&usg=AOvVaw3hGuqLko2kHL0OIUrkpJof).

[Linux man-pages project](http://www.google.com/url?q=http%3A%2F%2Fwww.kernel.org%2Fdoc%2Fman-pages%2F\&sa=D\&sntz=1\&usg=AOvVaw1RxqJLlPMUeyARmsgobSsq) 有關於 Linux kernel 與 C 函式庫 interfaces 的文件。

glibc 的官方網站在：[http://www.gnu.org/software/libc](http://www.google.com/url?q=http%3A%2F%2Fwww.gnu.org%2Fsoftware%2Flibc\&sa=D\&sntz=1\&usg=AOvVaw0atcGz1nS5pXGxgiTMtufn)。

glibc wiki 在[http://sourceware.org/glibc/wiki/HomePage](http://www.google.com/url?q=http%3A%2F%2Fsourceware.org%2Fglibc%2Fwiki%2FHomePage\&sa=D\&sntz=1\&usg=AOvVaw2dgfLYBInael2z5Lt\_yGxL)。

對於 bugs，glibc 專案使用 ‘glibc’ 元件的 [sourceware bugzilla](http://www.google.com/url?q=http%3A%2F%2Fsourceware.org%2Fbugzilla%2F\&sa=D\&sntz=1\&usg=AOvVaw0V90GbUO2E207mmNQXG24h)。

**我該如何找出我現在使用的 glibc 是哪個版本呢？**

若你想要從命令列簡單的找出所執行的 libc 執行檔版本，這或許不是每個平台都能做到，但是能用簡單的方式定位出 libc 共享函式庫，並以應用程式啟用，在 Linux 類似這樣：

/lib/libc.so.6

這將會產生全部你所需要的資訊。

一定能正常運作的方法是用 glibc 所提供的 API，編譯並執行下列的小程式就可以取得版本資訊：

\#include \<stdio.h>

\#include \<gnu/libc-version.h>

\


int main (void)

{

puts (gnu\_get\_libc\_version ());

return 0;

}

如果需要這個的話，這個 interface 也能用來在執行期執行測試。

**從 signal handlers（訊號處理常式）中使用 setcontext() 不會正常進行 Context switch。**

**XXX: 接下來還是對的嗎？**

setcontext() 的 Linux 實作［IA-64、S390 等］只支援同步的 context switches，這麼做有幾個理由：

UNIX 沒有提供有效同步 context switch［所謂的 co-routine switch］的其它［可移植］方法。有些版本透過 setjmp()/longjmp() 支援這項功能，但是這不是通用的方法。

如同 UNIX ’98 標準所定義的，唯一的方法 setcontext() 可以觸發非同步的 context switch，即若這個函式由 ucontext\_t 指標以第三個參數傳遞給 signal handler 時被呼叫。但是依據 draft 5、XPG6、XBD 2.4.3，setcontext() 不是在可以由 signal handler 呼叫的 routines 集合中。

若 setcontext() 用在非同步 context switches 中，各種同步與 re-entrancy 的議題都會產生，且這些問題已經由真正的多執行緒函式庫［如：POSIX 執行緒］解決了。同步 context switching 可以完全在使用者層（user-level）實作，且需要儲存/回存的狀態比非同步 context switch 要少。因此，可以用它來辨別兩種 context switches 的差異，的確，一些應用程式的供應商都知道要使用 setcontext() 在一般的［heavier-weight］pre-emptable 執行緒上實作 co-routines。

應該要注意的是，若有人對 signal handler 的第三個參數上使用 setcontext()，那麼 IA-64 Linux 可以特過特殊的 sigaction() 版本來支援這個功能，sigaction() 讓全部的 signal handlers 在一個 shim 函式中開始執行，shim 函式負責在呼叫真正的 signal handler 以前儲存保存的 registers，並在之後將它們回存。換句話說，我們能提供一個相容層，用來支援非同步 context switches 的 setcontext()。然而，我不認為上面所給的參數是合理的。setcontext() 提供了一個良好的 co-routine interface，而且我們應該只要勸阻任何非同步的使用［這只會招來麻煩］。

**尚未完成的新 FAQ 記錄**

下列的記錄並非 glibc git repository 中現有 FAQ 的一部分。請自由新增這邊的記錄，爾後將會被移到適當的地方。

**libm 函式的準確度目標是多少呢？**

請參考 [libc-alpha message](https://www.google.com/url?q=https%3A%2F%2Fsourceware.org%2Fml%2Flibc-alpha%2F2013-05%2Fmsg00132.html\&sa=D\&sntz=1\&usg=AOvVaw1DZgWrJY4cNSrSvMwFOaJP)，它詳細的討論了目標。除了類似 sqrt、fma 與 rint 這些用來綁到特定 IEEE 754 操作的函式，以及完整定義來正確地對全部的 rounding modes（對齊模式）進行對齊的結果（包含產生的例外），libm 函式不會特地正確地對齊，在 lulp 以下不會特地產生錯誤（對某些輸入可以有高達部分 ulp 的錯誤），且在底層數學函式是 monotonic （單調）的區域（regions）不會特定是 monotonic。一個綁定到 IEEE 754-2008 的 C 集合草案正在進行中，為了正確地對齊函式，期待將部分（TS 18661-4）定義成名為 crsin 的標準，且在未來的 glibc 能提供類似這類命名的函式。

**為什麼 libm 函式在一些輸入（inputs）中很慢呢？**

GNU C 函式庫包括一個數學函式庫，IBM 贊助了數量可觀的程式碼。IBM 程式碼使用特殊的演算法來幫特定數學函式的輸入計算近似結果。在一些案例中，為了提供精準的最終結果，中繼在運算期間的運算結果也需要有高精準度。實際上有很多學術研究想要證明，最大的精確度需要從中繼結果產生給予精確度的輸出［這些證據通常是依據每個函式而定］。若中繼結果所要求的較高精準度是硬體支援的，則函式會模擬所要求的高精確度。若你需要 100 個 bits，你一起聯合足夠的整數來模擬 100 個 bits，並使用特殊的演算法對那些大數進行操作以取得結果。最後，100 個 bits 的結果會向下對齊 float（浮點數）的大小，或者是 long double，這由所呼叫函式決定。然而，函式的輸入可能需要較高的精確度中繼計算（precision intermediate calculations），這可以輪流使用比較慢的整數多重精確度的值（integer multi-precision values）來計算一個精準的結果。如果沒有較高的中繼精確度，函式的精準度會很糟糕。你可以對幾個 libm 函式中的 slow paths 使用 libm systemtap 探測點（probe points），來偵測你是否正在呼叫 slow path。我們期盼你之後能夠使用探測觸發資訊來微調你的程式碼，以避免 slow paths。社群正在研究提供一種比 libm 快速的替代實作，或許可以用 -ffast-math 所選用的，這可以跳過在精確度的 slow paths 開支，並提供較快速的結果。

**為什麼沒有 strlcpy / strlcat?**

為了避免在修改大型的現有程式碼時，沒有詳細了解程式碼而導致 buffer overruns（過度使用緩衝區），已經[推廣](http://www.google.com/url?q=http%3A%2F%2Fstatic.usenix.org%2Fevent%2Fusenix99%2Fmillert.html\&sa=D\&sntz=1\&usg=AOvVaw0d1dZ-pT5yOwmONTRKK1yT)以更安全的字串複製方式 strlcpy 與 strlcat 函式。[C11 標準](http://www.google.com/url?q=http%3A%2F%2Fen.wikipedia.org%2Fwiki%2FC11\_%2528C\_standard\_revision%2529\&sa=D\&sntz=1\&usg=AOvVaw01GMKzXiCskeg6CRnD2LTE)的 Annex K 定義了選配式函式 strcpy\_s 與 strcat\_s，提供類似的需求，儘管以不一樣的 calling concentions（呼叫慣例）會比較沒有效率。無庸置疑的是實作這些函式會有問題，因為它們打算默默的將資料截斷、增加複雜度與降低效率、且沒有防止全部目地的 buffer overruns。新的標準函式庫函式應該考慮現有實作的優點，並且對於 glibc 已忽略的這些函式是好的實作不是很清楚。

以 gcc -D\_FORTIFY\_SOURCE 進行編譯可以捕捉到許多函式預期能捕獲的錯誤，而不需要修改程式碼。還有，若效率不是最重要的，snprintf 函式通常能做為這些函式的可移植替代品。

**在 ARM 編譯失敗**

你需要使用 ports add-on。

**我該如何打造一個在舊版 GNU/Linux distributions 上執行的二進位檔呢？**

［使用針對 LSB 的回答，利用 distro LSB packages 的相關資訊］。

**我該如何在 Ubuntu 上面打造 glibc 呢？［這裡列出其它 distributions 的類似問題］**

有些 distribution 的編譯器預設會啟用 -fstack-protector。GNU C 函式庫無法以它來編譯，因此你需要在 CFLAGS 新增 “-fno-stack-protector -U\_FORTIFY\_SOURCE”。

**在安裝 glibc 2.15 之後，我就不能編譯 GCC 了**

忠告：然而，遇到類似 siginfo\_t 變更與建立 libgcc 失敗相關的新問題是有幫助的，現有的 GCC releases［比 Thomas 還早的 patches］不能用目前的 glibc 來建立。

原文：[Frequently Asked Questions about the GNU C Library](http://www.google.com/url?q=http%3A%2F%2Fsourceware.org%2Fglibc%2Fwiki%2FFAQ\&sa=D\&sntz=1\&usg=AOvVaw1Fz5YrptMzYUQvJXfpcNvp)

譯者：Aaron Liao (aaron@netdpi.net)，對於譯文有任何建議請留言，謝謝。

修訂：2014/04/06，本中文版參照 [CarlosODonell](https://www.google.com/url?q=https%3A%2F%2Fsourceware.org%2Fglibc%2Fwiki%2FCarlosODonell\&sa=D\&sntz=1\&usg=AOvVaw39P5FySIB\_jfccbcjuuFP4) 2013-11-28 22:22:49 編輯的版本

授權：[GPL](http://www.google.com/url?q=http%3A%2F%2Fmoinmo.in%2FGPL\&sa=D\&sntz=1\&usg=AOvVaw02SkdDZqX0Oo2WCypPbGH-)
