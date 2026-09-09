# Requirements - Pharmacy Analytics Dashboard

---

## 1. Context and stakeholder

**Primary stakeholder:** Pharmacy manager: accountable for revenue, stock and staffing at a single retail location.

**Current situation.** The stock is made by the pharmacist manager. The actual value (on the shelf/drawer) is compared with the written value (management program). It is written by hand and compared on the sheet.
Products that are due to expire in the next 3 months are reported in the management program and the pharmacist manager checks them and signals them on the shelf/drawer with a dot that mentions the month of expiration. Although these products are signaled, nothing is done about their timely release from the pharmacy. 

**The need.** The manual work in this process is time-consuming. There are constant stock errors. Products expire on the shelf/drawer because they are not released from the pharmacy in a timely manner.

---

## 2. Stakeholders

| Stakeholder | Interest | Influence | Authority |
|---|---|---|---|
| Pharmacy manager | High - owns the outcome | High | Approves scope and priorities |
| Pharmacists | Medium - affected by staffing decisions | Medium, informal | None formal |

I would interview the pharmacist manager to find out what questions he is asking himself. Where is the business not going well? What would a solution look like for him?
I would observe or ask the pharmacists what they find difficult now and where the problem is from their point of view.

---

## 3. Business questions

1. How is revenue trending, and which product categories drive it?
2. Which categories generate margin as opposed to volume?
3. When are we busiest - by weekday and hour - and does staffing match?
4. What is the prescription versus over-the-counter mix, and how does it shift seasonally?
5. Which stock is at risk of expiring, and what is that worth?
6. Which products are slow movers tying up capital?
7. How often are medications refused without a valid prescription?
8. What products are almost always sold together?

---

## 4. User stories


### US-01. Revenue and margin by category
As a pharmacy manager, I want to see revenue and margin broken down by product category for any period I choose, so that I can tell which parts of the business are actually profitable rather than just busy.

**Acceptance criteria:**
1. Revenue, cost and margin are shown by month across the selected period
2. Margin is displayed both as an absolute value and as a percentage of revenue
3. The user can filter to one or more product categories
4. The current period can be compared with the same period a year earlier

**Priority:** Must have - this is the question asked most often.

### US-02. Transaction volume by weekday and hour
As a pharmacy manager, I want to see how many transactions occur by weekday and hour, so that I can decide when a second pharmacist is needed on shift.

**Acceptance criteria:**

1. A matrix shows transaction counts across weekday and hour of day
2. The busiest and quietest slots are distinguishable by colour without reading the numbers
3. The view can be narrowed to a chosen date range
4. Average basket value is available alongside transaction count, so volume is not mistaken for value

**Priority:** Should have - improves staffing decisions, but the pharmacy can operate on existing rotas without it.

### US-03. Stock expiring within 90 days
As a pharmacy manager, I want to see which products expire within the next 90 days and what they are worth, so that I can discount or return them before they are written off entirely.

**Acceptance criteria:**

1. Products expiring within 90 days are listed with quantity, expiry date and batch value
2. Stock that has already expired is flagged separately from stock still sellable
3. The total value at risk appears as a single figure at the top of the page
4. The list can be sorted by value and by days remaining

**Priority:** Must have - direct financial impact, since expired stock is written off in full.

### US-04. Prescription versus over-the-counter mix over time
As a pharmacy manager, I want to see how the split between prescription and non-prescription sales shifts across the year, so that I can plan ordering ahead of the seasons when each peaks.

**Acceptance criteria:**

1. The split is shown as a monthly trend, not only as a period total
2. Both revenue and transaction counts are available for each side of the split
3. The view can be broken down further by therapeutic class
4. Two years of data are visible together, so seasonal patterns can be distinguished from one-off changes

**Priority:** Should have - informs ordering, but seasonal patterns are partly known from experience

### US-05. Slow-moving products
As a pharmacy manager, I want to see which products sell slowest relative to the stock held, so that I can stop reordering them and free up the capital.

**Acceptance criteria:**

1. Products are ranked by units sold in the last 90 days against current stock on hand
2. The twenty slowest are shown with the capital value they represent
3. A category can be excluded from the ranking, since seasonal products distort it out of season
4. Products below their reorder level are marked, so genuine slow movers are not confused with items simply out of stock

**Priority:** Could have - useful for purchasing, but the information can be reconstructed manually if needed.

---

## 5. Business glossary

Terms defined so that two people reading the same number understand it the same way.

| Term | Definition |
|---|---|
| **Transaction** | One visit to the till, which may contain several product lines |
| **Line** | One product within a transaction |
| **Revenue** | Sum of line totals, after discount, excluding VAT |
| **Cost** | Purchase cost of goods sold, excluding staff and overheads |
| **Margin** | Revenue minus cost |
| **Rx item** | A product requiring a valid prescription |
| **At-risk stock** | Stock expiring within 90 days and not yet sold |
| **Slow mover** | A product that has sold fewer than five units in the last 90 days, even though there is stock. The threshold should be confirmed with the manager, as it differs by category |

---

## 6. Assumptions and constraints

**Assumptions**
- Cost price in `DimProduct` reflects actual purchase cost. Not verified with purchasing; all margin figures depend on it.
- All transactions reach the system
- The prices in the data are current
- There are no unrecorded returns

**Constraints**
- Inventory is a point-in-time snapshot, not a history, so stock levels cannot be trended.
- No data on the competition, so price is not comparable with the market
- Don't know why the customer came
- Don't have historical stock

**Risk**
- If the cost price assumption is wrong, margin by category is wrong, and any recommendation about product mix built on it is unsafe. I would compare some prices with the invoices from the supplier.

---

## 7. Out of scope

Individual customer identification, prescriber details, clinical outcomes, and any patient-level health data. This reports commercial and operational performance only.

---

## 8. Non-functional requirements

- Data refresh: daily - for catching expiring stock.
- Access: full report restricted to the pharmacy manager. Pharmacists have access to the operations and stock risk pages only, since cost and margin are commercially sensitive while expiry information is operationally useful to them..
- Performance: any page loads within five seconds on a standard laptop.

---

## 9. Success measures

**KPI:** Reduction in the value of stock written off due to expiry. Baseline to be established from the first three months of data, with a target agreed with the manager once the current level is known.

**Metric:** How many times a week the report is opened.

---

## 10. Questions I would ask the stakeholder

1. What is the target margin per category?
2. Who decides the orders?
3. What happens now with expired products?
4. Are there are any constraints from the pharmacy chain?
5. Who else should see the report?

---

## 11. Data sources

| Table | Grain | Key fields |
|---|---|---|
| FactSales | One product line within a transaction | SaleID, Date, Hour, ProductKey, CustomerKey, StaffKey, Quantity, UnitPrice, Discount, LineTotal |
| DimProduct | One product | Category, TherapeuticClass, RequiresRx, UnitCost, UnitPrice |
| DimCustomer | One customer | AgeGroup, Gender, City, LoyaltyMember |
| DimStaff | One staff member | Role |
| DimDate | One day | Year, Month, Weekday, Season |
| Inventory | One product | StockOnHand, ReorderLevel, ExpiryDate, BatchValue |

---

*Document version 1.0 · Ana-Maria Cherecheș*
