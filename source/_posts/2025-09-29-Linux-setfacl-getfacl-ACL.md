---
layout: post
title: Linux | setfacl | getfacl | ACL 文件访问控制列表
categories: [Linux]
tags: [Linux, HPC, Terminal, shell script, chmod]
author: wpsze
date: 2025-09-29 06:50:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/v9h42bG.png
banner_img: https://i.imgur.com/v9h42bG.png
---

- [Linux | Linux 權限詳解（chmod)](https://waipangsze.github.io/2025/05/14/Linux-chmod/)

---

# getfacl

`getfacl` 指令來自英文詞組 **get file access control list** 的縮寫，其功能是顯示檔案或目錄的ACL策略。對指定的檔案或目錄進行精確的權限控制，`FACL`（**File Access Control List，檔案存取控制清單**）一定是不二之選

![](https://i.imgur.com/sRq87WX.png){width=400}

```console
 $ getfacl maxmin.py
# file: maxmin.py
# owner: wpsze
# group: wpsze
user::rw-
group::rw-
other::r--
```

# setfacl

`setfacl` 命令 – 设置文件 `ACL` 策略规则

![](https://i.imgur.com/IQnDFUA.png){width=400}

- 参考示例 
  - 对目录进行FACL策略规则设置，允许指定用户进行读、写、执行操作:
    - `setfacl -Rm u:user:rwx File`
  - 清除指定目录上已有的FACL策略规则：
    - `setfacl -x u:linuxcool File`




