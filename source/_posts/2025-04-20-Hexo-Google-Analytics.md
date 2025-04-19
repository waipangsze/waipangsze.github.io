---
layout: post
title: Hexo | Google Analytics 4
categories: [Hexo]
tags: [Hexo, github, Google Analytics 4]
author: wpsze
date: 2025-04-20 00:05:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/EZLrwHq.png
banner_img: https://i.imgur.com/EZLrwHq.png
---

# Google Analytics

Google Analytics 是數百萬個網站和應用程式擁有者的首選平台，可幫助他們深入瞭解網站和應用程式的成效。

- [Google Analytics](https://developers.google.com/analytics?hl=zh-tw)
- [[GA4] 新一代 Analytics 隆重登場：Google Analytics 4](https://support.google.com/analytics/answer/10089681?hl=zh-Hant)

Google Analytics（谷歌分析）是一款由 Google 提供的專業數據分析工具，旨在幫助網站擁有者及商戶深入了解其網站的流量和用戶行為。它具備多項強大的功能，具體包括：

1. **實時數據分析**：
   - 能夠即時查看網站的訪問量、活躍用戶及其行為，讓商戶即時掌握網站運行狀況。

2. **流量來源追蹤**：
   - 分析訪客的來源，包括自然搜尋、付費廣告、社交媒體及直接訪問等，幫助商戶了解哪些渠道最有效。

3. **用戶行為分析**：
   - 追蹤用戶在網站上的行為，包括瀏覽的頁面、停留時間、跳出率等，這些數據有助於優化網站結構和內容。

4. **轉換追蹤**：
   - 設定目標並追蹤用戶完成特定行動（如填寫表單、購買產品）的情況，以評估行銷活動的成效。

5. **受眾分析**：
   - 提供詳細的用戶資料，包括年齡、性別、地理位置和興趣等，幫助商戶針對特定受眾制定行銷策略。

6. **自訂報告**：
   - 用戶可以創建自訂報告，根據特定需求分析數據，這樣能更靈活地獲取所需資訊。

7. **電子商務分析**：
   - 專為網上商店設計的功能，可以追蹤產品銷售、收入、交易數量及購物車放棄率等，幫助商戶優化銷售流程。

8. **A/B 測試**：
   - 提供測試功能，允許商戶比較不同版本的網頁表現，從而找出最佳設計和內容。

# Google Analytics 4 (GA4)

GA4 同時支援手機應用程式和網頁，而非好像舊版般，只能分析網頁。其次，GA4 會根據顧客的生命週期分析數據，讓商戶從不同階段，包括商戶開發（Acquisition）、參與（Engagement）、營利（Monetization）和回訪率（Rentention），清楚了解他們的消費行為。

# Setup

{% gi 6 3-3 %}
![](https://i.imgur.com/5xxIQaJ.png)
![](https://i.imgur.com/EZLrwHq.png)
![](https://i.imgur.com/LzNB76k.png)
{% endgi%}

## on Hexo

{% note primary %}
- copy above code into end of each `.ejs` file
{% endnote %}

# References

1. [【網絡營銷知識庫】Google Analytics 4 (GA4) 面世！一文看清基礎設定和升級步驟！](https://www.boutir.com/zh-hk/academy/google-analytics-4-ga4-basic-setup-upgrade-steps-overview)
2. [GA4 是什麼？ 5分鐘看懂最完整的 GA4 全解析與新功能設定教學](https://ranking.works/knowledge/ga4/)
3. [2023 實戰GA4教學：從基礎到高級設定立刻上手Google Analytics 4](https://www.kickads.co/zh/google-analytics-4-ga4-essential-guide/)
4. [GA4是什麼？Google Analytics 4基本功能介紹篇｜天矽科技客製化網頁設計](https://www.tsg.com.tw/blog-detail2-168-0-ga4.htm)