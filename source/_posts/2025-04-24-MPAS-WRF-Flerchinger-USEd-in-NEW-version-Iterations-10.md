---
layout: post
title: MPAS | WRF | Flerchinger USEd in NEW version. Iterations=10
categories: [MPAS]
tags: [WRF, MPAS, HPC, NWP, Noah surface scheme]
author: wpsze
date: 2025-04-24 13:10:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/QQZA3kH.png
banner_img: https://i.imgur.com/QQZA3kH.png
---

**This print comes from the Noah surface scheme**, but if you weren't using that scheme, you likely would still get a segmentation fault.

{% fold info @phys/module_sf_noahlsm.F#L1572 %}
```fortran
! ----------------------------------------------------------------------
! OPTION 2: EXPLICIT SOLUTION FOR FLERCHINGER EQ. i.e. CK=0
! IN KOREN ET AL., JGR, 1999, EQN 17
! APPLY PHYSICAL BOUNDS TO FLERCHINGER SOLUTION
! ----------------------------------------------------------------------
         IF (KCOUNT == 0) THEN
             PRINT *,'Flerchinger USEd in NEW version. Iterations=',NLOG
                  FK = ( ( (HLICE / (GS * ( - PSIS)))*                    &
                       ( (TKELV - T0)/ TKELV))** ( -1/ BX))* SMCMAX
!            FRH2O = MIN (FK, SMC)
             IF (FK < 0.02) FK = 0.02
             FREE = MIN (FK, SMC)
```
{% endfold %}

- [A Compendium of WRF Errors | April 17, 2011](https://stupidcomputerhints.blogspot.com/2011/04/compendium-of-wrf-errors.html)
  - Flerchinger USEd in NEW version. Iterations= 10
    - Meaning: **This condition (technically not an error) is thrown by the NOAH Land Surface Model module**. Apparently, it involves the amount of supercooled water available if the ground is frozen. Often, this is because **there is very low soil temperature in your model run**.
    - Effect: Because this is an explicit method of solving for the supercooled water available, the model slows down by several orders of magnitude. 
    - Solution: Unclear, but changing from the NOAH land surface model suppresses the error.
- [wrf.exe能启动一天还没转完就报Flerchinger USEd in NEW version. Iterations= ...  | 2017-10-17 15:10:41 ](https://bbs.06climate.com/forum.php?mod=viewthread&tid=56620)
  - 首先这个错误应该是来自路面过程，应该是Noah LSM或者NoahMP其中的一个，可能会有类似the model is losing water这样的错误，这种错误一般情况下会具体告诉你究竟爆在了哪个点，你去看看这个点是不是一个孤立的水体，对于复杂地形和复杂下垫面的情况就容易出现你碰到的这种问题，**你可以尝试换一套下垫面数据，当然如果你对下垫面要求很高，那只能是自己做修改**。
  - 因为我也被这个问题困扰过，我一般都只能换下垫面。
- [WRF on AWS segmentation fault | Oct 16, 2019](https://forum.mmm.ucar.edu/threads/wrf-on-aws-segmentation-fault.8531/#p14695)
  - **This print comes from the Noah surface scheme**, but if you weren't using that scheme, you likely would still get a segmentation fault. Many times this message **indicates a problem with the input soil data**. I notice that in the initial condition file (wrfinput_d01), **the variable TSLB (soil temperature) has several spots with values of 0 K**. Then later in the run (after 78 output times - or 78 hours), I see these 0 K values coming back in the same general area for T2, T, and TSLB, which I believe leads to the demise of the run. I would be curious to know whether the met_em* files also show similar values. You may need to play around with input. Take a look at this post from another WRF forum: <http://forum.wrfforum.com/viewtopic.php?p=26565>. The user ronbeag had a similar problem (theirs was related to soil moisture instead of soil temp), and perhaps may have some information that will be useful for you.
- [记一次非常奇怪的WRF-CMAQ错误 | 2020-01-13](https://atmosai.github.io/2020/01/%E8%AE%B0%E4%B8%80%E6%AC%A1%E9%9D%9E%E5%B8%B8%E5%A5%87%E6%80%AA%E7%9A%84wrf-cmaq%E9%94%99%E8%AF%AF/)
  - 通过搜索发现可能是 `wrfinput_d0*` 文件中的 **SH2O 或 SMOIS 变量存在0或负值导致**。然后检查这两个变量值发现，**SMOIS 中部分区域的值为0**。然后更改了Vtable文件，重新运行WPS和WRF模式，结果一切正常，问题解决！
- [Segmentation Error Because of gaps/issue in the input data | Jun 23, 2023](https://forum.mmm.ucar.edu/threads/segmentation-error-because-of-gaps-issue-in-the-input-data.13506/)
  - So I say the thread that it can be **because of 0 values in the input soil data**. Also when I examined the met files I say 0 values there.
  - The Issue got resolved. The downloaded met data did not had all the weather parameters. **I downloaded again with all weather parameters and it worked**
- [Using the ERA5 land/sea mask data in WRF | Oct 31, 2023 ](https://forum.mmm.ucar.edu/threads/using-the-era5-land-sea-mask-data-in-wrf.14449/#post-52119)
  - 5) Some (but not all) of the rsl files specific to each processor have strange repeated output:
    - **Flerchinger USEd in NEW version. Iterations= 10**
- [MPAS crashes with EXIT CODE: 174 | Apr 29, 2024](https://forum.mmm.ucar.edu/threads/mpas-crashes-with-exit-code-174.16944/#post-41670)
  - I think these errors (which unfortunately do not mention the word "error") **often result from problems with soil fields**. Could you **check the soil temperatures in your initial conditions file to verify that they are reasonable for all non-water cells?**
  - If the negative soil temperatures are only a result of the way that the soil temperature field was processed, then some adjustments to the processing workflow might help. On the other hand, if the soil temperatures in the original ERA5 files contain negative values (and assuming these should be in kelvins), then switching to a different dataset for soil fields might help.
- [MPAS-8.1 Restart Issues: Jobs Stopping Prematurely | Jul 18, 2024](https://forum.mmm.ucar.edu/threads/mpas-8-1-restart-issues-jobs-stopping-prematurely.18270/#post-44746)
  - Also, can you **save soil data (i.e., smcrel, sh2o, smois, tslb) at the time of restarting, and compare them with the data in your restart file? I would expect that they should be same**.

