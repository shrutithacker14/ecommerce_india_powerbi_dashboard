# Power BI Setup Checklist

## 1. Import

Load:
- `data/fact_sales.csv`
- `data/dim_customers.csv`
- `data/dim_products.csv`
- `data/dim_date.csv`
- `data/dim_geography.csv`

## 2. Relationships

Create these one-to-many relationships:

- `dim_date[date]` → `fact_sales[order_date]`
- `dim_products[product_id]` → `fact_sales[product_id]`
- `dim_customers[customer_id]` → `fact_sales[customer_id]`
- `dim_geography[state]` → `dim_customers[state]`

Cross-filter direction should be **Single** from the dimension to the fact (and from geography to customer).

## 3. Date table

In Power BI:
**Table tools → Mark as date table → `dim_date[date]`**

## 4. Formatting

Currency measures:
- ₹ with Indian grouping
- 0 or 2 decimal places depending on visual

Percentage measures:
- Gross Margin (%)
- Return Rate (%)
- YoY Revenue Growth (%)
- Repeat Customer Rate (%)

Format as **Percentage**, not a numeric value multiplied by 100.

## 5. Recommended slicers

- Year
- Month
- Zone
- State
- Category
- Payment Method
- RFM Segment

Avoid excessive slicers on the executive page.

## 6. QA before publishing

Check:
- KPI totals match the README validated snapshot.
- Date slicers change YoY correctly.
- State/category filters propagate to all visuals.
- Customer drill-through respects the selected customer.
- Returned-item visuals use `is_returned`.
- Processing orders do not silently enter Delivered KPIs.
