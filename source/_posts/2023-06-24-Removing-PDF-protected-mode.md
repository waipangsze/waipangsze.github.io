---
layout: post
title: Removing PDF protected mode
categories: [Linux]
tags: [PDF]
author: wpsze
index_img: 
banner_img: 
---

# 

Removing PDF usage restrictions, 

> view [qpdf](https://qpdf.sourceforge.io/), 

```sh
$ sudo apt install qpdf
$ qpdf --decrypt restricted-input.pdf unrestricted-output.pdf
```
