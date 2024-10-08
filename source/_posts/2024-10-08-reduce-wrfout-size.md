---
layout: post
title: WRF | Reduce the amount of wrfout data
categories: [WRF]
tags: [wrfout]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/RwpxULg.png
banner_img: https://i.imgur.com/RwpxULg.png
---

# How to reduce the amount of wrfout data

- [How to reduce the amount of wrfout data](https://forum.mmm.ucar.edu/threads/how-to-reduce-the-amount-of-wrfout-data.8642/)


Two methods,

- Go into the WRF/Registry/Registry.EM_COMMON file, and then look for the variables you don’t want. You can remove the ‘h’ from out I/O column. After you do this for all of the variables you want removed, you will need to save the file, go back to the WRF/ directory, issue a ‘clean -a’ and then reconfigure and recompile
- You can also use the Runtime I/O option, which will allow you to add a couple lines to your namelist, and create a text file that the model will read when it is run. With this method, you don't have to recompile the code, but you have to be careful to type everything correctly, and have no spaces in the text file. You can read about it here: <http://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.1/users_guide_chap5.html#runtimeio>

{% note warning %}
Just be careful removing variables. If you ever want to go back and plot specific things, or if you will use the files for anything else later, you may be missing something you didn't realize you would need.
{% endnote %}

# iofields_filename not working WRFv4

- [iofields_filename not working WRFv4](https://forum.mmm.ucar.edu/threads/iofields_filename-not-working-wrfv4.12353/#post-29058)
- iofields_filename = 'myoutfields.txt','youtfields.txt','myoutfields.txt','myoutfields.txt',
- Your 'myoutfields.txt' file is set up like
```text
-:h:0:Times,LU_INDEX,ZS,DZS,FNM
-:h:0:FNP,RDNW,RDN,DNW,DN,CFN
-:h:0:CFN1,THIS_IS_AN_IDEAL_RUN
-:h:0:CF1,CF2,CF3,ITIMESTEP
-:h:0:SHDMAX,SHDMIN,SNOALB,TSLB
-:h:0:SMOIS,SH2O,SMCREL,SFROFF
```

But it should all be on a single line. Change the file so that it's in the following format:

```text
-:h:0:Times,LU_INDEX,ZS,DZS,FNM,FNP,RDNW,RDN,DNW,DN,CFN,CFN1,THIS_IS_AN_IDEAL_RUN,CF1,CF2,CF3,ITIMESTEP
```

{% note warning %}
I also had to remove the variable "Times" from the myoutfields.txt file to get it to run correctly, so you may need to do that, as well. Hopefully these will fix the problem.
{% endnote %}

# 減少wrfout輸出變數

- [減少wrfout輸出變數](https://mp.weixin.qq.com/s/wIeD4TjlZus-3HeHQRxfvw)
- 具體方法在userguide中也提到過，可以透過修改Registry來實現，ARW核心對應的是Registry/Registry.EM_COMMON文件，對其修改，然後再重新編譯（clean -a、configure、compile）。
- 但在3.2版本後，可以透過直接在namelist.input中可以指定剔除不需要輸出的變量，即：
```text
&time_control
iofields_filename = "my_file_d01.txt", "my_file_d02.txt"
ignore_iofields_warning = .true.,
```

在my_file_d01.txt檔案中，可以指明需要刪除的變數有哪些，具體內容如下：

```text
-:h:0:RAINC,RAINNC
```

- **-** 表示刪除，**+** 表示增加；

- **h** 表示history，這裡可以理解為輸出，i表示輸入；

- **0-24** ：表示經過的通道，一般 **預設0** ；

可以直接進行換行，但每一行的開頭需要類似 **-:h:0:格式**，然後後面的變數之間用英文逗號隔開。

- ignore_iofields_warning表示碰到錯誤時如何處理，如果設定為.true.則會列印警告訊息且繼續運行，如果設定為.false.時碰到錯誤會直接中斷，建議設定為.true.，所有的domain都通用，不用單獨設定。

其中註意事項包括：

- 多個domain可以使用同一個控製文件，類似上面的 my_file_d01.txt;
- my_file_d01.txt檔案中的變數需完全正確，如果某個變數設定不正確，可以正常執行，但會出現warning，如下：WARNING: Unable to modify mask for wdntc. Variable not found. File: my_file_d01.txt at line 4，根據報錯可以發現第4行的wdntc不正確，重新修改以後可以正常運作。
