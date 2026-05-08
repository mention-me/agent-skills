---
name: mention-me-reporting
description: Trigger on queries about merchant/brand reporting metrics. This includes explicit mentions ("Mention Me metrics", "referral dashboard", "MM reporting") and implicit queries ("how many new customers did Huel get", "show me referral performance for ...", "what's the NPS score for ...", "new customer revenue trend", "share rate for ..."). The key signal is a question about a merchant/brand's performance metrics (referrals, new customers, revenue, NPS, shares, influencers, conversions, etc.) — even without mentioning "Mention Me".
---

# Mention Me Reporting Skill

You are querying Mention Me's reporting API on behalf of the user. Follow this workflow precisely.

## Step 1: Resolve the Merchant

Call `list_organizations` to get all orgs the user has access to, then match by name from the returned list.

- If no match: tell the user the merchant wasn't found. Do NOT guess or pick a partial match without confirming.
- If multiple partial matches: present the options and ask the user to pick.
- Never expose `org_id` values to the user.

## Step 2: Select the Right Metric

The metrics catalog contains both **Mention Me-scoped** and **Whole Business** metrics. Selecting the right scope is
critical.

### Scope Selection Rules

| User intent                                                                     | Scope                         | Example metrics                                                                                                                                            |
|---------------------------------------------------------------------------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| "Mention Me performance", "referral results", "how many customers did MM drive" | Mention Me                    | `new_customers`, `new_customer_revenue`, `influencer_orders`, `influencer_revenue`, `referral_orders`, `total_referral_revenue`, `share_rate`, `nps_score` |
| "total revenue", "all orders", "overall business"                               | Whole Business                | `orders`, `all_customer_revenue`                                                                                                                           |
| "what % of revenue comes from Mention Me", "share of orders"                    | Both scopes (calculate ratio) | `new_customer_revenue` / `all_customer_revenue`                                                                                                            |

- If the user's intent is ambiguous between scopes, present the options and let them choose.
- When presenting Whole Business metrics, always label them clearly as **"Whole Business"** so the user knows these
  aren't Mention Me-specific.

### Discovering Available Metrics

Read the `mentionme://metrics` resource to get the full catalog of available metrics, their display names, descriptions, and scopes. Use this to identify the correct metric key for the user's request. Never show metric keys to the user — only display names.

### Time Period

Read `references/time-periods.md` for the full list of supported time periods, their semantics, and required parameters.

If the user doesn't specify a time period, default to `LAST-30-DAYS` and state this in your response.

## Step 3: Fetch the Data

All four fetch tools accept these optional params:

- `campaign_id` — filter results to a specific campaign
- `start_date`, `end_date` — required for certain time periods (see `references/time-periods.md` for which periods need dates)

### Single value → `fetch_metric`

Use for questions like "how many X" or "what was the Y".

Required params: `metric`, `time_period`, `org_id`
Optional params: `campaign_id`, `start_date`, `end_date`, `compare_to`

### Time series → `fetch_metric_series`

Use for questions about trends, "over time", "month by month", or any request spanning multiple periods.

Required params: `metric`, `time_period`, `org_id`, `group_by`
Optional params: `campaign_id`, `start_date`, `end_date`, `compare_to`

Read `references/group-by.md` for supported `group_by` values and auto-inference rules. If the user doesn't specify a granularity, use the auto-inference mapping.

### Single value by dimension → `fetch_metric_by_dimension`

Use for questions like "X by campaign", "revenue per market", "break down new customers by locale".

Required params: `metric`, `time_period`, `org_id`, `dimension`
Optional params: `campaign_id`, `start_date`, `end_date`, `compare_to`

Read `references/dimensions.md` for supported dimensions and selection rules.

### Time series by dimension → `fetch_metric_series_by_dimension`

Use for questions like "revenue by campaign over time", "monthly new customers per market", "trend by share method".

Required params: `metric`, `time_period`, `org_id`, `group_by`, `dimension`
Optional params: `campaign_id`, `start_date`, `end_date`, `compare_to`

Read `references/dimensions.md` for dimension selection and `references/group-by.md` for `group_by` values.

### Comparisons (e.g. "vs last month", "compared to previous period")

All four fetch tools accept a `compare_to` parameter that returns comparison data in the same call. Read `references/compare-to.md` for supported values and limitations.

### Ratio calculations (e.g. "what % of total revenue comes from MM")

Fetch both the Mention Me-scoped metric and the Whole Business metric for the same period, then calculate the ratio.

## Step 4: Format & Present Results

### Rules (MANDATORY)

1. **Always use the metric's display name** as returned by the API (e.g. "New Referred Customers", not `new_customers`).
2. **Always include the time period** in your output (e.g. "Last Month", "Last 3 Months").
3. **Never expose internal identifiers** — no org IDs, no metric keys.
4. **Label scope clearly** — if showing a Whole Business metric, prefix with "Whole Business:".
5. **Format numbers properly:**
    - Currency: use the appropriate symbol (£, $, €) with commas (e.g. £1,234,567)
    - Percentages: one decimal place with % (e.g. 12.3%)
    - Counts: integers with commas (e.g. 5,583)

### Single metric

> **New Referred Customers** (Last Month): **5,583**

### Comparison

> **New Referred Customers**
> - Last Month: **5,583**
> - Previous Month: **4,920**
> - Change: **+663 (+13.5%)**

### Time series

Provide a brief 1–2 sentence summary of the trend, then a markdown table:

> New Referred Customers trended downward over the last 3 months, with January as the strongest month.
>
> | Month         | New Referred Customers |
> |---------------|------------------------|
> | January 2026  | 6,015                  |
> | February 2026 | 5,484                  |
> | March 2026    | 1,203                  |

### Single metric by dimension

Table with dimension values as rows:

> **New Referred Customers by Campaign** (Last 3 Months):
>
> | Campaign       | New Referred Customers |
> |----------------|------------------------|
> | Spring Sale    | 2,341                  |
> | Refer a Friend | 1,892                  |
> | Summer 2026    | 1,350                  |

### Time series by dimension

For low cardinality (≤5 dimension values), use dimension columns:

> New Customer Revenue by Market trended upward across all markets, with the UK leading.
>
> | Month         | UK        | US       | DE       |
> |---------------|-----------|----------|----------|
> | January 2026  | £120,000  | $95,000  | €45,000  |
> | February 2026 | £134,000  | $102,000 | €51,000  |
> | March 2026    | £141,000  | $110,000 | €48,000  |

For high cardinality (>5 values), use separate tables per dimension value or show only the top N with a note about the remainder.

### Multiple metrics

Lead with a summary sentence, then a table:

> | Metric                 | Value (Last Month) |
> |------------------------|--------------------|
> | New Referred Customers | 5,583              |
> | New Customer Revenue   | £234,567           |
> | Share Rate             | 12.3%              |

### Ratio / share-of calculations

> **Mention Me's Share of Total Revenue** (Last Month): **18.4%**
> - New Customer Revenue (Mention Me): £234,567
> - All Customer Revenue (Whole Business): £1,274,000

## Guardrails

- If the merchant is not found, say so clearly. Do not guess.
- If the requested metric doesn't exist, explain what's available and suggest the closest match.
- Do not mix Mention Me and Whole Business metrics without explicitly labeling each.
- If a fetch returns an error, report it plainly — do not fabricate data.
- When the user's request requires multiple API calls (comparisons, ratios), make them in parallel where possible.
