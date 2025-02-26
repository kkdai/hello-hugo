---
date: "2021-07-11T00:00:00Z"
description: ""
tags:
- LINEBot
- Chatbot
- DevRel
title: '[學習心得] LINE Bot 開發者指南 詳解 - 5. LINE Login (補充)'
---

<img src="../images/2021/linebot005.jpg">

## 前言:

各位好， 我是 LINE Taiwan 資深開發技術推廣工程師 – Evan Lin。 今天這篇文章為各位詳細解釋 「 LINE Bot 開發指南」這一份投影片文件。這一份文件是來自於 [Development guidelines](https://developers.line.biz/en/docs/partner-docs/development-guidelines/) 的投影片，考量到在台灣還沒有正式的公布與中文化。這一次跟總部共同合作準備中文版本之外，並且特定用這一系列文章加以解釋，希望可以讓更多開發者有更多的了解。  [Development guidelines](https://developers.line.biz/en/docs/partner-docs/development-guidelines/)  文件內容很多，本份投影片也將以五篇文章的篇幅來加以解釋。本篇文章為第五篇文章，主要講解的會是關於 LINE Login 與開發時候需要注意的事項。



## 文章索引:

#### 完整投影片鏈結： <https://speakerdeck.com/line_developers_tw2/line-bot-developer-guideline-chinese>

希望各位可以持續關注：

1. [關於LINE Bot ](https://www.evanlin.com/2021-05-25-line-bot-guide-1/)
2. [使用Webhook URL接收請求時的注意事項](https://www.evanlin.com/line-bot-guide-2/)
3. [發送 API 請求時的注意事項](http://www.evanlin.com/line-bot-guide-3/)
4. [LINE Login (本篇文章)](http://www.evanlin.com/line-bot-guide-4/)
5. [LINE Login (補充）(本篇文章)](http://www.evanlin.com/line-bot-guide-5/)
5.  其他相關功能

本篇文章將專注在第一個段落，也就是 Page 47 ~ Page 30 的部分。

##  LINE Login (補充)

<script async class="speakerdeck-embed" data-slide="47" data-id="0e9f6182ae864568a5940cbad5ef4bec" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

本篇注意事項中，將會帶出以下的相關項目。

- 關於防止以 state 不法使用的對策

- 依照不同流程 OS 進行 LINE Login 的用戶流程範例 (iOS)
- 依照不同流程 OS 進行 LINE Login 的用戶流程範例 (Android)
- 外部瀏覽器的登入流程（示意圖）
- 關於轉換目標端瀏覽器的設定方法

以下開始將會逐一針對每一個頁面詳細解釋：

## 關於防止以 state 不法使用的對策

<script async class="speakerdeck-embed" data-slide="48" data-id="0e9f6182ae864568a5940cbad5ef4bec" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

這邊主要是提到 state 參數的使用方式， 詳細的步驟可以參考教學部落格 [如何透過 Golang 開發 OAuth2 的 PKCE – 以 LINE Login 為例](https://engineering.linecorp.com/zh-hant/blog/pkce-line-login/) 。這邊也有列出在官方文章中的使用流程說明：

![img](https://developers.line.biz/assets/img/web-login-flow.2af66354.svg)

有兩張圖來對照可以看得更清楚，由於 `state` 是網站（或是 App) 開發商所隨機產生出來的一串文字。可以做為檢查之用，避免 Open ID 的需求被中間人攻擊後發送奇怪的訊息。這邊也提供一些作為 `state` 的開發指南：

- `state` 文字本身應該是沒有任何意義的，無法被其他人所猜透。
- `state` 文字每一次的認證請求應該都需要不同，才能做到保護。



#### 參考文章:

-  [如何透過 Golang 開發 OAuth2 的 PKCE – 以 LINE Login 為例](https://engineering.linecorp.com/zh-hant/blog/pkce-line-login/) 
- [開發LINE聊天機器人不可不知的十件事](https://engineering.linecorp.com/zh-hant/blog/line-device-10/) 



## 依照不同流程 OS 進行 LINE Login 的用戶流程範例 (iOS)

<script async class="speakerdeck-embed" data-slide="49" data-id="0e9f6182ae864568a5940cbad5ef4bec" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

以下兩個是根據不同的 OS 進行 LINE Login 的時候產生的使用者流程的範例，因為 iOS 跟 Android 兩者有些許的差異，特地使用兩張投影片來解釋。首先在這張 iOS 的投影片裡面有敘述在 LINE 裡面跟外部瀏覽器（Safari, Chrome) 使用 LINE Login 的流程：

- **應用程式內瀏覽器 (LINE)** ：
  - 開啟 LINE App -> 同意畫面（僅第一次） -> 開啟應用程式內瀏覽器（IAP: In-App-Browser) 的網站。
- **外部瀏覽器登入 (Safari, Chrome):**
  - 自動開啟 LINE App ->  同意畫面（僅第一次） -> 開啟自動登入畫面 ->  開啟應用程式內瀏覽器（IAP: In-App-Browser) 的網站。


## 依照不同流程 OS 進行 LINE Login 的用戶流程範例 (Android)

<script async class="speakerdeck-embed" data-slide="49" data-id="0e9f6182ae864568a5940cbad5ef4bec" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

可以發現 Android 開啟上跟 iOS 差別就是使用外部瀏覽器開啟網址的時候，唯一的差異就是開啟 LINE App 會有相關的詢問。以下詳細敘述如下:

- **外部瀏覽器登入 (Safari, Chrome):**
  - **詢問是否要開啟 LINE App** -> 自動開啟 LINE App ->  同意畫面（僅第一次） -> 開啟自動登入畫面 ->  開啟應用程式內瀏覽器（IAP: In-App-Browser) 的網站。

## 外部瀏覽器的登入流程（示意圖）

<script async class="speakerdeck-embed" data-slide="50" data-id="0e9f6182ae864568a5940cbad5ef4bec" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

最後是指在外部瀏覽器（通常是指桌機的狀況）的瀏覽行為，這裡也稍微解釋一下：

- 登入取得使用者同意( User Consent) 
- 查看瀏覽器是否有過去登入的紀錄
- 如果沒有登入過，會查看是否有 Universal Link (註解： 也就是可以直接開啟 App 的方式，通常在桌面系統是不會有的)。如果沒有，就會開啟網頁登入 LINE 。
- 完成登入，繼續 Redirect 到當初 LINE Login 設定的網址。

這邊主要差別就是，使用者如果是在桌面上來透過 LINE Login 這時候可能會進入帳號密碼的輸入畫面。但是許多使用者往往都忘記當初申請 LINE 的帳號跟密碼，這時候其實也是可以透過 QR Code 來登入。

並且， LINE Login  也可以知道使用者是如何登入:

- [Auto login](https://developers.line.biz/en/docs/line-login/integrate-line-login/#line-auto-login) 指的透過自動登入的設定，直接登入到該系統。
- [Log in with email address](https://developers.line.biz/en/docs/line-login/integrate-line-login/#mail-or-qrcode-login) 這邊就是使用 Email 的帳號跟密碼來登入。
- [Log in with QR code](https://developers.line.biz/en/docs/line-login/integrate-line-login/#mail-or-qrcode-login) 這邊是使用 QR Code 然後透過手機掃描來登入ˋ。
- [Single Sign On (SSO) login](https://developers.line.biz/en/docs/line-login/integrate-line-login/#line-sso-login) 透過 SSO 來登入。

#### 相關文件：

- [LINE Login: User authentication](https://developers.line.biz/en/docs/line-login/integrate-line-login/#authentication-process)

## 關於轉換目標端瀏覽器的設定方法

<script async class="speakerdeck-embed" data-slide="51" data-id="0e9f6182ae864568a5940cbad5ef4bec" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

在使用 LINE Login 的時候，就是依照上述三頁投影片的方式來載入。 也就是 iOS 與 Android 中 App 中的 In-App-Browser 以及透過外部瀏覽器的方式來登入。但是如果有需要指定開啟某一個登入方式（比如說：一定要透過外部瀏覽器），可以參考本篇的內容：

- **外部瀏覽器 (External Browser):**
  - 在 URL 的參數中加上 `openExternalBrowser=1` ，相關的文件可以參考 [Opening a URL in an external browser](https://developers.line.biz/en/docs/line-login/using-line-url-scheme/)
- **在 Android 中指定使用 Chrome In-App-Browser 開啟 (Opens target URL in a Chrome custom tab ):**
  - 在 URL 的參數中使用 `openInAppBrowser=0` ，相關文件可以參考 [Opening a URL in an external browser](https://developers.line.biz/en/docs/line-login/using-line-url-scheme/)

#### 相關文件

-  [Opening a URL in an external browser](https://developers.line.biz/en/docs/line-login/using-line-url-scheme/)



## 結論：

<a id="summary"></a>

以上就是「LINE Bot 開發指南」第五部分的補充與分享，想要知道更多內容可以查看完整投影片，或是找到其他篇的文章來了解。 

想了解更多開發者的活動？  立即加入「LINE 開發者官方社群」官方帳號，就能收到第一手 Meetup 活動，或與開發者計畫有關的最新消息的推播通知。▼

「LINE 開發者官方社群」官方帳號 ID：@line_tw_dev
![img](https://www.evanlin.com/images/2020/line-tw-dev-qr.png)

