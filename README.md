# SMAP Soil Moisture Drought Trigger for US Counties

A reproducible Python workflow that turns daily **SMAP 9 km satellite soil moisture** into a simple **county-level drought early-warning trigger** for the contiguous United States, and tests how well it anticipates drought recorded by the **US Drought Monitor (USDM)**.

> **Status:** exploratory research workflow (work in progress). Results are preliminary and not peer-reviewed.

## What it does

1. **Cleans SMAP data**: reads one CSV per year (exported from Google Earth Engine), keeps plausible values, and saves compact Parquet files.
2. **Maps pixels to counties**: assigns each 9 km pixel to the county containing its center.
3. **Aggregates to counties**: computes county-daily mean soil moisture.
4. **Removes the seasonal cycle**: builds a day-of-year climatology (±15-day window) per county and computes z-score anomalies.
5. **Flags dryness**: triggers when at least 3 of the last 5 calendar days have z ≤ −1.
6. **Evaluates skill**: compares the dryness score and trigger with future USDM drought events at 7–60 day lead times, using ROC-AUC, PR-AUC, hit rate, and false-alarm ratio, with an *onset-only* option that excludes counties already in drought.

## Repository contents

| File | Description |
|---|---|
| `smap_drought_trigger.ipynb` | Full workflow, from raw CSVs to skill scores and figures |
| `requirements.txt` | Python packages needed |
| `LICENSE` | MIT License |

## Data

Data files are **not** included in this repository. Place them in a `data/` folder (or edit the paths in the configuration cell).

| Input | Expected file | Source |
|---|---|---|
| SMAP soil moisture, 9 km, daily | `data/SMAP_9km_pixels_<year>.csv` with columns `date`, `lat`, `lon`, `sm` | NASA SMAP via Google Earth Engine (**add the exact collection ID**, e.g. `NASA/SMAP/SPL3SMP_E/...`) |
| USDM county statistics, weekly | `data/USDM_county_2016_2020.csv` with columns `FIPS`, `MapDate`, `DSCI` | [US Drought Monitor data download](https://droughtmonitor.unl.edu/DmData/DataDownload.aspx) |
| US county boundaries | downloaded automatically | [US Census cartographic boundary files (2020)](https://www.census.gov/geographies/mapping-files/time-series/geo/cartographic-boundary.html) |

## How to run

**Locally**
```bash
pip install -r requirements.txt
jupyter notebook smap_drought_trigger.ipynb
```

**Google Colab:** open the notebook in Colab, uncomment the Google Drive cell in Section 1, and point `DATA_DIR` and `OUT_DIR` to your Drive folders.

All settings (years, thresholds, lead times) are in the configuration cell at the top.

## Method notes and limitations

- **Drought event definition.** An event is `DSCI ≥ 200`. The USDM Drought Severity and Coverage Index is area-weighted (0–500), so 200 corresponds to the whole county in D1. Partial-area droughts can fall below it. For "D1 or worse" by area, USDM's percent-area county statistics are a stricter alternative.
- **Quality control.** Only a plausible-range filter (0–0.7 m³/m³) is applied. SMAP retrieval-quality, frozen-ground, and open-water flags should be applied when exporting from Earth Engine.
- **County coverage.** Counties smaller than a 9 km pixel may receive no pixel center and are omitted.
- **Climatology length.** Anomalies are relative to the years analyzed (2016–2020 by default), which is short, and the same years are used for evaluation.
- **Missing days.** SMAP 9 km has gaps; the trigger uses calendar-day windows, and each weekly USDM date is matched to the latest SMAP day within 3 days.

## Author

**Frank Anyoka Adekilae**, PhD student, Soil Hydrology and Remote Sensing (SHaRE) Lab, School of Plant, Environmental, and Soil Sciences, Louisiana State University
Website: [adekilae.github.io](https://adekilae.github.io) · Email: FAdekilae@agcenter.lsu.edu

## License

Code released under the [MIT License](LICENSE).
