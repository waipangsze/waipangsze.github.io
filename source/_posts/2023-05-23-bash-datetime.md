---
layout: post
title: Bash datetime (manipulate)
categories: [Linux]
tags: [bash]
author: wpsze
index_img: 
banner_img: 
---

# Bash datetime (manipulate)

```sh
NAME
       date - print or set the system date and time

SYNOPSIS
       date [OPTION]... [+FORMAT]
       date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]

```

We want to manipulate the datetime change. 

## Format=YYYMMDD
```sh
YYYYMMDD="20210315"
Next_day=$(date +%Y%m%d -d "$YYYYMMDD + 1 day")
# 20210316
Next_day=$(date +%Y-%m-%d -d "$YYYYMMDD + 1 day")
# 2021-03-16
Next_day=$(date +%Y-%m-%d_%H:%M:%S -d "$YYYYMMDD + 1 day")
# 2021-03-16_00:00:00
Next_day=$(date +%Y-%m-%d_%H:%M:%S -d "$YYYYMMDD + 1 hour")
# 2021-03-15_01:00:00
Next_day=$(date +%Y-%m-%d_%H:%M:%S -d "$YYYYMMDD + 1 min")
# 2021-03-15_00:01:00
Next_day=$(date +%Y-%m-%d_%H:%M:%S -d "$YYYYMMDD + 1 second")
# 2021-03-15_00:00:01
Next_day=$(date +%Y-%m-%d_%H:%M:%S -d "$YYYYMMDD + 1 day + 1 hour + 1 min + 1 second")
# 2021-03-16_01:01:01
```

## Format=YYYY-MM-DD
```sh
YYYYMMDD="2021-04-15"
Next_day=$(date +%Y-%m-%d_%H:%M:%S -d "$YYYYMMDD + 1 day + 1 hour + 1 min + 1 second")
# 2021-04-16_01:01:01
```

## Format=YYYY-MM-DD HH:MM:SS"
```sh
YYYYMMDD="2017-01-19 00:05:01"
Next_day=$(date +%Y-%m-%d_%H:%M:%S -d "$YYYYMMDD + 1 day + 1 hour + 1 min + 1 second")
# 2017-01-20_08:06:02
```

## To seperate 
```sh
FCST_DURATION="000_17:00:00"

fcst_DD=${FCST_DURATION:0:3}
fcst_hh=${FCST_DURATION:4:2}
fcst_mm=${FCST_DURATION:7:2}
fcst_ss=${FCST_DURATION:10:2}
```

