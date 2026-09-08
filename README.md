# E-Commerce Sales Analysis 🛒

A SQL-focused analysis of a sample e-commerce dataset (~34,500 orders), using
**DuckDB** to answer business questions about revenue, discounting, returns, and
customer behavior.

## 📌 Overview

This project examines what drives revenue and profitability across an online
retailer's product categories and regions. SQL handles the analytical heavy
lifting — aggregation, ranking, and time-series — while Python is used for data
loading and visualization, mirroring how the two tools are used together in
production.

## ❓ Business Questions

1. **Which products and categories drive the most revenue?** Ranking products
   within each category to find where sales are concentrated.
2. **How does revenue trend over time?** Month-over-month revenue and growth
   rate across the dataset's date range.
3. **Does discounting actually help?** Whether heavier discounts drive enough
   extra volume to offset their hit to profit margin.
4. **Which segments have the highest return rates?** Return rates by category,
   region, and discount level — and what that implies.
5. **Who are the most valuable customers?** Revenue broken down by customer age
   group, gender, and region.


## 🔑 Key Findings

Across five business questions, one pattern held: **the product dimension carried
real signal, while customer and pricing dimensions were largely flat** — a result
consistent with the dataset being synthetically generated.

- **Electronics dominates revenue.** Total revenue is heavily concentrated in one
  category — Electronics brings in over 3× the next-largest (Home), while Grocery
  and Toys trail far behind.
- **Revenue is mildly seasonal.** Month-over-month change stays within ±10% for
  most months, with two recurring patterns across both full years: a January dip
  (post-holiday, ~−15% to −19%) and consistent March and December growth.
  *(The dataset's first and last months were partial and excluded from this trend.)*
- **Discounting erodes profit without driving volume.** As discounts deepen,
  average per-order profit falls steadily (~$30 → ~$22, a ~28% drop), while average
  units per order stay flat (~1.5). Deeper discounts amount to giving away margin
  for no gain in volume.
- **Returns are driven by category, not price or geography.** Return rates vary
  sharply by product — Fashion (8%) and Electronics (7%) highest, Grocery (1%)
  lowest — but stay flat (~5–6%) across every discount level and region. The
  category pattern matches intuition: apparel and electronics are returned far
  more often than perishable groceries, which are rarely sent back.
- **Customer value is evenly distributed.** Revenue per customer is nearly
  identical across gender and (equal-width) age groups. Regionally, four of five
  areas cluster tightly (~$260/customer), with Central a mild exception (~$232),
  driven by slightly lower order frequency.

**Analytical note:** Several columns were verified before use — `total_amount` was
confirmed to be net of discount, while `profit_margin` could not be reconciled to
the available columns (no cost-of-goods field is provided), so profit findings are
reported with that caveat. Apparent effects that proved to be artifacts of bucket 
width or group size — an initial "40+ customers spend most" and "South is the top 
region" — were identified and corrected by normalizing to comparable, per-unit measures.

## 🛠️ Tools & Approach

- **SQL (DuckDB):** all aggregation, ranking, and time-series analysis
- **Python (pandas):** data loading and preparation
- **Visualization:** Matplotlib / Seaborn for charting key results

## 🧠 Techniques Demonstrated

- Window functions — `DENSE_RANK()` for top-N-per-category, `LAG()` for
  month-over-month growth
- Common Table Expressions (CTEs) for multi-step logic
- `CASE` expressions for bucketing (age groups, discount tiers)
- Aggregation and grouping across multiple dimensions
- Data-quality checks for null and disguised-missing values

## 📊 Sample Query

Top 3 revenue-generating products in each category:

```sql
SELECT
    category,
    product_id,
    SUM(total_amount) AS revenue,
    DENSE_RANK() OVER (
        PARTITION BY category
        ORDER BY SUM(total_amount) DESC
    ) AS rank_in_category
FROM sales
GROUP BY category, product_id
QUALIFY rank_in_category <= 3
ORDER BY category, rank_in_category;
```

## 🗂️ Dataset

Sample e-commerce dataset of ~34,500 orders. Columns include order and customer
IDs, product category, price, discount, quantity, order date, region, return
status, total amount, shipping cost, profit margin, and customer demographics.

_Source: [E-commerce Sales Transactions Dataset (Kaggle)](https://www.kaggle.com/datasets/miadul/e-commerce-sales-transactions-dataset/data)

## 📁 Repository Contents

- `ecommerce_analysis.ipynb` — full analysis notebook (queries + visualizations)
- `ecommerce_sales_34500.csv` — the dataset
- Full write-up: [t-primero.github.io](https://t-primero.github.io)