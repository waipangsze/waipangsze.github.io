---
layout: post
title: Linux | Linux 權限詳解（chmod)
categories: [Linux]
tags: [Linux, HPC, Terminal, shell script, chmod]
author: wpsze
date: 2025-05-14 11:50:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/WZjshPy.png
banner_img: https://i.imgur.com/WZjshPy.png
---

在Linux系統中，檔案權限受到嚴格控制，進行特定操作時必須擁有相應的權限才能成功執行。

- Linux中的檔案權限類型主要包括讀取、寫入和執行，分別用字母 r、w、x 表示。

權限的層級分為擁有者、群組和其他用戶。每個檔案可以針對這三個層級設置不同的 rwx（讀寫執行）權限。通常，一個檔案只能屬於一個使用者和一個群組。如果其他使用者需要訪問該檔案的權限，可以將其加入擁有權限的群組，並且一個使用者可以同時屬於多個群組。

在Linux中，通常使用 **chmod** 指令來設置和變更檔案的權限。

Linux chmod（英文全拼：change mode）指令是控制使用者對檔案的權限的指令

Linux/Unix 的檔案呼叫權限分為三級 : **檔案擁有者（Owner）、使用者群組（Group）、其它使用者（Other Users）**。

![](https://i.imgur.com/diwTOG0.png)

第一位代表的是文件的類型，類型可以是下面幾個中的一個：

```text
d代表的是目錄(directroy)
-代表的是檔案(regular file)
s代表的是套字檔(socket)
p代表的管道檔案(pipe)或命名管道檔案(named pipe)
l代表的是符號連結檔(symbolic link)
b代表的是該檔案是面向區塊的裝置檔案(block-oriented device file)
c代表的是該檔案是面向字元的裝置檔案(charcter-oriented device file)
```

只有檔案擁有者和超級使用者可以修改檔案或目錄的權限。可以使用絕對模式（八進制數字模式），符號模式指定檔案的權限。

![](https://i.imgur.com/2O0KdMf.png)


```console
chmod [-cfvR] [--help] [--version] mode file...
```

- `-c` : 若該檔案權限確實已更改，才顯示其更改動作
- `-f` : 若該檔案權限無法被變更也不要顯示錯誤訊息
- `-v` : 顯示權限變更的詳細資料
- `-R` : 對目前目錄下的所有檔案與子目錄進行相同的權限變更(即以遞歸的方式逐一變更)
- `--help` : 顯示輔助說明
- `--version` : 顯示版本

# Example

將檔案 file1.txt 設為所有人皆可讀取 :
```
chmod ugo+r file1.txt
```

將目前目錄下的所有檔案與子目錄皆設為任何人可讀取 :
```
chmod -R a+r *
```

另外chmod也可以用數字來表示權限如 :
```
chmod 777 file
```

```console
-rw------- (600) 只有擁有者有讀寫權限。 
-rw-r--r-- (644) 只有擁有者有讀寫權限；而屬群組使用者和其他使用者只有讀取權限。 
-rwx------ (700) 只有擁有者有讀、寫、執行權限。 
-rwxr-xr-x (755) 擁有者有讀取、寫入、執行權限；而屬群組使用者和其他使用者只有讀取、執行權限。 
-rwx--x--x (711) 擁有者有讀取、寫入、執行權限；而屬群組使用者和其他使用者只有執行權限。 
-rw-rw-rw- (666) 所有使用者都有檔案讀取、寫入權限。 
-rwxrwxrwx (777) 所有使用者都有讀取、寫入、執行權限。
```


# Reference

1. [Linux chmod命令](https://www.runoob.com/linux/linux-comm-chmod.html)
2. [Linux权限详解（chmod、600、644、666、700、711、755、777、4755、6755、7755）](https://cloud.tencent.com/developer/article/2098260)