---
layout: post
title: Linux | NWP | Google launches free IDX cloud hosting service
categories: [MPAS]
tags: [Linux, WRF, MPAS, HPC, NWP, Google]
author: wpsze
date: 2025-04-25 11:01:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/DosaRrh.png
banner_img: https://i.imgur.com/DosaRrh.png
---

**Google 推出了 IDX 免費雲端主機服務，其硬體配置相當出色，配備：**

- **16核心CPU**
- **64GB記憶體**
- **300GB SSD固態硬碟**
- **網路頻寬最高可達2-5Gbps**

---

**IDX（Integrated Development Experience** 是Google為開發者設計的雲端開發平台。該平台提供的免費雲端主機，旨在為開發者創造便利的學習和測試環境。用戶無需進行複雜的環境設置，也不需要綁定信用卡，這大大降低了使用門檻，對於開發者來說是一個非常吸引的選擇。

不過，該雲端主機有一些限制，特別是在連線穩定性方面，通常每隔 x小時 會自動斷線。儘管如此，連線中斷並不會造成資料遺失，使用者只需重新登錄即可繼續進行開發和測試。

考慮到其免費特點和資料安全保障，這樣的使用體驗仍然具有較高的性價比，在實際應用中非常實用，因此是完全可以接受的。

# Project IDX 現已整合至 Firebase Studio

1. [Project IDX 現已整合至 Firebase Studio](https://firebase.google.com/docs/studio/idx-is-firebase-studio?hl=zh-TW)
   1. Firebase Studio 是一種代理的雲端式開發環境，可讓您直接在瀏覽器中使用強大的工具和 AI 代理程式。有了 Firebase Studio，您就能在同一個位置設計原型、建構、測試、發布及迭代全端 AI 應用程式。

# Setup

{% gi 9 3-3-3 %}
![](https://i.imgur.com/3Ujabys.png)
![](https://i.imgur.com/39GS3v0.png)
![](https://i.imgur.com/DosaRrh.png)
![](https://i.imgur.com/N8drA1Z.png)
![](https://i.imgur.com/yYqouJE.png)
![](https://i.imgur.com/VdKPFFi.png)
![](https://i.imgur.com/Z39gVTg.png)
{% endgi %}

## Intel OneAPI

{% note primary %}
- [**Intel OneAPI**](https://waipangsze.github.io/2023/05/06/Intel_OneAPI/)
{% endnote %}

{% fold info @Installation location %}
```log
  Installation Complete | Intel® oneAPI Base Toolkit
--------------------------------------------------------------------------------

  The following tools have been installed successfully:

    Intel® oneAPI Base Toolkit



  Installation location: /home/user/Documents/intel/oneapi


--------------------------------------------------------------------------------

  Installation Complete | Intel® oneAPI HPC Toolkit
--------------------------------------------------------------------------------

  The following tools have been installed successfully:

    Intel® oneAPI HPC Toolkit



  Installation location: /home/user/Documents/intel/oneapi
```
{% endfold %}

{% gi 8 2-2-2 %}
![](https://i.imgur.com/DoGxDAP.png)
![](https://i.imgur.com/GRAMHzt.png)
{% endgi %}

# References

1. [谷歌IDX免费云主机：16核CPU + 64GB内存 + 300GB硬盘](https://mp.weixin.qq.com/s/eAOFAF2WZPYs3EbP3vucow)
2. [永久免费的Google谷歌IDX VPS搭建代理节点教程：ArgoSB脚本 | Sing-box脚本 | Argo隧道 | 赛风VPN多国家分流；独家总结使用注意点与技巧 - 四月 21, 2025](https://ygkkk.blogspot.com/2025/04/google-idx-vps.html)
   1. 关于分配的本地ip地区，**如果你原先代理是东半球的国家，大概率分配的都是台湾的谷歌IP**，如果是西半球的国家代理，大概率分配的都是美国的谷歌IP
   2. **教程仅测试关闭IDX网页后台的测试时长，仅可用1小时左右**。如果一直开着IDX网页，持续时间会有多久呢？大家自测，欢迎反馈
   3. 大多数网友测试了：**开着前台IDX网页，24小时可用，过后重置**。
3. [Google is bringing Android Studio app development to the web with Project IDX | July 2024](https://www.techloy.com/google-is-bringing-android-studio-app-development-to-the-web-with-project-idx/)