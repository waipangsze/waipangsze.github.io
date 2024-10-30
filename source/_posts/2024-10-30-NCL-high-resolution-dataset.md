---
layout: post
title: MPAS | NCL High-resolution coastlines
categories: [MPAS]
tags: [WRF,MPAS,NWP,HPC]
author: wpsze
date: 2024-10-30 11:51:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/RwpxULg.png
banner_img: https://i.imgur.com/RwpxULg.png
---

# NCL

## High-resolution coastlines

One way to get **high-resolution coastlines** in your NCL maps is by downloading and installing the multi-resolution coastline database RANGS (Regionally Accessible Nested Global Shorelines), developed by Rainer Feistel from Wessel and Smith's GSHHS (Global Self-consistent Hierarchical High-resolution Shoreline) database.
Note: do not use the high-resolution database for global data, as you will get streaks across your plot. This database is intended only for regional data.

NCL also supports [shapefiles](https://www.ncl.ucar.edu/Applications/shapefiles.shtml), if you need to add map outlines that NCL doesn't have.

To use this database, you must first download it from: 

- <https://www.io-warnemuende.de/rangs-en.html>

On this page you should see a table with ten *.zip files to download:

```
    rangs(0).zip     gshhs(0).zip
    rangs(1).zip     gshhs(1).zip
    rangs(2).zip     gshhs(2).zip
    rangs(3).zip     gshhs(3).zip
    rangs(4).zip     gshhs(4).zip
```

You must download all ten of these files, unzip them, and either put them in the directory

```
  $NCARG_ROOT/lib/ncarg/database/rangs
```

like micromamba/envs/venv/lib/ncarg/database/rangs/

(which NCL will look in by default), or put them somewhere else and set the environment variable NCARG_RANGS to this directory. The files take up **about 140 megabytes**, unzipped.

```
xxx/micromamba/envs/venv/lib/ncarg/database/rangs
$ ls
'gshhs(0).rim'  'gshhs(2).rim'  'gshhs(4).rim'  'rangs(0).zip'  'rangs(2).cat'  'rangs(3).cel'  'rangs(4).zip'
'gshhs(0).zip'  'gshhs(2).zip'  'gshhs(4).zip'  'rangs(1).cat'  'rangs(2).cel'  'rangs(3).zip'
'gshhs(1).rim'  'gshhs(3).rim'  'rangs(0).cat'  'rangs(1).cel'  'rangs(2).zip'  'rangs(4).cat'
'gshhs(1).zip'  'gshhs(3).zip'  'rangs(0).cel'  'rangs(1).zip'  'rangs(3).cat'  'rangs(4).cel'
```
