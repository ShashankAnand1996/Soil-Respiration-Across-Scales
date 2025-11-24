# Soil Heterotrophic Respiration

This repository compiles and analyzes soil respiration data from point-scale observations (flux tower measurements) to global gridded satellite-based estimates. The goal is to support further analyses of the drivers of soil heterotrophic respiration across spatial and temporal scales. Data are sourced from NEON, AmeriFlux, MODIS, and FLUXCOM at multiple spatial resolutions.

---

# Table of Contents

1. [Data Files](#data-files)  
   - [NEON Site Coordinates](#neonsites_lat_longcsv)  
   - [MODIS + AmeriFlux Monthly Dataset](#modis_ameriflux_input_data_finalcsv)  
   - [FLUXCOM Extracted Datasets](#fluxcom-extracted-datasets)  
   - [Long-term Climate Aridity (CRU)](#long_term_di_cruxlsx)
2. [Data Sources](#data-sources)  
   - [MODIS Satellite Data](#1-modis-satellite-data-google-earth-engine)  
   - [NEON](#2-neon-national-ecological-observatory-network)  
   - [AmeriFlux](#3-ameriflux)  
3. [Data Processing Information](#data-processing-information)  
   - [MODIS Data Processing](#1-modis-data-processing)  
   - [NEON PAR Data Processing](#2-neon-par-data-processing)  
   - [AmeriFlux Data Processing](#3-ameriflux-data-processing)  
   - [Missing Sites Processing](#4-missing-sites-processing)  
   - [Final Data Merging](#5-final-data-merging)  
4. [Growing Season Definition](#growing-season-definition)  
5. [Data Aggregation Summary](#data-aggregation-summary)  
6. [Contact](#contact)

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

Contains MODIS and AmeriFlux variables aggregated to monthly scale.

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
FLUXCOM RS dataset at **0.5° × 0.5°** resolution.

### `Neondata_Fluxcom_4320_2160.csv`  
FLUXCOM RS dataset at **0.0833° × 0.0833°** resolution.

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

### FLUXCOM Data Source

FLUXCOM carbon flux data products (https://www.fluxcom.org/CF-Download/), stored in netCDF-4 format.  
We use the **RS** (Remote Sensing) setup, which uses MODIS-only inputs.

Processing steps (see `Extraction_FLUXCOM_data.ipynb`):
1. Open annual NetCDF file from `dir_path` containing global GPP/NEE/TER values.  
2. Load NEON site latitude/longitude.  
3. Interpolate grid-cell values to site coordinates.  
4. Assemble outputs into a tidy table.  
5. Save as CSV for analysis.

---

## `Long_term_DI_CRU.xlsx`

Provides long-term climate aridity values for selected NEON sites.

| Variable | Source | Description | Units |
|----------|--------|-------------|-------|
| NEONSite | - | NEON site code | - |
| Ecosystem | - | Ecosystem type | - |
| location | - | Site name | - |
| DI | CRU | Dryness Index (PET / rainfall) | - |

### Data Source  
CRU global climate dataset (https://crudata.uea.ac.uk/cru/data/hrg/), providing long-term PET and rainfall at **0.5° × 0.5°** resolution.

---

# Data Sources

## 1. MODIS Satellite Data (Google Earth Engine)

Retrieved for 2018-01-01 to 2024-12-31:

### fPAR & LAI — MCD15A3H.061  
- Variables: `Fpar`, `Lai`, `FparLai_QC`  
- Temporal resolution: 4-day  
- Spatial resolution: 500 m  

### GPP and NPP — MOD17A2H.061  
- Variables: `Gpp`, `PsnNet` (NPP), `Psn_QC`  
- Temporal resolution: 8-day  
- Spatial resolution: 500 m  

### Annual GPP and NPP — MOD17A3HGF.061  
- Temporal resolution: annual  
- Spatial resolution: 500 m  

### ET & PET — MOD16A2.061  
- Variables: `ET`, `PET`, `ET_QC`  
- Temporal resolution: 8-day  
- Spatial resolution: 500 m  

---

## 2. NEON (National Ecological Observatory Network)

### PAR — DP1.00024.001  
- Temporal resolution: 30 minutes  
- Period: 2013–2023  
- Variable: `PARMean`

---

## 3. AmeriFlux

Using pre-downloaded monthly MM files:

### Eddy Covariance Variables  
- `NEE_VUT_REF`  
- `RECO_NT_VUT_REF`  
- `GPP_NT_VUT_REF`  
- `P_F` (Precipitation)  
- Temporal resolution: monthly  

### Missing Sites  
Three sites were processed using **ReddyProc** with `GPP_DT_uStar` (daytime partitioning).

---

# Data Processing Information

## 1. MODIS Data Processing

Steps:
1. Load raw MODIS CSV files.  
2. Extract year and month from datetime.  
3. Apply scaling factors:
   - fPAR × 0.01  
   - ET/PET × 0.1  
4. Compute **CUE = NPP / GPP**.  
5. Aggregate to monthly values:
   - fPAR → mean  
   - GPP/NPP/CUE → mean  
   - ET/PET → sum  
6. Assign growing season flags using site-specific SOS/EOS.  
7. Merge all MODIS variables into a single dataset.

---

## 2. NEON PAR Data Processing

Steps:
1. Load raw NEON PAR parquet files (30-minute resolution).  
2. Stack all sites using DuckDB.  
3. Extract year and month.  
4. Compute monthly PAR means.  
5. Assign growing season flags.  
6. Map NEON → FluxNet site codes.  

**Output:**  
`data/interim/neon_par_stacked_all_sites.parquet`

---

## 3. AmeriFlux Data Processing

Steps:
1. Load monthly AmeriFlux CSV files.  
2. Extract NEE, RECO, GPP, and precipitation.  
3. Extract year and month from timestamp.  
4. Map FluxNet ↔ NEON codes.  
5. Assign growing season flags based on site and month.

---

## 4. Missing Sites Processing

Steps:
1. Read gap-filled ReddyProc files.  
2. Extract `GPP_DT_uStar` (daytime partitioning).  
3. Convert DOY → date.  
4. Compute monthly means.  
5. Map site codes and assign growing season flags.

---

## 5. Final Data Merging

Steps:
1. Merge NEON PAR with MODIS by:  
   `NEON_Site`, `Fluxnet`, `year`, `month`, `growing_season`  
2. Merge with AmeriFlux using same keys.  
3. Standardize column names.  
4. Remove unused fields.

---

# Growing Season Definition

Growing season is defined per site using:

- **SOS** — Start of Season (month)  
- **EOS** — End of Season (month)  

Rules:
- Months between SOS and EOS (inclusive): **Growing season = 1**  
- Sites with no defined SOS/EOS: **Growing season = 0**  

---

# Data Aggregation Summary

| Data Source    | Original Resolution | Final Resolution | Method |
|----------------|---------------------|------------------|--------|
| NEON PAR       | 30-minute           | Monthly          | Mean   |
| MODIS fPAR     | 4-day               | Monthly          | Mean   |
| MODIS GPP/NPP  | 8-day               | Monthly          | Mean   |
| MODIS ET/PET   | 8-day               | Monthly          | Sum    |
| AmeriFlux      | Monthly             | Monthly          | None   |

---

# Contact

For more information, please contact:  
📧 **skanand@tamu.edu**
