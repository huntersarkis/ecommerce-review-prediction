# E-Commerce Review Prediction

**Author:** Hunter Sarkis  
**Framework:** CRISP-DM  
**Dataset:** [Olist Brazilian E-Commerce Public Dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

---

## Overview

This project applies the CRISP-DM framework to predict whether a customer will leave a negative review based on order characteristics — delivery time, price, freight cost, and payment method. Built on 95,000+ real delivered orders from the Olist marketplace (2016–2018).

The core business objective: identify high-risk orders *before* a review is submitted, enabling retailers to intervene proactively through customer service outreach, expedited shipping, or targeted compensation.

This project demonstrates all four analytics types:
- **Descriptive** — What happened?
- **Diagnostic** — Why did it happen?
- **Predictive** — What might happen next?
- **Prescriptive** — What should we do about it?

---

## Key Results

| Model | Accuracy | F1 | Recall | Precision |
|---|---|---|---|---|
| Logistic Regression | 81.7% | 0.895 | 0.984 | 0.821 |
| **Decision Tree** ✓ | **82.6%** | **0.900** | **0.988** | **0.827** |

The Decision Tree is the recommended model — it outperformed Logistic Regression across every metric.

**Key finding:** Dissatisfied customers waited an average of **16.71 days** for delivery vs. **10.56 days** for satisfied customers — a difference of over 6 days.

**Monte Carlo insight:** Reducing average delivery time does not significantly improve satisfaction. The bigger lever is **eliminating late deliveries** — orders that arrive after the estimated delivery date.

---

## Dataset

Six of the nine Olist CSV files were merged into a single master dataframe of **95,832 delivered orders**:

| File | Contents |
|---|---|
| `olist_orders_dataset.csv` | Order status, dates |
| `olist_order_items_dataset.csv` | Price, freight per item |
| `olist_order_reviews_dataset.csv` | Review scores and comments |
| `olist_order_payments_dataset.csv` | Payment type and value |
| `olist_products_dataset.csv` | Product category |
| `product_category_name_translation.csv` | Portuguese → English category names |

---

## Features Used

| Feature | Type | Description |
|---|---|---|
| `delivery_days` | Integer | Days from purchase to delivery (engineered) |
| `days_early_late` | Integer | Days ahead of or behind estimated delivery (engineered) |
| `price` | Float | Total item price per order (BRL) |
| `freight_value` | Float | Total shipping cost per order (BRL) |
| `payment_type` | Categorical | Credit card, boleto, voucher, or debit card |
| `satisfied` | Binary | 1 if review score ≥ 4, else 0 (label) |

---

## Project Structure

```
ecommerce-review-prediction/
│
├── notebook.ipynb                        # Full analysis notebook
├── ecommerce-review-prediction.docx      # Written report (CRISP-DM format)
│
├── olist_orders_dataset.csv
├── olist_order_items_dataset.csv
├── olist_order_reviews_dataset.csv
├── olist_order_payments_dataset.csv
├── olist_products_dataset.csv
├── olist_geolocation_dataset.csv
├── olist_customers_dataset.csv
├── olist_sellers_dataset.csv
└── product_category_name_translation.csv
```

---

## How to Run

1. Clone the repository:
```bash
git clone https://github.com/huntersarkis/ecommerce-review-prediction.git
cd ecommerce-review-prediction
```

2. Install dependencies:
```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy
```

3. Open the notebook:
```bash
jupyter notebook notebook.ipynb
```

Run all cells from top to bottom. All CSV files must be in the same directory as the notebook.

---

## Methodology

### Data Preparation
- Parsed datetime columns and engineered `delivery_days` and `days_early_late`
- Filtered to delivered orders only (95,832 of 98,673 total)
- Imputed missing values using median (numeric) and mode (categorical)
- Capped outliers using IQR fencing (factor = 3.0)
- Binned low-count product categories (< 300 orders) into `other`
- Created binary satisfaction label: score ≥ 4 = satisfied (78.9% of orders)

### Statistical Tests
- **t-test** for numeric features vs satisfaction label
- **Chi-square** for payment type vs satisfaction label
- All four features were statistically significant (p < 0.05)

### Modeling
- 80/20 train/test split (76,665 training rows, 19,167 test rows)
- Logistic Regression with StandardScaler
- Decision Tree (max depth = 5)

### Prescriptive Modeling
- Monte Carlo simulation: 10,000 simulated orders per scenario
- Tested delivery reductions of 0–10 days below the current average (11.85 days)
- Satisfaction rate remained stable at ~93% across all scenarios

---

## Business Recommendations

1. **Flag orders predicted to exceed 14 delivery days** — trigger proactive customer service outreach before dissatisfaction sets in
2. **Prioritize orders arriving after their estimated delivery date** — the gap between promise and reality is the strongest driver of negative reviews
3. **Apply additional fulfillment oversight to high-value orders (> 150 BRL)** — higher-spending customers have elevated dissatisfaction risk

---

## Tools & Libraries

`Python` · `pandas` · `NumPy` · `scikit-learn` · `matplotlib` · `seaborn` · `SciPy`