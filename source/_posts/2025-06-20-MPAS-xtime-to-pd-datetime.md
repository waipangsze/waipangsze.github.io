---
layout: post
title: MPAS | xtime to pd.datetime
categories: [MPAS]
tags: [NWP, MPAS]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-06-20 13:48:00
index_img: https://i.imgur.com/Pm7GzF6.png
banner_img: https://i.imgur.com/Pm7GzF6.png
---

# MPAS xtime format

![](https://i.imgur.com/Pm7GzF6.png)

```log
[[b'2023-01-01_00:00:00                                             ']
 [b'2023-01-01_12:08:00                                             ']
 [b'2023-01-02_00:06:17                                             ']
 [b'2023-01-02_12:04:35                                             ']]
```

## Convert to datetime

```python
datetime = []
tmp_datetime = dataset_base_atm["xtime"].values.astype(str)
for px in range(tmp_datetime.shape[0]):
    datetime.append(tmp_datetime[px][0].strip())

datetime = pd.to_datetime(datetime, format="%Y-%m-%d_%H:%M:%S")
```

![](https://i.imgur.com/rhKDYj6.png)

## plotting

```python
# Apply the 'ggplot' style
plt.style.use('ggplot')

# Creates a figure with a width of 10 inches and a height of 5 inches
plt.figure(figsize=(16, 8))

plt.plot(datetime, mean_t2m[0, :] - 273.15, 'ro-', label="default")
plt.plot(datetime, mean_t2m[1, :].T - 273.15, 'bo-', label="SST updated")

# Plot with default style
plt.title('t2m [degC]')
plt.xlabel('Date Time')
plt.ylabel('t2m [degC]')
plt.legend()
plt.show()
plt.close()
```

![](https://i.imgur.com/YIH5GKa.png)