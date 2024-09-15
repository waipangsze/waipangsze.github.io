---
layout: post
title: DA-Meteorological Data Encoding(氣象數據編碼)
categories: [DA]
tags: [DA, NWP, MPAS, WRF]
author: wpsze
mathjax: true
mathjax_autoNumber: true
---

# Meteorological Data Encoding 

氣象數據編碼

- [HKO 淺談氣象數據編碼 (2015)](https://www.hko.gov.hk/tc/education/weather/data-and-technology/00479-abc-of-meteorological-data-encoding.html)
- [BUFR: A METEOROLOGICAL CODE FOR THE 21ST CENTURY (2010)](https://web.archive.org/web/20180215023559/https://www.eumetsat.int/website/wcm/idc/idcplg?IdcService=GET_FILE&dDocName=PDF_CONF_P57_S5_01_KARHILA_V&RevisionSelectionMethod=LatestReleased&Rendition=Web)
- [中央氣象局新格式氣象觀測資料作業系統介紹 (2014)](https://photino.cwb.gov.tw/rdcweb/lib/cd/cd01conf/dissertation/2014WAF/A1/A1-23.pdf)

Currently, most meteorological observations are being produced and transmitted in special formats known as 

- Traditional Alphanumeric Codes (TAC) 傳統字符代碼
- Table-Driven Code Forms (TDCF) 表格驅動代碼
 
as promulgated by WMO. Both TAC and TDCF are ways of representing meteorological data intended for international exchange in real-time.

## BUFR

- Binary Universal Form for the Representation of meteorological data
- 二進制通用數據表示格式

BURF is TDCF.

The latest version is BUFR Edition 4. BUFR Edition 3 is also considered current for operational use. BUFR was created in 1988 with the goal of replacing the WMO's dozens of character-based, position-driven meteorological codes, such as **SYNOP (surface observations)**, **TEMP (upper air soundings)** and **CLIMAT (monthly climatological data)**. BUFR was designed to be portable, compact, and universal. Any kind of data can be represented, along with its specific spatial/temporal context and any other associated metadata. In the WMO terminology, BUFR belongs to the category of table-driven code forms, where the meaning of data elements is determined by referring to a set of tables that are kept and maintained separately from the message itself.

# LITTLE_R

[What is LITTLE_R?](https://www2.mmm.ucar.edu/wrf/users/wrfda/OnlineTutorial/Help/littler.html)

LITTLE_R is an ASCII-based observation file format, **in use since the MM5 era**. Because raw observation data files have many possible formats (such as ASCII, BUFR, PREPBUFR, MADIS, and HDF) LITTLE_R is designed to be an intermediate format so that **WRFDA might be able to assimilate as many observation types as possible in a universal manner**. It is a report-based file format, so all manner of observation types can easily be "cat-ted" together into an easy-to-read and -edit text file.

**It is the user's responsibility to develop an interface to convert their own observations to LITTLE_R format so they may be read by OBSPROC**. We (UCAR) do not support any existing tools which do this. However, on this help page, we hope to describe the format as thoroughly as possible to aid the user in the conversion process.