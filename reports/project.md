# Project Report — Amazon Sales Analysis

---

## 1. Cover Page

| Field               | Details                                               |
| ------------------- | ----------------------------------------------------- |
| **Project Title**   | Amazon Product Sales Analysis                         |
| **Sector**          | E-Commerce / Retail Technology                        |
| **Team ID**         | DETECTIVES                                            |
| **Team Members**    | Vipul · Ananya Gupta · Yash Mali · Abhay Pratap Yadav |
| **Faculty Mentor**  | Vrushali                                              |
| **Institute**       | Newton School Of Technology                           |
| **Submission Date** | 28 April 2026                                         |

---

## 2. Executive Summary

### Problem

Amazon sellers and category managers lack a data-driven view of which product attributes (price point, discount depth, rating, reviews, badge status, coupons, and sponsorship) actually drive monthly purchase volume and estimated revenue across product categories.

### Approach

We sourced a raw Amazon product dataset (~42,675 listings), performed a full ETL pipeline (extraction → cleaning → feature engineering), conducted exploratory data analysis across 18+ product categories, and ran five inferential statistical tests (Pearson/Spearman correlation, OLS multiple regression, Welch's t-test, one-way ANOVA, and chi-square). Findings were visualised in a Tableau dashboard.

### Key Insights

- **Power & Batteries** is the single highest-revenue category, generating an estimated **$526.7M** in the analysis window, driven by very high purchase volumes (~25.9M units/month).
- **91.3% of products** carry a rating ≥ 4.0, and high-rated products account for **95.8% of total estimated revenue** ($1.508B vs $0.067B).
- Only **0.9% of products** hold the "Best Seller" badge, yet they earn significantly higher mean revenue (**$510k vs $48k/month**, Welch's t-test t = 7.17, p = 7.74 × 10⁻¹²).
- **Discounts have no measurable revenue effect** (Pearson r = 0.000, p = 0.994) and **coupons are revenue-negative** (OLS −$56,470/month); both budgets should be redirected to sponsored placements.
- Category membership drives revenue more than any single price or discount lever (ANOVA F = 161.2, p < 0.001).

### Key Recommendations

1. **Invest in earning the Best Seller badge** — single largest revenue lever (+$396,500/month OLS; 10.6× mean revenue premium).
2. **Stop using discounts as a revenue lever** — zero correlation with revenue; redirect margin to sponsored placements (+$156,700/month).
3. **Cut coupon budgets and replace with sponsored campaigns** — coupons are revenue-negative (−$56,470/month) and independent of badge status (chi-square p = 0.489).
4. **Concentrate inventory and ad spend on top-revenue categories** — Power & Batteries, Laptops, Smart Home (ANOVA F = 161.2).
5. **Drive review velocity on high-rating, low-review SKUs** — +$1.74/review, near-zero marginal cost.

---

## 3. Sector and Business Context

### Sector Overview

E-commerce is the fastest-growing retail channel globally. Amazon dominates with >38% US market share. Products compete on algorithmic discoverability (ranking, badge signals, buy-box ownership) as much as on price. Data-driven sellers who optimise pricing, reviews, and promotional levers outperform reactive ones by significant margins.

### Decision-Maker / Stakeholder

- **Primary:** Amazon third-party sellers and brand managers seeking to maximise product-level revenue.
- **Secondary:** Category managers at Amazon who monitor segment health and competitive positioning.
- **Tertiary:** Retail investors tracking category-level demand trends.

### Why This Problem Matters

Without a structured, statistical view of what drives revenue, sellers default to intuition — discounting aggressively with unclear ROI, ignoring review strategy, or entering saturated categories. This project quantifies each lever independently so decisions can be evidence-based and ROI-focused.

---

## 4. Problem Statement and Objectives

### Formal Problem Definition

> _Which combination of product attributes — price, discount depth, rating, review count, best-seller badge, sponsorship, and coupon presence — most significantly determines estimated monthly revenue for an Amazon product listing, and how does this relationship vary across product categories?_

### Scope

- **In scope:** Product listings scraped from Amazon (August–September 2025), electronics-adjacent categories.
- **Out of scope:** Seasonal trend modelling (limited date range), international marketplaces, advertising cost data.

### Success Criteria

| Criterion                                              | Target                                               |
| ------------------------------------------------------ | ---------------------------------------------------- |
| Statistical significance of identified revenue drivers | p < 0.05 on ≥ 3 of 5 hypothesis tests                |
| EDA coverage                                           | ≥ 10 meaningful visual insights                      |
| Dashboard usability                                    | Executive view + Operational drill-down with filters |
| Data quality                                           | Retain ≥ 65% of raw rows after cleaning              |

---

## 5. Data Description

### Source Citation and Access Link

- **Dataset:** Amazon Product Sales Data (web scrape — single snapshot)
- **Collection date:** 2025-08-21 (see `data_collected_at` column)
- **Raw file:** `data/raw/amazon_products_sales_data_raw.csv`
- **Full column reference:** `docs/data_dictionary.md`

### Dataset Size and Coverage

| Metric              | Value                          |
| ------------------- | ------------------------------ |
| Raw rows            | 42,675                         |
| Cleaned rows        | 30,228                         |
| Retention rate      | 70.8%                          |
| File size (raw)     | ~36 MB                         |
| File size (cleaned) | ~8.2 MB                        |
| Categories covered  | 15+ electronics sub-categories |

### Key Columns

| Column                 | Type   | Source         | Description                                                                |
| ---------------------- | ------ | -------------- | -------------------------------------------------------------------------- |
| `product_title`        | String | raw            | Full product name as shown on Amazon                                       |
| `product_rating`       | Float  | raw            | Average star rating; observed range 2.0–5.0                                |
| `total_reviews`        | Float  | raw            | Total customer reviews on the listing                                      |
| `purchased_last_month` | Float  | raw            | Units sold in past 30 days — **never imputed**, rows with null are dropped |
| `discounted_price`     | Float  | raw            | Current selling price (USD)                                                |
| `original_price`       | Float  | raw            | Listed MSRP / pre-discount price (USD)                                     |
| `discount_percentage`  | Float  | raw            | Pre-computed discount %; validated ≤ 90; observed range 0–85.42            |
| `is_best_seller`       | String | raw            | Badge text, lowercased. Treat `"best seller"` as the true badge flag       |
| `is_sponsored`         | String | raw            | `"sponsored"` or `"organic"`                                               |
| `has_coupon`           | String | raw            | Coupon text, or `"no coupon"`. Anything ≠ `"no coupon"` = has coupon       |
| `buy_box_availability` | String | raw            | `"Add to cart"` or `"No"` (nulls filled with `"No"`)                       |
| `delivery_date`        | String | raw            | ISO date string — 17.8% null; re-parse with `pd.to_datetime()`             |
| `sustainability_tags`  | String | raw            | Sparse (92.1% null); common values: Small Business, Carbon impact          |
| `data_collected_at`    | String | raw            | Scrape timestamp (`2025-08-21`) — re-parse on load                         |
| `product_category`     | String | raw            | Normalised product category (lowercase + stripped)                         |
| `estimated_revenue`    | Float  | **engineered** | `purchased_last_month × discounted_price` — **primary KPI**                |
| `discount_value`       | Float  | **engineered** | `original_price − discounted_price` (absolute $ saved)                     |
| `rating_category`      | String | **engineered** | `"High"` if rating ≥ 4.0, else `"Low"`                                     |

### Data Quality Issues

- **~12,400 rows dropped:** missing values in core fields (`discounted_price`, `original_price`, `product_rating`, `purchased_last_month`) — net effect 42,675 → 30,228 rows (29% loss, predominantly missing sales counts).
- **Numeric columns stored as strings** (e.g., prices with `$` symbols) — required `pd.to_numeric()` coercion.
- **Boolean-like flags** (`is_best_seller`, `is_sponsored`, `has_coupon`) stored as raw lowercased badge strings — downstream analysis derives 0/1 flags in-notebook without modifying the CSV.
- **Date columns written as strings** after `df.to_csv()` — always call `pd.to_datetime()` on `delivery_date` and `data_collected_at` when loading `cleaned_data.csv`.
- **Discount % is pre-computed** by the scraper and may diverge slightly from `(original − discounted) / original × 100` due to time-of-scrape promotions.
- **Sustainability tags** too sparse (~8% present) for standalone analysis.
- Rows with `discount_percentage > 90` treated as data artefacts and removed.

---

## 6. Cleaning and Transformation

### Major Cleaning Steps

| Step | Action                                                                | Rows Before | Rows After |
| ---- | --------------------------------------------------------------------- | ----------- | ---------- |
| 1    | Drop exact duplicates                                                 | 42,675      | ~42,500    |
| 2    | Coerce 6 numeric columns (`pd.to_numeric`)                            | —           | —          |
| 3    | Parse 2 date columns (`pd.to_datetime`)                               | —           | —          |
| 4    | Fill `buy_box_availability` NaN → "No"                                | —           | —          |
| 5    | Drop rows missing core fields                                         | ~42,500     | ~31,000    |
| 6    | Validate ranges (price > 0, rating 0–5, discount ≤ 90%)               | ~31,000     | 30,228     |
| 7    | Normalise `product_category` (lowercase + strip)                      | —           | —          |
| 8    | Lowercase boolean-like flag columns                                   | —           | —          |
| 9    | **Engineer** `estimated_revenue`, `discount_value`, `rating_category` | —           | —          |
| 10   | Drop non-analytical columns (`product_image_url`, `product_page_url`) | —           | —          |

### Assumptions Made

- `estimated_revenue = purchased_last_month × discounted_price` is a conservative proxy for revenue (ignores returns, fees, and multi-channel sales).
- `product_rating ≥ 4.0` qualifies as "High" quality; below 4.0 is "Low".
- `buy_box_availability` NaN treated as "No" (product does not hold Buy Box by default).
- Rows with `discount_percentage > 90%` are data artefacts, not genuine listings.

### Output Dataset Description

- **File:** `data/processed/cleaned_data.csv`
- **Shape:** 30,228 rows × 18 columns
- **Final Tableau export:** `data/processed/final_tableau_data.csv`

---

## 7. KPI Framework

| KPI                                 | Formula                                          | Why It Matters                                           |
| ----------------------------------- | ------------------------------------------------ | -------------------------------------------------------- |
| **Estimated Revenue (per product)** | `purchased_last_month × discounted_price`        | Primary demand signal; proxy for seller monthly earnings |
| **Total Category Revenue**          | `Σ estimated_revenue` per category               | Identifies highest-value market segments                 |
| **Average Discount Depth**          | `mean(discount_percentage)`                      | Indicates competitive pricing pressure                   |
| **Rating Score (avg)**              | `mean(product_rating)`                           | Consumer trust and algorithmic ranking factor            |
| **Review Volume**                   | `mean(total_reviews)`                            | Social proof and discoverability metric                  |
| **Best Seller Penetration**         | `count(is_best_seller == 'best seller') / total` | Badge capture rate; quality signal                       |
| **Coupon Adoption Rate**            | `count(has_coupon != 'no coupon') / total`       | Promotional activity level                               |
| **Buy Box Win Rate**                | `count(buy_box_availability != 'No') / total`    | Competitive advantage on the listing page                |
| **Sponsorship Rate**                | `count(is_sponsored == 'sponsored') / total`     | Paid visibility investment                               |
| **High-Rating Share**               | `count(rating_category == 'High') / total`       | Portfolio quality health                                 |

---

## 8. Exploratory Analysis

### Major Trends

| Observation                                 | Value                       |
| ------------------------------------------- | --------------------------- |
| Total estimated revenue across all products | **$1.575B**                 |
| Average product rating                      | **4.44** (median: 4.5)      |
| Average discounted price                    | **$156.62**                 |
| Average original price                      | **$172.90**                 |
| Average discount depth                      | **7.78%**                   |
| Average monthly purchases per product       | **1,354.57 units**          |
| Products with rating ≥ 4.0 ("High")         | **91.3%** (27,580 / 30,228) |
| Products holding Buy Box                    | **77.0%**                   |
| Sponsored products                          | **11.9%**                   |
| Products with coupon                        | **3.7%**                    |
| Best Seller badge holders                   | **0.9%**                    |

### Segment-Level Insights

**Top 5 Categories by Estimated Revenue:**

| Rank | Category          | Estimated Revenue |
| ---- | ----------------- | ----------------- |
| 1    | Power & Batteries | $526.7M           |
| 2    | Laptops           | $312.4M           |
| 3    | Phones            | $240.1M           |
| 4    | Other Electronics | $141.0M           |
| 5    | Cameras           | $87.0M            |

**Top 5 Categories by Product Count:**

| Rank | Category          | Product Count |
| ---- | ----------------- | ------------- |
| 1    | Laptops           | 6,038         |
| 2    | Other Electronics | 5,295         |
| 3    | Phones            | 5,108         |
| 4    | Power & Batteries | 2,683         |
| 5    | Cameras           | 2,490         |

> Power & Batteries has only the 4th-highest product count but the #1 revenue position — driven by extremely high average purchase volume per product.

**High vs Low Rating Revenue Split:**

- High-rated products (≥ 4.0): **$1,508.2M (95.8% of total revenue)**
- Low-rated products (< 4.0): **$66.6M (4.2%)**

### Visual Summaries

_(Refer to Tableau dashboard for interactive equivalents)_

- Bar chart: Category revenue comparison
- Histogram: Distribution of product ratings (left-skewed, peaked at 4.5)
- Scatter: `total_reviews` vs `estimated_revenue` (positive correlation)
- Box plot: Revenue by `rating_category`
- Treemap: Revenue share by category

---

## 9. Statistical Analysis

### Methods Used

Five hypothesis tests were conducted on `data/processed/cleaned_data.csv` (n = 30,228). Actual statistics are taken from the executed `notebooks/04_statistical_analysis.ipynb`; α = 0.05 throughout.

| #   | Hypothesis                                         | Test Used                                        |
| --- | -------------------------------------------------- | ------------------------------------------------ |
| H1  | Reviews predict revenue                            | Pearson + Spearman correlation + OLS coefficient |
| H2  | Discounts drive sales                              | Pearson + Spearman correlation + OLS coefficient |
| H3  | Best Seller badge → higher revenue                 | Welch's two-sample t-test                        |
| H4  | Revenue differs across product categories          | One-way ANOVA                                    |
| H5  | Coupons and Best Seller status are not independent | Chi-square test of independence                  |

### Results

| #   | Hypothesis                        | Statistic                                           | p-value           | Decision                                                                |
| --- | --------------------------------- | --------------------------------------------------- | ----------------- | ----------------------------------------------------------------------- |
| H1  | Reviews → Revenue                 | Pearson r = 0.208; OLS coef = **+$1.74/review**     | < 0.001 / < 0.001 | **Reject H₀** — reviews significantly predict revenue                   |
| H2  | Discount % → Revenue              | Pearson r = **0.000**; OLS coef = −$58.4/pp         | 0.994 / 0.391     | **Fail to reject H₀** — discounts have **no detectable revenue effect** |
| H3  | Best Seller badge premium         | t = **7.17**, means $510k vs $48k (**10.6× ratio**) | **7.74 × 10⁻¹²**  | **Reject H₀** — best sellers earn significantly more                    |
| H4  | Category revenue differences      | F = **161.2**                                       | < 0.001           | **Reject H₀** — categories differ significantly                         |
| H5  | Coupon ↔ Best Seller independence | χ² = **0.480**, dof = 1                             | **0.489**         | **Fail to reject H₀** — coupons and Best Seller status are independent  |

**Additional OLS findings** (Model R² = 0.180, F = 944.9, p < 0.001):

| Predictor             | OLS Coefficient     | p-value | Interpretation                                             |
| --------------------- | ------------------- | ------- | ---------------------------------------------------------- |
| `flag_best_seller`    | **+$396,500/month** | < 0.001 | Largest independent effect in the model                    |
| `flag_sponsored`      | **+$156,700/month** | < 0.001 | Strongest controllable positive lever                      |
| `flag_has_coupon`     | **−$56,470/month**  | < 0.001 | Coupons are revenue-negative once other factors controlled |
| `total_reviews`       | **+$1.74/review**   | < 0.001 | Moderate, reliable positive effect                         |
| `discount_percentage` | −$58.4/pp           | 0.391   | Not significant — discounting does not drive revenue       |

### Business Interpretation

- **Best Seller badge** is the single largest controllable revenue lever (+$396,500/month, OLS; 10.6× mean revenue premium, t-test). Earning and maintaining the badge should be a top operational priority.
- **Sponsored placement** is the strongest controllable positive lever that does not require badge status (+$156,700/month). Budget should be shifted toward sponsored campaigns.
- **Discounts do not drive revenue** — Pearson r = 0.000, OLS coefficient insignificant (p = 0.391). Discount-led promotion is unsupported by the data.
- **Coupons are revenue-negative** in the regression (−$56,470/month, p < 0.001) and are **independent of Best Seller status** (chi-square p = 0.489) — coupons do not help win the badge and actively erode revenue; coupon budgets should be redirected to sponsored placements.
- **Review count** carries a reliable, low-cost marginal effect (+$1.74/review, p < 0.001); review-velocity campaigns on high-rating, low-review SKUs offer the highest ROI of the continuous levers.
- **Category membership** (ANOVA, F = 161.2, p < 0.001) explains more revenue variance than any individual product attribute — entering the right category is the highest-impact strategic decision.

---

## 10. Dashboard Walkthrough

### Dashboard Objective

Provide sellers and category managers with an interactive, decision-ready view of Amazon product performance across all categories, enabling rapid identification of high-revenue segments, quality gaps, and promotional opportunities.

### Executive View

- **KPI tiles:** Total estimated revenue, average rating, average discount %, buy-box win rate.
- **Category revenue bar chart:** Ranked by total estimated revenue, colour-coded by listing count.
- **Rating distribution:** Histogram showing overall product quality health.

### Operational View

- **Product-level table:** Sortable by revenue, rating, reviews, discount %, with badge filter.
- **Scatter plot:** `total_reviews` vs `estimated_revenue` coloured by `rating_category`.
- **Box plot:** Revenue distribution by `product_category` (outlier visibility).

### Filters and Interactivity

- **Category filter:** Select one or multiple product categories.
- **Rating range slider:** Filter by rating range (e.g., 4.0–5.0 only).
- **Badge filter:** Best Seller / No Badge / Limited Time Deal.
- **Price range:** Min–Max discounted price.
- **Buy Box toggle:** "Buy Box available" vs "Not available."
- **Sponsored filter:** Sponsored / Organic.

---

## 11. Key Insights

1. **Power & Batteries is the dominant revenue category** — $526.7M total estimated revenue; mean revenue per listing = **$196,303** (nearly 4× the next category). The high mean is driven by a small number of very high-volume bulk SKUs.
2. **Best Seller badge = 10.6× revenue premium** — badge holders average **$510,036/month** vs $48,141/month for non-badge listings (Welch's t-test, t = 7.17, p = 7.74 × 10⁻¹²).
3. **Sponsored placement is the strongest controllable lever** — OLS coefficient **+$156,700/month** (p < 0.001), the highest controllable positive effect excluding the Best Seller badge itself.
4. **Discounts do NOT drive revenue** — Pearson r = 0.000 (p = 0.994), OLS coefficient −$58.4/pp (p = 0.391). Discount-led promotion is statistically unsupported.
5. **Coupons are revenue-negative** once other factors are controlled — OLS coefficient **−$56,470/month** (p < 0.001); chi-square confirms coupons and Best Seller status are **independent** (χ² = 0.480, p = 0.489), meaning coupons don't help win the badge.
6. **Review count carries a reliable, low-cost marginal effect** — OLS coefficient **+$1.74 per additional review** (p < 0.001); second-strongest continuous predictor.
7. **91.3% of products are high-rated (≥ 4.0)** — quality is a baseline entry requirement, not a differentiator; differentiation happens through reviews, sponsorship, and badge status.
8. **High-rated products generate 95.8% of all estimated revenue** ($1.508B of $1.575B) — a rating < 4.0 is a disqualifying condition.
9. **Category membership explains more variance than any single attribute** — ANOVA F = 161.2, p < 0.001. Top categories by mean revenue: Power & Batteries ($196k), Laptops ($52k), Smart Home ($50k).
10. **Laptops have the most listings (6,038) but only rank 2nd in revenue** — the category is highly fragmented; per-product mean revenue is 3.8× lower than Power & Batteries.
11. **OLS model R² = 0.180** — the seven predictors (price, discount, rating, reviews, badge, sponsored, coupon) jointly explain 18% of revenue variance; 82% is attributable to unlisted factors (product age, advertising history, seasonal demand).
12. **Median rating of 4.5 indicates a ceiling effect** — at the top of the market, revenue is decided by review volume, sponsored visibility, and badge status — not by star rating alone.

---

## 12. Recommendations

> All five recommendations are grounded in specific test outputs from `notebooks/04_statistical_analysis.ipynb` and documented in full in `docs/recommendations.md`.

### R1 — Invest in Earning the Best-Seller Badge

**Evidence:** Welch's t-test: t = 7.17, p = 7.74 × 10⁻¹². Best-sellers earn **$510k vs $48k/month** (10.6× ratio). OLS confirms +$396,500/month independent effect (p < 0.001) — the single largest coefficient in the model.  
**Action:** Prioritise inventory, fulfilment SLA, review velocity, and listing-quality investment for the top ~10% of category-leading SKUs to push them into Amazon's Best-Seller badge.  
**Estimated Impact:** If 5 non-badge SKUs cross the threshold at half the observed lift: **≈ $1.15M/month** in incremental revenue.

### R2 — Stop Using Discounts as a Revenue Lever

**Evidence:** Pearson r = 0.000 (p = 0.994); Spearman ρ = −0.116 (p < 0.001 — slight _negative_ rank association); OLS coef = −$58.4/pp (p = 0.391, not significant). Discounts have no detectable positive effect on revenue.  
**Action:** Reduce average discount depth on listings where discounting is the primary lever; reallocate that margin to sponsored placements.  
**Estimated Impact:** Cutting average discount by 5 pp across ~7,500 bottom-quartile SKUs recovers ≈ **$1.4M/month** in margin without measurable sales loss.

### R3 — Concentrate Inventory and Ad Spend on High-Revenue Categories

**Evidence:** One-way ANOVA F = 161.2, p < 0.001. Top categories by mean monthly revenue: Power & Batteries ($196,303), Laptops ($51,740), Smart Home ($50,273).  
**Action:** Reallocate inventory commitment and sponsored-listing budget away from low-mean-revenue categories (Printers & Scanners, Networking) toward the top 3.  
**Estimated Impact:** Shifting 30% of ad budget from bottom-3 to top-3 categories yields an estimated **15–25% uplift** on the reallocated portion.

### R4 — Shift Promotion Budget from Coupons to Sponsored Placements

**Evidence:** OLS: `flag_sponsored` = **+$156,700/month** (p < 0.001); `flag_has_coupon` = **−$56,470/month** (p < 0.001). Chi-square confirms coupons and Best Seller status are independent (χ² = 0.480, p = 0.489) — coupons do not help win the badge.  
**Action:** Identify SKUs running coupons that are not simultaneously sponsored; cut the coupon and replace with an equivalent-cost sponsored campaign over 30 days.  
**Estimated Impact:** On a base of 50 listings, shifting from coupon to sponsored recovers the −$56k drag and captures part of the +$157k uplift — a modeled swing of up to **$10M/month** across the portfolio.

### R5 — Drive Review Velocity on High-Rating, Low-Review SKUs

**Evidence:** Pearson r = 0.208 (p < 0.001); OLS coef = +$1.74/review (p < 0.001) — second-strongest continuous predictor, with near-zero marginal cost.  
**Action:** Filter `cleaned_data.csv` for `product_rating ≥ 4.5 AND total_reviews < 500`; deploy post-purchase email + in-package QR insert review-request flow.  
**Estimated Impact:** Moving 10 SKUs from 200 → 1,000 reviews: **10 × 800 × $1.74 ≈ $13,920/month**, or ~$42k over a 90-day campaign.

---

## 13. Limitations and Next Steps

### Data Limitations

- **Single time-point snapshot:** Data collected August–September 2025 only. No seasonality analysis is possible with a single month's window.
- **Revenue is a proxy:** `estimated_revenue = purchased_last_month × discounted_price` ignores returns, Amazon fees, taxes, and multi-channel effects.
- **No cost data:** Without COGS or advertising spend, profit margin analysis is not possible.
- **Category labels are scraped strings:** Some category assignments may be imprecise due to seller-defined categorisation inconsistencies.
- **No seller-level data:** All analysis is at the product level; portfolio-level seller strategy cannot be assessed.

### Method Limitations

- OLS regression assumes linearity; revenue distributions are heavily right-skewed. Log-transforming the target variable would improve model fit (current R² = 0.180 likely underestimates true predictability).
- Correlation does not imply causation — e.g., the review-revenue and sponsored-revenue relationships may be inflated by selection bias (sellers sponsor and solicit reviews for already-performing SKUs).
- Large-sample tests (n = 30,228): even trivially small effects appear statistically significant. Always assess practical significance alongside p-values.
- The chi-square result (H5 fail-to-reject, p = 0.489) means coupon and badge status are independent — the previous report version incorrectly stated they were associated; this has been corrected.

### Suggested Future Work

1. **Time-series data collection** (6–12 months) to enable seasonality and trend modelling.
2. **Log-linear regression** on revenue for better distributional fit.
3. **Clustering / segmentation** of products using K-means to identify natural product archetypes.
4. **Profit margin modelling** by integrating advertising cost and estimated COGS data.
5. **Natural Language Processing** on `product_title` and `sustainability_tags` to assess keyword and ESG signal effects on revenue.
6. **Expand to non-electronics categories** (Clothing, Home & Kitchen, Books) for cross-sector comparison.

---

## 14. Contribution Matrix

> All members actively collaborated in discussions, validation of results, and final review of the project.

his project was completed as a collaborative effort, with all team members contributing across different stages of the data pipeline, analysis, and dashboard development.

- **Vipul Yadav**
  Contributed to data cleaning, preprocessing, feature engineering, and final dashboard publishing on Tableau Public.

- **Yash Kishor Mali**
  Managed project documentation, including README, project structure, and overall repository organization.

- **Ananya Gupta**
  Worked on dashboard design, KPI creation, and development of visualizations in Tableau.

- **Yashveer Singh**
  Focused on deriving key insights from the data and formulating business recommendations.

- **Abhay Pratap Yadav**
  Prepared the final project report and ensured proper documentation of analysis and findings.
