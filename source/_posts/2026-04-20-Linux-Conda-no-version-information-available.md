---
layout: post
title: Linux | conda | libtinfo.so.6 no version information available
categories: [Linux]
tags: [conda, Micromamba]
author: wpsze
date: 2026-04-20 06:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/0SF9NEC.png
banner_img: https://i.imgur.com/0SF9NEC.png
---

- [Conda/Micromamba](https://waipangsze.github.io/2024/03/02/Conda_Micromamba/)

---

# Error

{% note primary %}
The error `libtinfo.so.6`: no version information available typically occurs because the ncurses package provided by the default Conda channel lacks symbol versioning, which system binaries (like bash or samtools) expect.
{% endnote %}

# Check and Fix

根據 StackOverflow 上的建議，我需要升級 conda-forge 的 ncursesw

```
micromamba install -c conda-forge ncurses
```

但還是會報警。對比發現是安裝的版本來自 anaconda channel，而非conda-forge

```
micromamba list ncurses

  Name     Version  Build       Channel 
──────────────────────────────────────────
  ncurses  6.5      h7934f7d_0  anaconda
```

原來是兩個 channel 同時存在相同版本，在沒有設定channel priority的情況下可能優先安排了 anaconda 的版本。

為了確保安裝到 conda-forge 版本，還需要指定版本和 build 進行安裝

```
micromamba search ncurses
micromamba install -c conda-forge ncurses=6.4=hcb278e6_0
# micromamba install -c conda-forge ncurses=6.4=hcb278e6_0 --force-reinstall
```
Done

# remove channcels: defaults

```
micromamba config append channels conda-forge
micromamba config set channel_priority strict
```

# References

1. [conda 修复 'no version information available (required by ***)"](https://zhuanlan.zhihu.com/p/646413186)