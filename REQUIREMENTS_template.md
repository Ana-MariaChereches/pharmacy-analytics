# Requirements — Pharmacy Analytics Dashboard

> **How to use this file.** Sections marked ✏️ need to be written in your own words — those are the ones an interviewer will ask you about, and copied text shows. The rest are factual and can stay as they are. Delete these instruction lines before publishing.

---

## 1. Context and stakeholder

**Primary stakeholder:** Pharmacy manager — accountable for revenue, stock and staffing at a single retail location.

✏️ **Current situation.** *Two or three sentences on how reporting works today and why it is a problem. Draw on what you actually saw: exports, spreadsheets, how often anyone looked at the numbers, what nobody had time to check. Specifics are what make this credible.*

✏️ **The need.** *One sentence. What is missing, not what should be built. Resist naming the dashboard here — the need exists whether or not anyone builds one.*

---

## 2. Stakeholders

| Stakeholder | Interest | Influence | Authority |
|---|---|---|---|
| Pharmacy manager | High — owns the outcome | High | Approves scope and priorities |
| Pharmacists | Medium — affected by staffing decisions | Medium, informal | None formal |
| Accounting | Medium — margin and stock value | Low | None |

✏️ *Add a line on how you would engage each one, if this were real. Who would you interview, who would you simply keep informed?*

---

## 3. Business questions

1. How is revenue trending, and which product categories drive it?
2. Which categories generate margin as opposed to volume?
3. When are we busiest — by weekday and hour — and does staffing match?
4. What is the prescription versus over-the-counter mix, and how does it shift seasonally?
5. Which stock is at risk of expiring, and what is that worth?
6. Which products are slow movers tying up capital?

✏️ *Add a seventh question of your own. Something only someone who has worked in a pharmacy would think to ask.*

---

## 4. User stories

Each story follows: **As a** [role], **I want** [capability], **so that** [outcome].

### US-01 — Revenue and margin by category
✏️ *Write the story and three acceptance criteria.*

**Priority:** Must have — this is the question asked most often.

### US-02 — Transaction volume by weekday and hour
✏️ *Write the story and acceptance criteria.*

**Priority:** Should have.

### US-03 — Stock expiring within 90 days
✏️ *Write the story and acceptance criteria.*

**Priority:** Must have — direct financial impact; expired stock is written off entirely.

### US-04 — Prescription versus over-the-counter mix over time
✏️ *Write the story and acceptance criteria.*

**Priority:** Should have.

### US-05 — Slow-moving products
✏️ *Write the story and acceptance criteria.*

**Priority:** Could have.

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
| **Slow mover** | ✏️ *Define this one yourself — and note that the threshold is a judgement, not a fact* |

---

## 6. Assumptions and constraints

**Assumptions**
- Cost price in `DimProduct` reflects actual purchase cost. Not verified with purchasing; all margin figures depend on it.
- ✏️ *Add one more assumption you are aware you are making.*

**Constraints**
- Inventory is a point-in-time snapshot, not a history, so stock levels cannot be trended.
- ✏️ *Add one more constraint.*

**Risk**
- If the cost price assumption is wrong, margin by category is wrong, and any recommendation about product mix built on it is unsafe. ✏️ *Note how you would validate it.*

---

## 7. Out of scope

Individual customer identification, prescriber details, clinical outcomes, and any patient-level health data. This reports commercial and operational performance only.

---

## 8. Non-functional requirements

- Data refresh: ✏️ *how often would this need to update, and why that frequency?*
- Access: restricted to the pharmacy manager and accounting.
- Performance: any page loads within five seconds on a standard laptop.

---

## 9. Success measures

**KPI:** ✏️ *One measure tied to the outcome the stakeholder wants — not to dashboard usage.*

**Metric:** ✏️ *One measure of activity, so you can tell whether it is being used at all.*

---

## 10. Questions I would ask the stakeholder

✏️ *Five questions you could not answer from the data alone and would need to ask. This section shows you know the difference between requirements you validated and assumptions you made — write it honestly.*

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
