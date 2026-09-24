# Global Temperature Anomaly Data Cleaning & Analysis

A reproducible Python pipeline that cleans a deliberately corrupted monthly
temperature anomaly dataset (1880–2025), detects and repairs structural data-quality
issues, and produces a dual-encoded visualization and an IEEE-format report.

## What it does

- **Date standardization** — parses 12+ inconsistent date formats (`188001`,
  `1880/03`, `Jun 1880`, `Jan-43`, `7/1/07`, etc.), including correct handling of
  two-digit years (`26–99 → 19xx`, `00–25 → 20xx`) rather than relying on a
  library's default century pivot.
- **Swapped-field repair** — detects and fixes rows where the date and
  temperature value were swapped into the wrong columns.
- **Value parsing** — normalizes comma-decimal separators, strips stray `°C`
  suffixes, and maps every missing-value token (`--`, `N/A`, `null`, etc.) to `NaN`.
- **Deduplication** — removes duplicate monthly records, keeping valid readings
  over blanks.
- **Outlier removal (IQR method)** — flags and removes sensor-malfunction codes
  (`±500`, `±999`) using interquartile-range fences.
- **Imputation** — reindexes to a complete monthly grid and fills gaps via linear
  interpolation in time.
- **Normalization** — computes deviation from the 1901–2000 baseline (`d`) and a
  full-record z-score (`z`).
- **Dual-encoded chart** — a single time series where both line position and
  color (via `LineCollection`, diverging colormap centered at 0) encode the
  anomaly.
- **IEEE-format report** — a one-page LaTeX (`IEEEtran`) report summarizing the
  cleaning statistics, normalization equations, and top-5 warmest years.

## Repo contents

| File | Description |
|---|---|
| `Mini_Project_1_Global_Temperature_Anomalies.ipynb` | Full cleaning, analysis, and charting pipeline — run end-to-end on the raw CSV |

Running the notebook top-to-bottom (`Restart & Run All`) regenerates the cleaned
series, cleaning log counts, and dual-encoded chart entirely from the raw input
file — nothing is hardcoded or checked in separately.

### Requirements

- Python 3
- `pandas`, `numpy`, `matplotlib`

### Usage

Place `global_temp_dirty_v2.csv` in the same directory and run all cells in
`Mini_Project_1_Global_Temperature_Anomalies.ipynb`.

## Pipeline summary

1. Load raw CSV as text, strip header whitespace, drop true junk/footer lines
2. Repair swapped date/temperature fields
3. Expand two-digit years to four digits, then parse all dates
4. Parse temperature values (comma decimals, °C suffix, missing tokens)
5. Deduplicate and sort chronologically
6. Remove outliers via IQR fences
7. Reindex to a complete monthly grid and linearly interpolate gaps
8. Compute baseline deviation `d` and full-record z-score `z`
9. Summarize by calendar year and identify the five warmest years
10. Generate the dual-encoded chart and IEEE-format report

## Note

The dataset is simulated for teaching purposes — its long-term shape is modeled
on published climate records, but the values are **not** official NOAA, NASA
GISS, or HadCRUT data.
