> 原文：[eLinux.org](http://eLinux.org/Boot_Time "http://eLinux.org/Boot_Time")<br/>
> 翻譯：[@lzufalcon](https://github.com/lzufalcon)<br/>
> 校訂：[@ibrother](https://github.com/ibrother)

# 啟動時間

## 目錄

-   [1 簡介](#introduction)
-   [2 技術/項目主頁](#technology-project-pages)
    -   [2.1 測量啟動時間](#measuring-boot-up-time)
    -   [2.2 用於減少啟動時間的技術和技巧](#technologies-and-techniques-for-reducing-boot-time)
        -   [2.2.1 引導程序（Bootloader）加速](#bootloader-speedups)
        -   [2.2.2 內核加速](#kernel-speedups)
            -   [2.2.2.1 文件系統方面的問題](#file-system-issues)
        -   [2.2.3 用戶空間和應用程序加速](#user-space-and-application-speedups)
        -   [2.2.4 系統休眠相關的改進](#suspend-related-improvements)
        -   [2.2.5 雜項](#miscellaneous-topics)
        -   [2.2.6 一些未經驗證的想法](#uninvestigated-speedups)
-   [3 文章和演講稿](#articles-and-presentations)
    -   [3.1 案例研究](#case-studies)
-   [4 其他的項目/郵件列表/資源](#additional-projects-mailing-lists-resources)
    -   [4.1 SysV 'init' 的替代品](#replacements-for-sysv-init)
        -   [4.1.1 busybox init](#busybox-init)
        -   [4.1.2 upstart](#upstart)
        -   [4.1.3 Android init](#android-init)
        -   [4.1.4 systemd](#systemd)
    -   [4.2 Kexec](#kexec)
    -   [4.3 啟動畫面（Splash Screen）項目](#splash-screen-projects)
    -   [4.4 其他](#others)
-   [5 正在從事快速啟動工作的公司、個人或者項目](#companies-individuals-or-projects-working-on-fast-booting)
-   [6 啟動時間檢查清單](#boot-time-check-list)
<span id="introduction"></span>

## 簡介

本章包含的話題有啟動時間的測量、分析、人因工程（human factors）、初始化技術和優化技巧等。

產品花在啟動方面的時間直接影響終端用戶對該產品的第一印象。

一個消費電子設備不管如何引人注目或者設計得怎麼好，設備從關機狀態到可交互的使用狀態所需的時間對於獲得正面的用戶體驗尤為關鍵。案例 \#1 就是在關機狀態從頭啟動一個設備的例子。

啟動一個設備涉及到許多步驟和一系列的事件。為了使用前後一致的術語，消費電子 Linux 論壇（CE Linux Forum）的[啟動時間優化工作組](../../dev_portals/Boot_Time/Bootup_Time_Working_Group/Bootup_Time_Working_Group.md "Bootup Time Working Group")起草了一個術語詞彙表，該表包括了相關術語在該領域內通用的定義。該詞彙表如下：

-   [啟動時間相關的詞彙表](../../dev_portals/Boot_Time/Boot-up_Time_Definition_Of_Terms/Boot-up_Time_Definition_Of_Terms.md "Boot-up Time Definition Of Terms")
<span id="technology-project-pages"></span>

## 技術/項目主頁

下面主要介紹與減少 Linux 啟動時間有關的各種技術。

有一部分描述了 eLinux.org 上可以下載的本地補丁，而其餘部分則介紹了在其他地方維護的項目或者補丁。
<span id="measuring-boot-up-time"></span>

### 測量啟動時間

-   [Printk Times](../../dev_portals/Boot_Time/Printk_Times/Printk_Times.md "Printk Times") - 用於顯示每個 printk 的執行時間
-   [內核函數跟蹤（Ftrace）](../../dev_portals/Boot_Time/Kernel_Function_Trace/Kernel_Function_Trace.md "Kernel Function Trace") - 用於報告內核中每個函數的調用時間
-   [Linux 跟蹤工具箱（LTT）](../../dbg_portal/kernel_trace_and_profile/Linux_Trace_Toolkit/Linux_Trace_Toolkit.md "Linux Trace Toolkit") - 用於報告確切的內核和進程事件的時間數據
-   [Oprofile（譯註：最新替代品是 perf）](http://oprofile.sourceforge.net/news/) - 通用的 Linux 分析器（Profile）
-   [Bootchart](../../dev_portals/Boot_Time/Bootchart/Bootchart.md "Bootchart") - 用於 Linux 啟動過程的性能分析和數據展示。收集啟動過程中的用戶空間部分的資源使用情況和進程信息，然後渲染成 PNG、SVG 或者 EPS 格式的圖表。
-   [Bootprobe](http://people.redhat.com/berrange/systemtap/bootprobe/) - 一組用於分析系統啟動過程的 [System Tap](../../dbg_portal/kernel_trace_and_profile/System_Tap/System_Tap.md "System Tap") 腳本
-   當然，別忘了 `cat /proc/uptime` （譯註：統計系統已經運行的時間）
-   [grabserial](../../dev_portals/Boot_Time/Tims_Fastboot_Tools/Tims_Fastboot_Tools.md#grabserial "Tims Fastboot Tools") - Tim Bird （譯註：CE Linux Forum 主席）寫的一個非常讚的工具用於記錄控制檯輸出並打上時間戳
-   [進程跟蹤](../../dev_portals/Boot_Time/Tims_Fastboot_Tools/Tims_Fastboot_Tools.md#Tim.27s_quick_and_dirty_process_trace "Tims Fastboot Tools") - 同樣是 Tim Bird 寫的一個簡單補丁，用於記錄 exec、fork 和 exit 系統調用。
-   [ptx\_ts](http://pengutronix.de/software/ptx_ts/index_en.html) - Pengutronix 的時間戳記錄器（TimeStamper）：一個簡單的過濾器，可前置時間戳到標準輸出（STDOUT）上，有點像 grabserial 但是不限於串口。
-   [Initcall（內核初始化函數）調試](../../dev_portals/Boot_Time/Initcall_Debug/Initcall_Debug.md "Initcall Debug") - 一個用於顯示 initcalls 所花時間的內核命令行選項
-   也可以看下: [Kernel 檢測工具](../../dev_portals/Boot_Time/Kernel_Instrumentation/Kernel_Instrumentation.md "Kernel Instrumentation")，裡頭列舉了一些已知的內核檢測工具，這些對於測量內核啟動時間來說可能會有幫助。
<span id="technologies-and-techniques-for-reducing-boot-time"></span>

### 減少啟動時間的技術和技巧
<span id="bootloader-speedups"></span>

#### 引導程序（Bootloader）加速

-   [就地執行（XIP）內核](../../dev_portals/Boot_Time/Kernel_XIP/Kernel_XIP.md "Kernel XIP") - 允許內核在 ROM 或者 FLASH 上就地執行（XIP）
-   [在啟動時通過 DMA 拷貝內核鏡像](../../dev_portals/Boot_Time/DMA_Copy_Of_Kernel_On_Startup/DMA_Copy_Of_Kernel_On_Startup.md "DMA Copy Of Kernel On Startup")
    - 使用 DMA 從閃存（Flash）拷貝內核鏡像文件到內存中
-   [採用未壓縮的內核](../../dev_portals/Boot_Time/Uncompressed_kernel/Uncompressed_kernel.md "Uncompressed kernel") - 一個未壓縮的內核或許可以更快啟動
-   [快速內核解壓](../../dev_portals/Boot_Time/Fast_Kernel_Decompression/Fast_Kernel_Decompression.md "Fast Kernel Decompression")
<span id="kernel-speedups"></span>

#### 內核加速

-   [關閉控制檯](../../dev_portals/Boot_Time/Disable_Console/Disable_Console.md "Disable Console") - 避免啟動過程中的控制檯輸出開銷（譯註：尤其是串口控制檯，會嚴重拖慢系統啟動，甚至帶來各種實時問題）
-   關閉調試接口 和 `printk` - 避免調試接口和`printk`帶來的開銷，缺點是將丟失大量（對於調試可能有用）的信息
-   [不同步 RTC](../../dev_portals/Boot_Time/RTC_No_Sync/RTC_No_Sync.md "RTC No Sync") - 避免在啟動時延遲用 RTC 時鐘邊沿同步系統時間（可能帶來時鐘漂移）
-   [更短的 IDE 延遲時間](../../dev_portals/Boot_Time/Short_IDE_Delays/Short_IDE_Delays.md "Short IDE Delays") - 減少 IDE 啟動延遲的持續時間（有效但是可能危險）
-   [硬編碼內核模塊信息](../../dev_portals/Boot_Time/Hardcode_kernel_module_info/Hardcode_kernel_module_info.md "Hardcode kernel module info") - 通過硬編碼用於加載重定位信息的內容來減少加載模塊的開銷
-   [不偵測 IDE](../../dev_portals/Boot_Time/IDE_No_Probe/IDE_No_Probe.md "IDE No Probe") - 強制內核使用 `ide<x>=noprobe` 命令行選項，從而繞過 IDE 偵測
-   [預設 LPJ](../../dev_portals/Boot_Time/Preset_LPJ/Preset_LPJ.md "Preset LPJ") - 允許使用一個預設的 `loops_per_jiffy`（同樣可以通過內核命令行選項設置）
-   [異步函數調用](../../dev_portals/Boot_Time/Asynchronous_function_calls/Asynchronous_function_calls.md "Asynchronous function calls") - 允許偵測（Probe）函數或者其他函數並行處理，從而讓耗時的啟動活動並行起來
    -   [設備偵測函數線程化](../../dev_portals/Boot_Time/Threaded_Device_Probing/Threaded_Device_Probing.md "Threaded Device Probing") - 允許驅動並行地偵測設備（譯註：沒有提交到主線，不過內核有其他類似優化，比如異步函數調用，見 `kernel/async.c`）
-   [重排驅動初始化過程](../../dev_portals/Boot_Time/Reordering_of_driver_initialization/Reordering_of_driver_initialization.md "Reordering of driver initialization")
    -   允許驅動總線偵測儘可能快地啟動（譯註：總線 Probe 完以後，掛在上面的設備就可以相應地儘快完成 Probe）
-   [延遲 Initcalls](../../dev_portals/Boot_Time/Deferred_Initcalls/Deferred_Initcalls.md "Deferred Initcalls") - 延遲不重要的模塊初始化函數到主要啟動過程之後
-   NAND ECC（Error Correcting Code）技術改進 - 2.6.28 之前的 `nand_ecc.c` 有改進的空間，可以從 [mtd git 倉庫](http://git.infradead.org/mtd-2.6.git?a=blob_plain;f=drivers/mtd/nand/nand_ecc.c;hb=HEAD) 找到這樣一個改善的版本，相應文檔在[這裡](http://git.infradead.org/mtd-2.6.git?a=blob_plain;f=Documentation/mtd/nand_ecc.txt;hb=HEAD)。這個當且僅當系統使用軟 ECC 校驗時才有效
-   檢查內核正在使用哪個內存分配器，Slob 或者 Slub 可能比 Slab 更好，早期內核默認使用 Slab，可根據需要切換為 Slob 或者 Slub
-   如果系統不需要，可以從內核中去掉 SYSFS 甚至 PROCFS 支持。一項測試表明刪掉 SYSFS 可以節省 20 ms
-   仔細調研所有的內核配置選項，看看它們是否有用。即使選上的選項最終沒有用到，它也可能增加內核尺寸因而會增加內核加載時間（假設沒有使用就地執行 XIP 功能）。通常，這個需要一些試驗和測試！例如，選上 `CONFIG_CC_OPTIMIZE_FOR_SIZE`（在內核的 `General Setup` 配置菜單下面）在一個案例中能夠產生 20ms 的啟動優化。雖然不是很顯著，但是對於啟動時間優化來說，積少成多就能看到效果（Not dramatic, but when reducing boot time every penny counts!）。

-   遷移到一個不同的編譯器版本可能會產生更短和更快的代碼。通常，新的編譯器能夠產生更優的代碼。你也可以玩轉一下各種編譯器選項看看哪些表現最好。
-   如果在內核中用上 initramfs 和壓縮了的內核，那麼最好是不要再壓縮 initramfs。這個主要是為了避免重複兩次解壓數據。一個針對該問題的補丁被提交到了 LKML（譯註：Linux 內核郵件列表）：[http://lkml.org/lkml/2008/11/22/112](http://lkml.org/lkml/2008/11/22/112)
<span id="file-system-issues"></span>

##### 文件系統方面的問題

對於同樣的數據集，不同的文件系統擁有不同的初始化（即掛載，mounting）時間，這取決於元數據（meta-data）是否必須從存儲器讀到內存並且在掛載過程中使用哪種算法。

-   [文件系統信息](../../dev_portals/Boot_Time/Filesystem_Information/Filesystem_Information.md "Filesystem Information") - 介紹了各種文件系統啟動時間相關的信息
-   [文件系統](../../dev_portals/File_Systems/File_Systems.md "File Systems") - 嵌入式系統關注的各類文件系統，也包括一些優化建議
-   [避免使用 Initramfs](../../dev_portals/Boot_Time/Avoid_Initramfs/Avoid_Initramfs.md "Avoid Initramfs") - 解釋瞭如果想極小化啟動時間的話為什麼不能使用 initramfs
-   拆分分區。如果掛載一個文件系統耗費太久，那麼可以考慮把一個文件系統拆分成兩部分，一部分帶有在啟動時或者啟動後立即需要的信息，而另外一部分可以（延遲到）後面掛載
-   [Ramdisks 用法澄清](../../dev_portals/Boot_Time/Ramdisks_demasked/Ramdisks_demasked.md "Ramdisks demasked") - 解釋為什麼使用 Ramdisk 通常會引起更長的啟動時間而不是更短
<span id="user-space-and-application-speedups"></span>

#### 用戶空間和應用程序加速

-   [優化 RC 腳本](../../dev_portals/Boot_Time/Optimize_RC_Scripts/Optimize_RC_Scripts.md "Optimize RC Scripts") - 減少執行 RC 腳本的開銷
-   [並行執行 RC 腳本](../../dev_portals/Boot_Time/Parallel_RC_Scripts/Parallel_RC_Scripts.md "Parallel RC Scripts") - 以並行而不是串行方式執行 RC 腳本
-   [就地執行應用程序](../../dev_portals/Boot_Time/Application_XIP/Application_XIP.md "Application XIP") - 允許程序和庫能夠在 ROM 和 FLASH 中就地執行
-   [預鏈接](../../dev_portals/Boot_Time/Pre_Linking/Pre_Linking.md "Pre Linking") - 避免在首次加載程序時進行運行時鏈接
-   靜態鏈接應用程序。這樣可以避免運行時鏈接。如果應用程序少的話會有用，這樣也可以減少鏡像文件的大小，因為不再需要動態庫。
-   `GNU_HASH`: 在動態鏈接時有大約 50% 左右的速度改善
    -   可以看[這裡](http://sourceware.org/ml/binutils/2006-06/msg00418.html)
-   [應用程序初始化優化](../../dev_portals/Boot_Time/Application_Init_Optimizations/Application_Init_Optimizations.md "Application Init Optimizations") - 通過如下方法優化程序加載和初始化時間：
    -   使用 mmap vs. read
    -   採用頁映射的特性
-   [把模塊編譯到內核鏡像中](../../dev_portals/Boot_Time/Include_modules_in_kernel_image/Include_modules_in_kernel_image.md "Include modules in kernel image")
    -   通過把模塊加到內核鏡像中可以避免模塊加載的額外開銷
-   加速模塊加載 - 使用 Alessio Igor Bogani 的內核補丁來改善模塊加載時間：[加速符號定位進程](http://comments.gmane.org/gmane.linux.kernel.embedded/3508)。
-   避免使用 `udev`，因為它花費相當一部分時間來構建 `/dev` 目錄。在嵌入式系統中，通常會事先知道需要哪些設備，在哪些情況下用哪些驅動，所以我們知道在 `/dev` 下，哪些設備入口需要創建，這些應該靜態而不是動態創建。所以，在這裡，`mknod` 是友，`udev` 是敵。
-   如果你還是喜歡 `udev` 而且也喜歡快速啟動，也可以嘗試這種方法：在系統啟動時開啟 `udev` 並備份它創建的設備節點。接著，修改系統的初始化腳本成這樣：不再運行 `udev`，而是把剛備份的設備節點拷貝到 `/dev` 目錄中，之後再像往常一樣安裝熱插拔守護進程（即 `udev`）。這個戲法避免了啟動時的設備節點創建但是還是可以讓系統在之後（按需動態）創建設備節點。
-   如果設備有連接網絡，最好使用靜態 IP 地址。通過 DHCP 獲取地址會增加時間併產生相應的額外開銷。
-   遷移到不同的編譯器版本可以產生更短和更快的代碼。通常新編譯器產生更優的代碼。你也可以玩轉各個選項看看哪個最佳。
-   如果可能，從 glibc 轉到 uClibc。這個會產生更小的可執行文件因此會有更快的加載時間。
-   庫優化工具：[http://libraryopt.sourceforge.net/](http://libraryopt.sourceforge.net/)

    該工具允許創建優化的庫。不需要的函數會被移除因此而獲得更好性能。正常情況下，有些庫所佔用的內存頁面包含不會用到的代碼（臨近用到的代碼），經過優化後，這種情況不會再發生，所以可以花更少的內存頁面因此會有更少的內存加載，結果一些時間就會省掉。

-   [函數重排](http://www.celinux.org/elc08_presentations/DDLink%20FunctionReorder%2008%2004.pdf)

    這是一種技術，用於重排可執行文件中的函數，確保它們根據需要依次出現。它可以改善應用程序加載的時間，因為所有的初始化代碼被打包成一組頁面，而不是離散成多個不同的頁面。
<span id="suspend-related-improvements"></span>

#### 系統休眠相關的改進

另外一個改善啟動時間的途徑是利用休眠相關的機制。已知的兩種方案是：

-   使用標準的休眠/喚醒方案。這個已經由來自三星的 Chan Ju, Park 演示過。看錶格 23 以及之後的內容：[PPT](http://eLinux.org/images/9/98/LinuxBootupTimeReduction4DSC.ppt "LinuxBootupTimeReduction4DSC.ppt")
    以及這篇[論文](http://www.kernel.org/doc/ols/2006/ols2006v2-pages-239-248.pdf) 的第 2.7 節。

    該方法的一個問題是閃存寫比閃存讀慢很多，所以實際上創建一個休眠鏡像可能消耗相當長的一段時間。

-   實現快照啟動。這個由 Sony 的 Hiroki Kaminaga 完成並且描述在 [ARM 快照啟動](../../dev_portals/Boot_Time/Suspend_To_Disk_For_ARM/Suspend_To_Disk_For_ARM.md "Suspend To Disk For ARM") 和 [Snapshot-boot-final.pdf](http://elinux.org/images/3/37/Snapshot-boot-final.pdf)

    這個類似上述休眠/喚醒方案。但是休眠文件被保留並且在每次啟動時使用。缺點是在製作快照時不可以掛載可寫分區，否則當分區被修改，而休眠文件中的應用在映像中有未修改分區有關的信息時，就會產生不一致性。
<span id="miscellaneous-topics"></span>

#### 雜項

[關於壓縮](../../dev_portals/Boot_Time/About_Compression/About_Compression.md "About Compression") 一文討論了壓縮技術在啟動時間優化方面的效果。這個能夠影響內核與用戶空間兩者的啟動時間。
<span id="uninvestigated-speedups"></span>

#### 一些未經驗證的想法

該節只是對沒來得及實現但可能利於啟動優化的一些想法做一個記錄。

-   **預填充的緩衝區高速緩存** - 因為 initramfs 執行了額外的數據拷貝，所以想法是可以有一個預填充的緩衝區高速緩存。一種簡單的場景是當啟動完成並且應用初始化完了以後，就允許把緩衝區高速緩存轉存出來。然後這個數據可以在後續的啟動中用於初始化緩衝區高速緩存（當然不需要拷貝了）。一種可能的方法是把這些數據直接打包到內核鏡像中並直接使用，當然也可以選擇分開加載。不幸地是，我現在具有的這塊知識不足以做一個簡單的實現。注意事項：
    -   是否可能把緩衝區高速緩存拆分成兩個不同的部分，一個靜態分配，另外一個動態分配？
    -   預填充的緩衝區高速緩存的內存頁可能不能丟棄，所以他們需要被固定保護起來。
    -   除了緩衝區高速緩存數據外，一些其他的變量也可能需要保存
    -   類似的方法對於文件數據緩存也可能有效


-   **專用文件系統** - 當前的文件系統中有大量的抽象，這些抽象方便了新文件系統的添加並且為所有文件系統創建了統一視圖。無疑這個設計很漂亮整潔，但是這些抽象層也引入了一些開銷。一種解決方案或許是創建一個專有的文件系統，只支持 1 到 2 種文件系統，從而可以消除抽象層帶來的開銷。這或許會有用，不過合併到主線的機率為 0 。
<span id="articles-and-presentations"></span>

## 文章和演講稿

-   [嵌入式 Linux 啟動時間優化研討會材料](http://free-electrons.com/doc/training/boot-time/)
    -   由 Free Electrons 提供
    -   一則關於啟動時間優化技術的演講稿 - Atmel SAMA5 硬件上的實際實驗情況

-   "啟動時間優化" - （[幻燈](http://elinux.org/images/d/d1/Alexandre_Belloni_boottime_optimizations.pdf)
    -   Alexandre Belloni 於 2012 年 11 月 6 日 在 ELC Europe 上做的報告
    -   [Free-Electrons 上的入口](http://free-electrons.com/blog/elce-2012-videos/)


-   "減少啟動時間的正確途徑" - （[幻燈](http://elinux.org/images/f/f7/RightApproachMinimalBootTimes.pdf)
    -   Andrew Murray 於 2010 年 10 月 28 日 在 ELC Europe 上做的報告
    -   這個包含一個 1 秒鐘啟動的針對 SH7724 的 QT Linux 冷啟案例研究，也有用戶空間函數重排的信息
    -   類似的幻燈片，關於 1 秒鐘啟動的針對 OMAP3530EVM 的案例研究，可以從[這裡](http://www.slideshare.net/andrewmurraympc/t-iswift-boot) 找到。
-   "1 秒鐘的 Linux 啟動演示（新版）" ([Youtube 視頻（來自MontaVista）](http://www.youtube.com/watch?v=-l_DSZe8_F8))
-   "用於減少啟動時間的工具和技巧" - （[幻燈](http://eLinux.org/images/9/98/Tools-and-technique-for-reducing-bootup-time.ppt "Tools-and-technique-for-reducing-bootup-time.ppt") | [ODP](http://eLinux.org/images/4/40/Tools-and-technique-for-reducing-bootup-time.odp "Tools-and-technique-for-reducing-bootup-time.odp") | [PDF](http://eLinux.org/images/d/d2/Tools-and-technique-for-reducing-bootup-time.pdf "Tools-and-technique-for-reducing-bootup-time.pdf")
    -   Tim Bird 於 2008 年 11 月 7 日 在 ELC Europe 報告，展示了他當時收集的一些減少啟動時間的技巧
    -   [Tims 開發的快速啟動工具](../../dev_portals/Boot_Time/Tims_Fastboot_Tools/Tims_Fastboot_Tools.md "Tims Fastboot Tools") 提供了一些關於該報告的在線材料


-   [Christopher Hallinan](http://www.mvista.com/download/author.php?a=37) 於 2008 年在 MontaVista 視訊會議上做了一個關於如何減少啟動時間的報告，幻燈在[這裡](http://www.mvista.com/download/power/Reducing-boot-time-techniques-for-fast-booting.pdf)
-   [優化鏈接器加載時間](http://lwn.net/Articles/192082/)
    -   介紹各類啟動時間優化、預鏈接等

-   [X86 上的啟動延遲基準測試](http://benyossef.com/benchmarking-boot-latency-on-x86/)
    -   由 Gilad Ben-Yossef 於 2008 年 7 月完成
    -   該教程關於如何使用 TSC 寄存器和內核的 `PRINTK_TIMES` 特性來測量 x86 系統啟動時間，包括 BIOS、引導程序、內核以及第一個用戶程序。

-   [嵌入式 Linux 的快速啟動](http://tree.celinuxforum.org/CelfPubWiki/KoreaTechJamboree3?action=AttachFile&do=get&target=The_Fast_Booting_of_Embedded_Linux.pdf)
    -   來自韓國 ETRI 的 HoJoon Park 於 2008 年 7 月 在 CELF [3rd Korean Technical Jamboree](http://tree.celinuxforum.org/CelfPubWiki/KoreaTechJamboree3) 做的報告。
    -   解釋了用於減少不同階段啟動時間的不同技術

-   Tim Bird 做的關於啟動時間優化技術的調查：
    -   [用於優化 Linux 啟動時間的方法](http://kernel.org/doc/ols/2004/ols2004v1-pages-79-88.pdf)
        -   為 2004 年 Ottawa Linux 專題研討會準備的論文
    -   [減少嵌入式 Linux 系統的啟動時間](http://eLinux.org/images/7/78/ReducingStartupTime_v0.8.pdf "ReducingStartupTime v0.8.pdf")
        -   2003 年 11 月的報告，描述了一些存在的啟動時間優化技術和策略

-   在消費電子設備上並行化 Linux 啟動 - （[PDF](http://tree.celinuxforum.org/CelfPubWiki/ELCEurope2007Presentations?action=AttachFile&do=view&target=par.pdf)
-   [通過應用並行來獲得更快的 Linux 啟動速度](http://www.ibm.com/developerworks/cn/linux/l-boot-faster/)
    -   由來自 IBM Developer Works 的 M. Tim Jones 撰寫
    -   該文展示了一些用於提高 Linux 啟動速度的選項，包括兩個用於並行初始化過程的方法，也展示瞭如何用圖形可視化啟動過程的性能數據。

-   [Android 啟動時間優化](http://www.slideshare.net/kanru/android-boot-time-optimization)
    -   由來自 0xlab 的 Kan-Ru Chen 撰寫
    -   該報告涵蓋了 Android 啟動時間測量、分析、以及提出的一些優化方法、基於休眠的優化技術以及一些潛在的 Andriod 用戶空間優化技術。

-   TI 嵌入式處理器的維基也提供了一些信息，描述了 Linux/Android 啟動時間的優化過程
    -   [Linux 啟動時間優化](http://processors.wiki.ti.com/index.php/Optimize_Linux_Boot_Time)
    -   [Android 啟動時間優化](http://processors.wiki.ti.com/index.php/Android_Boot_Time_Optimization)

-   [實現 Android 的檢查點技術（Checkpointing）](http://www.slideshare.net/jserv/implement-checkpointing-for-android-elce2012)
    -   由 0xlab 的 Kito Cheng 和 Jim Huang 撰寫
    -   實現 Android 檢查點技術（Checkpointing）的原因
        -   恢復到事先存儲的狀態可以獲得更快的 Android 啟動時間
        -   基於定期檢查點技術可以獲得更好的產品試用體驗
<span id="case-studies"></span>

### 案例研究

-   [在用 NAND 的 ARM 機器上做到從引導程序啟動到 Shell 只花 300 ms](http://www.makelinux.com/emb/fastboot/omap)
-   三星的關於數碼照相機的可行性（Proof-Of-Acceptability）研究，看這裡：[啟動時間優化 PPT](http://eLinux.org/images/9/98/LinuxBootupTimeReduction4DSC.ppt "LinuxBootupTimeReduction4DSC.ppt") 和 [論文](http://www.kernel.org/doc/ols/2006/ols2006v2-pages-239-248.pdf)
-   [1 秒內把 Linux 從關機狀態啟動到用戶空間](https://docs.blackfin.uclinux.org/doku.php?id=fast_boot_example)
    -   在該文中，Robin Getz 描述了用於加速 blackfin 開發板的技術
-   [在 3.2 秒內啟動 Linux dm365 網絡照相機](http://e2e.ti.com/support/embedded/f/354/t/51158.aspx)
-   [在 0.5 秒內啟動內核和 Shell (不包括 U-boot 和解壓)](http://e2e.ti.com/support/dsp/davinci_digital_media_processors/f/100/p/7616/30088.aspx)
-   [Warp2, Lineo Solutions, 2008年，2.97 秒啟動, ARM11, 400MHz](http://www.theinquirer.net/inquirer/news/1049451/linux-boots)
<span id="additional-projects-mailing-lists-resources"></span>

## 其他的項目/郵件列表/資源
<span id="replacements-for-sysv-init"></span>

### SysV 'init' 的替代品

啟動 Linux 系統的傳統方法是用 `/sbin/init`，它是一個初始化程序，負責解析 `/etc/inittab`，並能夠針對不同的運行級別和系統事件（各類按鍵組合和電源事件）產生一系列動作。

可查看 [init(8) 手冊頁](http://linux.die.net/man/8/init) 和 [inittab(5) 首頁頁](http://linux.die.net/man/5/inittab) 獲取更多信息。
<span id="busybox-init"></span>

#### busybox init

[BusyBox](../../dev_portals/Boot_Time/BusyBox/BusyBox.md "BusyBox") 通常包含一個 `init` 小程序。

截止 2000 年，Busybox init 和全功能的 init 之間，`inittab` 支持的特性稍微有些差異。但是不知道到 2010 以後（譯註：現在都 2015 年了，看來這個文章真地有點老了，後面有時間很多材料得好好整理下），情況是否依舊。可以從[這裡](http://spblinux.de/2.0/doc/init.html)看到一些細節。

Denys Vlasenko, Busybox 的維護人員之一，曾經建議用名叫 `runsv` 的工具替換掉傳統的 `init`，可以看下[這裡](http://busybox.net/~vda/init_vs_runsv.html)。
<span id="upstart"></span>

#### upstart

`upstart` 是一個新的 Linux 桌面工具，它提供了 `/sbin/init`，但是實現了不同的操作語義：

-   首頁：[http://upstart.ubuntu.com/](http://upstart.ubuntu.com/)
-   維基百科地址：[http://en.wikipedia.org/wiki/Upstart](http://en.wikipedia.org/wiki/Upstart)
<span id="android-init"></span>

#### Android init

Android `init` 是專門為啟動 Andriod 系統而定製的程序，看[這裡](http://eLinux.org/Android_Booting#.27init.27 "Android Booting")。
<span id="systemd"></span>

#### systemd

systemd 是一個新的項目 (就當時 2010 年 5 月而言)，用於在 Linux 桌面系統中啟動守護進程和服務，可以看看[這裡](http://0pointer.de/blog/projects/systemd.html)。
<span id="kexec"></span>

### Kexec

-   Kexec 允許系統不經 BIOS 重啟（注：與通常的重啟不同）。也就是說，一個 Linux 內核可以無需進入引導程序就能直接啟動另外一個 Linux 內核，白皮書在[kexec.pdf](http://elinux.org/images/2/2f/ELC-2010-Damm-Kexec.pdf)。
    -   這裡是另外一篇文章：[使用 kexec 快速重啟 Linux](http://www.ibm.com/developerworks/cn/linux/l-kexec/index.html)
<span id="splash-screen-projects"></span>

### 啟動畫面（Splash Screen）項目

-   [Splashy](https://wiki.archlinux.org/index.php/Splashy_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) - 一種在啟動過程的早期階段放置啟動畫面的技術，這個是用戶空間代碼。
    -   這個貌似是最新的啟動畫面技術，為主流發行版所用。為了支持該技術，需要在內核中添加一個 Framebuffer 驅動。
-   [Gentoo Splashscreen](https://wiki.gentoo.org/wiki/Fbsplash) - 一種在啟動過程早期放置啟動畫面的新技術
-   [PSplash](http://git.yoctoproject.org/cgit/cgit.cgi/psplash) - PSplash 是一種用戶空間的圖形化引導啟動畫面，主要針對嵌入式 Linux 設備，支持 16bpp 或者 32bpp 的幀緩衝。
<span id="others"></span>

### 其他

-   [基於休眠快照啟動](http://tree.celinuxforum.org/CelfPubWiki/) - 一種用軟件喚醒來快速啟動系統的技術
<span id="companies-individuals-or-projects-working-on-fast-booting"></span>

## 正在從事快速啟動的公司、個人或者項目

-   Intel - Arjan van de Ven - see
    [http://lwn.net/Articles/299483/](http://lwn.net/Articles/299483/)
-   fastboot Git 倉庫 - see
    [http://lwn.net/Articles/299591/](http://lwn.net/Articles/299591/)
-   Free Electrons -
    [http://free-electrons.com/services/boot-time/](http://free-electrons.com/services/boot-time/)
<span id="boot-time-check-list"></span>

## 啟動時間檢查清單

可以從 2009 年 8 月的一個關於 ARM 設備上啟動時間的討論中找到一些事關啟動時間優化的提示和建議。

雖然可能會重複本文中已經介紹的內容，但是咱們還是從上述討論中提取了一個檢查清單（譯註：可以方便啟動優化時做檢查）：

-   CPU 時鐘頻率切到最大了嗎？如果內核、引導程序或者硬件負責設置 CPU 功率和速度，那麼我們得確保各 CPU 用最大速度而不是最低速度啟動。

-   SOC 上的內存接口硬件（寄存器）的定時配置（例如 RAM 和 NOR/NAND 定時）優化過嗎？許多供應商賣硬件時採用保守的設置：“好吧，工作了，以後再優化”。我們想要的配置是：“儘可能地快，但是還得穩定且可靠”。這部分可能需要一些硬件知識並且必須為不同內存設備做不同配置（譯註：不同內存設備特性不一樣，比如說工作頻率會有差異）。

-   我們的引導程序用了指令和數據緩存嗎？例如 U-Boot 在 ARM 設備上默認沒有使能數據緩存，因為要使能數據緩存，需要額外定製 MMU 表。

-   內核從存儲設備（例如 NOR 或者 NAND）拷貝到內存中使用了優化過的方法嗎？比如 DMA 或者是 ARM 上至少得用上 `load/store multiple` 命令（ldm/stm）？

-   如果用 U-boot 的 uImage，在啟動參數中設置 `verify=no` 可以避免校驗碼驗證。

-   優化內核的尺寸
    -   可以嘗試一些針對嵌入式系統的內核配置選項，例如，消除所有的 printk 字符串，減少數據結構或者消除不需要的功能。

-   拷貝了幾次內核鏡像數據？第一次由引導程序從存儲設備拷到內存，然後內核的解壓程序負責解壓到最終的位置？如果使用壓縮了的內核和 NOR 閃存，可以考慮在 NOR 閃存中就地執行（XIP）解壓器。

-   如果使用了壓縮內核，檢查下壓縮算法。zlib 在解壓方面較慢，而 lzo 則更快。所以如果採用 lzo 壓縮，我們也可以加速一些東西（可以從 LKML 查看這些）。如果沒有任何壓縮也可能是一件好事（看下一個話題）。

-   檢查是否使用了未壓縮的內核（根據系統配置）。在閃存系統上使用未壓縮的內核可以優化啟動時間。原因是當存儲設備的速率低於解壓速率時，壓縮過的內核才有優勢（譯註：拷貝時間 v.s. 解壓時間）。在支持 DMA 的典型嵌入式系統上，其讀取到內存的速率勝過基於 CPU 的解壓速率。當然，這取決於很多方面，比如閃存控制器的性能、內核存儲文件系統性能、DMA 控制器性能、緩存架構等。所以，每個系統會有個體差異。舉例來說：使用未解壓的內核（約 2.8MB）解壓（在 NOR 閃存中就地執行解壓程序）比從閃存拷貝 2.8MB 內核到內存中多花費了約 0.5 秒。

-   使用提前計算好的 `loops-per-jiffy`，（譯註：然後通過內核命令行參數傳給內核，可避免執行重新計算的代碼）

-   使能內核 `quiet` 選項（譯註：可關閉控制檯輸出）

-   如果使用 UBI 文件系統：UBI 用到 MTD 設備上相當緩慢。細節在 MTD 的 [UBI 伸縮性](http://www.linux-mtd.infradead.org/doc/ubi.html#L_scalability) 一文中有解釋。如果不用 UBI2，我們基本啥也優化不了，UBIFS 將沒啥用（UBIFS would stay intact），有一些關於這個的討論，並且貌似要做 UBI2 相當困難：（[一些想法](http://www.linux-mtd.infradead.org/faq/ubi.html#L_attach_faster)）。
    -  緊跟著的郵件裡頭，Sascha Hauer 寫到：

    > "非常有趣的是，內核 NAND 驅動比 U-boot 中的慢很多，看完發現結果是內核驅動用了中斷來判斷控制器是否準備好，換成輪詢以後 NAND 的性能提升了一倍。UBI 掛載更快並且這個為啟動過程又省掉了幾秒鐘 :-) “

-   啟動時使用靜態設備節點，之後再設置 Busybox mdev 來做動態熱插拔

-   如果使能了網絡，那麼在網絡代碼路徑中可能會有很長的超時設定，具體取決於是否指定了靜態地址。可以看下 `net/ipv4/ipconfig.c` 文件中的定義：`CONF_PRE_OPEN` 和 `CON_POST_OPEN` 以及[IP 延遲配置補丁](http://patchwork.kernel.org/patch/31678/).

-   並行化啟動過程

-   關閉該選項："Set system time from RTC on startup and resume"，可避免減慢內核啟動。（要實現 RTC 同步），我們可以在 init 末尾使用 `hwclock -s` 命令。


[分類](http://eLinux.org/Special:Categories "Special:Categories"):

-   [啟動時間](http://eLinux.org/Category:Boot_Time "Category:Boot Time")
-   [引導程序](http://eLinux.org/Category:Bootloader "Category:Bootloader")
-   [消費電子 Linux 工作組](http://eLinux.org/Category:CE_Linux_Working_Groups "Category:CE Linux Working Groups")
