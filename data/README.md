# Data

This folder contains the raw, intermediate (interim), and processed files for the pilot connection between
pesticide observations and the NWAA Data Companion (DC).

| Folder | Contents | In Git? |
|---|---|---|
| `raw/basins/` | NLDI basin polygons (`<station>_basin.geojson`) | Yes |
| `raw/` | Oct-2020 WBD HUC12 layer (`102020_wbd_hu12.gpkg`) and pesticide dataset (`t2_pest_concs_enriched_v2.parquet`) | No (large / private) |
| `interim/` | HUC12 weights, HUC12 monthly DC values, and basin monthly DC values per station | Yes |
| `processed/` | Combined files: pesticide observations joined with monthly DC values, one per station | Yes |

## Files in `interim/`

Each pilot station (`02091500`, `05451210`, `06800000`) has two types of files:

| File | Contents |
|---|---|
| `<station>_huc12_month.csv` | Monthly DC values for each HUC12 inside the NLDI basin (≥ 1 km² inside), before averaging: `dc_incqkflow_mm`, `dc_incbsflow_mm`, `dc_soilmstfr` |
| `<station>_basin_month.csv` | One area-weighted basin value per month for all 104 months (Jan 2013 – Aug 2021), combining the HUC12 values above |

## Files in `processed/`

Each pilot station has one combined file:

| File | Contents |
|---|---|
| `combined_<station>_with_dc.csv` | All pesticide observations at the station (all analytes, Jan 2013 – Aug 2021) with their original sample dates and observed environmental variables, plus the three monthly DC values for the sampling month: `dc_incqkflow_mm`, `dc_incbsflow_mm`, `dc_soilmstfr` |

| Station | Combined file | Pesticide rows |
|---|---|---|
| 06800000 Maple Creek | `combined_06800000_with_dc.csv` | 16,348 |
| 05451210 South Fork Iowa River | `combined_05451210_with_dc.csv` | 16,045 |
| 02091500 Contentnea Creek | `combined_02091500_with_dc.csv` | 16,056 |

Join rule: `station_id` + `year_month` of the sampling date. Every pesticide row is kept, and all samples in the
same month receive the same DC values. DC columns are prefixed `dc_` to keep modeled hydrology distinct from the
observed variables.

## DC variables

| Variable | Meaning | Unit |
|---|---|---|
| `incqkflow` | Incremental quickflow (fast storm runoff) | mm/month |
| `incbsflow` | Incremental baseflow (slow groundwater flow) | mm/month |
| `soilmstfr` | Soil moisture fraction (0 = dry, 1 = saturated) | fraction |

## Sources
- **NWAA Data Companion, NHM-PRMS product** `wqn-nhmprms-conus-nwaa-v1`:
  https://water.usgs.gov/nwaa-data/ (data file directory → water-quantity → wqn-nhmprms-conus-nwaa-v1).
  File used: `combined_wqn-nhmprms-conus-nwaa-v1_CONUS_198301-202109_long.csv` (5.3 GB, streamed).
- **HUC12 boundaries** (`102020_wbd_hu12.gpkg`): October 2020 Watershed Boundary Dataset,
  https://www.sciencebase.gov/catalog/item/67082a0fd34e969edc5a1cca
- **Upstream basins:** USGS NLDI,
  `https://api.water.usgs.gov/nldi/linked-data/nwissite/USGS-<station>/basin`