---
date: "2021-05-22T00:00:00Z"
description: ""
tags:
- Golang
- OAuth2
- PKCE
title: '[學習心得][Golang] 透過撰寫 Disqus 評論轉移到 Github Issue - disqus-importor-go'
---

<img src="https://github.com/kkdai/disqus-importor-go/raw/master/img/imported.jpg">

## 前言:

前幾天的文章中 [[Jekyll] 移除 Disqu 替換到 utteranc 來使用 github issue 作為文章評論](https://www.evanlin.com/jekyll-remove-disqus/) ，我有提到我將本來部落格中的 Disqus 評論有搬到 <https://utteranc.es/> 的過程，但是在 Disqus 裡面還有接近兩百個留言 (共有 189 個留言，分別在 55 篇文章內) 怎麼辦？

秉持著自己打造自己用的小工具原則（ --關在家裡太無聊-- ），就寫出了這個小工具來使用，希望能幫助到一些人。 也希望有更多的人一起加入開發相關功能。



# TL;DR 

本篇文將要介紹以下一些的部分，除了介紹如何使用外。人和

- <a href="#export">如何導出 Disqus Comment</a>
- <a href="#package">套件: Disqus to github issue Go </a>
- <a href="#features">包括哪些功能</a>
- <a href="#disqus-xml">Disqus XML Format 解析</a>
- <a href="#github-issue-go">Github issue created in Go</a> 
- <a href="#summary">結論</a>
- <a href="#result">成果</a>
- <a href="#refer">參考文章</a>



# 如何導出 Disqus Comment 到 Utteranc

<a id="export"></a>

![](https://avatars3.githubusercontent.com/u/27908738?v=3&s=88)

- 到 Disqus 管理介面，去導出： <http://disqus.com/admin/discussions/export/> 。
  - 大概需要過一兩天才會收到導出的檔案（不定時）。 
- 到 Utteranc 設定導入
- 修改 Github Page 設定。

可以參考文章  [[Jekyll] 移除 Disqu 替換到 utteranc 來使用 github issue 作為文章評論](https://www.evanlin.com/jekyll-remove-disqus/) 



# 套件: Disqus to github issues Go

<a id="package"></a>

## <https://github.com/kkdai/disqus-to-github-issues-go>

- 安裝套件方式：``go get github.com/kkdai/disqus-to-github-issues-go`
- 並且提供一個 CLI 給大家先用，大家可以安裝 `go install github.com/kkdai/disqus-to-github-issues-go/cmd/import_disqus_cli`

相關指令：

- `-f`: Import disqus exported xml file. (get from http://disqus.com/admin/discussions/export/)
- `-u`: github user name, you want to use for post github issue.
- `-r`: github repo name, you want to use for post github issue.
- `-t`: github token, you can request your from https://github.com/settings/tokens.

使用指令範例：

`./import_disqus_cli -f="EXPORTED_XML_FILE" -r="BLOG_REPO" -u="GITHUB_USER" -t="YOUR_TOKEN"`

# 包括哪些功能

<a id="features"></a>

- 讀取從 disqus export 的 XML file. (可以參考格式範例在 [github](https://github.com/kkdai/disqus-to-github-issues-go/blob/master/example/evanlin_20210517.xml) )
- 列出所有的 Comments
- 列出所有文章（標題，作者，時間）
- 導入到 github issues (使用 [utteranc](https://utteranc.es/) 格式)

接下來會針對幾個部分放上相關程式碼，希望讓大家分享如何寫。 



# Disqus XML Format 解析

<a id="disqus-xml"></a>

<script src="https://gist.github.com/kkdai/29bb2fe4805bba524763cb58c4d4ce22.js"></script>

這是簡單版本的 XML Parser，大家可以收到 XML 之後將檔案丟到 [XML to Go](https://www.onlinetool.io/xmltogo/) 就可以取得格式。

其中比較需要解釋的如下：

- `Articles`: (xml tag: thread) 這邊指的就是原本的文章。他跟留言是一對多的關係。（一篇文章內可以有個留言）
- `Comments`: (xml tag: post) 這邊是流言，資料比 Article 還後面需要另外做出 Mapping Table 來鏈結。

<script src="https://gist.github.com/kkdai/71aabad734a0ba96b027a93b7296bd3a.js"></script>

`Article` 與 `Comments` 連接關係為： `Comment.ArticleLink.ID` 跟 `Article.AttrID`。

### 如何作出準備 Import github issue 的資料

目前採取方式是根據在 Githib Issue 的 title 當作 key ，來做 map 。

如果以 path 來當 title ， ` mapping := make(map[string] Issue)` 來管理，可以很快速的透過 Map 來尋找到相關的資訊。

<script src="https://gist.github.com/kkdai/34e778f60b40e83a080ded84aedddfdb.js"></script>



### 留言排序

其中，每一篇文章的留言是需要排序的。為了確保留言可以在年度順序下呈現。需要相關 Sort 的準備如下：

<script src="https://gist.github.com/kkdai/4f08d942bf776035131c4f30e40ca2b8.js"></script>

透過準備 [Sort Interfaces](https://golang.org/pkg/sort/#Interface) 的方式來讓 `[]Comment` 支援 `Sort()`。



# Github issue created in Go

<a id="github-issue-go"></a>

關於發送 Github Issue 的部分，使用的是 Google 開源的 golang 套件。 <https://github.com/google/go-github> ，這邊記得要使用最新的版本（筆者時間最新版本是 v35）

```
import (
   ...
	"github.com/google/go-github/v35/github"
	"golang.org/x/oauth2"
)
```



相關程式碼如下： 



<script src="https://gist.github.com/kkdai/1c628d5e08f540e57cfa6b24cb35f44d.js"></script>

其中 `gIssue, _, err = client.Issues.Create(ctx, b.User, b.Repo, input)` 後會取得建立好的 Github Issue 相關訊息，需要相關的 ID 來後續加入 Comment 。

這一段程式碼，其實對於許多開發者想使用 Github Issue 作為資料管理的方式可以參考（聽說有人拿 Github Issue 來當 DB www) 。



# 成果 

<a id="result"></a>

![](https://github.com/kkdai/disqus-importor-go/raw/master/img/blog_result.jpg)

最後執行過後，可以將 Disqus 的留言全部整合進 Github Issue 之中，並且可以保留相關時間。也可以在部落格裡面正常的呈現。希望這個工具能夠幫助大家。



## 結論：

<a id="summary"></a>

本文分享了關於 Disqus 的輸出資料格式分析，並且分享了如何在 Github 上面建立 Issue 的相關程式碼。整合起來其實並不難，但是許多時候這些應用往往會變得很有趣。 大家可以參考筆者前一篇文章: [[TIL] 為了自己的習慣，弄了一個簡單的服務 Github issue bookmark](http://www.evanlin.com/til-2017-05-23/)。




## 相關文章：
<a id="refer"></a>

- [Removing Disqus and adding GitHub Issue Comments](https://asp.net-hacker.rocks/2018/11/19/github-comments.html)

- [Golang time doc](https://golang.org/pkg/time/)

- <https://github.com/goreleaser/goreleaser>

- <https://github.com/settings/tokens>

-  [[TIL] 為了自己的習慣，弄了一個簡單的服務 Github issue bookmark](http://www.evanlin.com/til-2017-05-23/)

  