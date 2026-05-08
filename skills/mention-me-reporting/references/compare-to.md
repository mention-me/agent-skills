# Compare To

Applies to all four fetch tools. Returns comparison data in the same API call — no need to fetch twice.

## Supported Values

| Value         | Display Name       | How It Works                                                                                                                                                                                                                    |
|---------------|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `NONE`        | No comparison      | No comparison data returned. **Default**                                                                                                                                                                                        |
| `LAST-YEAR`   | Prior year         | Subtracts 1 year from both start and end dates of the current period                                                                                                                                                            |
| `LAST-PERIOD` | Prior period       | Immediately preceding period of the same duration. E.g. if current period is 30 days, comparison is the 30 days before that. For whole-month periods, uses the equivalent whole-month period ending just before the current one |
| `LAST-MONTH`  | Prior month        | Shifts the current period back by 1 month. For whole-month periods (e.g. `CALENDAR-MONTH`, `3-MONTHS`), snaps to the prior whole calendar month(s). For non-whole periods, subtracts ~31 days                                   |
| `LAST-WEEK`   | Prior week         | Subtracts 7 days from both start and end dates                                                                                                                                                                                  |
| `CATEGORY`    | Industry benchmark | Compares against other merchants in the same business category. Removes merchant ID filter and queries category-wide data. Requires merchant to have a category assigned                                                        |

## Mapping User Language → `compare_to`

| User says                                          | Value         |
|----------------------------------------------------|---------------|
| "vs last year", "year over year", "YoY"            | `LAST-YEAR`   |
| "vs previous period", "compared to before"         | `LAST-PERIOD` |
| "vs last month", "month over month", "MoM"         | `LAST-MONTH`  |
| "vs last week", "week over week", "WoW"            | `LAST-WEEK`   |
| "vs industry", "benchmark", "compared to category" | `CATEGORY`    |

## Limitations

- **No comparison support**: `LAST-ALL-TIME` — prior-period comparison returns empty (duration is undefined).
- **`CUSTOM`**: prior-period comparison works by mirroring the exact same duration immediately before the selected range.
- **Insufficient history**: the system validates whether the merchant has enough historical data. If the merchant's start date falls after the comparison period's start, comparison is disabled.

## CATEGORY Benchmarking

Compares a merchant's metric against the median of peers in the same business category.

### Eligible Metrics

Only metrics with `benchmarkable: true` in the `mentionme://metrics` resource support category benchmarking. These are percentage/ratio metrics representing referral funnel conversion rates. Always check the `benchmarkable` field rather than hardcoding a list — it is the source of truth.

### Time Period Restriction

Only whole-month periods are valid:

- `LAST-3-WHOLE-MONTHS`
- `LAST-6-WHOLE-MONTHS`
- `LAST-12-WHOLE-MONTHS`

Other time periods will return empty comparison data.

### Category Requirement

The merchant must belong to a business category with enough peers to produce a meaningful benchmark. If the merchant has no category assigned or the category has insufficient data, comparison will be empty.

### Methodology

Uses **median** values across the category, not averages. This reduces the impact of outliers.
