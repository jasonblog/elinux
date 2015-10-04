
# 嵌入式 Linux 知識庫翻譯小組用戶手冊

* [翻譯計劃](#plan)
* [翻譯過程](#procedure)

<span id="plan"></span>
## 翻譯計劃

該翻譯計劃由 [@泰曉科技](http://tinylab.org) 於 2015年7月3日 發起：

> \#泰曉原譯\# [\#eLinux翻譯計劃\#](http://t.cn/RLqNvGt) 現籌備10人小組，進行 eLinux.org 的翻譯工作，持續發佈為 gitbook 電子書，並在獲得 Linux 基金會許可後出版為紙質書。參與翻譯，您將：1，系統學習嵌入式 Linux，2，結識業界一線工程師，3，獲得更多發展機會。關注@泰曉科技 微博私信報名，擇優錄取。

隨後做了大量準備工作：

* 註冊 github.com 帳號並建立 [項目 github 倉庫](https://github.com/tinyclub/elinux)
* 註冊 gitbook.com 帳號並建立[項目 gitbook 首頁](http://tinylab.gitbooks.io/elinux)
* 建立 [eLinux.org 中文版項目](http://www.tinylab.org/elinux/)
* 召集了 26 個同學，全體註冊 github 帳號並邀請加入協作團隊 elinux
* 建立微信協作群，及時溝通與交流翻譯過程中遇到的各類問題
* 建立 elinux.org 的 gitbook 目錄架構，並讓各位同學認領某個模塊
* 編寫自動轉換腳本 `tools/mediaiwiki-2-markdown.sh` 自動轉 elinux.org wiki page 為 markdown
* 自動構建輔助導入一級頁面
* 編寫自動構建二級頁面腳本 `tools/m2m-subpage.sh` 並導入 Develop Portal 的二級頁面
* 與此同時，部分同學已經翻譯了一些內容併發送了 Pull Request，部分同學開始準備 Review

接下來就是這裡的用戶手冊編寫，到這裡要做的工作是：

* 更詳細地描述翻譯和提交過程，簡化大家的參與過程

* 制定文檔格式規範

* 理清一些基本的翻譯約定

<span id="procedure"></span>
## 翻譯過程

* 註冊並登錄 github fork [代碼倉庫](https://github.com/tinyclub/elinux)

* 註冊 gitbook.com，在 gitbook.com 建立書籍並綁定到 github.com 剛 fork 的倉庫。

* clone 代碼倉庫並切到 develop 分支

        git clone https://github.com/tinyclub/elinux.git
        cd elinux && git checkout develop

* 找到自己認領的模塊，先翻譯一級鏈接，再翻譯二級鏈接

* 指定自己 fork 的遠程代碼倉庫（用於後面發 Pull Request），以 Github 帳號：`lzufalcon` 為例（請替換為自己的帳號，下同；之後 `myelinux` 就指向自己的 elinux 倉庫）：

        git remote add myelinux git@github.com:lzufalcon/elinux.git

* 基於自己計劃翻譯的內容創建分支， 以 `zh/dev_portals/Boot_Time/Boot_Time.md` 為例，可以基於遠程 `develop` 分支創建 `boot_time` 分支：

        git fetch --all
        git checkout -b boot_time tinyclub/develop

* 先檢測英文原文是否和“From”（即 eLinux.org）原稿一致，不一致就修復並提交。
    * 因為是腳本自動導入，所以難免會存在一定的出錯機率。
    * 部分 html 文本太複雜，`pandoc` 未能準確轉換，可根據人工分析轉為更精簡的 `markdown` 語法
        * 請檢查部分表格是否未能轉換過來，內容錯亂嚴重的一般都是這類問題。直接打開原文，查看源碼，複製對應的表格部分即可。
        * 請檢查縮進是否正確，部分原稿本身的縮進就有問題，建議修復。
        * 部分代碼顯示混亂，請縮進或者用兩個 \`\`\` 括起來。
        * 部分內部目錄鏈接無法跳轉到文章中或者外鏈失效，請檢查修復。

* 把 `en/` 下的英文原稿同步到 `zh/` 下。

    * *注*：請不要直接翻譯 `en/` 下的內容，只翻譯 `zh/` 下的即可。


* 翻譯前請參照如下安裝 `gitbook` 環境，以 Ubuntu 為例

        $ sudo aptitude install -y retext git nodejs npm
        $ sudo ln -fs /usr/bin/nodejs /usr/bin/node
        $ sudo aptitude install -y calibre fonts-arphic-gbsn00lp
        $ sudo npm install gitbook-cli -g

    *注*：`calibre` 提供 `ebook-converter`，用於生成 pdf 等格式。


* 開始翻譯

    * 翻譯時可以用 gitbook.com 的在線編輯工具
    * 也可以用本地的工具，如 vim 編輯
    * 也可下載 GitBook Editor，界面類似在線編輯工具

* 翻譯時

    * 請儘量遵守英文原稿的格式
    * 不要翻譯目錄裡頭的：`(#xxxx)`，`tools/build-toc.sh` 用它自動重構中文鏈接
    * 請規範使用 [Markdown 語法][markdown]
    * 中英文混排時，英文和數字短語前後須加空格，以便獲得更好的視覺感受
    * 代碼片段上下須加空行，代碼片段可以用 \`\`\` 前後括起來，請參考 [Markdown][markdown] `Code and Syntax Highlighting` 一節。
    * --target 和 --host 之類的命令或者參數最好用標示符 \` 括起來。效果如：`--target` 和 `--host`
    * 全篇要統一用中文標點符號，全部用全角。
    * 碰到專業名詞，特定縮寫，不需要翻譯。
    * 考慮到 Wiki 已經轉為 GitBook，`This page` 統一翻譯為`本文`。
    * 所有目錄的標題中的 `Contents` 翻譯為 `目錄`。
    * 不保留英文原文，只保留中文譯文。
    * 每翻譯完一個段落請務必通讀
        * 確保用詞沒有歧義，整段銜接流暢，如有必要請調整/添加必要的銜接詞彙
        * 並對照英文原文確保沒有漏掉原文任何需要表達的含義，不要刻意漏掉部分自己感覺模糊的詞彙
    * 如果有部分段落或者詞彙理解模糊，請優先在協作群討論或者藉助第三方翻譯工具協助，推薦 bing.com, iciba.com
    * 部分文章鏈接失效，請設法修復，比如說有指向 IBM Developer Works 的鏈接，可以 Google 鏈接最後的文件名找到正確的鏈接
    * 請在文件頭註明如下信息（校訂請列出所有提供了反饋並被採納的同學，翻譯和校訂以 `@lzufalcon` 為例），可以在 `zh/doc/PLAN.md` 的最後找到大家的 github 地址：

	> 原文：[eLinux.org](http://elinux.org/Boot_Time.md)<br/>
	> 翻譯：[@lzufalcon](https://github.com/lzufalcon)<br/>
	> 校訂：[@lzufalcon](https://github.com/lzufalcon)<br/>

    * 新同學參與 Review 而且其 Feedbacks 被採納後請把其 Github ID 以及鏈接追加到校訂者名單，多人請用逗號分開。
    * 重構文章內目錄（**注**：對管理員有效，各位譯者請忽略）

                export PATH=$PATH:/path/to/elinux/tools
                build-toc.sh xxx.md

    * 提交到 Git 倉庫
        * 請統一使用如下 Subject 和 Message，全部使用英文，其中 n = 1,2,3,4...，根據 Review 次數追加

                    zh: Translate xxx.md (Vn)
                    
                    V1: Fix up ...
                    V2: ...
                    V3: ...

* 翻譯後預覽和編譯

    * 在本地編寫可用 `Retext` 工具 預覽，也可用 `pandoc` 轉為 html（**注**：`pandoc` 轉換結果跟 `gitbook` 略有差異）

                pandoc -f markdown -t html xxx.markdown > xxx.html

    * 編譯可選方案

        * 本地編譯

                make && make pdf 或者 gitbook build && gitbook pdf

        * 直接提交到 github，會自動觸發 gitbook.com 構建，以 `boot_time` 分支為例（`myelinux` 指向自己的 `elinux` 倉庫）：

                git push myelinux boot_time

        * 添加 Travis-CI 自動構建支持
            * 登錄 <https://travis-ci.org/>
            * 綁定 github 帳號並添加上述 fork 過來的 elinux 項目
            * 進入 Settings，打開所有選項（本書已添加 [.travis.yml](../.travis.yml)）

* 編譯通過後可重新整理代碼倉庫

    * 針對某個文件，確保一個文件一條變更，可通過 `git rebase -i commit_id^` 來合併翻譯過程中針對某個文件所有未提交變更。
    * 創建 upstream 分支，更新遠程倉庫，`rebase` 到最新倉庫，並修復所有衝突

<!-- -->
        git checkout -b boot_time_upstream boot_time
        git fetch --all
        git rebase --onto tinyclub/develop --root
        git push myelinux boot_time_upstream


* 之後，通過 Github 發送 Pull Request：本地選 `boot_time_upstream`，遠程選 `develop`，也即是說要把自己倉庫中的 `boot_time_upstream` 合併到遠程的 `develop`

* 評審人員收到後會分配人員評審

* 根據評審人員反饋重新修改，並創建新分支用於進一步的評審，例如（記得追加 `Vn` 後綴，例如 V1，V2，而不是直接覆蓋原來的分支，方便備份）：

        git checkout -b boot_time_upstream_v1 boot_time_upstream

        // 處理來自校訂人員的各種反饋

        git fetch --all
        git rebase --onto tinyclub/develop --root
        git push myelinux boot_time_upstream_v1

* 重複上述 Pull Request 步驟，直到被 Merge 到主線

[markdown]:http://help.gitbook.com/format/markdown.html
