---
layout: post
title: Obs | China weather observations | q-weather.info
categories: [NWP]
tags: [NWP, WRF, MPAS, HKO, GBA, China, obs]
author: wpsze
date: 2026-01-27 05:56:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/wbciBdw.png
banner_img: https://i.imgur.com/wbciBdw.png
---

**數據下載 (方法) 僅供學術研究及教學用途**

---

# q-weather.info

- <https://q-weather.info/>
- <https://q-weather.info/about/>

- 本站目标是尽可能完整地搜集、展示并记录气象与空气质量数据。本站的数据均来自于公开的数据源
	- <https://q-weather.info/about/>
	- 注意：
	- 1.本站秉持免费开放的价值观，游客即可使用本站绝大部分功能，因此，注册原因中应当需要能看出必须要通过用户特有的功能才能达成，并需要包括使用这种特殊功能的目的。
	- 2.注册成功后无法立即登录，仍需等待管理员审核。审核通过后您将收到一封验证邮件，请在收到后点击邮件中的验证链接完成注册。审核的时间一般不超过24小时，请注意及时查收通知邮件，特别是留意垃圾邮件列表，否则验证链接过期后用户将因无法激活而作废。

# method

- <https://q-weather.info/weather/54511/>
- <https://q-weather.info/weather/54511/realtime/>
- <https://q-weather.info/weather/54511/today/>
- <https://q-weather.info/weather/54511/history/?date=2020-06-21>

```log
时次,瞬时温度,地面气压,相对湿度,瞬时风向,瞬时风速,1小时降水,10分钟平均能见度
2019-06-03 01:00 +0800,23.2,993.6,64,315/NW,3.3,0.6,9.03
2019-06-03 02:00 +0800,22.1,995.2,66,62/ENE,1.3,0.0,16.253
2019-06-03 03:00 +0800,21.9,995.7,62,45/NE,1.4,0.0,22.945
2019-06-03 04:00 +0800,21.5,995.6,63,113/ESE,4.3,0.0,19.53
2019-06-03 05:00 +0800,19.2,996.3,79,,0.0,0.0,7.316
2019-06-03 06:00 +0800,20.3,997.1,76,31/NNE,1.5,0.0,10.125
2019-06-03 07:00 +0800,23.0,997.9,64,70/ENE,0.8,0.0,7.159
2019-06-03 08:00 +0800,23.8,998.3,55,211/SSW,2.3,0.0,10.316
2019-06-03 09:00 +0800,24.8,998.7,51,194/SSW,2.2,0.0,15.378
2019-06-03 10:00 +0800,26.3,998.5,49,143/SE,1.5,0.0,19.856
2019-06-03 11:00 +0800,27.3,998.2,43,194/SSW,3.2,0.0,19.435
2019-06-03 12:00 +0800,29.0,997.6,34,113/ESE,2.8,0.0,25.154
2019-06-03 13:00 +0800,29.7,997.0,30,273/W,2.7,0.0,29.406
2019-06-03 14:00 +0800,30.5,996.5,28,163/SSE,3.7,0.0,29.425
2019-06-03 15:00 +0800,30.6,996.2,28,180/S,3.7,0.0,27.479
2019-06-03 16:00 +0800,30.9,996.0,29,166/SSE,5.2,0.0,28.094
2019-06-03 17:00 +0800,30.8,996.1,25,155/SSE,5.5,0.0,28.561
2019-06-03 18:00 +0800,30.5,996.6,25,186/S,5.2,0.0,30.0
2019-06-03 19:00 +0800,29.3,997.2,31,160/SSE,2.7,0.0,27.01
2019-06-03 20:00 +0800,27.8,998.0,36,177/S,4.5,0.0,25.976
2019-06-03 21:00 +0800,26.9,999.1,37,152/SSE,2.7,0.0,25.33
2019-06-03 22:00 +0800,26.1,1000.0,40,113/ESE,2.2,0.0,22.134
2019-06-03 23:00 +0800,22.9,1001.1,68,127/SE,7.2,0.0,6.271
2019-06-04 00:00 +0800,21.3,1001.4,78,124/SE,2.7,0.0,4.253
```

# get station's info

{% fold info @get_stations_info.sh %}
```sh
#!/bin/bash

set -o pipefail

source /home/wpsze/micromamba/bin/activate venv 
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

#!/bin/bash

# 定义输出文件名
input_file="all_stations.csv"
output="info_stations_data.csv"

# 检查输入文件是否存在
if [ ! -f "$input_file" ]; then
    echo "错误: 找不到文件 $input_file"
    exit 1
fi

echo "开始从 $input_file 读取编号并抓取数据..."

# 写入表头
echo "WMO编号,站名,所在市,纬度,经度,高度" > $output

echo "开始提取数据..."

# 逐行读取输入文件 (忽略空行)
while IFS= read -r id || [ -n "$id" ]; do
    # 去除编号两端的空格或回车符
    id=$(echo "$id" | xargs)
    
    # 如果行不为空则执行
    if [ -n "$id" ]; then        
        # 获取网页源码
        url="https://q-weather.info/weather/$id/"
        # 使用 curl 获取 HTML，-s 静默模式
        html=$(curl -s $url)

        # 1. 提取站名: 取 <td>站名</td> 下一行的数据
        name=$(echo "$html" | grep -A 1 "<td>站名</td>" | tail -n 1 | sed 's/.*<td>\(.*\)<\/td>.*/\1/')

        # 2. 提取城市: 取 <td>所在市</td> 下一行的数据
        city=$(echo "$html" | grep -A 1 "<td>所在市</td>" | tail -n 1 | sed 's/.*<td>\(.*\)<\/td>.*/\1/')

        # 3. 提取地理位置: 取 <td>地理位置</td> 下一行开始的块，并移除 HTML 标签和多余空格
        geo_block=$(echo "$html" | grep -A 4 "<td>地理位置</td>" | tail -n 4)
        
        # 分别提取纬度、经度、高度
        lat=$(echo "$geo_block" | sed -n '1p' | sed 's/<td>//g' | sed 's/N<br \/>//g' | xargs)
        lon=$(echo "$geo_block" | sed -n '2p' | sed 's/E<br \/>//g' | xargs)
        alt=$(echo "$geo_block" | sed -n '3p' | sed 's/<br \/>//g' | sed 's/m<\/td>//g' | xargs)

        # 如果抓取到了站名，则写入文件
        if [ -n "$name" ]; then
            echo "$id, $name, $city, $lat, $lon, $alt" >> $output
            echo "已处理: $id ($name)"
        else
            echo "错误: 无法获取站点 $id 的信息"
        fi

        # 稍微延迟，保护服务器
        sleep 2.0
    fi
done < "$input_file"

echo "抓取完成！结果保存在 $output"
```
{% endfold %}

