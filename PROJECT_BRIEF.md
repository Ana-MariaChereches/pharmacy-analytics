# Pharmacy Analytics Dashboard — Project Brief

*Portfolio project. All data is synthetic and generated for this exercise. No real patient or transaction data is used.*

---

## Part 1 — The requirements document

This is the part that separates your project from most junior portfolios. Write it before you open Power BI, and publish it alongside the dashboard.

### Stakeholder and context

**Stakeholder:** the pharmacy manager, responsible for revenue, stock and staffing across a single retail location.

**Current situation:** sales data sits in the till system and stock data in a separate spreadsheet. Reviewing performance means exporting both and reconciling them by hand, which happens roughly once a month and takes half a day.

**Need:** a single view that answers recurring questions without manual export, so decisions about ordering, staffing and product mix can be made weekly rather than monthly.

### Business questions the dashboard must answer

1. How is revenue trending, and which product categories drive it?
2. Which categories generate margin as opposed to volume?
3. When are we busiest — by day of week and hour — and does staffing match?
4. What is the prescription versus over-the-counter mix, and how does it shift seasonally?
5. Which stock is at risk of expiring, and what is that worth?
6. Which products are slow movers tying up capital?

### User stories with acceptance criteria

**US-01** — As a pharmacy manager, I want to see revenue and margin by month and category, so that I can identify which parts of the business are growing.
*Acceptance criteria:* revenue, cost and margin are shown by month for the selected period; the user can filter to one or more categories; margin percentage is displayed alongside absolute margin.

**US-02** — As a pharmacy manager, I want to see transaction volume by day of week and hour, so that I can plan staffing.
*Acceptance criteria:* a matrix or heatmap shows transaction counts across weekday and hour; the busiest and quietest slots are visually distinguishable without reading the numbers.

**US-03** — As a pharmacy manager, I want to see which stock will expire within 90 days and its value, so that I can act before it is written off.
*Acceptance criteria:* products expiring within 90 days are listed with quantity, expiry date and batch value; already-expired stock is flagged separately; total value at risk is shown as a single figure.

**US-04** — As a pharmacy manager, I want to compare prescription and over-the-counter sales over time, so that I understand how the business mix is changing.
*Acceptance criteria:* the split is shown as a trend over months, not only as a total; seasonal shifts are visible.

**US-05** — As a pharmacy manager, I want to see which products sell slowest relative to stock held, so that I can reduce future orders.
*Acceptance criteria:* products are ranked by units sold against stock on hand; the slowest twenty are shown with the capital they represent.

### Data sources

| Table | Grain | Notes |
|---|---|---|
| FactSales | One row per product line within a transaction | Includes hour, discount, prescription flag |
| DimProduct | One row per product | Category, therapeutic class, cost and price |
| DimCustomer | One row per customer | Anonymised: age group, gender, city, loyalty flag |
| DimStaff | One row per staff member | Role only |
| DimDate | One row per day | Includes weekday, month, season |
| Inventory | One row per product | Stock on hand, reorder level, expiry date |

### Out of scope

Individual customer identification, prescriber details, medical outcomes, and any patient-level clinical data. The dashboard reports commercial and operational performance only.

---

## Part 2 — Building it

### Data model

Star schema. FactSales in the centre, dimensions around it.

| Relationship | Cardinality | Direction |
|---|---|---|
| DimDate[Date] → FactSales[Date] | 1 to many | Single |
| DimProduct[ProductKey] → FactSales[ProductKey] | 1 to many | Single |
| DimCustomer[CustomerKey] → FactSales[CustomerKey] | 1 to many | Single |
| DimStaff[StaffKey] → FactSales[StaffKey] | 1 to many | Single |
| DimProduct[ProductKey] → Inventory[ProductKey] | 1 to 1 | Single |

Mark **DimDate** as the date table (Table tools → Mark as date table). Hide the key columns in the fact table from report view — a clean field list is part of the deliverable.

### Core measures

```
Revenue = SUM(FactSales[LineTotal])

Cost = SUMX(FactSales, FactSales[Quantity] * RELATED(DimProduct[UnitCost]))

Margin = [Revenue] - [Cost]

Margin % = DIVIDE([Margin], [Revenue])

Transactions = DISTINCTCOUNT(FactSales[SaleID])

Average Basket = DIVIDE([Revenue], [Transactions])

Items per Basket = DIVIDE(SUM(FactSales[Quantity]), [Transactions])

Rx Revenue = CALCULATE([Revenue], FactSales[PrescriptionRequired] = 1)

Rx Share % = DIVIDE([Rx Revenue], [Revenue])

Revenue PY = CALCULATE([Revenue], SAMEPERIODLASTYEAR(DimDate[Date]))

Revenue YoY % = DIVIDE([Revenue] - [Revenue PY], [Revenue PY])

Stock Value = SUMX(Inventory, Inventory[StockOnHand] * Inventory[UnitCost])

Value Expiring 90d =
CALCULATE(
    [Stock Value],
    FILTER(Inventory,
        Inventory[ExpiryDate] <= TODAY() + 90 &&
        Inventory[ExpiryDate] > TODAY())
)

Expired Stock Value =
CALCULATE([Stock Value], FILTER(Inventory, Inventory[ExpiryDate] <= TODAY()))
```

### Suggested pages

**Page 1 — Performance overview.** Cards for revenue, margin %, transactions, average basket, each with year-on-year change. Revenue trend by month with previous year overlaid. Revenue by category. Top ten products by margin. Slicers for date, category, city.

**Page 2 — Operations.** Heatmap of transactions by weekday and hour. Revenue by staff member. Average basket by hour. This page answers the staffing question.

**Page 3 — Product mix.** Rx versus OTC versus supplement trend over time. Therapeutic class breakdown with the seasonal pattern visible — respiratory peaks in winter, antihistamines in spring. Margin percentage by category, which will show that supplements carry better margin than prescription items.

**Page 4 — Stock risk.** Value expiring within 90 days as a headline figure. Table of at-risk products with days remaining. Slow movers: units sold against stock held. Products below reorder level.

### Design notes for the UI/UX angle

The job advert mentions front-end design and UI/UX explicitly, so treat this as part of the deliverable rather than decoration.

Pick one accent colour and use grey for everything else, so the accent means something. Put the most important number top left, since that is where the eye lands. Keep to four or five visuals per page — a crowded page reads as a data dump, not analysis. Write titles that state the finding rather than the field name: "Respiratory sales triple in winter" rather than "Revenue by month and class". Make sure every filter is visible; hidden slicers confuse people who did not build the report.

---

## Part 3 — What to publish

Put three things in one place, ideally a short GitHub repository or a LinkedIn post with images:

1. The requirements document above, lightly edited into your own words
2. Screenshots of each dashboard page
3. A short note on what you found — the seasonal pattern, the margin difference between supplements and prescription items, the stock at risk

That third item matters most. A dashboard shows you can build. A finding shows you can analyse.

---

## Interview material this gives you

- *"Walk me through a project."* You have requirements, a model, a build and a finding.
- *"How do you handle ambiguous requirements?"* You wrote user stories with acceptance criteria before building.
- *"Give an example of translating business needs into a deliverable."* This is exactly that, and it is the first line under "what makes you stand out" in the advert.
- *"What would you do differently?"* Have an answer ready — for instance, that you would validate the margin assumptions with whoever owns purchasing, since cost price in the data is a simplification.
