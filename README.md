# NWAA Data Companion × Pesticide Observations (Pilot)

This workflow connects monthly modeled hydrology from the USGS **National Water Availability Assessment (NWAA) Data Companion** with pesticide observations at three pilot USGS stations.

## Workflow

```text
USGS station
    ↓
NLDI upstream basin
    ↓
HUC12s intersecting basin
    ↓
Calculate HUC12 area inside basin
    ↓
Extract monthly DC variables
    ↓
Area-weighted basin values
    ↓
Join with pesticide observations
    (station ID + year-month)
```

| Item | Description |
|---|---|
| DC product | USGS NWAA Data Companion – NHM-PRMS CONUS water-quantity dataset |
| Product ID | `wqn-nhmprms-conus-nwaa-v1` |
| Source file | `combined_wqn-nhmprms-conus-nwaa-v1_CONUS_198301-202109_long.csv` |
| Source coverage | January 1983 – September 2021 |
| Study period used | January 2013 – August 2021 |
| Spatial unit | HUC12 |
| Temporal resolution | Monthly |
| Variables used | `incqkflow`, `incbsflow`, `soilmstfr` |

The Data Companion variables are modeled monthly upstream-basin hydrologic features. They are stored in separate dc_* columns and do not replace the observed environmental variables associated with pesticide sampling activities.

## Pilot Stations

| Station | Name | Pilot HUC12s | Joined pesticide rows |
|---|---|---:|---:|
| 06800000 | Maple Creek, NE | 10 | 16,348 |
| 05451210 | South Fork Iowa River, IA | 6 | 16,045 |
| 02091500 | Contentnea Creek, NC | 26 | 16,056 |

## Repository Structure

```text
nwaa-dc-pesticide-hydrology/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   ├── 01_dc_hydrology_pilot_3_sites.ipynb
│   └── 02_dc_harmonized_all_sites.ipynb
└── data/
    ├── README.md
    ├── raw/
    ├── interim/
    └── processed/
```

## How to Run

1. Open `notebooks/02_dc_harmonized_all_sites.ipynb`.
2. Place the pesticide dataset and WBD HUC12 file in the configured data location.
3. Add the USGS API key through the environment or Colab Secrets if required.
4. Run the notebook from top to bottom.
5. Add additional stations to `STATIONS` for the national run.

## Harmonized Method

The standardized workflow uses:

- October 2020 WBD HUC12 boundaries
- consistent HUC12 intersection criteria
- intersection-area-weighted aggregation
- `YYYY-MM` month format
- 8-digit text USGS station IDs
- standard DC columns:
  - `dc_incqkflow_mm`
  - `dc_incbsflow_mm`
  - `dc_soilmstfr`

## Temporal Alignment

Pesticide observations retain their original sampling dates and times.

The monthly DC values are joined using:

```text
station_id + year_month
```

The DC variables represent **monthly upstream-basin hydrologic context**, not conditions measured on the exact pesticide sampling date.

## Quality Checks

The workflow checks:

- monthly DC coverage
- duplicate station-month records
- missing DC values
- unmatched pesticide observations
- preservation of pesticide row counts
- station ID formatting
- representative spot checks