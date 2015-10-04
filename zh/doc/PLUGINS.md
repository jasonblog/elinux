
# Plugins

## Navigation

* [gitbook-plugin-anchor-navigation](https://github.com/yaneryou/gitbook-plugin-anchor-navigation)
    -   pdf 生成後，導航在頁面底部
    -   展示的導航很漂亮
* [gitbook-plugin-maxiang](http://plugins.gitbook.com/plugin/maxiang)
    -   只默認支持兩級(h2)
    -   不為 pdf 生成導航

* [gitbook-plugin-toc](http://plugins.gitbook.com/plugin/toc)
    -   需要修改 Markdown 文件，並且不支持中文，對於 pdf 可能會有一些用。可補充 maxiang（在anchor-navigation修復之前）

## Sidebar

* [tree](http://plugins.gitbook.com/plugin/tree)
    -   為側邊欄加樹狀結構
* [collapsible-menu](http://plugins.gitbook.com/plugin/collapsible-menu)
    -   可自動摺疊邊欄（不同與 tree 一起使用，效果不好）
* [toggle-chapters](http://plugins.gitbook.com/plugin/toggle-chapters)
    -   效果同上
* [gitbook-plugin-tocstyles](http://plugins.gitbook.com/plugin/tocstyles)
    -   可以設計更多側邊欄的花樣，符合中文要求
* [gitbook-plugin-multipart](http://plugins.gitbook.com/plugin/multipart)
    -   在 Chapters 之上加 Part I/II

## Comments

* [disqus](http://plugins.gitbook.com/plugin/disqus)

## Code

* [gitbook-plugin-google_code_prettify](http://plugins.gitbook.com/plugin/google_code_prettify)

## Chinese

* [gitbook-plugin-betterchinese](http://plugins.gitbook.com/plugin/betterchinese)
    -   本地沒有特別效果，需要到 gitbooks.io 上驗證

## Introduction DIY

* [diy-introduction](http://plugins.gitbook.com/plugin/diy-introduction)
    -   未看到生效？

## Exercises

* [exercises](http://plugins.gitbook.com/plugin/exercises)
    -   可交互式的練習（很有意義），gitbook 2.0 因為 markdown 解析 `{%` 出錯，導致無法使用。
    -   `sudo npm install gitbook -g gitbook@1.5.0`
    -   /usr/lib/node_modules/gitbook/bin/gitbook.js
    -   http://cowmanchiang.me/gitbook/gitbook/contents/plugin/exercises.html
