# Company Scoring Methodology [![License: CC BY 4.0](https://img.shields.io/badge/License-CC_BY_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/) [![Live Demo](https://img.shields.io/badge/Live_Demo-244M+_companies-blue)](https://score.get-scala.com)

> An open methodology for scoring business health, reliability, and financial risk. Used by [S.C.A.L.A. Score](https://score.get-scala.com) to rate 244M+ companies across 40+ countries.

## Overview

Most business credit scoring is a black box. Dun & Bradstreet, Moody's, and CreditSafe charge hundreds of dollars per report and don't explain how scores are calculated.

This document describes a transparent, reproducible methodology for scoring companies based on publicly available data. The methodology is used in production at [score.get-scala.com](https://score.get-scala.com), where anyone can look up any company for free.

## Score Components

The S.C.A.L.A. Score ranges from **0 to 100** and is composed of five weighted pillars:

### 1. Stability (25%)

How long has the company been in business? Is it actively operating?

| Factor | Weight | Scoring |
|--------|--------|---------|
| Company age | 10% | 0-2yr: 20, 2-5yr: 50, 5-10yr: 75, 10+yr: 100 |
| Active status | 10% | Active: 100, Dormant: 30, Dissolved: 0 |
| Legal form consistency | 5% | No changes: 100, 1 change: 70, 2+: 40 |

**Data sources**: Business registries, GLEIF, OpenCorporates

### 2. Capitalization (20%)

Does the company have adequate financial resources?

| Factor | Weight | Scoring |
|--------|--------|---------|
| Share capital | 8% | Scaled by country median |
| Revenue trend (3yr) | 7% | Growth: 100, Stable: 60, Declining: 20 |
| Assets/liabilities ratio | 5% | >2: 100, 1-2: 70, <1: 30 |

**Data sources**: Annual filings, financial registries, XBRL data

### 3. Activity (20%)

Is the company actively doing business?

| Factor | Weight | Scoring |
|--------|--------|---------|
| Recent filings | 8% | Filed this year: 100, Last year: 70, Older: 20 |
| Web presence | 4% | Website + socials: 100, Website only: 70, None: 20 |
| Employee count trend | 4% | Growing: 100, Stable: 70, Shrinking: 30 |
| Public procurement | 4% | Won tenders: +20 bonus |

**Data sources**: Registry filings, web scraping, EU TED

### 4. Legal (20%)

Are there any legal red flags?

| Factor | Weight | Scoring |
|--------|--------|---------|
| No sanctions matches | 8% | Clean: 100, Match: 0 |
| No adverse media | 4% | Clean: 100, Issues: -30 |
| Director bankruptcy check | 4% | Clean: 100, Issues: -50 |
| Legal disputes | 4% | None known: 100, Active: -30 |

**Data sources**: OpenSanctions, court records, media monitoring

### 5. Accessibility (15%)

How transparent is the company?

| Factor | Weight | Scoring |
|--------|--------|---------|
| Contact info available | 5% | Phone + email + address: 100, Partial: 50, None: 10 |
| Financial statements public | 5% | Full: 100, Summary: 60, None: 20 |
| Beneficial ownership disclosed | 5% | Yes: 100, Partial: 50, No: 20 |

**Data sources**: Registry data, Open Ownership, company websites

## Score Ranges

| Score | Rating | Interpretation |
|-------|--------|---------------|
| 80-100 | Excellent | Low risk, well-established, transparent |
| 60-79 | Good | Acceptable risk, established business |
| 40-59 | Fair | Moderate risk, limited information |
| 20-39 | Poor | High risk, missing data, or red flags |
| 0-19 | Critical | Very high risk, major issues |

## Country Normalization

Scores are normalized by country to account for different regulatory environments:

- **Countries with mandatory filing** (UK, France, Denmark, Norway): Higher baseline data availability
- **Countries with optional filing** (Germany for GmbH, many US states): Score adjusted for expected data gaps
- **Developing markets**: Weighted toward available data, no penalty for missing regulatory data

## Live Examples

Try the scoring methodology on real companies:

- [Search any company](https://score.get-scala.com) — Free, no signup required
- Enter a company name, VAT number, or tax ID
- View the detailed score breakdown by pillar

## Comparison with Traditional Providers

| Feature | S.C.A.L.A. Score | D&B | Moody's/BvD | CreditSafe |
|---------|-----------------|-----|-------------|------------|
| Companies covered | 244M+ | 500M+ | 400M+ | 365M+ |
| Price per report | **Free** | $100-500 | Enterprise | $50-200 |
| Methodology transparent | **Yes** | No | No | No |
| API access | Coming soon | Paid | Paid | Paid |
| Countries | 40+ | 200+ | 200+ | 100+ |
| Updated | Monthly | Continuous | Continuous | Daily |

## Data Pipeline

```
Official Registries ─┐
GLEIF LEI Data ──────┤
XBRL Financial Data ─┤──→ ETL ──→ Normalize ──→ Score ──→ Database
OpenSanctions ───────┤                                       │
Web Presence Data ───┘                                       ▼
                                                    score.get-scala.com
```

## Implementation Notes

### Handling Missing Data

When data for a scoring factor is unavailable:
1. The factor weight is redistributed proportionally to available factors
2. A "data completeness" indicator shows what percentage of the score is based on verified data
3. Missing critical data (e.g., no registration found) caps the maximum score at 50

### Update Frequency

- **Registry data**: Monthly bulk update
- **Financial data**: Quarterly (when filings are published)
- **Sanctions screening**: Weekly
- **Web presence**: Monthly

## Research & References

- Altman, E.I. (1968). "Financial Ratios, Discriminant Analysis and the Prediction of Corporate Bankruptcy." *Journal of Finance*, 23(4).
- Ohlson, J.A. (1980). "Financial Ratios and the Probabilistic Prediction of Bankruptcy." *Journal of Accounting Research*, 18(1).
- GLEIF (2024). "LEI Data Quality Reports." [gleif.org](https://www.gleif.org/)
- European Commission (2023). "Company Law Package — Digital Tools Directive."

## Try It

Search any company for free at **[score.get-scala.com](https://score.get-scala.com)** — no signup, no credit card, 244M+ companies.

## Contributing

Contributions are welcome! If you have suggestions for improving the scoring methodology, please open an issue or submit a pull request.

## License

[![CC BY 4.0](https://licensebuttons.net/l/by/4.0/88x31.png)](https://creativecommons.org/licenses/by/4.0/)

This methodology is licensed under CC BY 4.0. You are free to use and adapt it with attribution.
