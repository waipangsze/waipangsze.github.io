---
layout: post
title: Differences Between Weather Models and Climate Models
categories: [NWP]
tags: [NWP, MPAS, WRF, PGW]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
---

## Differences Between Weather Models and Climate Models

Understanding the distinctions between weather models and climate models is crucial for grasping how meteorologists and climate scientists predict atmospheric conditions over varying timescales. Here are the key differences:

### **1. Time Scale of Predictions**

- **Weather Models**: These models focus on short-term predictions, typically ranging from a few hours to about 15 days. They provide detailed forecasts for specific locations, such as whether it will rain tomorrow or if a storm is approaching.
- **Climate Models**: In contrast, climate models project conditions over much longer periods, often decades to hundreds of years. They analyze trends and averages rather than specific events, such as predicting whether summers will become drier in the next 50 years.

### **2. Initial vs. Foreced Boundary Value Problems**

- **Weather Models**: They operate as initial value problems, meaning they require precise initial conditions (like current temperature and pressure) to generate accurate forecasts. Small changes in these initial conditions can significantly impact the forecast outcome.
- **Climate Models**: In a climate model, you get climate variables for every day, but you don't care exactly on which day and exact location you get a certain value for this variable as long as the long term statistics are correct for the location and reference. This does not depend on the initial conditions of the simulation, but it depends on the parameters in the model itself or how the climate system is modelled (this is called a boundary value problem). These are "Foreced" boundary value problems, focusing on long-term changes influenced by factors like greenhouse gas concentrations and land use changes. The initial state is less critical; instead, the model uses projected future conditions to assess climate evolution.

### **3. Resolution**

- **Weather Models**: They typically have a finer spatial and temporal resolution, allowing for detailed predictions over smaller areas (often at a scale of 1 km or less). This granularity is essential for short-term forecasting.
- **Climate Models**: While they can also achieve high resolution, climate models generally operate on coarser grids (often around 50 km) due to the longer timeframes they analyze. The focus is on broader patterns rather than localized events.

### **4. Data Assimilation**

- **Weather Models**: These models assimilate real-time observational data from satellites, weather stations, and radar to continuously update forecasts. This real-time data integration enhances accuracy for short-term predictions.
- **Climate Models**: Climate models do not incorporate real-time observations but instead rely on historical data and projections of future conditions based on various scenarios (e.g., different levels of greenhouse gas emissions) to simulate long-term trends.

### **5. Purpose and Application**

- **Weather Models**: Primarily used for immediate forecasting needs such as daily weather reports, severe weather warnings, and event planning. They help individuals and organizations prepare for short-term atmospheric conditions.
- **Climate Models**: Climate models are used to determine how the average and extreme conditions will change. Used to understand long-term climate change impacts, assess risks associated with global warming, and inform policy decisions regarding environmental management and sustainability. Their outputs guide infrastructure planning and disaster preparedness over extended periods.

### Summary Table

| Feature                  | Weather Models                      | Climate Models                       |
|:--------------------------|:-------------------------------------|:--------------------------------------|
| Time Scale               | Hours to 15 days                   | Decades to hundreds of years        |
| Problem Type             | Initial value problem               | Boundary value problem               |
| Resolution               | High (1 km or less)                | Coarse (typically 50 km)            |
| Data Assimilation        | Real-time observational data        | Historical data and future projections|
| Purpose                  | Short-term forecasting              | Long-term climate change analysis    |

In summary, while both weather and climate models share foundational scientific principles, they serve different purposes and operate under distinct methodologies tailored to their respective timeframes and objectives.

![ ‘Weather’ refers to short-term changes, and ‘climate’ to weather conditions averaged over at least 30 years (image source: ESA).](https://www.esa.int/var/esa/storage/images/esa_multimedia/images/2021/04/weather_versus_climate_at_a_glance/23267896-1-eng-GB/Weather_versus_climate_at_a_glance_article.jpg)


# References

- [C3S User Learning Services Data Resources - Climate Models](https://climate.copernicus.eu/sites/default/files/2021-12/10-c3s-uls-data-resources-climate-models.pdf) (*** Good ***)
- [climateextremes.org.au: Types of models](https://climateextremes.org.au/a-closer-look-at-climate-modelling/) (*** Good ***)
- [The difference between weather and climate](https://skepticalscience.com/weather-forecasts-vs-climate-models-predictions.htm)
- [Weather and Climate @ Reading: Weather vs. Climate Prediction](https://blogs.reading.ac.uk/weather-and-climate-at-reading/2022/weather-vs-climate-prediction/)
- [Climate gov: Climate Models](https://www.climate.gov/maps-data/climate-data-primer/predicting-climate/climate-models)
- [earth-scan: weather-vs-climate](https://www.earth-scan.com/blog/weather-vs-climate)
- [Copernicus Climate Change Service – Global Impacts: What is bias correction?](https://climate.copernicus.eu/sites/default/files/2021-01/infosheet7.pdf)
- Kotamarthi R, Hayhoe K, Mearns LO, Wuebbles D, Jacobs J, Jurado J. **Empirical-Statistical Downscaling**. In: Downscaling Techniques for High-Resolution Climate Projections: From Global Change to Local Impacts. Cambridge University Press; 2021:82-101. ([link](https://sci-hub.se/https://doi.org/10.1017/9781108601269.006))
- Xu, Z., Y. Han, C.-Y. Tam, Z.-L. Yang and C. Fu, 2021: **A bias-corrected CMIP6 global dataset. for dynamical downscaling of future climate, 1979–2100**, Scientific Data, doi: 10.1038/s41597-021-01079-3. <https://atmosphericdynamicsgroup.github.io/database/cmip6.html>


