# UPI Growth Analysis — India 

![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-yellow?logo=powerbi)
![Python](https://img.shields.io/badge/Python-3.14-blue?logo=python)
![pandas](https://img.shields.io/badge/pandas-Data%20Analysis-green?logo=pandas)
![Excel](https://img.shields.io/badge/Excel-Data%20Storage-brightgreen?logo=microsoftexcel)
![SQL](https://img.shields.io/badge/SQL-Queries-orange?logo=mysql)

---

## Project Overview

This project analyses nearly a decade of UPI (Unified Payments Interface) transaction data in India. Using Python for data cleaning and EDA, SQL for querying, Excel for storage, and Power BI for dashboards, this project uncovers how UPI transformed India's payment landscape.

---

## Dashboard Preview

### Page 1 — Overview
![Overview](screenshots/page1_overview.png)

### Page 2 — Trends & Patterns
![Trends](screenshots/page2_trends.png)

### Page 3 — Key Insights & Findings
![Insights](screenshots/page3_insights.png)

---

## Key Findings

| # | Finding |
|---|---------|
| 1 | UPI grew **683x** from 2017 to 2025 — from 339 Mn to 2,31,750 Mn transactions |
| 2 | **COVID-19 accelerated adoption** — UPI grew 58.2% in 2020, contrary to expectations |
| 3 | Average transaction value **declined 45.7%** (₹24,287 → ₹13,199) showing shift from large transfers to everyday micro-payments |
| 4 | Festive months average **10.2% higher** transaction volume than non-festive months |
| 5 | Growth rate stabilising at **31.3% in 2025** — indicating market maturity, not decline |

---

## Tools & Technologies

| Tool | Purpose |
|------|---------|
| Python (pandas, matplotlib, seaborn) | Data cleaning, EDA, visualization |
| SQL (MySQL) | Data querying and aggregation |
| Microsoft Excel | Data storage and summary tables |
| Power BI | Interactive dashboard (3 pages) |
| Jupyter Notebook | Analysis documentation |

---

## Project Structure

```
UPI_Growth_Analysis/
├── data/
│   └── upi_india_monthly_enriched.csv      # Raw dataset (NPCI sourced)
├── notebooks/
│   └── upi_analysis.ipynb                  # Full Python EDA notebook
├── output/
│   ├── upi_cleaned.xlsx                    # Cleaned monthly data
│   ├── upi_yearly_summary.xlsx             # Yearly aggregated data
│   ├── chart1_volume_growth.png            # Year-wise volume bar chart
│   ├── chart2_value_growth.png             # Year-wise value line chart
│   ├── chart3_yoy_growth.png               # YoY growth rate chart
│   ├── chart4_seasonality_heatmap.png      # Monthly seasonality heatmap
│   ├── chart5_avg_txn_value.png            # Avg transaction value trend
│   ├── chart6_festive_vs_nonfestive.png    # Festive comparison chart
│   └── chart7_covid_impact.png             # COVID period timeline
├── screenshots/
│   ├── page1_overview.png                  # Power BI Page 1
│   ├── page2_trends.png                    # Power BI Page 2
│   └── page3_insights.png                  # Power BI Page 3
├── UPI_Growth_Analysis_SKA_Sameer.pbix     # Power BI file
└── README.md
```

---

## Data Source

- **Primary source:** NPCI (National Payments Corporation of India) — npci.org.in
- **Coverage:** August 2016 to December 2025 — 113 months
- **Fields:** Monthly transaction volume (Mn), value (₹ Crore), MoM%, YoY%, festive flags, COVID period flags

---

## Python Analysis Steps

### 1. Data Cleaning
```python
import pandas as pd

df = pd.read_csv('data/upi_india_monthly_enriched.csv')
df['Date'] = pd.to_datetime(df['Date'])
df = df[df['Volume_Mn'] > 0].reset_index(drop=True)
df['Avg_Txn_Value'] = (df['Value_Cr'] / df['Volume_Mn']) * 100
```

### 2. Year-wise Aggregation
```python
yearly = df.groupby('Year').agg(
    Total_Volume_Mn=('Volume_Mn', 'sum'),
    Total_Value_Cr=('Value_Cr', 'sum'),
    Avg_Txn_Value=('Avg_Txn_Value', 'mean')
).reset_index()

yearly['Volume_YoY_Growth'] = yearly['Total_Volume_Mn'].pct_change() * 100
```

### 3. Key Insight — COVID Impact
```python
covid_growth = ((yearly[yearly['Year']==2020]['Total_Volume_Mn'].values[0] -
                 yearly[yearly['Year']==2019]['Total_Volume_Mn'].values[0]) /
                 yearly[yearly['Year']==2019]['Total_Volume_Mn'].values[0]) * 100
# Result: +58.18% growth during COVID-19
```

---

## SQL Queries Used

```sql
-- Year-wise total volume and value
SELECT Year,
       SUM(Volume_Mn) AS Total_Volume_Mn,
       SUM(Value_Cr) AS Total_Value_Cr,
       AVG(Avg_Txn_Value) AS Avg_Transaction_Value
FROM upi_monthly
GROUP BY Year
ORDER BY Year;

-- Festive vs Non-Festive comparison
SELECT
    CASE WHEN Is_Festive_Season = 1 THEN 'Festive' ELSE 'Non-Festive' END AS Period,
    AVG(Volume_Mn) AS Avg_Volume,
    AVG(Value_Cr) AS Avg_Value
FROM upi_monthly
GROUP BY Is_Festive_Season;

-- COVID period analysis
SELECT Year, SUM(Volume_Mn) AS Total_Volume, 
       ROUND(((SUM(Volume_Mn) - LAG(SUM(Volume_Mn)) OVER (ORDER BY Year)) /
       LAG(SUM(Volume_Mn)) OVER (ORDER BY Year)) * 100, 2) AS YoY_Growth
FROM upi_monthly
WHERE Year BETWEEN 2018 AND 2022
GROUP BY Year;
```

---

## Power BI Dashboard Pages

**Page 1 — Overview**
- 5 KPI cards: Total Transactions, Total Value, Avg Transaction Value, Months Analysed, Peak Year
- Year slicer for dynamic filtering
- Column chart: Year-wise volume growth
- Line chart: Year-wise value growth

**Page 2 — Trends & Patterns**
- YoY growth rate bar chart (colour coded — red to green)
- Avg transaction value trend line
- Monthly volume trend line (2016–2025)
- Festive vs Non-Festive comparison bar chart

**Page 3 — Key Insights**
- 3 insight KPI cards: 683x growth, 58.18% COVID growth, -45.7% avg value change
- Dual axis combo chart: Volume vs Value
- COVID period zoom-in chart (2019–2022)
- 3 insight text boxes with findings and business implications

---

## How to Run

1. Clone this repository
```bash
git clone https://github.com/YOUR_USERNAME/upi-growth-analysis.git
cd upi-growth-analysis
```

2. Install Python dependencies
```bash
pip install pandas matplotlib seaborn openpyxl
```

3. Open Jupyter Notebook
```bash
jupyter notebook notebooks/upi_analysis.ipynb
```

4. Open Power BI dashboard
- Open `UPI_Growth_Analysis_SKA_Sameer.pbix` in Power BI Desktop
- Refresh data if prompted

---

## Author

**SK Ahamed Sameer**
- Email: skasameer8919@gmail.com
- LinkedIn: linkedin.com/in/ska-sameer
- GitHub: github.com/sameer-2025-web

---

## Acknowledgements

- Data sourced from NPCI official statistics
- Built as part of a data analytics portfolio to demonstrate end-to-end analytical skills

