# Group By

Relevant for `fetch_metric_series` and `fetch_metric_series_by_dimension`. The non-series endpoints (`fetch_metric`, `fetch_metric_by_dimension`) do not accept `group_by`.

## Supported Values

| Value    | Display Name | Notes                                                                     |
|----------|--------------|---------------------------------------------------------------------------|
| `day`    | Day          | Data points per day. Default for short periods (7d, 30d)                  |
| `week`   | Week         | Data points per week. Default for medium periods (60d, 3 months)          |
| `month`  | Month        | Data points per month. Default for long periods (6mo+, all time)          |
| `hour`   | Hour         | Data points per hour. Most granular option                                |
| `period` | Period       | Aggregates the entire time period into a single data point                |
| `offer`  | Offer        | Splits data by offer/experiment rather than time. For campaign breakdowns |

## Auto-Inference Rules

If the user doesn't specify a granularity, select `group_by` based on the time period:

| group_by | Time Periods                                                                                      |
|----------|---------------------------------------------------------------------------------------------------|
| `day`    | `LAST-7-DAYS`, `LAST-30-DAYS`, `CUSTOM` (short range)                                             |
| `week`   | `LAST-60-DAYS`, `LAST-3-MONTHS`, `LAST-3-WHOLE-MONTHS`                                            |
| `month`  | `LAST-6-MONTHS`, `LAST-12-MONTHS`, `LAST-6-WHOLE-MONTHS`, `LAST-12-WHOLE-MONTHS`, `LAST-ALL-TIME` |

For time periods not listed (e.g. `LAST-WEEK`, `LAST-8-WEEKS`, `MONTH-TO-DATE`, anchored variants like `7-DAYS`), infer from the span: ≤30 days → `day`, ≤3 months → `week`, otherwise → `month`.
