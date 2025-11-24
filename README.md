# Soil Heterotrophic Respiration

This repository compiles and analyzes soil respiration data from point-scale observations (flux tower measurements) to global gridded satellite-based estimates. The goal is to support further analyses of the drivers of soil heterotrophic respiration across spatial and temporal scales. Data are sourced from NEON, AmeriFlux, MODIS, and FLUXCOM at multiple spatial resolutions.

---

# Table of Contents

1. [Data Files](#data-files)  
2. [Data Sources](#data-sources)  
   - [MODIS Satellite Data](#1-modis-satellite-data-google-earth-engine)  
   - [NEON](#2-neon-national-ecological-observatory-network)  
   - [AmeriFlux](#3-ameriflux)  
3. [Data Processing Information](#data-processing-information)  
   - [MODIS Data](#1-modis-data-processing)  
   - [NEON PAR Data](#2-neon-par-data-processing)  
   - [AmeriFlux Data](#3-ameriflux-data-processing)  
   - [Missing Sites](#4-missing-sites-processing)  
   - [Final Merge](#5-final-data-merging)  
4. [Growing Season Definition](#growing-season-definition)  
5. [Data Aggregation Summary](#data-aggregation-summary)  
6. [FLUXCOM Extracted Datasets](#fluxcom-extracted-datasets)  
7. [Long-term Climate Aridity (CRU)](#long_term_di_cruxlsx)  
8. [Contact](#contact)

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
| GrowingSeason | Derived | Growing season flag (1=growing, 0=non-growing) | binary |
| NEON_PAR | NEON | Photosynthetically Active Radiation | μmol/m²/s |
| MODIS_FPAR | MODIS | Fraction of PAR absorbed by vegetation | 0–1 |
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

# Data Sources

## 1. MODIS Satellite Data (Google Earth Engine)

Retrieved for 2018-01-01 to 2024-12-31.

- **MCD15A3H.061** — fPAR, LAI  
  - Variables: `Fpar`, `Lai`, `FparLai_QC`  
  - 4-day, 500 m  

- **MOD17A2H.061** — GPP, NPP (8-day)  
  - Variables: `Gpp`, `PsnNet`, `Psn_QC`  
  - 8-day, 500 m  

- **MOD17A3HGF.061** — Annual GPP, NPP  
  - Annual, 500 m  

- **MOD16A2.061** — ET, PET  
  - Variables: `ET`, `PET`, `ET_QC`  
  - 8-day, 500 m  

---

## 2. NEON (National Ecological Observatory Network)

- **PAR** — DP1.00024.001  
  - Temporal resolution: 30-minute  
  - Period: 2013–2023  
  - Variable: `PARMean`

---

## 3. AmeriFlux

Using pre-downloaded monthly MM files:

- Variables:  
  - `NEE_VUT_REF`  
  - `RECO_NT_VUT_REF`  
  - `GPP_NT_VUT_REF`  
  - `P_F` (precipitation)  
- Temporal resolution: monthly  

**Missing sites:**  
Three sites processed separately using ReddyProc (`GPP_DT_uStar`).

---

# Data Processing Information

## 1. MODIS Data Processing
Steps:
1. Load raw MODIS CSV files.
2. Extract year/month.
3. Apply scaling factors:  
   - fPAR × 0.01  
   - ET/PET × 0.1  
4. Compute CUE = NPP / GPP.
5. Aggregate to monthly values.
6. Assign growing season flags.
7. Merge MODIS variables into a monthly dataset.

---

## 2. NEON PAR Data Processing
1. Load 30-minute PAR parquet files.
2. Stack all sites using DuckDB.
3. Extract year and month.
4. Compute monthly means.
5. Assign growing season flags.
6. Map NEON → FluxNet codes.

Output:  
`data/interim/neon_par_stacked_all_sites.parquet`

---

## 3. AmeriFlux Data Processing
1. Load monthly MM files.
2. Extract NEE, RE, GPP, precipitation.
3. Extract year and month.
4. Map FluxNet ↔ NEON site codes.
5. Assign growing season flags.

---

## 4. Missing Sites Processing
1. Read ReddyProc text files.
2. Extract `GPP_DT_uStar`.
3. Convert DOY → date.
4. Compute monthly averages.
5. Assign growing season flags.

---

## 5. Final Data Merging
1. Merge NEON PAR + MODIS by site, year, month, season.
2. Merge the result with AmeriFlux.
3. Standardize column names.
4. Remove unused fields.

---

# Growing Season Definition

Growing season is defined per site using:

- **SOS** — Start of Season  
- **EOS** — End of Season  

Months between SOS and EOS (inclusive) are flagged as **1**.  
Sites without defined growing seasons receive **0** for all months.

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

# FLUXCOM Extracted Datasets

## `Neondata_Fluxcom_720_360.csv`  
0.5° × 0.5° (720 × 360 grid)

## `Neondata_Fluxcom_4320_2160.csv`  
0.0833° × 0.0833° (4320 × 2160 grid)

Both files contain monthly GPP, NEE, and TER extracted for ~30 NEON sites.

| Variable | Source | Description | Units |
|----------|--------|-------------|-------|
| NEONSite | - | NEON site code | - |
| FluxnetSite | - | FluxNet site code | - |
| location | - | Site name | - |
| month | - | Month | 1–12 |
| GPP_YEAR | MODIS | Gross Primary Production | g C/m²/day |
| NEE_YEAR | MODIS | Net Ecosystem Exchange | g C/m²/day |
| TER_YEAR | MODIS | Total Ecosystem Respiration | g C/m²/day |

### FLUXCOM Data Source  
FLUXCOM RS (Remote Sensing) data products (netCDF-4).  
Processed using `Extraction_FLUXCOM_data.ipynb`:

1. Open NetCDF file (`dir_path`).  
2. Load NEON site coordinates.  
3. Bi-linear interpolate grid cell values.  
4. Assemble tidy output table.  
5. Save to CSV.

---

# `Long_term_DI_CRU.xlsx`

Provides long-term climate aridity for selected NEON sites.

| Variable | Source | Description | Units |
|----------|--------|-------------|-------|
| NEONSite | - | NEON site code | - |
| Ecosystem | - | Ecosystem type | - |
| location | - | Site name | - |
| DI | CRU | Dryness Index (PET / rainfall) | - |

Data Source: CRU global climatology (0.5° resolution, netCDF-4).

---

# Contact

For more information, please contact:  
📧
