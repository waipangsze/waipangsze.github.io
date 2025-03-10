---
layout: post
title: MPAS | MPAS-CMAQ | US EPA
categories: [MPAS]
tags: [MPAS, NWP, WRF, CMAQ, US EPA, ERA5, GFS]
author: wpsze
date: 2025-02-27 10:46:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/g3DB0G8.png
banner_img: https://i.imgur.com/g3DB0G8.png
---

# MPAS-CMAQ

- [The Next-Generation Air Quality Model | US-EPA | Motivation for Improving Combined Meteorological and Air Quality Models](https://www.epa.gov/cmaq/next-generation-air-quality-model)
  - New meteorological models have been developed that include seamless mesh refinement from global to local scales. These are the future of multi-scale global air quality models. Such mesh structures are ideal for air quality modeling since they cover the entire globe with a coarse mesh, but can resolve areas of interest like the United States with a much finer mesh. 
- [CMAQ: The Community Multiscale Air Quality Modeling System | US-EPA](https://www.epa.gov/cmaq)
- [The 2024 Release of MPAS-CMAQ](https://www2.mmm.ucar.edu/wrf/users/workshops/WS2024/presentations/day1/7_Willison.pdf)
  - MPAS-CMAQ Design
    - Unified coupler approach allows for coupling of CMAQ with either WRF or MPAS
    - New Model I/O (MIO)
    - CMAQ is treated as a column model when coupled with MPAS; horizontal transport handled in MPAS.
  - MPAS Updates
    - Added US EPA physics routines to MPAS
    - Implemented analysis FDDA as in WRF
    - Implemented indirect soil moisture data assimilation in PX LSM
    - Meteorological Evaluation
- [CMAS Forum | MPAS-CMAQ](https://forum.cmascenter.org/c/mpas-cmaq/99)
- [MPAS-CMAQ github](https://github.com/USEPA/CMAQ/tree/MPAS_CMAQ)
  - [MPAS_CMAQ_guide.pdf](https://github.com/USEPA/CMAQ/blob/MPAS_CMAQ/DOCS/Users_Guide/PDF/MPAS_CMAQ_guide.pdf)
  - The coupled system has been evaluated for global domains across several years (Wong et al. 2024, preprint: <https://gmd.copernicus.org/preprints/gmd-2024-52/gmd-2024-52.pdf>).
  - Unified Coupler
    - In this work, we adopted a simpler approach tailored for our specific application. For both the WRF-CMAQ and the MPASCMAQ coupled models, **CMAQ inherits the domain structure from either WRF or MPAS**, and therefore, the coupler inherits the map projection, grid alignment, and grid spacing seamlessly. For simplicity, we just need a straightforward mechanism for data exchange between the two model.
  - **Meteorological data is made available to drive the CMAQ model, and subsequently aerosol information is passed back to the meteorological model so that it can affect the radiation calculations (aerosol radiative direct effect).**
  - The unified coupler design addresses two major characteristic differences among the WRF-CMAQ and MPAS-CMAQ coupled models. 
    - Firstly, the WRF model decomposes the horizonal domain with rectangular grids and stores information using a 2-D data structure. In contrast, the MPAS model decomposes the horizonal domain with an unstructured mesh and stores information using a 1-D data structure. 
    - Secondly, in the WRF-CMAQ coupled model, the WRF time step could be sub-divided by the advection time step algorithm on the CMAQ side. In addition, two time steps of meteorological data are required and stored in a circular buffer to support interpolation. With the MPAS-CMAQ coupled model, the transport is handled in MPAS and the time step in CMAQ is fully synchronized with MPAS.
  - The preliminary results show the MPAS-CMAQ coupled model performed reasonably well with respect to ozone and PM2.5 for North America, where the fine mesh is located, as well as the rest of the world. For future work, we will implement the aerosol radiative direct effect in this coupled model with a robust and flexible method to handle different CMAQ chemical mechanisms. We will also implement a switch so the aerosol radiative direct effect can be turned on or off at run time.
  - Code and data availability
    - Entire MPAS-CMAQ with internal version CMAQ 5.4 is available at Zenodo (10.5281/zenodo.10982420).
    - MIO is available at Zenodo (10.5281/zenodo.10994279).
    - **data which was used in generating Figure 4 - 9, is available at Zenodo (10.5281/zenodo.10994244).**
    -  `$ ncdump -h mpas_July_avg.nc |head -n 40`
      {% fold info @mpas_July_avg.nc %}
      ```
      netcdf mpas_July_avg {
      dimensions:
        Time = UNLIMITED ; // (1 currently)
        nCells = 40962 ;
        nVertLevels = 50 ;
        nVertices = 81920 ;
        maxEdges = 10 ;
        StrLen = 64 ;
        nVertLevelsP1 = 51 ;
      variables:
        float O3(Time, nCells, nVertLevels) ;
          O3:long_name = "O3 concentration" ;
          O3:units = "ppmV" ;
          O3:cell_methods = "Time: mean" ;
        float hpbl(Time, nCells) ;
          hpbl:units = "m" ;
          hpbl:long_name = "Planetary Boundary Layer (PBL) height" ;
          hpbl:cell_methods = "Time: mean" ;
      ```
      {% endfold %}
      - {% gi 4 2-2 %}
        ![](https://i.imgur.com/SpkDS9P.png)
        ![](https://i.imgur.com/NhUWaaO.png)
        ![](https://i.imgur.com/6Spm7oN.png)
        ![](https://i.imgur.com/90b9mzX.png)
        {% endgi %}

- [Recent advancement of EPA’s global air quality modeling system: MPAS-CMAQ | 2023 | US-EPA](https://ral.ucar.edu/sites/default/files/docs//jon-willisonetalams2023mpasams.pdf)
  - {% gi 4 2-2 %}
    ![](https://i.imgur.com/s85kXsR.png)
    ![](https://i.imgur.com/bMwDQDX.png)
    ![](https://i.imgur.com/stNlI2P.png)
    ![](https://i.imgur.com/hFDPHpm.png)
    {% endgi %}

# MPAS-CMAQ compilation

- [Encounter an error during MPAS compilation | Lan 2025](https://forum.mmm.ucar.edu/threads/encounter-an-error-during-mpas-compilation.20474/)

vim bldit_project.csh
set CMAQ_HOME =/EM/wpsze/CMAQ/CMAQ-MPAS_CMAQ/CMAQ_5.5 ##改成自己的路径

./bldit_project.csh intel
cd /EM/wpsze/CMAQ/CMAQ-MPAS_CMAQ/CMAQ_5.5

vim config_cmaq.csh

# CMAQ

CMAQ（Community Multiscale Air Quality 通用多尺度空氣品質）模型，是美國環保署開發的第三代空氣品質預報和評估系統（Models-3）的核心組成之一。是一套**三維歐拉網格化的大氣化學和傳輸模擬系統**，能夠模擬整個對流層的臭氧、酸沉降、能見度和細顆粒物。**整個模式遵循「一個大氣」概念，可以同時處理從局部到半球的不同空間尺度的多個複雜的空氣品質問題**，作為一個有效的第三代空氣品質模型與評估工具，其能夠支持從策略分析到大氣科學研究的各類空氣品質模擬應用。

- **三維歐拉網格化的大氣化學和傳輸模擬系統**，其根據來自WRF的氣象資訊及場域內的污染排放清單，基於物理和化學反應原理模擬污染物等的變化過程，繼而得到具體時間點或時間段的預報結果
- 模式可預報多種污染物，其種類可達80多種。
- CMAQ可模擬的空間尺度以及時間尺度都跨越了數個數量級。對模式選擇不同的時間尺度，既可用於評估長時間（每年至數年）的污染大氣學，也可用於模擬短時間內（數週至數月）的污染源擴散情況，選擇不同的空間尺度，可用於城市或特定區域的模擬。 CMAQ也具有在不同尺度下同時處理多種污染的能力。
- CMAQ包含了以下的處理和化學傳遞模組：
  - 大氣-化學交接處理模組（MCIP）
  - JPROC
  - 初始條件模組（ICON）/ 邊界模組 (BCON)
    - ICON則是一開始整體領域空間的污染物初始濃度的建置
    - BCON則是從邊界傳輸到模擬領域的污染物濃度
    - BCON 與 ICON 均有兩種情境，分別是固定排放與變動排放
  - 化學傳輸模組（CCTM, CMAQ Chemistry-Transport Model）
  - 排放量資料(smoke)
- CMAQ 主程式所需要的輸入資料，主要有如下幾個必須要事先準備的輸入資料：
  - 來自WRF大氣模式的MCIP輸出
  - 參考MCIP大氣資料而計算的SMOKE排放量處理模組
  - 來自最外層固定的、或上一層執行後所產生的初始資料與邊界資料 
- 每個排放資料均為單一的化學物質，例如平均濃度小時值的輸出檔當中，懸浮微粒相關的物種濃度有ASO4I, ASO4J, ANH4I, ANH4J, ANO3I, ANO3J, AECI, AECJ, AOTHRI, AOTHRJ…，此類物種需要與兩種懸浮微粒波峰比例進行加總之後，方可得到PM2.5與PM10的濃度值。因此，CMAQ之CCTM模式執行完畢後，尚須使用 combine模組進行污染物整合。

![The framework for the WRF-SMOKE-CMAQ modeling system. From Yang, Xiaochun, et al. "New method for evaluating winter air quality: PM2. 5 assessment using Community Multi-Scale Air Quality Modeling (CMAQ) in Xi'an." Atmospheric Environment 211 (2019): 18-28.](https://i.imgur.com/EKYGIut.jpeg){width=500}
![](https://i.imgur.com/BQmCiOd.png){width=500}
![](https://i.imgur.com/g3DB0G8.png){width=500}
![](https://i.imgur.com/aK64IsW.png){width=500}

# 空氣品質模式簡介

- [空氣品質模式簡介與操作 - 概說](https://linux.vbird.org/enve/illustration.php#)
  - 空氣污染物
  - 排放量分析
  - 空氣品質的定義
  - 擴散模式 - 非化學反應
  - 軌跡模式 - 非化學反應
  - 箱模式 - 簡單化學模式
  - 網格模式 - 複雜化學模式

# 臭氧

臭氧的生成過程複雜，主要是由工廠、汽機車等所排放的廢氣之中含有具**揮發性的有機物 (VOCs, 揮發性有機化合物是指含碳的任何揮發性化合物)**、**氮氧化物 (NOx)** 等前驅物，在太陽光的照射下產生明顯的日變化，因此當有足夠的累積條件及陽光強度時，就會生成高濃度的臭氧。臭氧是最難控制的污染物之一，因為它不是人類活動直接排放的。取而代之的是，從車輛，發電廠，垃圾堆填區以及其他生物質和化石燃料燃燒設施中釋放出的危險化合物和一氧化二氮與陽光發生反應，形成這種二次污染物。根據香港環境保護署2019年的空氣質量監測結果，儘管政府努力減排和改善空氣質素，令整體空氣質量和其他主要污染物的水平在穩步改善，但過去20年來平均臭氧水平一直在上升，沒有下降的跡象。

- [區域性臭氧](https://www.legco.gov.hk/yr2022/chinese/panels/ea/papers/ea20220523cb1-271-2-c.pdf)
  - 臭氧是一個複雜的區域性空氣污染問題。臭氧並非從污染源直接排出，**而是經不同的空氣中的氮氧化物和揮發性有機化合物（ VOCs）在陽光下經光化學反應形成**。鄰近區域形成的臭氧，亦可以隨著風向傳輸到本地。因此，**減少臭氧是一個區域性問題，需要整個較廣區域的共同合作和努力**。
  - 另一方面，大氣中的臭氧會與其他氣體產生化學反應而被消耗，令臭氧水平下降，當中包括車輛排放的一氧化氮。然而，**近年本地車輛排放的一氧化氮逐步減少，同時亦減少一氧化氮與臭氧的化學反應**。如同不少主要國際城市的經驗，**這是整體改善空氣質素過程中可預見會間接導致路邊及市區臭氧濃度上升的過渡情況**。
  - 根據一些歐美地區的空氣污染管制經驗，減少從光化學反應所. **形成的臭氧常出現上述滯後現象**。所以，與不少主要國際城市處理臭氧問題一樣，**香港的臭氧水平相信會先略為增加，到達峰頂後才開始下降**。然而，**持續減少臭氧前驅物（即氮氧化物和 VOCs）的排放長遠可改善臭氧的水平。**
- [環保署公布二○一九年香港空氣質素監測結果](https://www.info.gov.hk/gia/general/202001/20/P2020012000747.htm)
  - 臭氧是複雜的空氣污染問題，亦是區域性問題。它不是從污染源直接排出，而是由空氣中的氮氧化物（NOx）及揮發性有機化合物（VOCs）在陽光下經光化學反應形成。近年本港臭氧濃度上升，除了因區域背景臭氧水平偏高外，本地車輛減少氮氧化物的排放，亦會間接導致路邊及市區的平均臭氧水平上升（**主要是因為少了一氧化氮與臭氧作化學反應，因而減少臭氧的消耗，令較多臭氧存在空氣中**）。
  - 歐美其他地區改善空氣污染的經驗也常有臭氧問題滯後的現象，因為複雜的光化學原因，煙霧問題和氮氧化物改善的初期常會突出臭氧問題，**當減排工作再持續一段時間後，臭氧濃度才開始下降**。
- [立法會二十二題：空氣中臭氧濃度 | 2019](https://www.info.gov.hk/gia/general/201901/30/P2019013000777p.htm)
  - 臭氧是一個複雜的區域性空氣污染問題。臭氧並非從污染源直接排出，而是經不同的空氣污染物在大氣中的化學反應所產生。**臭氧主要是氮氧化物（包括一氧化氮和二氧化氮及揮發性有機化合物在陽光下經光化學反應而形成**；而另一方面，**一氧化氮亦會與臭氧產生化學反應而把臭氧消耗及轉化成二氧化氮**。近年本地推行的車輛減排措施有效減少車輛氮氧化物（主要為一氧化氮，亦有二氧化氮） 的排放，但亦因此減少市區及路邊臭氧的消耗，導致臭氧有所上升，情況亦與很多其他城市在處理空氣污染物的經驗相似。要持續降低本港的臭氧濃度，整個區域包括香港必須共同持續減少氮氧化物及揮發性有機化合物的排放。
  - 至於針對臭氧的政策，政府正雙管齊下，一方面致力**減少本地形成臭氧的前驅污染物（即氮氧化物和揮發性有機化合物）**；另一方面則**加強區域性合作**。
- [2022 年 香港空氣質素](https://www.aqhi.gov.hk/common/api_history/tc_chi/report/files/AQR2022c_final.pdf)
  - 臭氧是光化學煙霧的主要成分。臭氧並非從污染源直接排出，而是由氮氧化物及揮發性有機化合物（VOCs）在陽光下經光化學反應形成。由於光化學反應需要幾個小時才能完成，所以某地方錄得的臭氧可能源自遠處排放的氮氧化物及揮發性有機化合物。因此，**臭氧主要是區域性的空氣污染問題**。
  - **臭氧是強烈的氧化劑，即使低濃度的臭氧也能刺激眼睛、鼻和咽喉。高水平臭氧更可增加人體感染呼吸系統疾病的機會，亦可令哮喘病等呼吸系統疾病患者的病情惡化**。
  - 在香港，高臭氧空氣污染日子多數於粵港澳大灣區（大灣區）天氣炎熱、晴朗和無風時出現，這種天氣有利於臭氧經光化學反應而形成和積聚。這類天氣情況多出現於夏秋兩季，特別是當有熱帶氣旋在台灣或菲律賓附近時，外圍下沉氣流會影響香港和大灣區。
  - 臭氧是複雜的區域性空氣污染問題。臭氧由氮氧化物和揮發性有機化合物等前驅物在陽光下進行複雜光化學反應形成，可以遠距離傳輸並影響下風區域。**另一方面，臭氧可與某些污染物（例如車輛等燃燒源排放的一氧化氮）發生化學反應並被消耗**。因此，在某一地方錄得的臭氧濃度受區域臭氧背景水平、在該地方產生的臭氧及其消耗情況影響。
  - **車輛排放的一氧化氮會與臭氧產生化學反應並消耗臭氧，因此交通繁忙地區的臭氧水平通常較車流量低的地區低**。
  - 自2000年代初開始，郊區的臭氧水平呈溫和上升趨勢，新市鎮和市區的上升趨勢則較為明顯。**本港（特別是新市鎮和市區）的臭氧水平上升趨勢主要是由背景區域臭氧的溫和增加及本地車輛排放減少導致，後者令大氣中的一氧化氮減少，因此減少通過化學反應而消耗臭氧**。
- [立法會十三題：空氣中的臭氧濃度 | 2020](https://www.info.gov.hk/gia/general/202005/20/P2020052000477.htm) 
  - **臭氧會與空氣中的一些污染物（例如一氧化氮（NO））產生化學反應而被消耗**。**本港臭氧濃度上升幅度遠較背景水平大的主因是本地車輛減少了NOx排放，導致少了NO與臭氧反應，因而減少臭氧的消耗，令更多臭氧存在空氣中，做成量度到的臭氧濃度有較大升幅**。但因為NOx及VOCs是臭氧的前驅污染物，持續減少NOx排放不但能令本港的二氧化氮水平降低，長遠更將會有助降低區域內及本港整體的臭氧濃度及超標情況。
- [大气中臭氧的产生、耗损与健康危害 | 2022](https://sthj.sh.gov.cn/hbzhywpt6040/hbzhywpt6066/20220727/3c05ccec7b334fb1b6302c0f11b9efed.html)
  - 氮氧化物（NOx=NO+NO2）是形成臭氧的主要前驅污染物之一，而臭氧與NOx有快速的光化學循環過程，是對流層臭氧產生和耗損的主要驅動力。 NO2在紫外光照射下發生光解產生NO和基態氧原子O(3P)，氧原子與空氣中的氧氣反應生成臭氧；**而NO可以透過與臭氧發生還原反應生成NO2和O2來消耗臭氧**。上述反應為快速循環過程，反應平衡主要依賴UV強度和NOx濃度。
  - 揮發性有機化合物（VOCs）也是形成臭氧的主要前驅污染物之一，因為VOCs氧化過程中形成的大氣烷基過氧自由基（RO2）或過氧羥基自由基（HO2）是將NO氧化為NO2的氧化劑。 VOCs的自由基氧化鏈是由與羥基自由基（OH）的反應引發的，水蒸氣存在下紫外線對臭氧的光解是對流層中OH的主要來源。在污染地區，醛類（如HCHO）、亞硝酸（HONO）和過氧化氫（H2O2）的光解也可能是OH或HO2自由基的重要來源。上述過程也伴隨著OH和NO2反應生成硝酸（HN臭氧），以及過氧基之間相互反應生成過氧化氫，這些反應都會逐步減少生成臭氧過程中催化劑的數量。大氣中VOCs包括生物來源和人為來源，對臭氧光化學形成有重要作用的VOCs類別有烷烴、烯烴、芳香烴、羰基化合物（如醛和酮）、醇、有機過氧化物和鹵化有機化合物（如鹵代烷）。
  - 雖然臭氧的光化學生成受到前驅物的濃度限制，但**臭氧與NOx、VOCs 間不是簡單的線性依賴關係，而是非線性變化的**。
  - 在典型的環境大氣顆粒物濃度水準下，臭氧和大氣顆粒的直接反應很慢，無法顯著減少臭氧的形成。此外，Cl自由基對碳氫化合物的氧化可導致過氧自由基的快速形成，並在特定的沿海環境中提高臭氧的生成速率。
  - 偏遠大陸地區和城市中心下風處的農村和郊區，NOx濃度較低，臭氧的淨產量隨著NOx的增加而增加。而市中心，特別是在繁忙街道和高速公路附近以及在電廠羽流中，**NOx濃度較高，在「滴定效應（NO+O3→NO2+O2）」作用下，臭氧因與NO反應耗損致使其濃度降低**。
  - **研究發現，新冠疫情期間，世界各地大氣中的臭氧濃度都升高。這是由於採取了管控措施，交通廢氣排放減少，相應氮氧化物排放減少，在「滴定效應」影響下，城市臭氧濃度自然而然升高了**。
  - 近地面臭氧濃度與太陽輻射和氣溫有很好的相關性，**太陽輻射強、氣溫高有利於臭氧生成**相關的大氣化學或光化學過程，故臭氧濃度較高

## 消耗臭氧的反應

一氧化氮（NO）與臭氧（O3）之間的反應是重要的化學過程，特別是在大氣化學中。當NO與臭氧反應時，會產生二氧化氮（NO₂）和氧氣（O₂）。這個反應的化學方程式可以表示為：

$$
\text{NO} + \text{O}_3 \rightarrow \text{NO}_2 + \text{O}_2 
$$

這個反應對於空氣品質有重要影響，因為NO是一種污染物，通常來自於汽車排放和工業活動，而O3則是一種主要的地面臭氧污染物。這些反應有助於減少大氣中的臭氧濃度，但同時也會產生NO₂，這也是一種有害的空氣污染物。

{% gi 4 2-2%}
![](https://i.imgur.com/SOd6pGr.png)
![](https://i.imgur.com/ctiahcF.png)
![](https://i.imgur.com/xUFhGT3.png)
{% endgi %}

# TW-行政院環境保護署

- [網格類模式的簡要描述](https://aqmc.moenv.gov.tw/airquality_3.html)
- [公告模式 CMAQ 使用方式指引](https://aqmc.moenv.gov.tw/datasheetlist_3.html)
  - 網格類公告模式CMAQ技術文件
  - 公告模式CMAQ排放量工具（SMOKE）使用國網方式指引
  - 附件二 新增污染源處理程序排放檔製作
  - 附件四 空氣品質模式訓練教材

# HK-EPD

## PATH Data Download

- [PATH v3.0 Data Download](https://path.epd.gov.hk/download.html)
  - Air Quality Module   
  - Meteorology Input
  - Emission Input
  - Initial and Boundary Conditions
  - Emission Editor and Extractor
  - Standard Hourly Pollutant Output
- [Briefing on the release of updated PATH modelling system and air quality modelling guidelines](https://path.epd.gov.hk/doc/Briefing%20on%20the%20release%20of%20updated%20PATH%2020210622%20(Final_Clean).pdf)

## [改善珠三角空氣質素的PM2.5研究 – 研究摘要](https://www.epd.gov.hk/epd/sites/default/files/epd/tc_chi/environmentinhk/air/studyrpts/files/PM2.5_study_improve_prd_chi.pdf)

- 本研究以 2015 年 2 月（冬季）、7 月（颱風）、10 月（夏秋季過渡）和 12 月作為四個具代表性的月份，綜合分析其中四個典型嚴重污染事故和分析四個政策情景，探討氣象條件促成污染的比重和評估假設控制方案之成效，並根據分析結果提出相關的政策建議。
- 對嚴重污染事故影響最大的氣象條件，是位於珠三角地區東、東南或東北的熱帶氣旋所導致較穩定的地區性氣流。空氣污染物在此條件下快速積聚，特別在吹微弱北風或西北風時，香港及珠三角地區的南部沿海城市的污染物濃度快速升高，導致可長達一星期的高污染物水平（在夏季或秋季）。
- 除熱帶氣旋外，華南地區在夏季或秋季亦會有弱風情況，導致污染物積聚。當轉吹西北風，被困的污染物有機會傳到香港，往往令香港污染物的濃度上升。
- 在香港，空氣污染物水平在冬季亦會短暫上升，通常與增強的北或西北氣流有關。當較弱的東北風成為主導風向，加上較低溫度（即更穩定邊界層）和地表弱風，華南內陸的污染物會慢慢大量積聚。

# WRF和CMAQ 垂直層sigma座標設定 

- [網格類公告模式CMAQ 技術文件](https://aqmc.moenv.gov.tw/download/CMAQ/%E7%B6%B2%E6%A0%BC%E9%A1%9E%E5%85%AC%E5%91%8A%E6%A8%A1%E5%BC%8FCMAQ%E6%8A%80%E8%A1%93%E6%96%87%E4%BB%B6.pdf)
  - 表1.3-3 WRF和CMAQ 垂直層sigma座標設定 

| LEVEL | SIGMA | 氣壓(Pa) | Full LevelHeight (m) | Half LevelHeight (m) |
|-------|-------|----------|---------------------|---------------------|
|      | 1.000 | 100000   | 0                   |                     |
| 1     | 0.995 | 99550    | 38                  | 19                  |
| 2     | 0.990 | 99100    | 76                  | 57                  |
| 3     | 0.980 | 98200    | 153                 | 115                 |
| 4     | 0.960 | 96400    | 309                 | 231                 |
| 5     | 0.930 | 93700    | 548                 | 429                 |
| 6     | 0.890 | 90100    | 878                 | 713                 |
| 7     | 0.850 | 86500    | 1221                | 1050                |
| 8     | 0.800 | 82000    | 1671                | 1446                |
| 9     | 0.750 | 77500    | 2146                | 1909                |
| 10    | 0.700 | 73000    | 2649                | 2398                |
| 11    | 0.650 | 68500    | 3185                | 2917                |
| 12    | 0.600 | 64000    | 3757                | 3471                |
| 13    | 0.550 | 59500    | 4371                | 4064                |
| 14    | 0.500 | 55000    | 5033                | 4702                |
| 15    | 0.450 | 50500    | 5751                | 5392                |
| 16    | 0.400 | 46000    | 6537                | 6144                |
| 17    | 0.350 | 41500    | 7404                | 6971                |
| 18    | 0.300 | 37000    | 8370                | 7887                |
| 19    | 0.250 | 32500    | 9462                | 8916                |
| 20    | 0.200 | 28000    | 10716               | 10089               |
| 21    | 0.150 | 23500    | 12191               | 11454               |
| 22    | 0.100 | 19000    | 13981               | 13086               |
| 23    | 0.050 | 14500    | 16256               | 15119               |
| 24    | 0.000 | 10000    | 19384               | 17820               |