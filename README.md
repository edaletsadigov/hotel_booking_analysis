# Hotel Booking Analysis: Seasonality and Cancellation Drivers

An analysis of **119,390 hotel bookings** (City Hotel and Resort Hotel, July 2015 - August 2017) that answers three revenue-management questions: *when is demand highest, who cancels and why, and what should the hotels do about it?*

**Tools:** Python, pandas, NumPy, matplotlib, seaborn, SciPy | **Format:** Jupyter notebook with 9 charts, statistical tests and business recommendations

## Results at a glance

| Metric | Value | Context |
|---|---|---|
| Bookings analysed | **119,390** | 66.5% City Hotel, 33.5% Resort Hotel |
| Overall cancellation rate | **37.0%** | City Hotel 41.7% vs Resort Hotel 27.8% |
| Cancellation rate without `Non Refund` deposits | **28.4%** | City-Resort gap shrinks from 14.0 pp to 5.8 pp |
| `Non Refund` bookings | **12.2%** of all | 99.4% cancelled, 97.2% from Portugal, 88.2% at City Hotel |
| Median lead time, cancelled vs kept | **113 vs 45 days** | Flexible bookings: cancellation rises from 9.2% (0-7 days) to 39.8% (181-365 days) |
| `Online TA` vs `Direct` cancellation | **36.7% vs 15.3%** | Almost identical ADR on kept bookings (about 115 vs 116) |
| Resort ADR range | **50 - 184** | City ADR range: 84 - 123 |
| Potential room revenue in cancelled bookings | **43.0% City / 33.5% Resort** | Flexible-deposit part is about 31% at both hotels |

## Documentation map

| Document | What you will find |
|---|---|
| [`hotel_bookings.ipynb`](hotel_bookings.ipynb) | The full analysis with code, outputs and charts |
| [Notebook Guide](docs/NOTEBOOK_README.md) | Libraries used and why, section-by-section structure, cleaning decisions, how to run |
| [Dataset Guide](docs/DATASET_README.md) | Rows, columns, every column explained, data quality issues |
| [Chart Gallery](charts/README.md) | All 9 charts with a written analysis of each |

## Repository structure

```text
hotel-booking-analysis/
├── README.md                      <- you are here
├── hotel_bookings.ipynb           <- analysis notebook (outputs included)
├── hotel_bookings.csv             <- dataset (download from Kaggle, see below)
├── charts/
│   ├── README.md                  <- chart gallery and analysis
│   └── 01 ... 09 *.png            <- all charts
└── docs/
    ├── NOTEBOOK_README.md
    └── DATASET_README.md
```

## What the analysis covers

1. **Data cleaning and validation** - missing values (`country`, `agent`, `company`, `children`), duplicates, odd `adr` values, a proper `arrival_date` built from three columns, and a **coverage check** (July-August appear three times in the data, other months twice).
2. **Pivot tables and heatmaps** - bookings by month and hotel.
3. **Cancellation analysis** - monthly rate per hotel, lead time of cancelled vs kept bookings, Top 10 countries, market segments, cancellation drivers.
4. **Bonus** - average daily rate (ADR) by month and hotel on a dual-axis chart; lowest-cancellation segment; revenue exposure.
5. **Statistics** - Mann-Whitney U, chi-square with Cramer's V, Spearman, and a robustness check that removes `Non Refund` deposits, duplicates and Portugal.

## Findings and business insights

### Demand: seasonality is weaker than raw totals suggest

![Corrected booking heatmaps](charts/02_booking_heatmap_corrected.png)

Raw counts say **August** is the peak (13,877 bookings, [chart 1](charts/README.md#1-booking-heatmap---raw-counts)), but July and August appear **three times** in the data and every other month only twice. After correcting for this (chart above), the busiest month per observed year is **May**, and in 2016, the only complete year, it is **October (6,203 bookings)**. **January is the weakest month in every view.** `City Hotel` has more bookings than `Resort Hotel` in all 12 months. Details: [charts 1-2](charts/README.md#2-booking-heatmaps---corrected-for-uneven-coverage).

---

### Insight 1 - Separate non-refundable bookings from operational demand

![Cancellation rate by deposit type and other drivers](charts/09_cancellation_drivers.png)

**FACT**
`Non Refund` bookings are 12.2% of all reservations and **99.4% of them are cancelled**. They are concentrated at `City Hotel` (88.2%) and among Portuguese guests (97.2%), and they make up about a third of all cancellations. Without them the overall rate falls from 37.0% to **28.4%** and the City-Resort gap from 14.0 to **5.8 pp**. Deposit type has the strongest association with cancellation of all variables tested (Cramer's V = 0.48).

![Top 10 countries](charts/05_top10_countries.png)

Portugal (`PRT`) is 40.7% of bookings with a 56.6% cancellation rate, but this is largely the `Non Refund` effect: `PRT` bookings with no deposit cancel at 38.9% (other countries: 23.2%).

![Revenue exposure](charts/08_revenue_exposure.png)

**BUSINESS IMPLICATION**
Cancellation status alone does not show a financial loss. If deposits were collected and retained, the impact differs sharply from unpaid reservations. If they were not, these bookings block inventory without protecting revenue. Excluding `Non Refund` deposits, flexible-deposit cancellations hold about **31% of potential room revenue at both hotels**, so the hotel gap is mostly a `Non Refund` effect.

**RECOMMENDED ACTIONS**
- Report `Non Refund` bookings separately in demand forecasts, pickup reports and cancellation dashboards.
- Verify deposit collection, refund status and retained revenue before treating these reservations as lost demand.
- Review inventory-release rules and set clear release deadlines where the deposit does not protect revenue.
- Compare outcomes across deposit policies before changing the product.

**CONFIDENCE:** Medium. The patterns are clear, but deposit amounts, payment collection and retained revenue are not in the data.

---

### Insight 2 - Use lead time to monitor cancellation risk

![Lead time analysis](charts/04_lead_time_analysis.png)

**FACT**
Cancelled bookings have a median lead time of **113 days vs 45 days** for kept bookings. Among flexible-deposit bookings the cancellation rate rises from **9.2%** within seven days to **about 35-40%** for longer lead times; bookings made 91-365 days ahead are 40.2% of the dataset. The extreme 67.7% rate at 365+ days is produced by non-refundable group bookings (it falls to 20.7% without them).

**BUSINESS IMPLICATION**
Long lead times give an early flag for reservations that need closer monitoring. The data shows *whether* a booking cancelled, not *when*, so it cannot size an overbooking buffer by itself.

**RECOMMENDED ACTIONS**
- Segment bookings by lead-time bucket and deposit policy.
- Introduce reconfirmation for bookings made 91 or more days before arrival.
- Track cancellation rates and cancellation timing separately.
- Estimate overbooking levels from historical cancellations, timing, room capacity and the cost of turning away guests; backtest before use.

**CONFIDENCE:** High for the observed association; medium for operational recommendations until timing and capacity data are analysed.

---

### Insight 3 - Evaluate opportunities to increase direct bookings

![Market segments](charts/06_market_segments.png)

**FACT**
`Online TA` is **47.3% of bookings and 46.9% of cancellations** (rate 36.7%). `Direct` cancels at **15.3%** - the lowest among segments with at least 1,000 bookings - while earning about the same ADR on kept bookings (116.2 vs 115.0). `Groups` cancel most (61.1%), but only 28.1% without `Non Refund` deposits, so on flexible bookings `Online TA` is the highest-risk segment.

A hypothetical shift of 1% of `Online TA` bookings (about 565 reservations) to `Direct` would correspond to roughly **120 fewer cancellations** if those guests behaved like current direct guests.

**BUSINESS IMPLICATION**
Direct bookings may be more stable, but customer profile, booking conditions and acquisition cost can explain part of the gap. The benefit is an estimate, not a guaranteed outcome.

**RECOMMENDED ACTIONS**
- Test member-only rates, loyalty benefits and direct-booking incentives.
- Compare net revenue after commissions, marketing cost, discounts and cancellations across channels.
- Review cancellation policies and booking conditions by channel.
- Run a controlled or phased campaign to measure incremental direct bookings and their real cancellation outcomes.

**CONFIDENCE:** Medium. The association is clear; the causal effect of moving guests between channels is not established.

---

### Insight 4 - Align Resort pricing with seasonal demand

![Monthly cancellation rate by hotel](charts/03_monthly_cancellation_rate.png)

![ADR by month and hotel](charts/07_adr_dual_axis.png)

**FACT**
`Resort Hotel` is strongly seasonal: ADR reaches about **153 in July and 184 in August** while monthly cancellation rises to **31.4-33.4%**; in January-March and November ADR falls to about **50-58** and cancellation to 14.8-25.6%. `City Hotel` is flat: ADR stays in a narrow 84-123 range and cancellation at 36.9-46.3% all year (peak: April, 46.3%).

**BUSINESS IMPLICATION**
Resort's summer combines the highest room rates with the highest cancellation exposure. Weaker months are an opportunity to stimulate bookings. `City Hotel` may benefit more from targeted demand generation and better cancellation monitoring.

**RECOMMENDED ACTIONS**
- **Resort, peak season:** evaluate first-night deposits, advance payment or differentiated cancellation terms.
- **Resort, off-season:** test promotions, packages and length-of-stay incentives.
- **City, demand generation:** test targeted offers in January and February, subject to verified occupancy and demand forecasts.
- **City, monitoring:** investigate April's elevated cancellation rate (booking mix, policy or timing).
- Judge every policy change by net revenue, conversion and guest experience, not by cancellation rate alone.

**CONFIDENCE:** Medium. The seasonal patterns are useful, but the data covers a short period and has no room capacity or occupancy.

## Recommendations summary

| Priority | Area | Recommended action | Expected benefit |
|---|---|---|---|
| High | Booking policy | Audit `Non Refund` reservations, deposits and inventory-release rules | More reliable demand and revenue reporting |
| High | Forecasting | Report demand with and without `Non Refund` bookings | Less distortion in demand forecasts |
| High | Cancellation risk | Segment cancellation rates by lead time and deposit type | Earlier identification of high-risk bookings |
| Medium | Distribution | Test initiatives to increase profitable `Direct` bookings | Potentially lower cancellation exposure and distribution cost |
| Medium | Pricing | Evaluate seasonal pricing and cancellation policies at `Resort Hotel` | Better alignment of price and demand |
| Medium | Data quality | Investigate duplicate records and unusual booking clusters | More reliable performance metrics |

## What holds up and what does not (robustness)

| Scenario | Rows | Cancellation rate | City | Resort | City - Resort gap |
|---|---|---|---|---|---|
| Full dataset | 119,390 | 37.0% | 41.7% | 27.8% | 14.0 pp |
| Without `Non Refund` deposits | 104,803 | 28.4% | 30.5% | 24.7% | 5.8 pp |
| Without duplicate rows | 87,396 | 27.5% | 30.0% | 23.5% | 6.6 pp |
| Without Portugal (`PRT`) | 70,800 | 23.6% | 26.9% | 16.4% | 10.5 pp |

**Direction survives every check** (City cancels more than Resort; cancelled bookings are made earlier). **The size of the headline number does not** - always quote the 37.0% rate together with the `Non Refund` share.

## Limitations

- **Coverage:** July 2015 - August 2017; only 2016 is a complete year and July-August appear three times.
- **No capacity or occupancy data:** bookings by arrival month are demand signals, not occupancy.
- **Duplicates:** 26.8% of rows are exact duplicates and cannot be verified without a booking ID.
- **`Non Refund` cluster:** 97% from one country and mostly one hotel; it may reflect a real policy or a data/process artefact. Deposit amounts are unknown.
- **ADR and revenue are proxies** (`adr` x nights), with mixed room types and meal plans; the currency is not stated.
- **Post-booking fields** (special requests, booking changes, parking) can follow the outcome and are read as indicators, not causes.
- **Observational data:** every relationship is an association. Testing a cause needs a controlled experiment, for example randomising deposit terms for a subset of bookings.
- **Next step:** `reservation_status_date` can show how many days before arrival bookings are cancelled, turning Insight 2 from a rate into a timing model.

## How to reproduce

1. Download `hotel_bookings.csv` from [Kaggle](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand) and place it next to the notebook.
2. Install the requirements: `pip install numpy pandas matplotlib seaborn scipy jupyter`
3. Open `hotel_bookings.ipynb` and choose **Kernel > Restart & Run All**.

See the [Notebook Guide](docs/NOTEBOOK_README.md) for versions and design decisions.

## Author

**Ədalət Sadıqov** - data analyst | [GitHub](https://github.com/edaletsadigov)
