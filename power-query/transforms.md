# Power Query (M) - load + transform

each block below = one query in Power Query Editor (Home -> Advanced Editor).
source assumed to be the local `data/` folder, change `Folder.Files(...)` path or swap to `Csv.Document(File.Contents(...))` if loading from a single web URL.

## fact_sales

```m
let
    Source = Csv.Document(
        File.Contents("C:\path\to\repo\data\fact_sales.csv"),
        [Delimiter = ",", Encoding = 65001, QuoteStyle = QuoteStyle.Csv]
    ),
    Promoted = Table.PromoteHeaders(Source, [PromoteAllScalars = true]),
    Typed = Table.TransformColumnTypes(Promoted, {
        {"invoice_id",  type text},
        {"date",        type date},
        {"customer_id", Int64.Type},
        {"product_id",  type text},
        {"quantity",    Int64.Type},
        {"unit_price",  type number}
    }),
    AddedRevenue = Table.AddColumn(Typed, "line_revenue",
        each [quantity] * [unit_price], type number),
    AddedReturnFlag = Table.AddColumn(AddedRevenue, "is_return",
        each [quantity] < 0, type logical)
in
    AddedReturnFlag
```

## dim_date

```m
let
    Source = Csv.Document(
        File.Contents("C:\path\to\repo\data\dim_date.csv"),
        [Delimiter = ",", Encoding = 65001, QuoteStyle = QuoteStyle.Csv]
    ),
    Promoted = Table.PromoteHeaders(Source, [PromoteAllScalars = true]),
    Typed = Table.TransformColumnTypes(Promoted, {
        {"date",        type date},
        {"year",        Int64.Type},
        {"quarter",     type text},
        {"month",       Int64.Type},
        {"month_name",  type text},
        {"day",         Int64.Type},
        {"weekday",     type text}
    })
in
    Typed
```

mark this table as a Date table in Power BI: Modeling -> Mark as Date Table -> column = `date`.

## dim_product

```m
let
    Source = Csv.Document(
        File.Contents("C:\path\to\repo\data\dim_product.csv"),
        [Delimiter = ",", Encoding = 65001, QuoteStyle = QuoteStyle.Csv]
    ),
    Promoted = Table.PromoteHeaders(Source, [PromoteAllScalars = true]),
    Typed = Table.TransformColumnTypes(Promoted, {
        {"product_id",   type text},
        {"product_name", type text},
        {"category",     type text},
        {"unit_cost",    type number}
    })
in
    Typed
```

## dim_customer

```m
let
    Source = Csv.Document(
        File.Contents("C:\path\to\repo\data\dim_customer.csv"),
        [Delimiter = ",", Encoding = 65001, QuoteStyle = QuoteStyle.Csv]
    ),
    Promoted = Table.PromoteHeaders(Source, [PromoteAllScalars = true]),
    Typed = Table.TransformColumnTypes(Promoted, {
        {"customer_id", Int64.Type},
        {"country",     type text},
        {"signup_date", type date}
    })
in
    Typed
```

## dim_country

```m
let
    Source = Csv.Document(
        File.Contents("C:\path\to\repo\data\dim_country.csv"),
        [Delimiter = ",", Encoding = 65001, QuoteStyle = QuoteStyle.Csv]
    ),
    Promoted = Table.PromoteHeaders(Source, [PromoteAllScalars = true]),
    Typed = Table.TransformColumnTypes(Promoted, {
        {"country", type text},
        {"region",  type text}
    })
in
    Typed
```

## relationships (set in Model view)

- `fact_sales[date]`        ->  `dim_date[date]`        (many-to-one, single)
- `fact_sales[product_id]`  ->  `dim_product[product_id]` (many-to-one, single)
- `fact_sales[customer_id]` ->  `dim_customer[customer_id]` (many-to-one, single)
- `dim_customer[country]`   ->  `dim_country[country]`  (many-to-one, single)

basic star schema, fact in the middle, no bidirectional cross-filter (yet).
