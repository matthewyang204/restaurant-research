# San Francisco Restaurant Inspection Findings

## Scope and method

- **Research period:** January 1, 2025 through October 28, 2026, inclusive. This is the period specified in the request; it is longer than 12 months.
- **Population:** The 27 restaurants in [`restaurants.txt`](./restaurants.txt), matched against San Francisco's [Health Inspection Scores (2024–Present)](https://data.sf.gov/Health-and-Social-Services/Health-Inspection-Scores-2024-Present-/tvy3-wexg) dataset.
- **Records found:** 66 inspection rows matched 24 restaurant addresses. Three listed restaurants did not have a matching address record in the queried dataset. These are treated as **missing**, not as having zero violations.
- **Restaurant comparison unit:** To avoid treating repeat visits as independent restaurants, each restaurant contributes its single highest reported inspection violation count in the period. If counts tie, the higher-severity category and then the latest date breaks the tie. A reported count of zero is kept when a matching inspection record exists.
- **Inspection record details:** All matched records, violation-count fields, published violation narratives, status, severity score, and direct source links are in [`inspection_records.csv`](./inspection_records.csv). Restaurant-by-restaurant summaries are in [`restaurant-files/`](./restaurant-files/). The restaurant-level comparison table is [`restaurant_metrics.csv`](./restaurant_metrics.csv).
- **Unweighted files:** [`unweighted_inspection_counts.csv`](./unweighted_inspection_counts.csv) contains all 66 raw per-inspection counts without severity multipliers. [`unweighted_peak_counts.csv`](./unweighted_peak_counts.csv) has the 27 restaurant-level peak counts plus the Grant Place example.

## Severity weighting

The current dataset provides a violation count and overall facility status, but does not supply a severity category for each violation. For a transparent comparison, each peak inspection is assigned a single severity weight based on the published violation narrative and status:

| Category | Weight | Interpretation |
|---|---:|---|
| High | 3 | Closure status or a direct food-safety hazard is explicitly cited, such as pests/vermin, unsafe food temperature or handling, contamination, inadequate sanitizing/handwashing, or a chemical/poisoning concern. |
| Moderate | 2 | Conditional-pass status or material sanitation, food-handling, facility, equipment, or management deficiencies without a high-risk trigger. |
| Low | 1 | No high/moderate trigger in the matched record. |

**Weighted count = reported violation count × severity weight.** This is a comparative research index, not an official San Francisco rating or a claim that every violation in a record has identical risk. City status labels (Pass, Conditional Pass, Closure) are preserved separately in the data.

## Unweighted analysis

This version uses the inspection's **reported violation count as-is**. No severity multiplier is applied. Each matched restaurant contributes its peak inspection count for the period; the Grant Place example is included in a separate second run.

| Dataset | n | Mean | Median | Mode(s) | Population standard deviation |
|---|---:|---:|---:|---|---:|
| 24 matched restaurants — peak reported violations | 24 | 6.458 | 7 | 4, 7, 8, 10 | 3.329 |
| Same 24 plus Grant Place example (8 violations) | 25 | 6.520 | 7 | 8 | 3.276 |

The raw per-restaurant observations, including missing-value indicators, are in [`unweighted_peak_counts.csv`](./unweighted_peak_counts.csv); the full per-inspection raw counts are in [`unweighted_inspection_counts.csv`](./unweighted_inspection_counts.csv). Unweighted counts and narratives are also shown for every visit in the individual restaurant files.

## Severity-weighted analysis

Applying the severity rubric above, each restaurant contributes its peak reported count multiplied by the applicable severity weight. The standard deviation below is the **population** standard deviation. Calculations were run with `dproc`; values are rounded to three decimal places. Modes list every tied value.

| Dataset | n | Mean | Median | Mode(s) | Population standard deviation |
|---|---:|---:|---:|---|---:|
| 24 matched restaurants — peak weighted count | 24 | 19.333 | 21 | 12, 21, 24, 30 | 10.057 |
| Same 24 plus Grant Place example — weighted count | 25 | 19.520 | 21 | 24 | 9.896 |

The data for both comparison runs is in [`comparison_with_example.csv`](./comparison_with_example.csv).

## Comparison with the Grant Place example

The example reports **8 violations** at Grant Place Restaurant on March 25, 2025. The official city dataset also has an eight-violation **Closure** record at the supplied address for that date. Under the severity rubric here, that is High (weight 3), or a weighted count of **24**.

Compared with the 24 matched restaurants, the example:

- is one violation above the unweighted mean (8 versus 6.458) and the median (7), but below the observed maximum (13);
- has a weighted count of 24, above the weighted mean (19.333) and median (21), and tied with three restaurants in the base dataset;
- in the **unweighted** run, raises the mean from 6.458 to 6.520, leaves the median at 7, changes the mode to 8, and lowers the population SD from 3.329 to 3.276;
- in the **weighted** run, raises the mean from 19.333 to 19.520, leaves the median at 21, changes the mode to 24, and lowers the population SD from 10.057 to 9.896.

The example's text and linked news sources describe pest evidence, food-temperature/handling issues, sanitation, and facility problems. The primary numerical comparison uses the city's recorded violation count and official status rather than trying to re-count individual claims in news coverage.

## Findings and caveats

- The largest peak count in the matched sample is **13** at Harborview Restaurant & Bar. The lowest nonzero peak count is **1**; one matched restaurant, Spicy & Cloud Restaurant, has a recorded peak of **0**. That is an inspection result, not a missing value.
- The weighted distribution is more spread out than the raw counts (population SD 10.057 versus 3.329), because the multiplier separates direct food-safety hazards and closure actions from less serious findings.
- A facility's peak inspection count is not a measure of its typical performance or of how long a problem persisted. It is the largest count in the specified window, selected for a like-for-like restaurant comparison.
- Multiple inspections may be related follow-ups, repeat violations, or separate visits. The 66 individual rows are provided for review; the statistics use one peak per matched restaurant and do not sum repeat visits.
- Three no-match cases are listed in the individual files: R & G Lounge, Eight Tables by George Chen, and San Wang Restaurant. The missing result could reflect dataset coverage, address/permit differences, or no inspection in this extract; it does not establish that no inspection or violation occurred.
- Google was used to find recent reporting, and articles are cited where they provided directly relevant details. News reporting is supplemental because it may summarize several observations without a standardized total count. For example, a July 10, 2025 WhatNow story about Hunan House reports a July 9 closure inspection and describes pests and food-safety issues; the queried dataset extract has no matching July 9 row for the supplied address. This is recorded as supplemental context in the Hunan House file and is not double-counted in the statistics.
- No inspection PDFs were downloaded for this research. The city dataset's published violation narratives and targeted article reporting were used instead.

## Sources

- [DataSF: Health Inspection Scores (2024–Present)](https://data.sf.gov/Health-and-Social-Services/Health-Inspection-Scores-2024-Present-/tvy3-wexg/about_data) — official inspection data, updated monthly.
- [San Francisco restaurant inspection lookup](https://inspections.myhealthdepartment.com/san-francisco) — official public inspection lookup.
- [WhatNow: Hunan House ordered to close due to infestation and facility issues](https://whatnow.com/san-francisco/local-news/a-chinese-restaurant-in-sf-was-ordered-to-close-due-to-infestation-and-facility-issues/) — supplemental report on the July 2025 inspection; the article also links to the inspection report.
- [WhatNow: Grant Place Restaurant closed during health inspection](https://whatnow.com/san-francisco/local-news/a-cantonese-restaurant-in-san-francisco-was-ordered-to-close-during-health-inspection/) and [follow-up report](https://whatnow.com/san-francisco/local-news/san-francisco-cantonese-suggested-to-close-after-routine-inspection/) — the news sources supplied with the example.
