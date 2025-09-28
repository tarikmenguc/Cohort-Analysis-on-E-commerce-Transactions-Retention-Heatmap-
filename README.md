# Cohort Analysis on E-commerce Transactions (Retention Heatmap)

This notebook performs a classic cohort analysis to measure **user retention** after their first purchase month.  
We form monthly cohorts (by each user's first purchase month), compute **Ay+N** (months since cohort start), and visualize **retention = active_users / cohort_size** as a heatmap.

---

## 🎯 Objectives
- Create **monthly user cohorts** based on each user’s first purchase month.
- Compute **Ay+N** (0, 1, 2, … months since first purchase).
- Calculate **active users** per (cohort_month × month_index) and **retention rates**.
- Plot a **retention heatmap** for quick comparison across cohorts.

---

## 🧰 Tech Stack
- **Python**: pandas, DuckDB, matplotlib
- **Notebook**: Jupyter

---

## 🗂 Data Assumptions
The raw data contains at least:
- `CustomerID` (user identifier)
- `InvoiceDate` (timestamp)
- `Quantity`, `UnitPrice` (optional if `Amount` is precomputed)
- `Amount` = `Quantity * UnitPrice` (if not present, it is computed)

> _Note:_ The dataset is **not included** in this repository. Provide your CSV locally (e.g., `data/online_retail_II.csv`) or update the path in the notebook.

---

## 📓 Method (High-level)
1. **Clean** data (drop returns/invalid rows, ensure `CustomerID` and `InvoiceDate` exist).
2. Build an **orders** table at **user × month** grain:
   - `order_month = date_trunc('month', InvoiceDate)`
   - `amount = SUM(Amount)` per (user, order_month)
3. Derive **first_orders** (cohort):
   - `cohort_month = MIN(order_month)` per user
4. Label each (user, order_month) with **`month_index = DATE_DIFF('month', cohort_month, order_month)`**.
5. Aggregate:
   - `active_users = COUNT(DISTINCT user_id)` by (cohort_month, month_index)
   - `cohort_size` = number of users starting in each cohort
   - `retention = active_users / cohort_size`
6. **Pivot** to matrix and **plot** heatmap.

---

## ▶️ How to Run
```bash
# 1) Install deps
pip install -r requirements.txt

# 2) Launch Jupyter
jupyter notebook
