# 🇮🇳 Indian E-Commerce Executive Performance Dashboard — Power BI

An executive-style Power BI portfolio project for Indian retail/e-commerce analytics using INR (₹). The project demonstrates **star-schema modeling, DAX, time intelligence, customer segmentation, return diagnostics, and executive recommendations**.

> **Important:** This repository contains the data model, source data, DAX, documentation, and QA evidence. The original ZIP did **not** contain a `.pbix` Power BI report file, so the visual canvas itself could not be audited or repaired here.

---

## 🎯 Business Problem

**How is the business performing across India, where are returns and discounting creating operational/profit pressure, and what should management do next?**

The analysis focuses on:
- Revenue and order performance
- Customer retention/repeat purchasing
- Product/category profitability
- Returns and reverse-logistics pressure
- Regional performance
- Festive-season demand
- Discount-depth economics

---

## 📊 Validated Dataset Snapshot

The KPI definitions below are based on **Delivered** order items, which matches the business logic used by the original README.

| KPI | Validated value |
|---|---:|
| Net Revenue | ₹4.69 Cr |
| Delivered Orders | 13,370 |
| Delivered Customers | 3,010 |
| AOV | ₹3,506.97 |
| Gross Profit | ₹2.71 Cr |
| Gross Margin | 57.87% |
| Return Rate | 9.26% |
| Repeat Customer Rate | 65.15% |
| Champions' Revenue Share | 59.07% |

The source data contains **22,049 order-item rows**, covering **2023-01-29 to 2024-12-31**.

### YoY caveat

The data supports a reported 2024-vs-2023 net-revenue increase of approximately **289.5%**. This is very different from the original README's `+18.4%`, which was not reproducible from the supplied data.

Because the transaction history begins on **2023-01-29**, YoY comparisons should be presented with a data-coverage caveat. Do **not** claim `+18.4%` from this dataset.

---

## 🔎 Key Findings Supported by the Data

### Regional demand
Maharashtra and Karnataka are the two largest states by delivered net revenue.

### Return bottleneck
**Apparel & Ethnic Wear** has an approximately **18.1% item return rate**, materially above the overall delivered-item return rate of 9.26%.

### Payment-method signal
COD has the highest delivered-item return rate among the payment methods in the supplied data. This supports investigating COD/RTO friction, but the dataset is observational and does **not** prove that COD causes returns.

### Discount economics
Items with discounts above 20% have an aggregate gross margin of approximately **49.8%** in the supplied data. This supports testing discount guardrails, but a causal claim that discounts *cause* the margin decline should not be made without a controlled or otherwise robust causal analysis.

### Customer concentration
Champions account for approximately **59.1% of delivered net revenue**, indicating meaningful revenue concentration in the highest-value customer segment.

### Festive season
The supplied fact-level festive flag agrees with the October/November Diwali-season flag in `dim_date`.

---

## 🧠 Management Recommendations

1. **Test discount guardrails** rather than declaring a universal 15% maximum. Evaluate margin and conversion by category, SKU, and customer segment.
2. **Investigate COD/RTO friction** using return rate, order status, payment method, geography, and category together. Consider prepaid incentives only after estimating the incremental conversion and margin impact.
3. **Prioritize Apparel return reduction** through better size charts, fit information, product imagery, and SKU-level return reason tracking.
4. **Protect Champions** with retention/personalization initiatives because this segment contributes a disproportionate share of revenue.
5. **Monitor festive-season operations** because October/November show pronounced revenue peaks.

---

## 🧱 Star Schema

### Fact
- `fact_sales` — one row per order item

### Dimensions
- `dim_customers` — customer, geography, and RFM attributes
- `dim_products` — product/category/pricing attributes
- `dim_date` — calendar and festive-season attributes
- `dim_geography` — state-to-zone mapping

### Recommended relationships

```text
dim_date[date]            1 ──── * fact_sales[order_date]
dim_products[product_id]  1 ──── * fact_sales[product_id]
dim_customers[customer_id]1 ──── * fact_sales[customer_id]
dim_geography[state]      1 ──── * dim_customers[state]
```

Use **single-direction filtering from dimensions to fact**.

Mark `dim_date` as the Power BI **Date table** using `dim_date[date]`.

---

## ⚙️ DAX

See:

`dax/calculated_measures.dax`

The corrected measures:
- Apply the project's **Delivered** KPI definition consistently
- Calculate repeat customers using **distinct delivered orders**, not line-item quantity
- Return percentages as ratios so Power BI can format them correctly as `%`
- Add operational return/processing measures
- Preserve time intelligence using `dim_date`

---

## 📁 Repository Structure

```text
ecommerce_india_powerbi_dashboard/
│
├── data/
│   ├── fact_sales.csv
│   ├── dim_customers.csv
│   ├── dim_products.csv
│   ├── dim_date.csv
│   └── dim_geography.csv
│
├── dax/
│   └── calculated_measures.dax
│
├── AUDIT_REPORT.md
├── POWER_BI_SETUP.md
├── requirements.txt
├── .gitignore
└── README.md
```

---

## 🛠️ Power BI Page Architecture

### Page 1 — Executive Overview
- Net Revenue
- Orders
- Customers
- AOV
- Gross Profit
- Gross Margin
- Return Rate
- Monthly revenue trend
- Revenue by state
- Revenue by category
- AOV by zone

### Page 2 — Customer Intelligence
- Repeat Customer Rate
- New vs Repeat customers
- RFM segment revenue
- Customer count by RFM segment
- Revenue concentration of Champions
- Zone/customer drill-through

### Page 3 — Product Performance
- Revenue by category/SKU
- Gross profit by SKU
- Gross margin by category
- Discount-depth vs margin
- Return rate by category/product

### Page 4 — Management Insights
Use evidence-backed callouts:
- What happened?
- Where is the problem?
- What is the likely driver?
- What should management test next?
- What metric should be monitored?

Avoid presenting correlation as causation.

---

## ⚠️ Data / Modeling Notes

- `fact_sales` contains denormalized `category` and `retail_price_inr` fields that also exist in `dim_products`. In Power BI, use the dimension attributes for slicing/grouping to maintain a clean star-schema design.
- `dim_geography.csv` has been added because the original README referenced it but the file was missing.
- `order_status` contains `Delivered` and `Processing`. KPI measures intentionally use `Delivered`.
- Returned delivered items remain present in `net_revenue_inr`; return impact is separately visible through `is_returned` and the stored `gross_profit_inr` values. Do not describe the KPI as "revenue after refunds" unless the source data is redesigned for refund accounting.
- The data is synthetic/portfolio data unless an external provenance is added. Do not present the findings as real Indian e-commerce market statistics.

---

## 🚀 How to Use

1. Open Power BI Desktop.
2. Load all five CSVs from `data/`.
3. Set data types:
   - `dim_date[date]` → Date
   - `fact_sales[order_date]` → Date
   - Currency fields → Decimal number
   - `discount_rate` → Decimal number
   - `is_returned` → Whole number / Boolean as appropriate
4. Create the relationships shown above.
5. Mark `dim_date` as the Date table.
6. Add the measures from `dax/calculated_measures.dax`.
7. Format ₹ measures as Indian currency and percentage measures as `%`.
8. Build the four-page report using the recommended architecture.
9. Add a visible **Data Coverage / Caveats** note to the executive page.

---

## 📌 Portfolio Positioning

This project is strongest when presented as a **business analytics case study**, not just a dashboard screenshot. In interviews, emphasize:
- Metric definition
- Data validation
- Star-schema modeling
- DAX design
- Customer segmentation
- Return/discount diagnostics
- Business recommendations
- Limitations and causal caveats
