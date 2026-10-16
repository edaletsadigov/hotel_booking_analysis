# Notebook Guide: `hotel_bookings.ipynb`

This document explains how the analysis notebook is built: what each section does, which libraries are used, and why each one was chosen.

- **Notebook:** [`hotel_bookings.ipynb`](../hotel_bookings.ipynb) (123 cells: 76 code, 47 markdown; all outputs and charts included)
- **Input:** `hotel_bookings.csv` (see [Dataset Guide](DATASET_README.md)), read with a relative path, so the file must sit **next to the notebook**
- **Output:** tables, 9 charts ([gallery](../charts/README.md)), statistical tests and a written summary with business recommendations

## How to run

```bash
pip install numpy pandas matplotlib seaborn scipy jupyter
jupyter notebook hotel_bookings.ipynb
```

Place `hotel_bookings.csv` in the same folder as the notebook, then run all cells from top to bottom (**Kernel > Restart & Run All**). The notebook runs without errors on the versions below.

| Tool | Version used |
|---|---|
| Python | 3.12 |
| pandas | 3.0.2 |
| NumPy | 2.4.4 |
| matplotlib | 3.10.8 |
| seaborn | 0.13.2 |
| SciPy | 1.17.1 |

## Libraries: what they do and why they are used

| Library | What it is used for in this notebook | Why this library |
|---|---|---|
| **pandas** | Loading the CSV, missing-value handling, building `arrival_date`, `groupby` aggregations, `pivot_table`, `pd.cut` for lead-time buckets, `pd.crosstab` for tests | The task asks for pivot tables and time-based aggregation; pandas does both in a few readable lines on a 119k-row table, and `pivot_table` returns exactly the month x hotel shape needed for a heatmap |
| **NumPy** | `np.inf` as the open upper bin edge, `np.where` for the parking flag, `np.sqrt` for Cramer's V | Needed for numerical edge cases that plain Python handles badly; pandas is built on it |
| **matplotlib** | All charts: line, bar, horizontal bar, pie, stacked bar, dual-axis figure (`twinx`), subplot grids, figure sizing and saving | Full control over titles, axis labels and layout; the only way to build a true dual-axis chart (ADR line + volume bars) |
| **seaborn** | Heatmaps (`sns.heatmap`), box plots (`sns.boxplot`), the shared visual theme and colour palettes | Heatmaps with annotations and a colour bar are one call; box plots split by two categories are concise; consistent professional styling |
| **SciPy (`scipy.stats`)** | `mannwhitneyu`, `chi2_contingency`, `spearmanr`, `skew` | Standard, well-tested implementations of the hypothesis tests used in section 3; no need to hand-code statistics |

> No machine-learning library is used. The goal is descriptive and inferential analysis, not prediction.

### Key functions and the reason behind each choice

| Function / technique | Where | Why it was chosen |
|---|---|---|
| `pd.to_datetime(dict(year=..., month=..., day=...))` | 1. Cleaning | Builds a real datetime from three separate columns; the month name is mapped to a number first |
| `pivot_table(..., aggfunc='count')` and `aggfunc='mean'` | 2.2, 2.3, 2.7 | One function gives booking counts, cancellation rates and ADR in the month x hotel layout |
| `reindex(MONTHS)` | 2.2, 2.3, 2.7 | Calendar order instead of alphabetical order (April would otherwise come before January) |
| `div(months_observed, axis=0)` | 2.2 | Corrects the uneven month coverage (July-August are observed 3 times, other months 2 times) |
| `pd.cut` with bins `[-1, 7, 30, 90, 180, 365, inf]` | 2.4 | Turns skewed lead time into business-readable buckets |
| Named aggregation `.agg(bookings=('col', 'size'), ...)` | 2.1, 2.5, 2.6 | Clear column names in the output, no flattening of MultiIndex columns |
| Median instead of mean for lead time | 2.4 | Lead time is right-skewed (skewness 1.35, mean 104.0 vs median 69) |
| `mannwhitneyu` | 3 | Compares lead time of cancelled vs kept bookings without assuming a normal distribution |
| `chi2_contingency` + Cramer's V | 3 | Tests independence of two categorical variables, and Cramer's V shows how *large* the effect is |
| `spearmanr` | 2.3, 3 | Rank correlation, robust to outliers and non-linear relationships |
| Robustness scenarios | 3 | Re-computes headline rates without `Non Refund` deposits, without duplicates and without `PRT` to show which results are stable |
| `ax.twinx()` | 2.7 | A second y-axis, so ADR (price) and volume (bookings) can share one x-axis |

## Notebook structure

| Section | Cells (approx.) | Content |
|---|---|---|
| **Setup** | 0-4 | Imports, constants (`MONTHS`, `HOTEL_COLORS`, `ALPHA`), loading the data |
| **1. Data Overview and Cleaning** | 5-29 | `info`, `describe`, missing values, duplicates, value counts; fills `country`, `agent`, `company`, `children`; checks odd `adr`, nights and guest values; builds `arrival_date` and derived columns; **coverage guard** (which months appear how many times) |
| **2.1 Hotel-level overview** | 31-35 | Bookings, cancellations, lead time and stay length per hotel |
| **2.2 Pivot: month x hotel** | 36-46 | Booking-count pivot, heatmaps (charts 1-2), 2016 baseline |
| **2.3 Monthly cancellation rate** | 47-53 | Cancellation pivot, line chart (chart 3), Spearman check |
| **2.4 Lead time** | 54-57 | Cancelled vs kept lead time, lead-time buckets (chart 4) |
| **2.5 Top 10 countries** | 58-65 | Country table, bar charts (chart 5), `PRT` deep dive, `Non Refund` check |
| **2.6 Market segments** | 66-77 | Segment table, pie and bar (chart 6), segment x deposit rates |
| **2.7 ADR (bonus)** | 78-84 | ADR by month and hotel, dual-axis chart (chart 7) |
| **2.8 Revenue exposure** | 85-91 | `adr` x nights proxy, cancelled revenue split (chart 8) |
| **2.9 Cancellation drivers** | 92-96 | Six driver bar charts (chart 9) |
| **3. Statistical Analysis** | 97-108 | Mann-Whitney U, chi-square + Cramer's V, robustness table, **check against the reference notes** |
| **4. Final Summary** | 109-122 | Key findings, four business insights, recommendations table |

## Cleaning and design decisions

| Decision | Reason |
|---|---|
| `country` missing -> `'Unknown'` (488 rows) | The booking is valid; only the origin is missing |
| `agent` / `company` missing -> `0` (13.7% / 94.3%) | Real IDs start at 1 and 6, so `0` works as a "none" flag; it is never averaged |
| `children` missing -> `0` (4 rows) | Negligible (0.003%) |
| Duplicate rows (31,994 = 26.8%) are **kept** | No booking ID exists, so identical rows can be separate guests; the effect is tested in the robustness check |
| Odd rows (`adr = 0`, zero nights, zero guests) are **kept** for counts and cancellation | They are real records with real outcomes |
| ADR / revenue use only `0 < adr < 1,000` (117,429 rows) | A negative rate, free stays and one 5,400 outlier would distort averages |
| 2016 is the seasonal baseline | It is the only complete calendar year; July-August appear 3 times in the raw data |
| `is_canceled` includes no-shows (1,207) | It is the flag provided; "cancelled" here means "the room was not used" |
| `reservation_status` is **not** used as a predictor | It contains the outcome itself (data leakage) |

## Known limitations of the notebook

- Cancellation **timing** is not analysed (`reservation_status_date` could be used for this).
- Revenue figures are a proxy (`adr` x nights); deposit amounts are not in the data.
- Special requests, booking changes and parking spaces can follow the outcome, so they are read as indicators, not causes.
- All results are observational; they show association, not causation.

## Related documents

- [Main README](../README.md) - results, insights and recommendations
- [Chart gallery](../charts/README.md) - analysis of each chart
- [Dataset Guide](DATASET_README.md) - columns and data quality

