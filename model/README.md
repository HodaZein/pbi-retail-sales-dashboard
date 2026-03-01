# data model

star schema: `fact_sales` in the middle, four dimension tables.

```
                  +-------------+
                  |  dim_date   |
                  +-------------+
                         |
                         | (date)
                         v
+------------+    +-------------+    +-------------+
| dim_country|<---| dim_customer|<---| fact_sales  |--->| dim_product |
+------------+    +-------------+    +-------------+    +-------------+
                                                              ^
                                                              | (product_id)
                                                              |
                                            (relationships single-direction, many-to-one)
```

## tables

### fact_sales (~15k rows)
| column      | type    | notes                          |
|-------------|---------|--------------------------------|
| invoice_id  | text    | one row per invoice line       |
| date        | date    | -> dim_date                    |
| customer_id | int     | -> dim_customer                |
| product_id  | text    | -> dim_product                 |
| quantity    | int     | negative = return              |
| unit_price  | decimal | per-unit price at time of sale |

### dim_date
generated 2024-01-01 to 2025-12-31. mark as date table.

### dim_product
15 products across 6 categories (Tableware, Decor, Party, Bags, Seasonal, Kitchen). includes `unit_cost` for margin calcs.

### dim_customer
800 customers with country + signup_date.

### dim_country
12 countries grouped into 5 regions (UK, EU-DACH, EU-West, EU-South, EU-Nordics, APAC).

## why this layout

- a single fact keeps the star simple, easy to slice
- date dim separate so time intelligence DAX (`SAMEPERIODLASTYEAR`, `TOTALYTD` etc) works cleanly
- country pulled out of customer because we want region-level rollups without polluting the customer table
- all relationships single-direction: avoids unintended filter propagation, easier to debug

## things I'd reconsider

- merging `dim_country` into `dim_customer` is fine if we don't add a country-only fact later
- `unit_cost` on the product is a snapshot - if cost changes over time we'd need a slowly-changing-dimension (SCD2) instead. probably overkill for this scope.
