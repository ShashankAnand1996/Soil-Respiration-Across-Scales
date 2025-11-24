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

(…content unchanged…)

## 2. NEON (National Ecological Observatory Network)

(…content unchanged…)

## 3. AmeriFlux

(…content unchanged…)

---

# Data Processing Information

(…content unchanged…)

---

# Growing Season Definition

(…content unchanged…)

---

# Data Aggregation Summary

(…content unchanged…)

---

# Contact

For questions or additional information, contact:  
📧 **skanand@tamu.edu**

