> 原文：[eLinux.org](http://eLinux.org/Bootloader "http://eLinux.org/Bootloader")<br/>
> 翻譯：[@lzufalcon](https://github.com/lzufalcon)


# 引導程序

簡單講，引導程序是計算機開機後運行的第一個軟件程序。它負責裝載並移交控制權給操作系統內核（例如 Hurd 或者 Linux）。而內核轉而初始化接下來的操作系統部分（例如 GNU）。

## 引導程序清單

<table class="wikitable" border="0" cellspacing="0" cellpadding="4">
<caption align="bottom"> 圖例: <b>?</b>: 未知 <b>-</b>: 不支持
</caption>
<tr>
<th> 引導程序  </th>
<th> ARM </th>
<th> BFIN </th>
<th> MIPS </th>
<th> PPC </th>
<th> SH </th>
<th> x86 </th>
<th> 備註
</th></tr>
<tr>
<td> <a href="http://elinux.org/APEX" title="APEX" class="mw-redirect">APEX</a>
</td>
<td>                 y  </td>
<td>   -  </td>
<td> &#160;?   </td>
<td> &#160;?  </td>
<td> &#160;?  </td>
<td> &#160;?  </td>
<td>
</td></tr>
<tr>
<td> <a href="http://elinux.org/Barebox" title="Barebox">Barebox (U-Boot-v2)</a>
</td>
<td>                 y  </td>
<td>   y  </td>
<td>   y  </td>
<td>  y  </td>
<td>  (進行中) </td>
<td>  y  </td>
<td> 支持網絡安裝（系統），集成編輯器和腳本環境
</td></tr>
<tr>
<td> <a href="http://elinux.org/Blob" title="Blob">Blob</a>
</td>
<td>                 y  </td>
<td>  -  </td>
<td>  -   </td>
<td>  -  </td>
<td>  -  </td>
<td>  -  </td>
<td>
</td></tr>
<tr>
<td> <a rel="nofollow" class="external text" href="http://www.linux-mips.org/wiki/Common_Firmware_Environment">CFE</a>
</td>
<td>                 -  </td>
<td>  -  </td>
<td>  y   </td>
<td>  -  </td>
<td>  -  </td>
<td>  -  </td>
<td> 只支持特定的博通芯片
</td></tr>
<tr>
<td> <a href="http://elinux.org/Coreboot" title="Coreboot"> coreboot (<i>LinuxBIOS</i>)</a> <a rel="nofollow" class="external autonumber" href="http://www.coreboot.org/Welcome_to_coreboot">[1]</a>
</td>
<td>                 y  </td>
<td>   -  </td>
<td>  -   </td>
<td>  -  </td>
<td>  -  </td>
<td>  y  </td>
<td> Peter Stuge 在 2008 年度 嵌入式 Linux 歐洲研討會上的談話，<a rel="nofollow" class="external text" href="http://free-electrons.com/pub/video/2008/elce/nluug-fall2008-stuge-coreboot.ogv">視頻</a>
</td></tr>
<tr>
<td> <a href="http://elinux.org/Kexecboot" title="Kexecboot">Kexecboot</a>
</td>
<td>                 y  </td>
<td>   -  </td>
<td>  -   </td>
<td>  -  </td>
<td>  -  </td>
<td> &#160;?  </td>
<td> 是一個二階段引導程序，包含 Linux 內核和一個小型 GUI
</td></tr>
<tr>
<td> <a href="../../.././dev_portals/Development_Platforms/Tegra/Mainline_SW/U-Boot/Tegra/Mainline_SW/U-Boot.md" title="U-Boot">U-Boot</a>
</td>
<td>                 y  </td>
<td>   y  </td>
<td>   y  </td>
<td>  y  </td>
<td>  y </td>
<td>  y  </td>
<td> 支持網絡安裝
</td></tr>
<tr>
<td> <a href="http://elinux.org/Grub" title="Grub">Grub</a>
</td>
<td>                &#160;?  </td>
<td>   -  </td>
<td>  &#160;?  </td>
<td>  y  </td>
<td>  -  </td>
<td>  y  </td>
<td>
</td></tr>
<tr>
<td> <a href="http://elinux.org/Lilo" title="Lilo">Lilo</a>
</td>
<td>                 -  </td>
<td>   -  </td>
<td>   -  </td>
<td>  -  </td>
<td>  - </td>
<td>  y  </td>
<td> 僅支持 x86，需要用 nasm 編譯
</td></tr>
<tr>
<td> <a rel="nofollow" class="external text" href="http://www.microcross.com/html/micromonitor.html">MicroMonitor</a>
</td>
<td>                 y  </td>
<td>   y  </td>
<td>  &#160;?  </td>
<td>  y  </td>
<td>  y  </td>
<td> &#160;?  </td>
<td>
</td></tr>
<tr>
<td> <a rel="nofollow" class="external text" href="http://www.linux-mips.org/wiki/PMON_2000">PMON 2000</a>
</td>
<td>                 -  </td>
<td>   -  </td>
<td>  y   </td>
<td>  -  </td>
<td>  -  </td>
<td>  -  </td>
<td>
</td></tr>
<tr>
<td> <a href="http://elinux.org/index.php?title=Qi&amp;action=edit&amp;redlink=1" class="new" title="Qi (page does not exist)">Qi</a>
</td>
<td>                 y  </td>
<td>   -  </td>
<td>  -   </td>
<td>  -  </td>
<td>  - </td>
<td>  -  </td>
<td> 非常快，可簡單直接地引導進 Linux
</td></tr>

<tr>
<td> <a href="http://elinux.org/index.php?title=RedBoot&amp;action=edit&amp;redlink=1" class="new" title="RedBoot (page does not exist)">RedBoot</a>
</td>
<td>                 y  </td>
<td>  &#160;?  </td>
<td>   y  </td>
<td>  y  </td>
<td>  y </td>
<td>  y  </td>
<td> 支持網絡安裝
</td></tr>
<tr>
<td> <a href="http://elinux.org/Syslinux" title="Syslinux">Syslinux</a>
</td>
<td>                 -  </td>
<td>   -  </td>
<td>  -   </td>
<td>  -  </td>
<td>  -  </td>
<td>  y  </td>
<td> 變體有 isolinux，可非常靈活地引導啟動 x86 機器
</td></tr>
<tr>
<td> <a rel="nofollow" class="external text" href="http://yaboot.ozlabs.org/">Yaboot</a>
</td>
<td>                 -  </td>
<td>   -  </td>
<td>  -   </td>
<td>  y  </td>
<td>  -  </td>
<td>  -  </td>
<td>
</td></tr>
<tr>
<td> <a rel="nofollow" class="external text" href="http://www.linux-mips.org/wiki/YAMON">YAMON</a>
</td>
<td>                 -  </td>
<td>   -  </td>
<td>  y   </td>
<td>  -  </td>
<td>  -  </td>
<td>  -  </td>
<td>
</td></tr></table>

更多詳盡的列表可以從[維基百科](http://en.wikipedia.org/wiki/Comparison_of_boot_loaders)上找到。

## 一些傳統的引導程序

下述內容較舊，請不要用於新設計中！

-   rrload（RidgeRun, 曾用於以前的 TI OMAP 開發板）
-   [pmon](http://www.linux-mips.org/wiki/PMON)（曾用 MIPS Linux，包括博通 Wifi 路由器，譬如 Linksys

## 看過來

-   [引導程序安全相關資源](../../.././dev_portals/Security/Bootloader_Security_Resources/Bootloader_Security_Resources.md "引導程序安全相關資源")


[分類](http://eLinux.org/Special:Categories "Special:Categories"):

-   [引導程序](http://eLinux.org/Category:Bootloaders "Category:Bootloaders")

