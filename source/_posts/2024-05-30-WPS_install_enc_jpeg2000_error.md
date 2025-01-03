---
layout: post
title:  WRF | error - jas_image_t aka struct anonymous has no member named inmem_ and image.inmem_=1
categories: [WRF]
tags: [WPS, Installation]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/RwpxULg.png
banner_img: https://i.imgur.com/RwpxULg.png
---

# Issue

While compiling WPSv3.8.1,

```
 17 gcc -c  -D_UNDERSCORE -DBYTESWAP -DLINUX -DIO_NETCDF -DBIT32 -DNO_SIGNAL -D_MPI -I/home/wpsze/WRF/WRFv440/Library/include/ -DUSE_JPEG2000 -DUSE_PNG -D__64BIT__ enc_jp    eg2000.c
 18 enc_jpeg2000.c: In function ‘enc_jpeg2000_’:
 19 enc_jpeg2000.c:141:10: error: ‘jas_image_t’ {aka ‘struct <anonymous>’} has no member named ‘inmem_’
 20   141 |     image.inmem_=1;
 21       |          ^
 22 make[2]: [Makefile:75: enc_jpeg2000.o] Error 1 (ignored)
```

# Search

From Grib2 output is broken -- <https://github.com/wrf-model/WRF/issues/993>

I'm unsure if the issue has been resolved, or if anyone still cares, but I think I see what might be going on here. The offending part of enc_jpeg2000.c relates to a C struct of type jas_image_t (in line 87 in the version of this file I'm looking at):

>    jas_image_t image;

It throws an error when the code tries to access the inmem_ element:

>     image.inmem_=1;

This C struct is defined by jasper/jas_image.h (version 2.0.14, which is what I have on the system I'm working on).
```
/* Image class. */

typedef struct {

        jas_image_coord_t tlx_;
        /* The x-coordinate of the top-left corner of the image bounding box. */

        jas_image_coord_t tly_;
        /* The y-coordinate of the top-left corner of the image bounding box. */

        jas_image_coord_t brx_;
        /* The x-coordinate of the bottom-right corner of the image bounding
          box (plus one). */

        jas_image_coord_t bry_;
        /* The y-coordinate of the bottom-right corner of the image bounding
          box (plus one). */

        int numcmpts_;
        /* The number of components. */

        int maxcmpts_;
        /* The maximum number of components that this image can have (i.e., the
          allocated size of the components array). */

        jas_image_cmpt_t **cmpts_;
        /* Per-component information. */

        jas_clrspc_t clrspc_;

        jas_cmprof_t *cmprof_;

//      bool inmem_;

} jas_image_t;
```

You can see that the bool component inmem_ has been commented out. If you go back to older versions of jasper you can get back to one that doesn't have inmem_ commented out. For example, in the [github repo for this library](https://github.com/jasper-software/jasper), that would be something like SHAH 2b2efba4eda0a654ab02 or version 1.900.24, which dates to Nov 2016.

# Workaround
## In WPSv3.8.1 code

```
./ungrib/src/ngl/g2/dec_jpeg2000.c:104:    printf(" inmem %d \n",image->inmem_);
./ungrib/src/ngl/g2/enc_jpeg2000.c:141:    image.inmem_=1;
```

and, modify enc_jpeg2000.c

```
 /* 
  * Does not seem to be needed, and throws a compiler error
  * image.inmem_=1;
  */
```

Finally, ungrib.exe is generated.

but, is it right?  will that break something...?


Ref:
[WRF 3.9.1.1 安装 记录](https://www.cnblogs.com/jiangleads/articles/11422607.html)
>此外，如果安装的是较新的jasper版本，还需要更改文件enc_jpeg2000.c （在目录WPS/ungrib/src/ngl/g2/enc_jpeg2000.c）
> 参见 <http://forum.wrfforum.com/viewtopic.php?f=20&t=10035>
>将第141行
>> image.inmem_=1;
>注释掉
>这样，就能生成ungrib.exe了
