---
layout: post
title: WRF | make -- time No such file or directory
categories: [WRF]
tags: [WRF, Installation]
author: wpsze
index_img: 
banner_img: 
---

# Issue

After moving to new cluster, a error appear when I reinstalled WRF. It is

> make[3]: time: No such file or directory

Or,

> make[3]: time: No such file or directory
> make[3]: [makefile:42: module_io_int_idx.o] Error 127 (ignored)

On WRFv450, v440, v433, v430

# Workaround

Reference: [compiling wrf 4.2 on centos7 failed--->Problems building executables, look for errors in the build log](https://forum.mmm.ucar.edu/threads/compiling-wrf-4-2-on-centos7-failed-problems-building-executables-look-for-errors-in-the-build-log.12864/)

'time' is a simple linux command that is typically included with your operating system. I believe, ultimately, the issue is that you're using a very old version of GNU to compile a newer version of WRF. GNU is free software, so can you try to install the latest version of GNU to see if that makes any difference in your compile? If you aren't sure how to do this, seek the help of a systems administrator (IT) at your institution. Once you install the new GNU version, you'll need to rebuild NetCDF and your MPI installation with the updated GNU, as well.

If you do that, and still run into the above error regarding the 'time' command, you can try a couple things:
1) Ask a systems admin for assistance in getting this command installed on your system. 'time' is a very simple linux command and typically comes readily available with the OS.
2) You can try modifying the configure.wrf file. After issuing 
```
./clean -a
./configure
```
3) , and reconfiguring, open your configure.wrf file and look for this line:
```
FC        = time $(DM_FC)
```
4) Remove 'time' from the line so that it now reads as
```
FC        =  $(DM_FC)
```
Save the configure.wrf with the change and try recompiling.
```
./compile -j 8 em_real 2>&1 | tee compile.log
```

or 

```sh
	#cp -r WRF-4.5.1-raw WRFV4.5.1

	cd WRFV4.5.1/
	
	./clean -a
	./configure 
	##  34 (dmpar) GNU [gfortran/gcc]

	## rewrite: The character class \s will match the whitespace characters <tab> and <space>.
	## and don't include $(DM_FC) 
	sed -i "s/\s\stime\s//g" configure.wrf
	#sed -i "s/-DNMM_CORE=$(WRF_NMM_CORE)/-DNMM_CORE=$(WRF_NMM_CORE)\s-DLANDREAD_STUB/g" configure.wrf

	./compile -j 4 em_real 2>&1 | tee compile.log
```