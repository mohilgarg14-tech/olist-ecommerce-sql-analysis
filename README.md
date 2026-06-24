# Olist E-Commerce — End-to-End SQL & Business Intelligence Analysis

<div align="center">

![SQL](https://img.shields.io/badge/SQL-BigQuery-4285F4?style=for-the-badge&logo=google-cloud&logoColor=white)
![Python](https://img.shields.io/badge/Python-pandas-3776AB?style=for-the-badge&logo=python&logoColor=white)
![PowerBI](https://img.shields.io/badge/Power_BI-Dashboard-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Tableau](https://img.shields.io/badge/Tableau-Public-E97627?style=for-the-badge&logo=tableau&logoColor=white)
![Status](https://img.shields.io/badge/Status-Active-2ea44f?style=for-the-badge)

**18 SQL queries · 9 relational tables · 99,441 orders · Google BigQuery**

</div>

---

## Business Problem

Olist is Brazil's largest e-commerce marketplace, connecting 3,000+ small
businesses to major retail channels across 27 states. As order volumes scaled
between 2016 and 2018, three operational challenges emerged that directly
impacted customer satisfaction and revenue retention:

- **Delivery reliability** — which seller–state corridors generate the most delays?
- **Customer retention** — what percentage of customers ever place a second order?
- **Seller quality** — do high-volume sellers actually deliver high customer satisfaction?

This project applies structured SQL analysis across a 9-table relational schema
to surface actionable answers — the same analytical approach used in
consulting operations and supply chain engagements.

---

## Key Findings (From Real Query Outputs)

| # | Finding | Data Point |
|---|---|---|
| 1 | **97.73% on-time delivery rate** overall across all delivered orders | Q06 output |
| 2 | **GO, SC and RS state sellers** scored highest on composite delay + review risk | Q18 composite score ~225 |
| 3 | **January 2017 saw +3,929% MoM GMV growth** — platform scaling inflection point | Q01 output |
| 4 | **December 2017 saw +46% MoM spike** — Black Friday / year-end seasonality confirmed | Q01 output |
| 5 | **High-volume sellers ≠ high quality** — volume rank and review score are weakly correlated | Q17 + Q18 |

---

## Dataset Schema — 9 Relational Tables

```
olist_orders ──────────────┬──── olist_order_items ──── olist_products
      │                    │           │                      │
      │              olist_order_      │                product_category_
      │              payments     olist_sellers          name_translation
      │
      ├──── olist_customers ──── olist_geolocation
      │
      └──── olist_order_reviews
```

**Primary join key:** `order_id` links orders → items → payments → reviews  
**Customer identity:** `customer_unique_id` (not `customer_id`) for true repeat-purchase analysis  
**Volume:** 99,441 orders · 112,650 items · 99,224 reviews · 1,000,163 geolocation records

---

## SQL Analysis — 18 Queries

### Revenue & Growth (Q01–Q05)

| Query | Business Question | Techniques Used |
|---|---|---|
| Q01 | Monthly GMV trend + MoM growth % | `GROUP BY` month · `LAG()` window function |
| Q02 | Revenue by state — top 10 ranking | Multi-table `JOIN` · `RANK() OVER` |
| Q03 | Top 10 product categories by revenue | `JOIN` · `SUM()` · contribution % via `SUM OVER()` |
| Q04 | Average order value by state & month | `GROUP BY` · `HAVING COUNT > 100` noise filter |
| Q05 | Payment type distribution & installments | `GROUP BY` · `AVG()` · `COUNT()` |

### Delivery Performance (Q06–Q10)

| Query | Business Question | Techniques Used |
|---|---|---|
| Q06 | On-time delivery rate overall & by month | `CASE WHEN` · `AVG()` · date comparison |
| Q07 | Top 10 worst sellers by average delay | `JOIN` · `HAVING` · `ORDER BY` delay DESC |
| Q08 | Delay heatmap: seller state → customer state | Multi-table `JOIN` · `AVG()` GROUP BY corridor |
| Q09 | Carrier handoff speed by seller | `DATE_DIFF` · `RANK() OVER` |
| Q10 | Delivery time percentiles by category | `NTILE(4) OVER PARTITION BY` category |

### Customer & Review Intelligence (Q11–Q15)

| Query | Business Question | Techniques Used |
|---|---|---|
| Q11 | Repeat purchase rate | `CTE` · `COUNT DISTINCT` · filter |
| Q12 | RFM base table — recency, frequency, monetary | `CTE` · `MAX` · `COUNT` · `SUM` per customer |
| Q13 | Cohort retention matrix | Double `CTE` · `DATE_DIFF` months since cohort |
| Q14 | Review score distribution by category & seller | `AVG` · % bad reviews · `ORDER BY` risk |
| Q15 | Review response lag vs. score correlation | `DATE_DIFF` · `GROUP BY` review score |

### Advanced — Window Functions (Q16–Q18)

| Query | Business Question | Techniques Used |
|---|---|---|
| Q16 | Running total GMV by month | `SUM() OVER (ROWS UNBOUNDED PRECEDING)` |
| Q17 | Seller revenue rank with percentile tier | `NTILE(4) OVER` · multi-metric `JOIN` |
| **Q18** | **Top delivery bottlenecks — composite score** | **3-CTE weighted rank · composite score formula** |

> **Q18 is the hero query** — combines seller delay rank, volume rank, and review score
> into a single weighted composite to surface the highest-risk seller corridors.
> GO, SC, RS, PA and PR states emerged as the top risk corridors.

---

## Dashboards

### Power BI — 3 Pages *(In Progress)*
- **Executive KPI Overview** — GMV trend, on-time delivery rate, MoM growth
- **Delivery Operations** — delay heatmap by state corridor, worst seller ranking
- **Customer & Category Intelligence** — RFM segments, review distribution, cohort grid

### Tableau Public *(In Progress)*
 **Dashboard link will be added here on completion**

---

## Python EDA *(In Progress)*

Planned analyses in `notebooks/olist_eda.ipynb`:
- Delivery delay distribution (histogram + KDE)
- Monthly GMV with 3-month rolling average
- RFM customer segmentation (Bronze → Platinum)
- Seller volume vs. review score scatter plot

---

## Repository Structure

```
olist-ecommerce-sql-analysis/
│
├── sql/                          ← 18 .sql query files
│   ├── Q01_monthly_gmv_trend.sql
│   ├── Q02_revenue_by_state.sql
│   ├── Q03_top_product_categories.sql
│   ├── Q04_avg_order_value_state_month.sql
│   ├── Q05_payment_type_distribution.sql
│   ├── Q06_on_time_delivery_rate.sql
│   ├── Q07_worst_sellers_by_delay.sql
│   ├── Q08_delay_by_state_corridor.sql
│   ├── Q09_carrier_handoff_speed.sql
│   ├── Q10_delivery_percentiles_by_category.sql
│   ├── Q11_repeat_purchase_rate.sql
│   ├── Q12_rfm_base_table.sql
│   ├── Q13_cohort_retention.sql
│   ├── Q14_review_score_by_category.sql
│   ├── Q15_review_response_lag.sql
│   ├── Q16_running_total_gmv.sql
│   ├── Q17_seller_revenue_rank.sql
│   └── Q18_top_delay_bottlenecks_composite.sql
│
├── data/
│   └── outputs/                  ← CSV results from each query
│       ├── Q01_monthly_gmv_trend.csv
│       ├── Q06_ontime_delivery_rate.csv
│       ├── Q18_top_delay_bottlenecks.csv
│       └── ...
│
├── notebooks/
│   └── olist_eda.ipynb           ← Python EDA (in progress)
│
├── dashboards/
│   ├── olist_dashboard.pbix      ← Power BI file (in progress)
│   └── tableau_public_link.txt   ← Tableau Public link
│
├── data_dictionary.md            ← Column definitions for all 9 tables
├── requirements.txt
└── README.md
```

---

## How to Reproduce

```bash
# 1. Clone the repository
git clone https://github.com/mohilgarg14-tech/olist-ecommerce-sql-analysis
cd olist-ecommerce-sql-analysis

# 2. Download dataset
# Kaggle: https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce
# Place all 9 CSVs in data/raw/

# 3. Load into SQLite (local option)
pip install -r requirements.txt
python setup.py

# 4. Run any query
# Option A: DB Browser for SQLite — open data/olist.db
# Option B: Google BigQuery — upload CSVs to a dataset named 'olist'

# 5. Open dashboards
# Power BI: open dashboards/olist_dashboard.pbix in Power BI Desktop
# Tableau: see dashboards/tableau_public_link.txt
```

---

## Business Recommendations

**1. Prioritise GO, SC and RS seller corridors for logistics review**
These states consistently score highest on the composite delay + review risk
metric (Q18). Targeted SLA enforcement or carrier switching in these corridors
would have outsized impact on overall on-time delivery rate.

**2. Investigate the January 2017 growth spike**
A +3,929% MoM GMV jump signals a major platform event — possible marketing
campaign, new merchant onboarding wave, or marketplace expansion. Understanding
the driver can inform repeatable growth levers.

**3. Decouple seller visibility from volume alone**
Q17 and Q18 show high-volume sellers do not consistently deliver high review
scores. Introducing a composite seller quality tier (volume + review + delivery)
would improve buyer experience without reducing seller diversity.

---

## Tools & Skills Demonstrated

| Category | Tools |
|---|---|
| **Database & SQL** | Google BigQuery · SQLite · CTEs · Window Functions · Multi-table JOINs |
| **Languages** | SQL · Python (pandas, seaborn) |
| **BI & Visualisation** | Power BI · DAX · Tableau · Google Data Studio |
| **Cloud** | Google Cloud Platform · BigQuery Sandbox |
| **Version Control** | Git · GitHub |

---

## Author

**Mohil Garg**  
B.Tech Mechanical Engineering · Delhi Technological University  
[LinkedIn](www.linkedin.com/in/mohil-g-28825422b) · 
[GitHub](https://github.com/mohilgarg14-tech)

---

*Completed as part of a data analytics portfolio targeting consulting and 
finance roles (Bain, BCG, JPMC, ZS Associates). June–July 2026.*
