# 📊 Amazon Sales Analysis Dashboard (DVA Capstone Project)

## 🎯 Project Overview

This project analyzes Amazon product sales data to uncover meaningful insights about revenue trends, customer behavior, and product performance. The goal is to transform raw data into actionable business insights using data analysis and visualization techniques.

The project covers:

* Data extraction and cleaning
* Exploratory Data Analysis (EDA)
* Statistical analysis
* Interactive dashboard creation using Tableau

---

## 📂 Dataset Description

**Dataset:** Amazon Product Sales Dataset (42K+ records)

### 🔹 Features:

* Product Category
* Discounted Price & Original Price
* Discount Percentage
* Product Rating
* Total Reviews
* Purchased Last Month
* Best Seller Indicator

### 🔹 Data Processing:

* Removed duplicates and handled missing values
* Converted data types for analysis
* Created new features:

  * Estimated Revenue
  * Rating Category (High / Low)
  * Best Seller Clean

---

## ⚙️ Technologies Used

* Python (Pandas, NumPy, Matplotlib, SciPy)
* Tableau Public
* Git & GitHub

---

## 📁 Project Structure

```
AmazonSalesAnalysis/

├── data/
│   ├── raw/                 
│   └── processed/           

├── docs/
│   ├── data_dictionary.md
│   └── recommendations.md

├── notebooks/
│   ├── 01_extraction.ipynb
│   ├── 02_cleaning.ipynb
│   ├── 03_eda.ipynb
│   ├── 04_statistical_analysis.ipynb
│   └── 05_final_load_prep.ipynb

├── scripts/
│   └── etl_pipeline.py

├── tableau/
│   ├── screenshots/
│   └── dashboard_links.md

├── requirements.txt
├── README.md
└── .gitignore
```

---

## 📊 Tableau Dashboard

🔗 **Dashboard Link:**
(Refer to `tableau/dashboard_links.md`)

### Features:

* KPI Cards:

  * Total Revenue
  * Total Sales
  * Average Rating
  * Total Reviews

* Visualizations:

  * Revenue by Product Category
  * Sales Comparison by Rating Category
  * Discount vs Revenue Relationship
  * Reviews vs Sales Relationship
  * Best Seller vs Non-Best Seller Revenue

* Interactive Filters:

  * Product Category
  * Rating Category

---

## 🔍 Key Insights

* High-rated products contribute significantly more to total sales.
* Discounts do not always result in higher revenue, indicating diminishing returns.
* Non-best seller products generate a larger portion of total revenue.
* Certain product categories dominate overall sales performance.
* Reviews show limited correlation with sales, suggesting other influencing factors.

---

## 💡 Business Recommendations

* Focus on improving product quality to increase ratings and sales.
* Apply discounts strategically instead of aggressively.
* Invest in high-performing product categories for better returns.
* Strengthen marketing efforts to convert products into best sellers.
* Combine review management with pricing strategies to boost performance.

---

## ▶️ How to Run the Project

```bash
git clone <your-repo-link>
cd AmazonSalesAnalysis
pip install -r requirements.txt
```

Run notebooks sequentially from the `/notebooks` folder.

---

## 📌 Notes

* Cleaned dataset is used for all analysis
* Dashboard is interactive and designed for decision-making
* No hard-coded values are used in visualizations

---

## 🚀 Project Status

✔ Data Cleaning Completed
✔ Analysis Completed
✔ Dashboard Published on Tableau Public
✔ Documentation Completed

---

## 👥 Team Members & Contributions

This project was completed as a collaborative effort, with all team members contributing across different stages of the data pipeline, analysis, and dashboard development.

* **Vipul Yadav**
  Contributed to data cleaning, preprocessing, feature engineering, and final dashboard publishing on Tableau Public.

* **Yash Kishor Mali**
  Managed project documentation, including README, project structure, and overall repository organization.

* **Ananya Gupta**
  Worked on dashboard design, KPI creation, and development of visualizations in Tableau.

* **Yashveer Singh**
  Focused on deriving key insights from the data and formulating business recommendations.

* **Abhay Pratap Yadav**
  Prepared the final project report and ensured proper documentation of analysis and findings.

> 🔹 *Note:* All members actively collaborated in discussions, validation of results, and final review of the project.

---