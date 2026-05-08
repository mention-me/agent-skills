# Time Periods

Default period: `LAST-30-DAYS` (if the user doesn't specify, use this and state it in your response).

## Full Catalog

| Period Key             | Display Name              | Required Dates   | Description                                                              |
|------------------------|---------------------------|------------------|--------------------------------------------------------------------------|
| `LAST-7-DAYS`          | Last 7 days               | None             | 7 days ending yesterday                                                  |
| `7-DAYS`               | 7 days                    | End date         | 7-day window ending at the given end date                                |
| `LAST-30-DAYS`         | Last 30 days              | None             | 30 days ending yesterday **(default)**                                   |
| `30-DAYS`              | 30 days                   | End date         | 30-day window ending at the given end date                               |
| `LAST-60-DAYS`         | Last 60 days              | None             | 60 days ending yesterday                                                 |
| `60-DAYS`              | 60 days                   | End date         | 60-day window ending at the given end date                               |
| `LAST-WEEK`            | Last whole week           | None             | Most recent complete Mon–Sun calendar week                               |
| `WEEK`                 | Whole week                | End date         | Calendar week ending at the given end date                               |
| `LAST-8-WEEKS`         | Last 8 whole weeks        | None             | 8 most recent complete calendar weeks                                    |
| `8-WEEKS`              | 8 whole weeks             | End date         | 8 calendar weeks ending at the given end date                            |
| `LAST-12-WEEKS`        | Last 12 whole weeks       | None             | 12 most recent complete calendar weeks                                   |
| `12-WEEKS`             | 12 whole weeks            | End date         | 12 calendar weeks ending at the given end date                           |
| `LAST-26-WEEKS`        | Last 26 whole weeks       | None             | 26 most recent complete calendar weeks (~6 months)                       |
| `26-WEEKS`             | 26 whole weeks            | End date         | 26 calendar weeks ending at the given end date                           |
| `LAST-52-WEEKS`        | Last 52 whole weeks       | None             | 52 most recent complete calendar weeks (~1 year)                         |
| `MONTH-TO-DATE`        | Month to date             | None             | 1st of current month through yesterday                                   |
| `LAST-MONTH`           | Last month                | None             | ~30 days ending yesterday (not calendar-aligned)                         |
| `LAST-3-MONTHS`        | Last 3 months             | None             | ~90 days ending yesterday (not calendar-aligned)                         |
| `LAST-6-MONTHS`        | Last 6 months             | None             | ~180 days ending yesterday (not calendar-aligned)                        |
| `LAST-12-MONTHS`       | Last 12 months            | None             | ~365 days ending yesterday (not calendar-aligned)                        |
| `CALENDAR-MONTH`       | Last whole calendar month | None             | Last complete calendar month (1st to last day)                           |
| `3-MONTHS`             | 3 whole months            | End date         | 3 complete calendar months ending at the given end date                  |
| `6-MONTHS`             | 6 whole months            | End date         | 6 complete calendar months ending at the given end date                  |
| `12-MONTHS`            | 12 whole months           | End date         | 12 complete calendar months ending at the given end date                 |
| `13-MONTHS`            | 13 whole months           | End date         | 13 complete calendar months ending at the given end date                 |
| `24-MONTHS`            | 24 whole months           | End date         | 24 complete calendar months ending at the given end date                 |
| `LAST-3-WHOLE-MONTHS`  | Last 3 whole months       | None             | 3 most recent complete calendar months                                   |
| `LAST-6-WHOLE-MONTHS`  | Last 6 whole months       | None             | 6 most recent complete calendar months                                   |
| `LAST-12-WHOLE-MONTHS` | Last 12 whole months      | None             | 12 most recent complete calendar months                                  |
| `LAST-ALL-TIME`        | All time                  | None             | All data from client start date through yesterday. No comparison support |
| `CUSTOM`               | Custom date range         | Start + End date | User-specified range. Both `startDate` and `endDate` required            |

## Key Distinctions

### LAST-X vs X (e.g. `LAST-7-DAYS` vs `7-DAYS`)

`LAST-` periods always end yesterday and require no dates. The version without `LAST-` is a relative window that requires an end date — the start is computed by subtracting the duration from that end date. If the end date happens to be yesterday, they produce identical ranges.

### LAST-X-MONTHS vs LAST-X-WHOLE-MONTHS vs X-MONTHS

Three variants exist for monthly periods:

- **`LAST-3-MONTHS`** — counts back exactly 3 months of days from yesterday (e.g. Mar 13 → Dec 14). Not calendar-aligned.
- **`LAST-3-WHOLE-MONTHS`** — the 3 most recent complete calendar months (e.g. Dec 1 → Feb 28). Always starts/ends on month boundaries. No dates needed.
- **`3-MONTHS`** — 3 complete calendar months ending at a specified end date. Requires an end date.

### No-comparison periods

`LAST-ALL-TIME` does not support comparison (there is no "previous all time").
