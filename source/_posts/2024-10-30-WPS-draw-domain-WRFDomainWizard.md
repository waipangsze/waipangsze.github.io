---
layout: post
title: WRF | WPS | WRFDomainWizard
categories: [WRF]
tags: [WRF,WPS,NWP,HPC]
author: wpsze
date: 2024-10-30 22:26:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/e9Xd5Z3.png
banner_img: https://i.imgur.com/e9Xd5Z3.png
---

# WRF | WRFDomainWizard：更简单的确定区域和网格

- <https://mp.weixin.qq.com/s/GxUqMWWyKeOXN4DbuSDubQ>

WRF模拟中目标区域确定后，确定网格数目一直是一个比较烦人的问题，单层网格还好，嵌套网格尤其麻烦（eg. 要确定嵌套网格的数据、不同层网格的起始点等）。因此，本篇向大家介绍一个很好的工具，可以更简单的确定网格的相关参数。

该工具为：<https://jiririchter.github.io/WRFDomainWizard/>

from <https://github.com/JiriRichter/WRFDomainWizard>

该工具可以在地图上进行手动地勾画，勾画的同时会在左边同时形成相关参数，可以更方便的确定网格的相关参数，可能略有的不足就是只有一些地点名的标识，没有明确的省界、县级等区域边界，具体情况还请自己探索吧。

![](https://i.imgur.com/4nQVELM.jpeg)
![](https://i.imgur.com/yRo6vyN.png)
![](https://i.imgur.com/e9Xd5Z3.png)