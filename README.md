# Soil Heterotrophic Respiration

This repository compiles relevant carbon fluxes to compute soil respiration data from point-scale observations (flux tower measurements) to global gridded satellite-based estimates (e.g., MODIS). The goal of this work is to support further analyses of the drivers of soil heterotrophic respiration across spatial and temporal scales. Data are sourced from NEON, AmeriFlux, MODIS, and FLUXCOM at multiple spatial and temporal resolutions, as described in detail below.

---

# Table of Contents

1. [Data Files](#data-files)  
   - [NEON Site Coordinates](#neonsites_lat_longcsv)  
   - [MODIS + AmeriFlux Monthly Dataset](#modis_ameriflux_input_data_finalcsv)  
   - [FLUXCOM Extracted Datasets](#fluxcom-extracted-datasets)  
   - [Long-term Climate Aridity (CRU)](#long_term_di_cruxlsx)
2. [Data Sources](#data-sources)  
   - [MODIS](#1-modis-satellite-data-google-earth-engine)  
   - [NEON](#2-neon-national-ecological-observatory-network)  
   - [AmeriFlux](#3-ameriflux)
   - [FLUXCOM](#4-fluxcom)
   - [CRU](#5-cru-climatic-research-unit)
3. [Data Processing Information](#data-processing-information)  
   - [MODIS Data Processing](#1-modis-data-processing)  
   - [NEON PAR Data Processing](#2-neon-par-data-processing)  
   - [AmeriFlux Data Processing](#3-ameriflux-data-processing)  
   - [Missing Sites Processing](#4-missing-sites-processing)  
   - [Final Data Merging](#5-final-data-merging)
   - [Growing Season Definition](#6-growing-season-definition)
   - [Data Aggregation Summary](#7-data-aggregation-summary)
   - [Fluxcom Interpolation Steps](#8-fluxcom-interpolation-steps-see-extraction-fluxcom-dataipynb)
   - [Fluxtower Data Analysis](#9-fluxtower-data-analysis-see-fluxtower-data-analysisipynb)
4. [Authorthip_and Contact](#authorship-and-contact)
   
---

# Data Files

## `Neonsites_lat_long.csv`

Provides ~30 NEON sites with coordinate information.

| Variable     | Source | Description                           | Units  |
|--------------|--------|---------------------------------------|--------|
| NEONSite     | -      | NEON site code                        | -      |
| FluxnetSite  | -      | AmeriFlux/FLUXNET site code           | -      |
| location     | -      | NEON site location name               | -      |
| lat          | -      | Latitude (decimal degrees)            | degree |
| long         | -      | Longitude (decimal degrees)           | degree |

---

## `MODIS_Ameriflux_input_data_final.csv`

Contains MODIS and AmeriFlux variables aggregated to a monthly scale.

| Variable | Source | Description | Units |
|----------|--------|-------------|-------|
| NEONSite | - | NEON site code | - |
| FluxnetSite | - | AmeriFlux/FLUXNET site code | - |
| Year | - | Year of observation | - |
| Month | - | Month of observation | 1–12 |
| GrowingSeason | Derived | Growing season flag | binary |
| NEON_PAR | NEON | Photosynthetically Active Radiation | μmol/m²/s |
| MODIS_FPAR | MODIS | Fraction of PAR absorbed | 0–1 |
| MODIS_GPP | MODIS | Gross Primary Production | kg C/m²/8-day |
| MODIS_NPP | MODIS | Net Primary Production | kg C/m²/8-day |
| MODIS_CUE | MODIS | Carbon Use Efficiency | ratio |
| MODIS_ET | MODIS | Evapotranspiration | mm/day |
| MODIS_PET | MODIS | Potential Evapotranspiration | mm/day |
| Ameriflux_NEE | AmeriFlux | Net Ecosystem Exchange | gC/m²/month |
| Ameriflux_GPP | AmeriFlux | Gross Primary Production | gC/m²/month |
| Ameriflux_RE | AmeriFlux | Ecosystem Respiration | gC/m²/month |
| Ameriflux_Precipitation | AmeriFlux | Precipitation | mm/month |

---

## FLUXCOM Extracted Datasets

### `Neondata_Fluxcom_720_360.csv`  
Carbon flux data values for the NEON sites interpolated from the FLUXCOM RS dataset at **0.5° × 0.5°** resolution.

### `Neondata_Fluxcom_4320_2160.csv`  
Carbon flux data values for the NEON sites interpolated from the FLUXCOM RS dataset at **0.0833° × 0.0833°** resolution.

Both files contain monthly GPP, NEE, and TER extracted for ~30 NEON sites.

| Variable | Source | Description | Units |
|----------|--------|-------------|-------|
| NEONSite | - | NEON site code | - |
| FluxnetSite | - | FluxNet site code | - |
| location | - | Site name | - |
| month | - | Month | 1–12 |
| GPP_YEAR | FLUXCOM | Gross Primary Production | g C/m²/day |
| NEE_YEAR | FLUXCOM | Net Ecosystem Exchange | g C/m²/day |
| TER_YEAR | FLUXCOM | Total Ecosystem Respiration | g C/m²/day |

---

## `Long_term_DI_CRU.xlsx`

Provides long-term climate aridity values for selected NEON sites.

| Variable | Source | Description | Units |
|----------|--------|-------------|-------|
| NEONSite | - | NEON site code | - |
| Ecosystem | - | Ecosystem type | - |
| location | - | Site name | - |
| DI | CRU | Dryness Index (PET / rainfall) | - |

---

# Data Sources

## 1. MODIS Satellite Data (Google Earth Engine)
Retrieved for the period 2018-01-01 to 2024-12-31:

- **fPAR (Fraction of Photosynthetically Active Radiation)**: MCD15A3H.061
  - Variables: `Fpar`, `Lai`, `FparLai_QC`
  - Temporal resolution: 4-day
  - Spatial resolution: 500m

- **GPP and NPP (8-day)**: MOD17A2H.061
  - Variables: `Gpp`, `PsnNet` (NPP), `Psn_QC`
  - Temporal resolution: 8-day
  - Spatial resolution: 500m

- **GPP and NPP (Yearly)**: MOD17A3HGF.061
  - Variables: `Gpp`, `Npp`, `Npp_QC`
  - Temporal resolution: Annual
  - Spatial resolution: 500m

- **ET and PET (Evapotranspiration)**: MOD16A2.061
  - Variables: `ET`, `PET`, `ET_QC`
  - Temporal resolution: 8-day
  - Spatial resolution: 500m

## 2. NEON (National Ecological Observatory Network)
- **PAR (Photosynthetically Active Radiation)**: DP1.00024.001
  - Temporal resolution: 30-minute intervals
  - Period: 2013-01-01 to 2023-12-31
  - Variables: `PARMean`

## 3. AmeriFlux
Retrieved from pre-downloaded monthly data files:

- **Eddy Covariance Flux Data**:
  - Variables: 
    - `NEE_VUT_REF`: Net Ecosystem Exchange
    - `RECO_NT_VUT_REF`: Ecosystem Respiration (Nighttime partitioning)
    - `GPP_NT_VUT_REF`: Gross Primary Production (Nighttime partitioning)
    - `P_F`: Precipitation
  - Temporal resolution: Monthly (MM files)

- **Missing Sites Data**: 
  - Three sites processed separately with gap-filled GPP using ReddyProc
  - Variables: `GPP_DT_uStar` (Daytime partitioning)

## 4. FLUXCOM

FLUXCOM carbon flux data products (https://www.fluxcom.org/CF-Download/), stored in netCDF-4 format. We use the ensemble of **RS** (Remote Sensing) data setup, which uses MODIS-only inputs to estimate global-scale carbon flux.

## 5. CRU (Climatic Research Unit) 
CRU global climate dataset (https://crudata.uea.ac.uk/cru/data/hrg/), providing long-term PET and rainfall at **0.5° × 0.5°** resolution.

---

# Data Processing Information

## 1. MODIS Data Processing
- **Input**: Raw MODIS CSV files from Google Earth Engine
- **Processing steps**:
  1. Load data for each variable (fPAR, GPP/NPP, ET/PET)
  2. Extract year and month from datetime
  3. Apply scaling factors:
     - fPAR: multiply by 0.01
     - ET/PET: multiply by 0.1
  4. Calculate CUE as NPP/GPP ratio
  5. Aggregate to monthly resolution:
     - fPAR: monthly mean
     - GPP/NPP/CUE: monthly mean
     - ET/PET: monthly sum
  6. Flag growing season based on site-specific SOS (Start of Season) and EOS (End of Season)
  7. Merge all MODIS variables into a single monthly dataset

## 2. NEON PAR Data Processing
- **Input**: Raw NEON PAR parquet files (30-minute resolution)
- **Processing steps**:
  1. Stack all site files using DuckDB for efficient processing
  2. Extract year and month from endDateTime
  3. Calculate monthly averages of PAR by site
  4. Add growing season flags based on site coordinates
  5. Map NEON site IDs to FluxNet site codes

## 3. AmeriFlux Data Processing
- **Input**: Monthly AmeriFlux CSV files from multiple sites
- **Processing steps**:
  1. Load monthly (MM) files for all sites
  2. Extract target columns: NEE, RECO, GPP, Precipitation
  3. Extract year and month from TIMESTAMP
  4. Map FluxNet site codes to NEON site codes
  5. Add growing season flags based on month and site-specific SOS/EOS


## 4. Missing Sites Processing
- **Input**: Gap-filled data from ReddyProc for 3 sites (PR-xLA, US-xNW, US-xRN)
- **Processing steps**:
  1. Read tab-separated text files
  2. Extract GPP_DT_uStar (daytime partitioning)
  3. Convert Day of Year to proper dates
  4. Calculate monthly averages
  5. Map site codes and add growing season flags

## 5. Final Data Merging
- **Input**: All interim processed datasets
- **Processing steps**:
  1. Merge NEON PAR with MODIS data on [NEON_Site, Fluxnet, year, month, growing_season]
  2. Merge result with AmeriFlux data using same keys
  3. Rename columns to standardized names
  4. Remove unnecessary columns

## 6. Growing Season Definition

The growing season is determined on a site-by-site basis using:
- **SOS**: Start of Season (month)
- **EOS**: End of Season (month)

Sites without defined growing seasons are flagged as 0 (non-growing) for all months.
For sites with defined seasons, months between SOS and EOS (inclusive) are flagged as 1 (growing season).

## 7. Data Aggregation Summary

| Data Source | Original Resolution | Final Resolution | Aggregation Method |
|-------------|---------------------|------------------|-------------------|
| NEON PAR | 30-minute | Monthly | Mean |
| MODIS fPAR | 4-day | Monthly | Mean |
| MODIS GPP/NPP | 8-day | Monthly | Mean |
| MODIS ET/PET | 8-day | Monthly | Sum |
| AmeriFlux | Monthly | Monthly | None (already monthly) |


## 8. Fluxcom Interpolation Steps (see `Extraction-FLUXCOM-Data.ipynb`)
1. Open an annual NetCDF file from `dir_path` containing month-wise global GPP/NEE/TER values.  
2. Load NEON site latitude/longitude.  
3. Interpolate grid-cell values to site coordinates.  
4. Assemble monthly outputs into a tidy table.  
5. Save as CSV for analysis.

## 9. Fluxtower Data Analysis (see `Fluxtower-Data-Analysis.ipynb`)
1. From the MODIS_Ameriflux_input_data_final.csv, calculate Ameriflux-based NPP using MODIS CUE and Ameriflux GPP.
2. Calculate Ameriflux-based heterotrophic respiration by adding the computed Ameriflux-based NPP to Ameriflux_NEE.
3. Calculate the long-term averages of Ameriflux-based heterotrophic respiration and Ameriflux-based NPP and analyze their variability with the dryness index compiled in Long_term_DI_CRU.xlsx for the NEON sites.

---

# Authorship and Contact
This compilation of carbon flux data across scales was prepared by Shashank Kumar Anand, Rodolfo Souza, and Salvatore Calabrese in the Department of Biological and Agricultural Engineering at Texas A&M University.

For more information, please contact:  
[Shashank Kumar Anand](mailto:skanannd@tamu.edu) [(Linkedin)](https://www.linkedin.com/in/shashank-kumar-anand/)
