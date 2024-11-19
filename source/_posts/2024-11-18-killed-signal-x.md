---
layout: post
title: MPAS | killed - signal X
categories: [MPAS]
tags: [MPAS, WRF, NWP]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2024-11-18 09:55:00
index_img: https://i.imgur.com/htYBb8s.png
banner_img: https://i.imgur.com/htYBb8s.png
---

# KILLED BY SIGNAL: 9 (Killed)

{% note primary %}
Ouf Of Memory (OOM). A rough approximation for the memory required to run a simulation on a mesh with N cells is N * 0.175 MB
{% endnote %}

- [atmosphere_model - bad termination of one of your application processes](https://forum.mmm.ucar.edu/threads/atmosphere_model-bad-termination-of-one-of-your-application-processes.10105/)

I agree that the issue may be a memory limitation. A rough approximation for the memory required to run a simulation on a mesh with **N cells is N * 0.175 MB**, which suggests **around 7.2 GB of memory would be required for the 120-km mesh with 40962 cells**. There is more discussion on computational requirements in [this thread](https://forum.mmm.ucar.edu/phpBB3/viewtopic.php?f=12&t=10047) that may be useful.

