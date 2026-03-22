# DAX measures - retail sales dashboard

paste these into Power BI Desktop -> Modeling -> New measure.

## basics

```dax
Total Quantity =
SUM ( fact_sales[quantity] )
```

```dax
Gross Sales =
SUMX (
    fact_sales,
    fact_sales[quantity] * fact_sales[unit_price]
)
```

```dax
Net Sales =
CALCULATE (
    [Gross Sales],
    fact_sales[quantity] > 0
)
```

```dax
Distinct Invoices =
DISTINCTCOUNT ( fact_sales[invoice_id] )
```

```dax
Distinct Customers =
DISTINCTCOUNT ( fact_sales[customer_id] )
```

## todo
- returns / return rate
- profit (need RELATED dim_product[unit_cost])
- time intelligence
- rank / top N
