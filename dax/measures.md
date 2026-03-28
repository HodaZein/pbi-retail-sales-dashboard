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

## returns

a return is a row with negative quantity (online-retail convention).

```dax
Returned Quantity =
CALCULATE (
    SUMX ( fact_sales, -fact_sales[quantity] ),
    fact_sales[quantity] < 0
)
```

```dax
Return Value =
CALCULATE (
    SUMX ( fact_sales, -fact_sales[quantity] * fact_sales[unit_price] ),
    fact_sales[quantity] < 0
)
```

```dax
Return Rate % =
DIVIDE ( [Returned Quantity], [Total Quantity] + [Returned Quantity] )
```

## profit

needs `unit_cost` from dim_product (RELATED).

```dax
COGS =
SUMX (
    fact_sales,
    fact_sales[quantity] * RELATED ( dim_product[unit_cost] )
)
```

```dax
Gross Profit =
[Net Sales] - [COGS]
```

```dax
Gross Margin % =
DIVIDE ( [Gross Profit], [Net Sales] )
```

```dax
AOV =
DIVIDE ( [Net Sales], [Distinct Invoices] )
```

## todo
- time intelligence
- rank / top N
