# Dimensions

Dimensions split a metric's data by a categorical attribute. Use `fetch_metric_by_dimension` for a single aggregated value per dimension bucket, or `fetch_metric_series_by_dimension` for a time series per bucket.

## Supported Dimensions

| Value                     | User language                                       |
|---------------------------|-----------------------------------------------------|
| `CAMPAIGN`                | "by campaign", "per campaign", "campaign breakdown" |
| `EXPERIMENT`              | "by experiment", "per experiment"                   |
| `OFFER`                   | "by offer", "per offer"                             |
| `MARKET`                  | "by market", "per market", "by country"             |
| `MARKET_GROUP`            | "by market group", "by region"                      |
| `LOCALE`                  | "by locale", "by language"                          |
| `CURRENCY_CODE`           | "by currency"                                       |
| `SHARE_METHOD`            | "by share method", "by channel"                     |
| `GAMING_SCORE_HIGH`       | "by gaming score", "flagged vs unflagged"           |
| `INFLUENCER_CONTENT_TYPE` | "by content type", "by influencer content type"     |
| `SHARE_TYPE`              | "by share type"                                      |
| `SITUATION`               | "by situation", "by promotion point", "by touchpoint"|
| `SMALL_SCREEN`            | "by device", "by screen size", "mobile vs desktop"   |

## Dimension × Metric Guidance

Not every dimension produces meaningful splits for every metric. When a dimension doesn't apply to a metric, the API returns all data collapsed into a single bucket — it does not error.

| Dimension                 | Works well with                                                       |
|---------------------------|-----------------------------------------------------------------------|
| `CAMPAIGN`                | Most metrics — the most common and broadly useful dimension           |
| `EXPERIMENT`              | Conversion-related metrics, A/B test analysis                         |
| `OFFER`                   | Similar to campaign; useful when campaigns contain multiple offers    |
| `MARKET` / `MARKET_GROUP` | Any metric, for merchants operating across multiple countries/regions |
| `LOCALE`                  | Any metric, for multi-language programmes                             |
| `CURRENCY_CODE`           | Revenue metrics — shows revenue in each transacted currency           |
| `SHARE_METHOD`            | Share-related metrics (`share_rate`, shares, referrals)               |
| `GAMING_SCORE_HIGH`       | Order/customer metrics — separates flagged (suspected gaming) traffic |
| `INFLUENCER_CONTENT_TYPE` | Influencer metrics (`influencer_orders`, `influencer_revenue`)        |
| `SHARE_TYPE`              | Share-related metrics (`share_rate`, shares, referrals)               |
| `SITUATION`               | Any metric — splits by promotion point / touchpoint placement         |
| `SMALL_SCREEN`            | Any metric — splits by device screen type (mobile vs desktop)         |

## Selection Rules

1. Match the user's language to a dimension using the table above.
2. If ambiguous (e.g. "by channel" could mean share method or campaign), ask the user to clarify.
3. If the user asks for a dimension that doesn't exist, explain what's available and suggest the closest match.
4. Never expose dimension enum values to the user — use natural names (e.g. "Campaign", not `CAMPAIGN`).
