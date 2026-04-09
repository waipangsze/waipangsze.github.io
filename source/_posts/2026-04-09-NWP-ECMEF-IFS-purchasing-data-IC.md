---
layout: post
title: NWP | ECMWF（歐洲中期天氣預報中心）的IFS（Integrated Forecasting System）購買數據實際成本
categories: [NWP]
tags: [MPAS, WRF, NWP, GFS, FNL, IFS, ERA5, CMA, BUFR]
author: wpsze
date: 2026-04-09 06:30:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/uTwSKOT.png
banner_img: https://i.imgur.com/uTwSKOT.png
---

[**NWP | ECMWF | IFS | Open data**](https://waipangsze.github.io/2024/11/12/ECMWF-IFS/)

---

# IFS

**歐洲中期天氣預報中心（ECMWF）的IFS（Integrated Forecasting System）數據，目前主要採用開放數據政策，數據本身基本免費，但根據使用方式和交付需求，可能產生服務費（分發/傳輸成本）。**

購買 ECMWF（歐洲中期天氣預報中心）IFS（整合預測系統）數據的成本在 2025 年 10 月 1 日之後發生了重大變革。根據 ECMWF 最新實施的開放數據路線圖（Open Data Roadmap），目前（2026年）的收費結構已從「購買數據本身」轉向「支付服務與分發費用」。

## 主要情況總結（截至2026年最新政策）：

- **自2025年10月1日起**，ECMWF已全面開放即時數據目錄（包括IFS和AIFS模型）。整個即時產品目錄均採用**CC-BY-4.0許可**，允許科研、商業再分發和使用（需適當署名）。**數據本身不再收取「資訊成本」（Information Cost）**，即不再按數據內容收費。
- **免費開放的部分**：
  - 25公里分辨率（0.25度）的即時IFS預報數據（部分參數集），可直接線上下載或透過雲平台（如AWS、Azure）獲取，無需額外許可。
  - 未來計劃擴展至9公里更高分辨率（可能有1-2小時延遲）。
  - 這些開放數據集可商業使用，只需遵守CC-BY-4.0署名要求。
  - 零數據購置費 (Zero Information Cost)
    - 從 2025 年 10 月起，ECMWF 已取消了所有即時數據的**資訊成本（Information Cost）**。
      - * **以前：** 商業用戶每年可能需要支付數萬歐元的許可費（最高曾達 70,000 歐元）。
      - * **現在：** 全部的 IFS 即時數據目錄均基於 **CC-BY-4.0** 授權協議開放。這意味著您可以**免費**獲得數據的使用權（包括商業用途），僅需註明來源。
- **需要付費的情況（主要是服務費）**：
  - 如果需要**完整/高分辨率/低延遲/批量交付**（如直接推送到您的伺服器、FTP端點，或處理超大體積數據），則需簽訂**即時數據分發服務協議（Real-time Data Dissemination Service Agreement）**。
  - 費用基於**ECMWF服務收費模型**（自2024年7月更新），包括：
    - **Volume Band**（數據量分級）和**Service Pack**（服務包），按數據傳輸體積計算。
    - 商業用戶可能有固定服務費，例如某些Web API或高級服務每年約**3,000歐元**（具體取決於服務類型）。
    - 最高費用上限已降低，整體服務費較以往減少。
  - 對於國家氣象水文服務（NMHS）等特定用戶，可能有費用減免（Fee Waiver）或50%折扣，前提是不從事商業競爭活動。
- 依然存在的成本：服務與分發費 (Service & Volume Charges)
  - 雖然數據本身免費，但如果您需要從 ECMWF 的伺服器直接提取大量、客製化或高可靠性的即時數據，則需要支付**服務費用**。這主要取決於您對頻寬和技術支援的需求：
    - * **年度最高上限 (Maximum Charge)：** 從 2025 年 10 月起，年度最高服務費已從 70,000 歐元調降至 **47,500 歐元**。
    - * **基礎單位費用 (EPU)：** 計費單位 EPU 從 0.25 歐元下調至 **0.15 歐元**。
      - * **費用構成：**
        - * **服務包 (Service Pack)：** 提供技術支援與交付保證的固定費用。
        - * **容量階層 (Volume Band)：** 根據您每天接收的數據量（GB）確定的階梯式費用。
- 如何實現「零成本」獲取？
  - 可以透過以下途徑：
    - * **公共雲端平台：** 透過 AWS、Google Cloud (GCP) 或 Azure 的「開放數據計畫」獲取。這些平台通常會同步一份 IFS 的子集數據，您只需支付雲端平台的流量或存儲費用。
    - * **ECMWF Open Data 入口網站：** 訪問 ECMWF 官網提供的免費子集（通常是 0.25° 或更高解析度的部分參數）。   * **2026 年新規：** 預計在 2026 年晚些時候，ECMWF 將進一步開放 **9km 高解析度** 的預測數據（帶有 2 小時延遲），這部分也將提供免費下載。

## 實際成本如何確定？

- **小型/標準使用**（線上下載開放數據集）：**基本為0歐元**（僅網絡流量自負）。
- **大規模/客製化使用**：需聯絡ECMWF，透過其**產品需求目錄（Products Requirement Catalogue）**提交訂單，他們會根據您的具體需求（分辨率、參數、頻率、交付方式、數據量）提供報價。費用主要涵蓋分發成本，而非數據本身。
- 歷史存檔數據（archive）訪問可能仍有少量費用或需Web API服務協議。

{% note primary %}
**建議**：或透過他們的購物車系統（<https://products.ecmwf.int/shopping-cart/>）提交需求以獲取精確報價。政策更新較快，具體以ECMWF最新官方資訊為準。
{% endnote %}

---

## 總結參考表

| 成本項目 | 2025 年以前 | 2026 年（當前） |
| :--- | :--- | :--- |
| **數據許可費 (Information Cost)** | 高昂 (最高 €70k/年) | **€0 (免費)** |
| **年度服務費上限** | €70,000 | **€47,500** |
| **EPU 單價** | €0.25 | **€0.15** |
| **使用許可** | 嚴格限制 | **CC-BY-4.0 (允許商業化)** |

# For Global Simulation:

- As of 2026-April
- Service Package: Basic
- **Volume Band: 1 - 10 GiB per day**
- **Research Project Fee-Waiver Service Agreement: No**

![](https://i.imgur.com/TCvNOjJ.png)

- 100 歐元 等於 912.43 港元
- 100 歐元 等於 795.75 人民幣

# About the Service Charges

- <https://confluence.ecmwf.int/display/DAC/Service+Charges%3A+From+01+July+2024>

![](https://i.imgur.com/1BEkTRS.png)