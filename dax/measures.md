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

## time intelligence

dim_date must be marked as a date table (Modeling -> Mark as date table -> date column).

```dax
Net Sales LY =
CALCULATE ( [Net Sales], SAMEPERIODLASTYEAR ( dim_date[date] ) )
```

```dax
Net Sales YoY =
[Net Sales] - [Net Sales LY]
```

```dax
Net Sales YoY % =
DIVIDE ( [Net Sales YoY], [Net Sales LY] )
```

```dax
Net Sales YTD =
TOTALYTD ( [Net Sales], dim_date[date] )
```

```dax
Net Sales MTD =
TOTALMTD ( [Net Sales], dim_date[date] )
```

```dax
Net Sales 3M Rolling =
CALCULATE (
    [Net Sales],
    DATESINPERIOD ( dim_date[date], MAX ( dim_date[date] ), -3, MONTH )
)
```

## ranking

```dax
Product Rank by Sales =
RANKX (
    ALL ( dim_product[product_name] ),
    [Net Sales],
    ,
    DESC,
    DENSE
)
```

```dax
Top 10 Products Sales =
IF ( [Product Rank by Sales] <= 10, [Net Sales], BLANK () )
```

```dax
Customer Rank =
RANKX (
    ALL ( dim_customer[customer_id] ),
    [Net Sales],
    ,
    DESC,
    DENSE
)
```

## todo
- pbix file + screenshots
- maybe a what-if parameter for discount %

## scratch - discount what-if (wip, not sure about this yet)

```dax
Discount % = SELECTEDVALUE ( 'Discount'[Discount %], 0 )

Net Sales w Discount =
[Net Sales] * ( 1 - [Discount %] / 100 )
```
