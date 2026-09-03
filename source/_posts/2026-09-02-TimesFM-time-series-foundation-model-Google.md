---
layout: post
title: TimesFM | time-series foundation model | Google
categories: NWP
tags: [NWP, ML, AI, ECMWF, Anemoi, TimesFM, Google, time-series forecasting]
author: wpsze
date: 2026-09-02 07:03:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj4G0lBOLUqlPIXJ3R68kjS984MBIKBPDBrCWtgmjVVTyQRqY6-rn3aHJjgxCbG-8csyBLsp0POILdeJ2VcsRy8lrip0k5DWsUpuL9LU1qOPXLW99mraNdd6HVU791NYqJeTyY7LjuMnOIo6RGmkxBQqqaPrSsC0dELrwy21QUs1Jgwxr8flmdNkDV2tZsT/s1084/image3.jpg
banner_img: https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj4G0lBOLUqlPIXJ3R68kjS984MBIKBPDBrCWtgmjVVTyQRqY6-rn3aHJjgxCbG-8csyBLsp0POILdeJ2VcsRy8lrip0k5DWsUpuL9LU1qOPXLW99mraNdd6HVU791NYqJeTyY7LjuMnOIo6RGmkxBQqqaPrSsC0dELrwy21QUs1Jgwxr8flmdNkDV2tZsT/s1084/image3.jpg
---

# TimesFM

- <https://github.com/google-research/timesfm>
  - <https://research.google/blog/a-decoder-only-foundation-model-for-time-series-forecasting/>

`TimesFM` (Time Series Foundation Model) is a pretrained time-series foundation model developed by Google Research for time-series forecasting.

- (NEW!) TimesFM 3.0 Checkpoint: `google/timesfm-3.0-pytorch.`
- Apache License Version 2.0 完全允許商業使用，您可以安心在營利性產品中使用、修改和銷售，且不需要公開您自己的源代碼。

```sh
# Install TimesFM with PyTorch
pip install timesfm[torch]
```

# examples

1. Univariate Forecasting (Variable Lengths)
Pass a batch of 1D NumPy arrays of different context lengths to forecast univariate time series:

```sh
import numpy as np
from timesfm3 import TimesFM3Evaluator, ModelConfig

# Initialize TimesFM 3.0
config = ModelConfig(
    checkpoint_path="google/timesfm-3.0-pytorch",
    per_core_batch_size=32,
    device="cuda"
)
forecaster = TimesFM3Evaluator(config)

# Two univariate series of different lengths (100 and 72 steps)
ts1 = np.linspace(0, 1, 100).astype(np.float32)
ts2 = np.sin(np.linspace(0, 24, 72)).astype(np.float32)

# Generate forecast (point predictions + 9 quantiles: 0.1 to 0.9)
outputs = list(forecaster.predict_batch([ts1, ts2], horizon=12, return_quantiles=True, use_symmetric_averaging=False))

print("Series 1 forecast shape:", outputs[0].forecast.shape)   # (12,)
print("Series 1 quantiles shape:", outputs[0].quantiles.shape) # (12, 9)

print("Series 2 forecast shape:", outputs[1].forecast.shape)   # (12,)
print("Series 2 quantiles shape:", outputs[1].quantiles.shape) # (12, 9)
```

2. Multivariate Forecasting with Covariates
Pass a 2D array of shape (num_variates, context_length) along with optional past-only and past-and-future covariates:

```sh
import numpy as np
from timesfm3 import TimesFM3Evaluator, ModelConfig

# Initialize TimesFM 3.0
config = ModelConfig(
    checkpoint_path="google/timesfm-3.0-pytorch",
    per_core_batch_size=16,
    device="cuda"
)
forecaster = TimesFM3Evaluator(config)

context_len = 128
horizon = 24

# 3 target variates across past context: (3, 128)
target = np.random.randn(3, context_len).astype(np.float32)

# 1 past-only covariate channel across past context: (1, 128)
past_only_cov = np.random.randn(1, context_len).astype(np.float32)

# 2 past-and-future covariate channels across context + horizon: (2, 152)
past_future_cov = np.random.randn(2, context_len + horizon).astype(np.float32)

# Generate joint forecast across all 3 target variates
outputs = list(
    forecaster.predict_batch(
        contexts=[target],
        horizon=horizon,
        past_only_covariates=[past_only_cov],
        past_future_covariates=[past_future_cov],
        return_quantiles=True,
        use_symmetric_averaging=False,
    )
)

print("Multivariate forecast shape:", outputs[0].forecast.shape)   # (3, 24)
print("Multivariate quantiles shape:", outputs[0].quantiles.shape) # (3, 24, 9)
```
