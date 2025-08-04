---
layout: post
title: Obs | China weather observations
categories: [NWP]
tags: [NWP, WRF, MPAS, HKO, GBA, China, obs]
author: wpsze
date: 2025-08-01 05:56:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/HnCsMuw.png
banner_img: https://i.imgur.com/HnCsMuw.png
---

**數據下載 (方法) 僅供學術研究及教學用途**

---

# 中国天气网

- <https://www.weather.com.cn/wzfw/about.shtml>

中国天气网（www.weather.com.cn）是公益性气象服务门户网站，是中国气象局公众气象服务权威品牌“中国天气”的重要载体和组成部分，由中国气象局公共气象服务中心主办、华风气象传媒集团负责运营，于2008年7月正式上线运行。

## city list

- <https://i.tq121.com.cn/j/webgis_v2/city.json>

![](https://i.imgur.com/5RAaevK.png){width=500}

- 3240 in total

```json
{
  "101010100": {
    "n": "北京",
    "x": "116.407526",
    "y": "39.904030"
  },
  "101010200": {
    "n": "海淀",
    "x": "116.298346",
    "y": "39.957809"
  },
...
}
```

- convert this json to csv by online tool. 
- rename

```csv
areaid,cityname,lon,lat
101010100,北京,116.407526,39.904030
101010200,海淀,116.298346,39.957809
101010300,朝阳,116.443229,39.921368
101010400,顺义,116.654561,40.130347
101010500,怀柔,116.633717,40.319141
101010600,通州,116.655702,39.910234
101010700,昌平,116.231204,40.220660
101010800,延庆,115.974848,40.456951
101010900,丰台,116.285986,39.85668
......
```

## get your IP

- check or get your IP address

{% fold info @ip.sh %}
```sh
curl 'https://wgeo.weather.com.cn/ip/?_=1754014328834' \
  -H 'Accept: */*' \
  -H 'Accept-Language: en-US,en;q=0.9,zh-HK;q=0.8,zh;q=0.7,zh-CN;q=0.6' \
  -H 'Connection: keep-alive' \
  -b 'f_city=%E5%8C%97%E4%BA%AC%7C101010100%7C%2C%E4%B8%8A%E6%B5%B7%7C101020100%7C%2C%E5%B9%BF%E5%B7%9E%7C101280101%7C; Hm_lvt_080dabacb001ad3dc8b9b9049b36d43b=1754012142; HMACCOUNT=F49474D9B2D9D090; Hm_lpvt_080dabacb001ad3dc8b9b9049b36d43b=1754014285' \
  -H 'Referer: https://www.weather.com.cn/' \
  -H 'Sec-Fetch-Dest: script' \
  -H 'Sec-Fetch-Mode: no-cors' \
  -H 'Sec-Fetch-Site: same-site' \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36' \
  -H 'sec-ch-ua: "Not)A;Brand";v="8", "Chromium";v="138", "Google Chrome";v="138"' \
  -H 'sec-ch-ua-mobile: ?0' \
  -H 'sec-ch-ua-platform: "Linux"'
```
{% endfold %}

```log
var ip="xxx.xxx.xxx.xxx";var id="101320xxxx";var addr="香港,香港,香港";
```

## get data (5 mins interval/实时天气)

{% fold info @curl-data.sh %}
```sh
curl 'https://d1.weather.com.cn/sk_2d/101010100.html?_=1754014328691' \
  -H 'Accept: */*' \
  -H 'Accept-Language: en-US,en;q=0.9,zh-HK;q=0.8,zh;q=0.7,zh-CN;q=0.6' \
  -H 'Connection: keep-alive' \
  -b 'f_city=%E5%8C%97%E4%BA%AC%7C101010100%7C%2C%E4%B8%8A%E6%B5%B7%7C101020100%7C%2C%E5%B9%BF%E5%B7%9E%7C101280101%7C; Hm_lvt_080dabacb001ad3dc8b9b9049b36d43b=1754012142; HMACCOUNT=F49474D9B2D9D090; Hm_lpvt_080dabacb001ad3dc8b9b9049b36d43b=1754014285' \
  -H 'Referer: https://www.weather.com.cn/' \
  -H 'Sec-Fetch-Dest: script' \
  -H 'Sec-Fetch-Mode: no-cors' \
  -H 'Sec-Fetch-Site: same-site' \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36' \
  -H 'sec-ch-ua: "Not)A;Brand";v="8", "Chromium";v="138", "Google Chrome";v="138"' \
  -H 'sec-ch-ua-mobile: ?0' \
  -H 'sec-ch-ua-platform: "Linux"'
```
{% endfold %}

{% fold info @output.json %}
```json
var dataSK={"nameen":"beijing","cityname":"北京","city":"101010100","temp":"31.9","tempf":"89.4","WD":"南风","wde":"S","WS":"2级","wse":"9km\/h","SD":"58%","sd":"58%","qy":"997","njd":"19km","time":"16:30","rain":"0","rain24h":"0","aqi":"77","aqi_pm25":"77","weather":"多云","weathere":"Cloudy","weathercode":"d01","limitnumber":"4和9","date":"08月01日(星期五)"}
```
{% endfold %}

### convert json to csv

{% fold info @convert_json2csv.py %}
```python
 $ cat convert_json2csv.py 
#!/bin/python
# -*- coding: utf-8 -*-
#------------------------------------------------#
# Author:         wpsze
# Email:          wpsze@gmail.com
# File:           convert_json2csv.py
# Date:           2025-08-01 11:16:07
# Version:        0.0 
# Description:    The purpose of the script
# Copyright (C):  2025 All rights reserved
#------------------------------------------------#

import json
import csv
import argparse
from datetime import datetime

def convert_to_csv(data_str):
    # Extract the JSON part from the string
    json_data = json.loads(data_str.split('=', 1)[1])

    # Generate the CSV file name based on the current date and time
    #csv_file = datetime.now().strftime('%Y-%m-%d_%H-%M-%S') + '.csv'
    #csv_file = datetime.now().strftime('%Y-%m-%d_%H') + '.csv'
    csv_file = datetime.now().strftime('%Y-%m-%d') + '.csv'

    # Writing to CSV
    with open(csv_file, mode='w', newline='', encoding='utf-8') as file:
        writer = csv.DictWriter(file, fieldnames=json_data.keys())
        writer.writeheader()  # Write the header
        writer.writerow(json_data)  # Write the data

    print(f'Data has been written to {csv_file}')

if __name__ == "__main__":
    # Set up argument parsing
    parser = argparse.ArgumentParser(description='Convert data string to CSV format.')
    parser.add_argument('data_str', type=str, help='Input data string')

    args = parser.parse_args()
    convert_to_csv(args.data_str)
```
{% endfold %}

{% fold info @run.sh %}
```sh
#!/bin/bash

#------------------------------------------------#
# Author:         wpsze
# Email:          wpsze@gmail.com
# File:           run.sh
# Date:           2025-08-01 11:19:08
# Version:        0.0 
# Description:    The purpose of the script
# Copyright (C):  2025 All rights reserved
#------------------------------------------------#

#set -e # stop the shell on first error
#set -u # fail when using an undefined variable
#set -x # echo script lines as they are executed
set -o pipefail

source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate venv
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

tmp_ym_dir=$(date +'%Y-%m')
tmp_ymd_dir=$(date +'%Y-%m-%d')
mkdir -p ${tmp_ym_dir}/${tmp_ymd_dir}/
cd ${tmp_ym_dir}/${tmp_ymd_dir}/

# Define the CSV file with city IDs
input_file="${SCRIPT_DIR}/test_city_ids.csv"
echo "input city id file = ${input_file}. "

# Read the CSV file and skip the header
tail -n +2 "$input_file" | while IFS=',' read -r city_id; do
  # Call the Python script to crawl data for each city ID
  echo "Crawling data for city ID: $city_id"

  tmp_var=$(curl 'https://d1.weather.com.cn/sk_2d/'${city_id}'.html' \
    -H 'Accept: */*' \
    -H 'Accept-Language: en-US,en;q=0.9,zh-HK;q=0.8,zh;q=0.7,zh-CN;q=0.6' \
    -H 'Connection: keep-alive' \
    -b 'f_city=%E5%8C%97%E4%BA%AC%7C101010100%7C%2C%E4%B8%8A%E6%B5%B7%7C101020100%7C%2C%E5%B9%BF%E5%B7%9E%7C101280101%7C; Hm_lvt_080dabacb001ad3dc8b9b9049b36d43b=1754012142; HMACCOUNT=F49474D9B2D9D090; Hm_lpvt_080dabacb001ad3dc8b9b9049b36d43b=1754014285' \
    -H 'Referer: https://www.weather.com.cn/' \
    -H 'Sec-Fetch-Dest: script' \
    -H 'Sec-Fetch-Mode: no-cors' \
    -H 'Sec-Fetch-Site: same-site' \
    -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36' \
    -H 'sec-ch-ua: "Not)A;Brand";v="8", "Chromium";v="138", "Google Chrome";v="138"' \
    -H 'sec-ch-ua-mobile: ?0' \
    -H 'sec-ch-ua-platform: "Linux"')

  python ${SCRIPT_DIR}/convert_json2csv.py "${tmp_var}"  # Modify this line as needed
done

# You can also use awk to remove duplicates without sorting:
awk '!seen[$0]++' ${tmp_ymd_dir}.csv > tmp_output.csv
rm ${tmp_ymd_dir}.csv
mv tmp_output.csv ${tmp_ymd_dir}.csv

# Point 1,
# If you want to pass the variable without using backslashes for escaping quotes, 
# you can use single quotes around the entire string in your shell command.
# Define the variable with single quotes around the whole string:
#tmp_dataSK='var dataSK={"nameen":"yinchuan","cityname":"银川","city":"101170101","temp":"24.3","tempf":"75.7","WD":"北风","wde":"N","WS":"2级","wse":"5km\/h","SD":"78%","sd":"78%","qy":"881","njd":"6km","time":"10:55","rain":"0","rain24h":"0","aqi":"36","aqi_pm25":"36","weather":"多云","weathere":"Cloudy","weathercode":"d01","limitnumber":"","date":"08月01日(星期五)"}'

# Point 2,
# The "double quotes" around $tmp_dataSK when calling the Python script 
# ensure that the entire content of the variable is treated as a single argument.
#python convert_json2csv.py "${tmp_dataSK}"
```
{% endfold %}

```log
nameen,cityname,city,temp,tempf,WD,wde,WS,wse,SD,sd,qy,njd,time,rain,rain24h,aqi,aqi_pm25,weather,weathere,weathercode,limitnumber,date
beijing,北京,101010100,32,89.6,南风,S,2级,7km/h,58%,58%,997,20km,16:40,0,0,77,77,多云,Cloudy,d01,4和9,08月01日(星期五)
haidian,海淀,101010200,32.6,90.7,西南风,SW,2级,7km/h,54%,54%,996,24km,16:45,0,0,85,85,多云,Cloudy,d01,4和9,08月01日(星期五)
chaoyang,朝阳,101010300,32,89.6,南风,S,2级,9km/h,56%,56%,997,18km,16:45,0,0,85,85,多云,Cloudy,d01,4和9,08月01日(星期五)
```

## get data (逐小时天气)

## 天气预报-变量名

- [天气预报-变量名](https://market-isv-1258344699.cos.ap-shanghai.myqcloud.com/20220104/document/202304/212f935a-a2d0-4f9a-8169-5afa5b39497e/%E5%A4%A9%E6%B0%94%E9%A2%84%E6%8A%A5.pdf)

![](https://i.imgur.com/vJoSnb5.png){width=600}


### WeatherCode

| WeatherCode | Weather         |   |
|-------------|-----------------|---|
| 00          | 晴              |   |
| 01          | 多云            |   |
| 02          | 阴              |   |
| 03          | 阵雨            |   |
| 04          | 雷阵雨          |   |
| 05          | 雷阵雨伴有冰雹  |   |
| 06          | 雨夹雪          |   |
| 07          | 小雨            |   |
| 08          | 中雨            |   |
| 09          | 大雨            |   |
| 10          | 暴雨            |   |
| 11          | 大暴雨          |   |
| 12          | 特大暴雨        |   |
| 13          | 阵雪            |   |
| 14          | 小雪            |   |
| 15          | 中雪            |   |
| 16          | 大雪            |   |
| 17          | 暴雪            |   |
| 18          | 雾              |   |
| 19          | 冻雨            |   |
| 20          | 沙尘暴          |   |
| 21          | 小雨-中雨       |   |
| 22          | 中雨-大雨       |   |
| 23          | 大雨-暴雨       |   |
| 24          | 暴雨-大暴雨     |   |
| 25          | 大暴雨-特大暴雨 |   |
| 26          | 小雪-中雪       |   |
| 27          | 中雪-大雪       |   |
| 28          | 大雪-暴雪       |   |
| 29          | 浮尘            |   |
| 30          | 扬沙            |   |
| 31          | 强沙尘暴        |   |
| 32          | 霾              |   |

# Error: "ASCII text, with CRLF line terminators,"

If your CSV file is identified as "ASCII text, with CRLF line terminators," it means it uses Windows-style line endings (`\r\n`). To convert it to Unix-style line endings (`\n`), you can use various tools available in Linux. Here are a few methods:

```sh
 $ file city_ids.csv 
city_ids.csv: ASCII text, with CRLF line terminators
```

## Method 1: Using `sed`

You can also use `sed` to remove the carriage return characters:

```bash
sed 's/\r$//' input.csv > output.csv
```

## Method 2: Using `awk`

You can also use `awk`:

```bash
awk '{ sub("\r$", ""); print }' input.csv > output.csv
```

# Block IP?