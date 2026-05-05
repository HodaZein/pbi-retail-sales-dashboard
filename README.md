# pbi-retail-sales-dashboard

power bi dashboard on a small synthetic retail dataset (online-retail-style). practising end-to-end: Power Query for ingest, star schema model, DAX measures, then a multi-page report.

This repo is the source data + the M + the DAX in plain text so it's reviewable without opening the .pbix.

## what's here

- `data/` - five small CSVs (1 fact + 4 dims, ~15k fact rows)
- `model/` - the star schema description
- `power-query/transforms.md` - the M code per query, paste-ready
- `dax/measures.md` - all measures grouped by topic
- `docs/` - dashboard screenshots (page-by-page)
- `*.pbix` - **not yet committed** (built locally on a windows machine, todo: publish to web)

## the report (3 pages, planned)

1. **Overview** - KPI cards (Net Sales, Gross Margin %, AOV, Distinct Customers), monthly trend with YoY, top 10 products bar
2. **Geography** - revenue by region/country, return rate by country, world map visual
3. **Customers** - new vs returning, top customers table with conditional formatting

slicers: year, quarter, region, product category.

## measure highlights (full list in `dax/measures.md`)

- Net Sales, COGS, Gross Profit, Gross Margin %
- Net Sales LY / YoY % / YTD / MTD / 3M Rolling
- Return Rate %, AOV, Distinct Customers
- Product Rank by Sales (RANKX)

## status
- [x] data model + star schema decided
- [x] Power Query transforms drafted
- [x] core DAX measures written + documented
- [ ] build the pbix on windows, take screenshots
- [ ] publish to web (free) and add link
- [ ] add the customer/RFM page

## related
the same dataset in pure SQL + pandas: https://github.com/HodaZein/retail-sales-sql-analysis (with a [live eda report](https://hodazein.github.io/retail-sales-sql-analysis/))
