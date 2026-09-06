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

- «e.g., The top 3 products per category account for ~X% of that category's revenue.»
- «e.g., Revenue grew/declined M% month-over-month, peaking in [month].»
- «e.g., Discounts above X% eroded profit margin without a matching lift in quantity.»
- «e.g., [Category/region] had the highest return rate at X%.»

_(Findings to be filled in as each query is run.)_

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

_Source: «add dataset source / Kaggle link»_

## 📁 Repository Contents

- `ecommerce_analysis.ipynb` — full analysis notebook (queries + visualizations)
- `ecommerce_sales_34500.csv` — the dataset
- Full write-up: [t-primero.github.io](https://t-primero.github.io)