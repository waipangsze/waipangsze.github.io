---
layout: post
title: AI for Numerical Weather Prediction (NWP)
categories: [NWP, ML]
tags: [NWP, ML]
author: wpsze
date: 2024-10-09 21:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://lh3.googleusercontent.com/PIGlxJLhF3Eit7xSXvVPmm3ZnOYspa9a8RcRkzfEdSnhy2rVubJmIondGQGiKF3TbHTIUOi6w_8xAej5UJ--c7o_8OukH_bDi4gYuEaQ0N6d_BXRQw=w2140-rw
banner_img: https://lh3.googleusercontent.com/PIGlxJLhF3Eit7xSXvVPmm3ZnOYspa9a8RcRkzfEdSnhy2rVubJmIondGQGiKF3TbHTIUOi6w_8xAej5UJ--c7o_8OukH_bDi4gYuEaQ0N6d_BXRQw=w2140-rw
---

# Introduction to AI in Numerical Weather Prediction

Artificial Intelligence (AI) is transforming the field of Numerical Weather Prediction (NWP), significantly enhancing the accuracy and efficiency of weather forecasting. Traditional NWP relies on complex physical models to simulate atmospheric processes, but the integration of AI and machine learning (ML) is introducing new methodologies that leverage vast datasets to improve predictions.

AI technologies, particularly machine learning algorithms, excel at analyzing large volumes of meteorological data from diverse sources, including satellites, radar systems, and surface sensors. These algorithms can identify intricate patterns and correlations that may be overlooked by conventional methods, leading to more precise forecasts. For instance, models like Google DeepMind's GraphCast can predict weather conditions up to ten days in advance in less than a minute, outperforming traditional systems that require hours on supercomputers.

{% note warning %}
[ECMWF | AIFS Blog](https://www.ecmwf.int/en/about/media-centre/aifs-blog)
{% endnote %}

- [Scores of forecasts of upper-air parameters by AIFS - experimental machine learning model](https://charts.ecmwf.int/products/plwww_3m_fc_aifs_wp_mean?area=Northern%20Extra-tropics&parameter=Geopotential%20500hPa&score=Root%20mean%20square%20error)
- [Scores of forecasts of upper-air parameters by experimental machine learning models](https://charts.ecmwf.int/streaming/20241009-0750/81/ps2png-worker-commands-76f744c54b-4xnd6-6fe5cac1a363ec1525f54343b6cc9fd8-zisedq81.png)

![ECMWF | Scores of forecasts of upper-air parameters by experimental machine learning models **(Higher is better)**](https://charts.ecmwf.int/streaming/20241009-0750/81/ps2png-worker-commands-76f744c54b-4xnd6-6fe5cac1a363ec1525f54343b6cc9fd8-zisedq81.png)

![ECMWF | Scores of forecasts of upper-air parameters by experimental machine learning models **(Lower is better)**](https://charts.ecmwf.int/streaming/20241009-0710/e3/ps2png-worker-commands-76f744c54b-rk8tn-6fe5cac1a363ec1525f54343b6cc9fd8-ue79gh2y.png)

# Run AI models yourself from ECMWF open data

26 September 2024\
The AIFS team

- <https://www.ecmwf.int/en/about/media-centre/aifs-blog/2024/run-ai-models-yourself-ecmwf-open-data>
- To announce that users can now run AI models using ECMWF's open data themselves. This breakthrough democratizes access to cutting-edge weather forecasting technology and opens new possibilities for research, education, and application development.
- success of machine learning models like FourCastNet, Pangu-Weather, GraphCast and FuXi, ECMWF is now empowering users to harness these AI tools directly. 

## The power of open data and AI

By combining ECMWF's high-quality open data with state-of-the-art AI models, users can:

- generate rapid forecasts on their own computer
- explore multi-model ensemble forecasting techniques with AI
- conduct research on model performance and intercomparison without any accounts/licences.
- **Plugins already exist for Pangu-Weather, FourCastNet (versions 1 and 2), GraphCast, FuXi and, most recently, Aurora.**
- <https://github.com/ecmwf-lab/ai-models>

```sh
pip install ai-models 

pip install ai-models-panguweather # Or another model 
pip install ai-models-fourcastnet
pip install ai-models-graphcast  # Install details at https://github.com/ecmwf-lab/ai-models-graphcast
pip install ai-models-fourcastnetv2 

ai-models panguweather --input ecmwf-open-data 
```

See 

- [ai-models-panguweather](https://github.com/ecmwf-lab/ai-models-panguweather), 
- [ai-models-fourcastnet](ai-models-fourcastnet), 
- [ai-models-fourcastnetv2](ai-models-fourcastnetv2) and 
- [ai-models-graphcast](ai-models-graphcast)

for more details about these models.

{% note warning %}
To fix ONNX or version issue, we suggest that you **install ai-models in a conda environment and install the CUDA libraries in that environment**.
{% endnote %}

```sh
conda create -n ai-models python=3.10
conda activate ai-models
conda install cudatoolkit
pip install ai-models
...
```

### Running the models

To run model, make sure it has been installed, then simply run:

{% note warning %}
By default, the model will be run for **a 10-day lead time (240 hours)**, using **yesterday's 12Z analysis** from ECMWF's MARS archive.
{% endnote %}

```sh
ai-models <model-name>

# To produce a 15 days forecast, use the --lead-time HOURS option:
ai-models --lead-time 360 <model-name>
```

### Performances Considerations

The AI models can run on a CPU; however, they perform significantly better on a GPU. 

{% note warning %}
**A 10-day forecast can take several hours on a CPU but only around one minute on a modern GPU.**
{% endnote %}

{% note warning %}
**We strongly recommend running these models on a computer equipped with a GPU for optimal performance.**
{% endnote %}

It you see the following message when running a model, it means that the **ONNX runtime was not able to find a the CUDA libraries on your system**:

> [W:onnxruntime:Default, onnxruntime_pybind_state.cc:541 CreateExecutionProviderInstance] Failed to create CUDAExecutionProvider. Please reference https://onnxruntime.ai/docs/reference/execution-providers/CUDA-ExecutionProvider.html#requirements to ensure all dependencies are met.

### Assets
The AI models rely on weights and other assets created during training. The first time you run a model, you will need to download the **trained weights** and **any additional required assets**.

```sh
# To download the assets before running a model, use the following command:
ai-models --download-assets <model-name>

# The assets will be downloaded if needed and stored in the current directory. You can provide a different directory to store the assets:
ai-models --download-assets --assets <some-directory> <model-name>

# Then, for running, later on, simply use:
ai-models --assets <some-directory>  <model-name>
# or 
export AI_MODELS_ASSETS=<some-directory>
ai-models <model-name>
```

For better organisation of the assets directory, you can use the **--assets-sub-directory** option. This option will store the assets of each model in its own subdirectory within the specified assets directory.

### Input data
The models require input data (initial conditions) to run. You can provide the input data using different sources, as described below:

#### From MARS

{% note warning %}
By default, **ai-models use yesterday's 12Z analysis from ECMWF**, fetched from the Centre's MARS archive using the [ECMWF WebAPI](https://www.ecmwf.int/en/computing/software/ecmwf-web-api). **You will need an ECMWF account to access that service**.
{% endnote %}

```sh
# To change the date or time, use the --date and --time options, respectively:

ai-models --date YYYYMMDD --time HHMM <model-name>
```

#### From the CDS (ERA5)

You can start the models **using ERA5 (ECMWF Reanalysis version 5) data** for the [Copernicus Climate Data Store (CDS)](https://cds.climate.copernicus.eu/). You will need to create an account on the CDS. The data will be downloaded using the [CDS API](https://cds.climate.copernicus.eu/api-how-to)).

To access the CDS, simply add **--input cds** on the command line. **Please note that ERA5 data is added to the CDS with a delay**, so you will also have to provide a date with --date YYYYMMDD.

```sh
ai-models --input cds --date 20230110 --time 0000 <model-name>
```

#### From a GRIB file

If you have input data in the GRIB format, you can provide the file using the **--file** option:

```sh
ai-models --file <some-grib-file> <model-name>
```

{% note warning %}
The GRIB file can contain more fields than the ones required by the model. The ai-models command will automatically select the necessary fields from the file.
{% endnote %}

{% note warning %}
GFS grib files?
{% endnote %}

To find out the list of fields needed by a specific model as initial conditions, use the following command:

```sh
ai-models --fields <model-name>
```

### Output

By default, the model output will be written in GRIB format in a file called **\<model-name\>.grib**. You can change the file name with the option **--path <file-name>**. If the path you specify contains placeholders between { and }, multiple files will be created based on the [eccodes](https://confluence.ecmwf.int/display/ECC) keys. For example:

```sh
ai-models --path 'out-{step}.grib' <model-name>
```

**This command will create a file for each forecasted time step.**

If you want to disable writing the output to a file, use the **--output none** option.

### Command line options

It has the following options:

- `--help`: Displays this help message.
- `--models`: Lists all installed models.
- `--debug`: Turns on debug mode. This will print additional information to the console.

#### Input

- `--input INPUT`: The input source for the model. This can be a `mars`, `cds` or `file`.
- `--file FILE`: The specific file to use as input. This option will set `--source` to `file`.

- `--date DATE`: The analysis date for the model. This defaults to yesterday.
- `--time TIME`: The analysis time for the model. This defaults to 1200.

#### Output

- `--output OUTPUT`: The output destination for the model. Values are `file` or `none`.
- `--path PATH`: The path to write the output of the model.

#### Run

- `--lead-time HOURS`: The number of hours to forecast. The default is 240 (10 days).

#### Assets management

- `--assets ASSETS`: Specifies the path to the directory containing the model assets. The default is the current directory, but you can override it by setting the `$AI_MODELS_ASSETS` environment variable.
- `--assets-sub-directory`: Enables organising assets in `<assets-directory>/<model-name>` subdirectories.
- `--download-assets`: Downloads the assets if they do not exist.

#### Misc. options

- `--fields`: Print the list of fields needed by a model as initial conditions.
- `--expver EXPVER`: The experiment version of the model output.
- `--class CLASS`: The 'class' metadata of the model output.
- `--metadata KEY=VALUE`: Additional metadata metadata in the model output

# Available products
The output of these ML models are forecasts with 6-hourly time steps out to 10 days initialised from the ECMWF operational analysis. All forecasts are produced on a 0.25 x 0.25-degree grid.

## FourCastNetv2-small:
Upper-air fields are z (geopotential), r (relative humidity), t (temperature), u (U component of wind), and v (V component of wind) on the following pressure levels: 1000hPa, 925hPa, 850hPa, 700hPa, 600hPa, 500hPa, 400hP, 300hPa, 250hPa, 200hPa, 150hPa, 100hPa and 50hPa

The single-level fields are msl (mean sea level pressure), sp (surface pressure), 10u (10 metre U wind component), 10v (10 metre U wind component), 100u (100 metre U wind component), 100v (100 metre V wind component, 2t (2 metre temperature) and tcwv (total column vertically-integrated water vapour).

## Graphcast:
Upper-air fields are z (geopotential), q (specific humidity), t (temperature), u (U component of wind), v (V component of wind), w (vertical wind component) on the following pressure levels: 1000hPa, 925hPa, 850hPa, 700hPa, 600hPa, 500hPa, 400hP, 300hPa, 250hPa, 200hPa, 150hPa, 100hPa and 50hPa

The single-level fields are msl (mean sea level pressure), 10u (10 metre U wind component), 10v (10 metre U wind component), and 2t (2 metre temperature). 

## Pangu-Weather:
Upper-air fields are z (geopotential), q (specific humidity), t (temperature), u (U component of wind), and v (V component of wind) on the following pressure levels: 1000hPa, 925hPa, 850hPa, 700hPa, 600hPa, 500hPa, 400hPa, 300hPa, 250hPa, 200hPa, 150hPa, 100hPa and 50hPa

The single-level fields are msl (mean sea level pressure), 10u (10 metre U wind component), 10v (10 metre U wind component) and 2t (2 metre temperature).

# Hands-on



# References

1. [ECMWF | AIFS Blog](https://www.ecmwf.int/en/about/media-centre/aifs-blog)
2. [ECMWF | 2024 Run AI models yourself from ECMWF open data](https://www.ecmwf.int/en/about/media-centre/aifs-blog/2024/run-ai-models-yourself-ecmwf-open-data)
3. [ECMWF | 2024 Data-driven regional modelling](https://www.ecmwf.int/en/about/media-centre/aifs-blog/2024/data-driven-regional-modelling)
   - some great work for limited-area modelling with Neural-LAM (Oskarsson et al. 2023).
   - In contrast to Neural-LAM, the approach we are currently testing keeps a global model. Over the domain where high-resolution data are available, here the Nordics, a finer-resolution graph is used in the model, to be able to ingest and output data from high-resolution regional analysis, reanalysis or forecast datasets.
4. [ECMWF | 2024 Enter the ensembles](https://www.ecmwf.int/en/about/media-centre/aifs-blog/2024/enter-ensembles)
5. [ECMWF | 2023 Machine Learning model data](https://www.ecmwf.int/en/forecasts/dataset/machine-learning-model-data)