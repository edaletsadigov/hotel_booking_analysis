# Dataset Guide: Hotel Booking Demand

## Overview

| Property | Value |
|---|---|
| **File** | `hotel_bookings.csv` |
| **Source** | Kaggle - [jessemostipak/hotel-booking-demand](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand) (check the Kaggle page for licence terms) |
| **Rows** | **119,390** (one row = one hotel booking) |
| **Columns** | **32** |
| **Hotels** | 2 - `City Hotel` (79,330 rows, 66.5%) and `Resort Hotel` (40,060 rows, 33.5%) |
| **Arrival dates** | 2015-07-01 to 2017-08-31 (26 months) |
| **Target-like column** | `is_canceled` - 1 for 44,224 bookings (**37.0%**), 0 for 75,166 (63.0%) |
| **File size** | about 16.9 MB (about 98 MB in memory) |
| **Column types** | 16 integer, 4 float, 12 text |
| **Missing values** | 4 columns: `company`, `agent`, `country`, `children` |
| **Exact duplicate rows** | 31,994 (26.8%) |

**Observation period:** the data is not evenly spread. 2015 covers only July-December (21,996 rows), 2016 covers the whole year (56,707 rows) and 2017 covers January-August (40,687 rows). July and August are therefore observed **three** times, every other month **twice**.

**Origin:** the dataset is the hotel demand data published with *Hotel booking demand datasets* (Antonio, de Almeida and Nunes, Data in Brief, 2019), describing bookings of a city hotel and a resort hotel. Please check the citation details on the Kaggle page.

## Column reference

### Booking and outcome

| # | Column | Type | Description | Values / range |
|---|---|---|---|---|
| 1 | `hotel` | text | Hotel type | `City Hotel`, `Resort Hotel` |
| 2 | `is_canceled` | int | 1 if the booking was cancelled (including no-shows), 0 otherwise | 0 / 1 (37.0% = 1) |
| 3 | `lead_time` | int | Days between the booking entering the system and the arrival date | 0 - 737, mean 104.0 |
| 31 | `reservation_status` | text | Last status of the booking. **Contains the outcome - do not use as a predictor** | `Check-Out` 75,166; `Canceled` 43,017; `No-Show` 1,207 |
| 32 | `reservation_status_date` | text | Date when the last status was set (for cancelled bookings, the cancellation date) | 2014-10-17 to 2017-09-14 |

### Arrival date

| # | Column | Type | Description | Values / range |
|---|---|---|---|---|
| 4 | `arrival_date_year` | int | Year of arrival | 2015, 2016, 2017 |
| 5 | `arrival_date_month` | text | Month of arrival (English name) | `January` - `December` |
| 6 | `arrival_date_week_number` | int | Week number of the year of arrival | 1 - 53 |
| 7 | `arrival_date_day_of_month` | int | Day of the month of arrival | 1 - 31 |

> The dataset has no single date column; the notebook combines columns 4, 5 and 7 into `arrival_date`.

### Stay and guests

| # | Column | Type | Description | Values / range |
|---|---|---|---|---|
| 8 | `stays_in_weekend_nights` | int | Number of Saturday/Sunday nights booked | 0 - 19, mean 0.93 |
| 9 | `stays_in_week_nights` | int | Number of Monday-Friday nights booked | 0 - 50, mean 2.50 |
| 10 | `adults` | int | Number of adults | 0 - 55, mean 1.86 |
| 11 | `children` | float | Number of children | 0 - 10; **4 missing** |
| 12 | `babies` | int | Number of babies | 0 - 10 |
| 13 | `meal` | text | Meal package booked | `BB` bed and breakfast 92,310; `HB` half board 14,463; `SC` no meal package 10,650; `Undefined` 1,169; `FB` full board 798 |
| 14 | `country` | text | Guest's country of origin (ISO 3166 alpha-3 code) | 177 codes; `PRT` 48,590 rows; **488 missing** |

### Channel and segment

| # | Column | Type | Description | Values / range |
|---|---|---|---|---|
| 15 | `market_segment` | text | Market segment designation (TA = travel agents, TO = tour operators) | `Online TA` 56,477; `Offline TA/TO` 24,219; `Groups` 19,811; `Direct` 12,606; `Corporate` 5,295; `Complementary` 743; `Aviation` 237; `Undefined` 2 |
| 16 | `distribution_channel` | text | Booking distribution channel | `TA/TO` 97,870; `Direct` 14,645; `Corporate` 6,677; `GDS` 193; `Undefined` 5 |
| 24 | `agent` | float | ID of the travel agency that made the booking | 333 distinct IDs; **16,340 missing (13.7%)** = no agent |
| 25 | `company` | float | ID of the company/entity that made or pays for the booking | 352 distinct IDs; **112,593 missing (94.3%)** = no company |
| 27 | `customer_type` | text | Type of booking | `Transient` 89,613; `Transient-Party` 25,124; `Contract` 4,076; `Group` 577 |

### Guest history

| # | Column | Type | Description | Values / range |
|---|---|---|---|---|
| 17 | `is_repeated_guest` | int | 1 if the booking name is from a repeated guest | 0 / 1 (3.2% = 1) |
| 18 | `previous_cancellations` | int | Number of earlier bookings of this guest that were cancelled | 0 - 26 |
| 19 | `previous_bookings_not_canceled` | int | Number of earlier bookings of this guest that were not cancelled | 0 - 72 |

### Rooms and changes

| # | Column | Type | Description | Values / range |
|---|---|---|---|---|
| 20 | `reserved_room_type` | text | Room type code reserved (codes are anonymised) | 10 codes (`A`-`H`, `L`, `P`) |
| 21 | `assigned_room_type` | text | Room type code assigned at check-in; may differ from the reserved one | 12 codes (`A`-`H`, `I`, `K`, `L`, `P`) |
| 22 | `booking_changes` | int | Number of changes made to the booking between entry and check-in or cancellation | 0 - 21, mean 0.22 |
| 26 | `days_in_waiting_list` | int | Days the booking waited on the list before it was confirmed | 0 - 391; 3.1% of rows > 0 |

### Payment and price

| # | Column | Type | Description | Values / range |
|---|---|---|---|---|
| 23 | `deposit_type` | text | Deposit arrangement | `No Deposit` 104,641; `Non Refund` 14,587; `Refundable` 162 |
| 28 | `adr` | float | Average daily rate: total lodging charges divided by number of nights | -6.38 to 5,400, mean 101.83 (currency not stated) |
| 29 | `required_car_parking_spaces` | int | Number of parking spaces requested | 0 - 8 |
| 30 | `total_of_special_requests` | int | Number of special requests (e.g. twin beds, high floor) | 0 - 5, mean 0.57 |

> Column numbers show the order in the CSV file; groups above are for readability only.

## Data quality notes

| Issue | Count | How it is handled in the notebook |
|---|---|---|
| `company` missing | 112,593 (94.3%) | Filled with `0` = "no company" flag, never averaged |
| `agent` missing | 16,340 (13.7%) | Filled with `0` = "no agent" flag |
| `country` missing | 488 (0.4%) | Filled with `'Unknown'` |
| `children` missing | 4 (0.003%) | Filled with `0` |
| Exact duplicate rows | 31,994 (26.8%) | Kept (no booking ID); effect tested in the robustness check |
| `adr` = 0 | 1,959 (1.6%) | Kept for counts; excluded from ADR/revenue analysis |
| `adr` < 0 | 1 | Excluded from ADR/revenue analysis |
| `adr` = 5,400 | 1 | Excluded from ADR/revenue analysis (extreme outlier) |
| Zero nights | 715 | Kept (real records) |
| Zero guests (adults + children + babies = 0) | 180 | Kept (real records) |
| `market_segment` = `Undefined` | 2 | Kept in counts; excluded from segment tests |
| `Non Refund` deposits | 14,587 (12.2%) | 99.4% are cancelled, 97.2% from `PRT`, 88.2% at `City Hotel`; analysed separately |
| Uneven month coverage | July-August seen 3 times | 2016 (the only full year) used as seasonal baseline |

## Columns created in the notebook

| Column | Definition |
|---|---|
| `arrival_date` | Date built from `arrival_date_year`, `arrival_date_month`, `arrival_date_day_of_month` |
| `total_nights` | `stays_in_weekend_nights` + `stays_in_week_nights` |
| `total_guests` | `adults` + `children` + `babies` |
| `lead_time_bin` | `lead_time` grouped into 0-7, 8-30, 31-90, 91-180, 181-365, 365+ days |
| `adr_valid` | True when `0 < adr < 1,000` |
| `room_revenue` | `adr` x `total_nights` (a revenue proxy, not booked revenue) |

## Using the dataset

```python
import pandas as pd

df = pd.read_csv('hotel_bookings.csv')
print(df.shape)                                  # (119390, 32)
print(df['is_canceled'].mean())                  # about 0.37
print(df.groupby('hotel')['is_canceled'].mean())
```

**Things to remember when using this data**

- `is_canceled` counts no-shows as cancellations.
- `reservation_status` and `reservation_status_date` reveal the outcome; leave them out when predicting cancellation.
- `special_requests`, `booking_changes` and parking can be recorded after the booking and may follow the outcome.
- Prices are not tied to a stated currency and `adr` is an average over room and meal types.
- The data has no room capacity, occupancy, payment amounts or booking ID.

## Related documents

- [Main README](../README.md) - results and recommendations
- [Notebook Guide](NOTEBOOK_README.md) - how the analysis is built
- [Chart gallery](../charts/README.md) - analysis of each chart

