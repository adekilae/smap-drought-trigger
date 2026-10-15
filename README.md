# SMAP soil-moisture dryness trigger for U.S. Drought Monitor onset

Code for the manuscript by F. A. Adekilae on whether SMAP surface soil-moisture anomalies give early warning of county-level drought development in the U.S. Drought Monitor (USDM), 2016–2020.

## Notebooks

| Notebook | What it does |
|---|---|
| `smap_drought_trigger.ipynb` | Original processing: SMAP export from Google Earth Engine, county aggregation, anomaly (z-score) computation, and the dryness trigger. |
| `SMAP_corrected_lead_windows.ipynb` | Verification analysis used for the revised manuscript (sections below). |

### `SMAP_corrected_lead_windows.ipynb`

1–7. **Corrected lead windows.** Event labels are DSCI ≥ 200 on any USDM map date in (t, t+L] days (Eq. 7), and the trigger is ≥ 3 dry days (z ≤ −1) among calendar days t−4 to t (Eq. 6). Version A reproduces the original method; version C matches the Methods text.
8. **Onset-only and continuous score.** Verifies the binary trigger and the continuous dryness score (−z) on all cases and on counties below DSCI 200 at initialization.
9. **Strict onset with uncertainty.** Requires no DSCI ≥ 200 in the preceding 14, 28 or 42 days (28 days is primary), and gives county-cluster bootstrap 95% confidence intervals (1,000 resamples, seed 2026).
10. **Persistence baseline.** Compares initial DSCI alone, SMAP alone, and both combined, and reports SMAP's ROC–AUC within starting-DSCI groups (0, 1–99, 100–149, 150–199), with the number of onsets in each group.

Lead windows: 7, 14, 21, 30, 45 and 60 days.

## Running it

The notebooks run in Google Colab (**Runtime → Run all**) and read from Google Drive:

```
MyDrive/SMAP_preprocessed/
├── SMAP_county_daily_2016_2020_with_anom.parquet   # county_fips, date, sm_z
└── USDM/USDM_county_2016_2020.csv.csv              # FIPS, MapDate, DSCI
```

Results are written to `MyDrive/SMAP_preprocessed/Corrected_LeadWindow_Results/`. To run elsewhere, remove the Drive-mount lines in section 1 and set `out_base` to your data folder.

Python packages: `numpy`, `pandas`, `pyarrow`, `scikit-learn`, `matplotlib` (see `requirements.txt`).

## Data

- SMAP: NASA SMAP soil moisture, accessed through Google Earth Engine.
- USDM: county statistics (DSCI) from the U.S. Drought Monitor, https://droughtmonitor.unl.edu.

The derived county-level tables are available from the author on reasonable request.

## Contact

Frank Anyoka Adekilae — fadekilae@agcenter.lsu.edu
