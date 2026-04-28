---
marp: true
theme: default
paginate: true
size: 16:9
title: Amazon Product Sales Analysis
description: Drivers of Revenue, Quality, and Competitive Positioning — DVA Capstone
style: |
  section {
    font-size: 16px;
    padding: 25px 45px;
  }
  h1 {
    font-size: 1.55em;
    margin-bottom: 0.25em;
  }
  h2 {
    font-size: 1.2em;
    margin-bottom: 0.2em;
  }
  h3 {
    font-size: 1.05em;
    margin-bottom: 0.15em;
  }
  table {
    font-size: 0.82em;
    width: 100%;
  }
  p, li {
    line-height: 1.45;
    margin: 0.2em 0;
  }
  blockquote {
    font-size: 0.9em;
    padding: 6px 14px;
  }
  ul, ol {
    margin: 0.3em 0;
    padding-left: 1.4em;
  }
  strong {
    color: #1a1a1a;
  }
---

# Amazon Product Sales Analysis
## Drivers of Revenue, Quality, and Competitive Positioning

| Field              | Details                                                                              |
| ------------------ | ------------------------------------------------------------------------------------ |
| **Sector**         | E-Commerce / Retail Technology                                                       |
| **Team ID**        | DETECTIVES                                                                           |
| **Team Members**   | Vipul Yadav · Yash Kishor Mali · Ananya Gupta · Yashveer Singh · Abhay Pratap Yadav |
| **Faculty Mentor** | Vrushali                                                                             |
| **Date**           | 28 April 2026                                                                        |

---

# Context and Problem Statement

**Sector Context**
- Amazon holds **>38% of US e-commerce market share**
- Sellers compete on algorithmic discoverability: ratings, reviews, badge signals, Buy Box ownership
- Price alone is insufficient — social proof and promotional levers now drive purchase velocity

**Core Business Question**
> _Which product attributes most strongly drive estimated monthly revenue — and which levers should sellers prioritise?_

**Objectives**
1. Identify top revenue drivers through correlation, regression, and hypothesis testing
2. Quantify revenue gaps between quality tiers and badge types
3. Surface actionable, category-specific recommendations via a Tableau dashboard

---

# Data Engineering

| Metric             | Value                          |
| ------------------ | ------------------------------ |
| Raw rows           | 42,675                         |
| Cleaned rows       | 30,228                         |
| Retention rate     | 70.8%                          |
| Categories covered | 15+ electronics sub-categories |
| Scrape date        | 2025-08-21 (single snapshot)   |

**Major Cleaning Steps**
1. Dropped duplicates; coerced numeric columns from string to float
2. Dropped rows missing core fields (`discounted_price`, `product_rating`, `purchased_last_month`)
3. Validated: price > 0, rating 0–5, discount ≤ 90%
4. Engineered `estimated_revenue`, `discount_value`, `rating_category`

---

# KPI Framework

| KPI                         | Formula                                   | Business Purpose                  |
| --------------------------- | ----------------------------------------- | --------------------------------- |
| **Estimated Revenue**       | `purchased_last_month × discounted_price` | Primary revenue proxy per product |
| **Total Category Revenue**  | `Σ estimated_revenue` per category        | Category investment priority      |
| **Average Rating**          | `mean(product_rating)`                    | Portfolio quality health          |
| **Review Volume**           | `mean(total_reviews)`                     | Social proof and discoverability  |
| **Best Seller Penetration** | Best seller listings / total              | Badge capture rate                |
| **Buy Box Win Rate**        | Buy Box available / total                 | Listing competitiveness           |
| **Coupon Adoption Rate**    | Coupon listings / total                   | Promotional activity level        |
| **Sponsorship Rate**        | Sponsored listings / total                | Paid visibility investment        |

---

# Key EDA Insights

| Insight                                        | Value                                          |
| ---------------------------------------------- | ---------------------------------------------- |
| Total estimated revenue (30,228 products)      | **$1.575 Billion**                             |
| Average product rating                         | **4.44** (median 4.5) — compressed range       |
| Products rated ≥ 4.0                           | **91.3%** — quality is a baseline requirement  |
| High-rated products' revenue share             | **95.8%** ($1.508B of $1.575B)                 |
| Avg monthly purchases per product              | **1,354 units**                                |
| Best Seller badge holders                      | **0.9%** of listings                           |

**Top Categories by Total Revenue**

| Rank | Category          | Total Revenue | Mean Rev / Listing |
| ---- | ----------------- | ------------- | ------------------ |
| 1    | Power & Batteries | $526.7M       | $196,303           |
| 2    | Laptops           | $312.4M       | $51,740            |
| 3    | Phones            | $240.1M       | —                  |

> Top 3 by **mean** revenue: Power & Batteries ($196k), Laptops ($52k), Smart Home ($50k)

---

# Statistical Testing — Results

n = 30,228 · α = 0.05 · Source: `notebooks/04_statistical_analysis.ipynb`

| Test | Result | p-value | Decision |
| ---- | ------ | ------- | -------- |
| Pearson r — `total_reviews` / revenue | r = **0.208** | < 0.001 | Reject H0 — reviews predict revenue |
| Pearson r — `discount_%` / revenue | r = **0.000** | 0.994 | Fail to reject H0 — **no effect** |
| Welch's T-Test — Best Seller badge | $510k vs $48k (10.6×), t = **7.17** | 7.74 × 10⁻¹² | Reject H0 — badge = significant premium |
| One-Way ANOVA — 15 categories | F = **161.2** | < 0.001 | Reject H0 — categories differ |
| Chi-Square — coupon × badge | χ² = **0.480** | 0.489 | Fail to reject H0 — **independent** |

**OLS Model (R² = 0.180, F = 944.9)**

| Predictor | Coefficient | p-value |
| --------- | ----------- | ------- |
| Best Seller badge | **+$396,500/mo** | < 0.001 |
| Sponsored | **+$156,700/mo** | < 0.001 |
| Coupon | **−$56,470/mo** | < 0.001 |
| Reviews | **+$1.74/review** | < 0.001 |

---

# Dashboard Overview

**Built in Tableau** · Data: `data/processed/final_tableau_data.csv`

**Executive View**
- KPI tiles: Total revenue · Avg rating · Avg discount % · Buy Box win rate
- Ranked bar chart: Category revenue by listing count
- Rating histogram: Portfolio quality distribution

**Operational View**
- Sortable product table: Revenue, rating, reviews, discount %, badge status
- Scatter plot: `total_reviews` vs `estimated_revenue` by `rating_category`
- Box plot: Revenue distribution by category

**Filters**
- Category multi-select · Rating range slider
- Badge filter (Best Seller / No Badge / Limited Time Deal)
- Buy Box toggle · Sponsored / Organic toggle

---

# Top Insights

**1 — Best Seller Badge = 10.6× Revenue Premium**
Badge holders: **$510,036/mo** vs **$48,141/mo** non-badge (t = 7.17, p = 7.74 × 10⁻¹²) · OLS: **+$396,500/mo**

**2 — Sponsored Placement is the Strongest Controllable Lever**
OLS coefficient = **+$156,700/month** (p < 0.001) — achievable without earning the badge first

**3 — Discounts Don't Drive Revenue — Coupons Actively Hurt It**
Discount %: r = **0.000** (p = 0.994) — zero relationship
Coupons: OLS **−$56,470/mo** (p < 0.001) — revenue-negative once controlled

**4 — Reviews: High ROI, Near-Zero Cost**
OLS: **+$1.74 per additional review** (p < 0.001)
10 SKUs from 200 → 1,000 reviews ≈ **$13,920/month** incremental

---

# Recommendations

> Full evidence chains in `docs/recommendations.md`

| Priority | Recommendation | Estimated Impact |
| -------- | -------------- | ---------------- |
| High | **Earn the Best-Seller badge** | 5 SKUs ≈ **$1.15M/month** |
| High | **Stop discounting — shift to sponsored** | ≈ **$1.4M/month** margin recovery |
| Medium | **Cut coupons → sponsored campaigns** | Up to **$10M/month** across 50 SKUs |
| Medium | **Concentrate spend on top categories** | **15–25% uplift** on reallocated budget |
| Medium | **Drive reviews on low-review SKUs** | 10 SKUs ≈ **$13,920/month** |

---

# Impact and Feasibility

| Action | Timeframe | Estimated Impact |
| ------ | --------- | ---------------- |
| Best Seller badge push (top 5 SKUs) | 60-day pilot → 6–12 months | ≈ $1.15M/month |
| Discount reduction | 30-day A/B test | ≈ $1.4M/month margin recovery |
| Coupon → sponsored shift (50 SKUs) | 30-day pilot | Up to $10M/month |
| Category reallocation (top 3) | 2-week ramp | 15–25% uplift |
| Review-velocity campaign (10 SKUs) | 90 days | ≈ $42k |

| Recommendation | Feasibility | Revenue Impact |
| -------------- | ----------- | -------------- |
| Best Seller badge targeting | Medium | Very High |
| Stop discounting / margin recovery | High | High |
| Coupon → sponsored shift | High | Very High |
| Category spend concentration | Medium | Very High |
| Review velocity campaign | High | Medium |

---

# Limitations

**Data Constraints**
- Single snapshot (2025-08-21) — no seasonality analysis possible
- Revenue is a proxy — rounding in purchase counts; excludes returns, fees
- No COGS or advertising spend data — profit analysis not possible
- Sustainability tags 92.1% null — not usable as predictor

**Method Constraints**
- OLS R² = 0.180 — 82% of variance unexplained (product age, ad history, seasonal demand)
- Reverse causation risk: sellers sponsor and solicit reviews for already-performing SKUs
- Large n (30,228): small effects appear statistically significant — check practical significance
- H5 corrected: chi-square p = 0.489 — coupons and badge status are **independent**

---

# Contribution Matrix

This project was completed as a collaborative effort across all stages of the data pipeline, analysis, and dashboard development.

| Team Member        | Role                                      |
| ------------------ | ----------------------------------------- |
| **Vipul Yadav**    | Data cleaning, preprocessing, feature engineering, Tableau dashboard publishing |
| **Yash Kishor Mali** | Project documentation, README, repository structure |
| **Ananya Gupta**   | Dashboard design, KPI creation, Tableau visualisations |
| **Yashveer Singh** | Statistical analysis, hypothesis testing, key insights and recommendations |
| **Abhay Pratap Yadav** | Data extraction, project report, presentation |

---

# Future Extensions & Closing

**Next Steps**

| Extension | Description |
| --------- | ----------- |
| Time-series collection | 6–12 months for seasonality modelling |
| Log-linear regression | Better fit for right-skewed revenue |
| K-means clustering | Segment products into natural archetypes |
| NLP on product titles | Keyword and sustainability tag effects |
| Profit margin modelling | Integrate advertising cost and COGS |

**Closing Summary**

> Amazon success is **not** won on price or discounts (r = 0.000, p = 0.994). It is won through the **Best Seller badge** (+$396k/mo), **sponsored placement** (+$157k/mo), **review volume** (+$1.74/review), and **right category** (ANOVA F = 161.2).

---

<!-- _class: lead -->

# Thank You

**Questions Welcome**

---

_Vipul Yadav · Yash Kishor Mali · Ananya Gupta · Yashveer Singh · Abhay Pratap Yadav_
_DVA Capstone · Newton School Of Technology · 28 April 2026_
