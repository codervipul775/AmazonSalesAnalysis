#  Amazon Sales Analysis (DVA Capstone Project)

## 👥 Team Project

This project focuses on analyzing Amazon product sales data using Python and Tableau to extract meaningful business insights.

---

##  Project Structure

```
data/
 ├── raw/              # Original dataset
 ├── processed/        # Cleaned & final datasets

notebooks/
 ├── 01_extraction.ipynb
 ├── 02_cleaning.ipynb
 ├── 03_eda.ipynb
 ├── 04_statistical_analysis.ipynb
 ├── 05_final_load_prep.ipynb

tableau/               # Tableau dashboard files
docs/                  # Report and documentation
```

---

##  Setup Instructions

### 1. Clone Repository

```
git clone <your-repo-link>
cd AmazonSalesAnalysis
```

---

### 2. Create Virtual Environment

```
python -m venv venv
source venv/bin/activate   # Mac/Linux
venv\Scripts\activate      # Windows
```

---

### 3. Install Dependencies

```
pip install -r requirements.txt
```

---

##  How to Run

Run notebooks in order:

1. `01_extraction.ipynb`
2. `02_cleaning.ipynb`
3. `03_eda.ipynb`
4. `04_statistical_analysis.ipynb`
5. `05_final_load_prep.ipynb`

---

##  Output

Final dataset for Tableau:

```
data/processed/final_tableau_data.csv
```

---

##  Key Features

* Data Cleaning & Preprocessing
* Exploratory Data Analysis (EDA)
* Statistical Analysis
* Feature Engineering
* Tableau Dashboard Visualization

---

##  Tools Used

* Python (Pandas, NumPy, Matplotlib, SciPy)
* Tableau

---

## Notes

* Raw dataset is kept unchanged
* Cleaned dataset is used for analysis
* Tableau dashboard built using final processed data

---

##  Status

✔ Data Cleaning Completed
✔ EDA Completed
✔ Statistical Analysis Completed
✔ Tableau Integration Ready

---
