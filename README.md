# Marketing-Campaign-Performance----Power-Bi
# Marketing Campaign Performance Dashboard

A single-page Power BI report that tracks the performance of **200,000 marketing campaigns run between January 2021 and December 2021**. It focuses on return on investment (ROI), acquisition cost, click efficiency and conversion, and lets you slice them by campaign type, channel, customer segment, company and location.

- **File:** `Marketing_Campaign_Dashboard.pbix`
- **Page:** 1 page, 1920 × 1080 (Fit to page), dark navy theme (`#04012A` canvas, `#0A0632` panels, `#A06EFF` accents)
- **Base theme:** Fluent 2

---

## What's on the dashboard

### KPI cards (top row)

| KPI | Measure | Definition |
|---|---|---|
| Cost per Click | `Cost per Click` | Total acquisition cost ÷ total clicks |
| Average ROI | `Avg Roi` | Mean of `ROI` across campaigns in the current filter |
| Click-through rate | `CTR %` | Total clicks ÷ total impressions |
| Average Conversion | `Avg Conversion` | Mean of `Conversion_Rate` |
| Acquisition Cost | `Acquision Cost` | Sum of `Acquisition_Cost` |

### Charts

| Visual | Type | Shows |
|---|---|---|
| ROI trend | Stacked area chart | Average ROI by month |
| ROI by Channel | Clustered bar | Average ROI per `Channel_Used` |
| ROI by Customer Segment | Clustered bar | Average ROI per `Customer_Segment` |
| ROI by Campaign Type | Clustered bar | Average ROI per `Campaign_Type` |
| Location table | Table | By `Location`: Avg ROI, Total Campaigns, Avg Acquisition Cost |

### Filters (left panel)

Slicers for **Year**, **Company**, **Channel Used**, **Campaign Type** and **Customer Segment**. All visuals respond to them.

---

## Data model

### Source

- Excel workbook `marketing_campaign_dataset.xlsx`, table `Table1`, loaded via Power Query (import mode) with explicit type changes.
- **Update the file path** in Power Query (*Home → Transform data → Source*) before refreshing on another machine. The path currently points to a local OneDrive folder.

### Tables

**`Fact_Table`** (200,000 rows, one row per campaign)

| Column | Type | Notes |
|---|---|---|
| `Campaign_ID` | Whole number | Unique per campaign |
| `Company` | Text | 5 distinct values |
| `Campaign_Type` | Text | 5 distinct values |
| `Target_Audience` | Text | 5 distinct values |
| `Duration` | Text | 4 distinct values |
| `Channel_Used` | Text | 6 distinct values |
| `Conversion_Rate` | Decimal | |
| `Acquisition_Cost` | Whole number | |
| `ROI` | Decimal | |
| `Location` | Text | 5 distinct values |
| `Language` | Text | 5 distinct values |
| `Clicks` | Whole number | |
| `Impressions` | Whole number | |
| `Engagement_Score` | Whole number | 1–10 scale (10 distinct values) |
| `Customer_Segment` | Text | 5 distinct values |
| `Date` | Date | 365 distinct days |

**`Dim_Date`** (calculated table, 365 rows)

Built in DAX from the min and max of `Fact_Table[Date]`:

```dax
ADDCOLUMNS(
    CALENDAR(MIN('Fact_Table'[Date]), MAX('Fact_Table'[Date])),
    "Year", YEAR([Date]),
    "Month No", MONTH([Date]),
    "Month", FORMAT([Date], "MMMM"),
    "Short Month", FORMAT([Date], "MMM"),
    "Quarter", "Q" & FORMAT([Date], "Q"),
    "Day", DAY([Date]),
    "Day Name", FORMAT([Date], "dddd"),
    "Year-Month", FORMAT([Date], "yyyy-MM")
)
```

The file also contains Power BI's auto-generated `DateTableTemplate_*` and `LocalDateTable_*` helper tables. These are system-generated and can be ignored.

### Measures (all on `Fact_Table`)

```dax
Total Campaigns     = DISTINCTCOUNT(Fact_Table[Campaign_ID])
Total Clicks        = SUM(Fact_Table[Clicks])
Total Impression    = SUM(Fact_Table[Impressions])
Acquision Cost      = SUM(Fact_Table[Acquisition_Cost])
Avg Roi             = AVERAGE(Fact_Table[ROI])
Avg Conversion      = AVERAGE(Fact_Table[Conversion_Rate])
Avg Acquision cost  = AVERAGE(Fact_Table[Acquisition_Cost])
CTR %               = DIVIDE([Total Clicks], [Total Impression])
Cost per Click      = DIVIDE([Acquision Cost], [Total Clicks])
```

> Note: "Acquision" is spelled this way in the measure names (a typo for "Acquisition"). Renaming the measures in Power BI updates the visuals automatically.

---

## How to use

1. Open `Marketing_Campaign_Dashboard.pbix` in **Power BI Desktop** (the file was created with the 2026.10 release; use a recent version).
2. If prompted, point the data source at your copy of `marketing_campaign_dataset.xlsx` and click **Refresh**.
3. Use the slicers on the left to filter. Click a bar or area to cross-filter the other visuals.

---

## Known issues and suggested improvements

- **No relationship between `Dim_Date` and `Fact_Table` was found in the model metadata.** The ROI trend chart uses `Dim_Date[Month]` with a `Fact_Table` measure, and the Year slicer uses `Dim_Date[Year]`. Without a relationship on `Date`, these may not filter the facts as expected. Check *Model view* and, if missing, create a many-to-one relationship `Fact_Table[Date]` → `Dim_Date[Date]`.
- Month labels from `FORMAT(..., "MMMM")` sort alphabetically unless **Sort by column** is set to `Month No`.
- Average ROI is an unweighted mean across campaigns. If you want a spend-weighted ROI, add a measure based on total revenue and total cost.
- Data source is a local file path, so scheduled refresh in the Power BI Service needs a gateway or a move to SharePoint/OneDrive for Business.

---

## Project structure (inside the `.pbix`)

```
DataModel                 # compressed tabular model (tables, measures, Power Query)
Report/definition/        # page layout and visual definitions (PBIR format)
Report/StaticResources/   # theme + background and KPI icon images
```

---

## Author

_Add your name, date and contact or portfolio link here._
