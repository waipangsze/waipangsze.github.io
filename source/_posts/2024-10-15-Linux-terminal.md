---
layout: post
title: Linux | Terminal Command
categories: [Terminal]
tags: [HPC]
author: wpsze
date: 2024-10-15 16:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

Linux

```sh

# -m displays percentages
# -N displays line numbers
# -i ignores case when searching
# h Display the help interface
# Q quits less command
less -miN file # alias less='less -miN'

sh run.sh | tee output.txt
# If you want to print the error output to the screen and the file at the same time
sh run.sh 2>&1 | tee lsls.log

sh run.sh >/dev/null 2>&1 &

# -C : Print Around Lines
grep -irn -C 3 "xxx" file
grep -irn -C 3 "xxx" ./*

find ./ -name "xxx"
find ./ -type f | wc -l # count how many files
find ./ -size +1M       # large 1MB size

ldd xxx

rename IMG img IMG* # IMG001.jpg to img001.jpg
```