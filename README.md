# 📊 Credit Note Analysis Dashboard — Power BI

> **Finance Analytics Project | Camfil A/S (anonymized)**  
> Built by: Michael Schannong | Finance & BI Analyst | D365 F&O · Power BI · Power Query

---

## 🎯 Project Overview

This Power BI dashboard was developed to replace a fully manual credit note reporting process at a global industrial filtration company. The solution provides systematic analysis of credit note patterns, enabling proactive customer follow-up and root cause identification.

**The dashboard is actively used weekly by CEO and CFO.**

Before this solution existed, credit note data had to be printed manually — there was no systematic way to identify trends, root causes, or which customers generated the most credit notes.

---

## 🔍 Business Problem

The company had no structured overview of:
- Which customers generated the most credit notes
- Whether errors were caused by the company or the customer
- What the underlying reasons were (discount errors, admin issues, warehouse mistakes, etc.)
- How credit note volume trended over time and by week

This led to reactive customer handling and no data-driven basis for corrective action.

---

## 💡 Solution

A multi-page Power BI dashboard built from scratch — from initial idea to working prototype in **3 months**, with continuous refinement over the following 7 months.

The solution was later scaled by a global BI team, who connected the data model to a **SQL server** for automated refresh across the organization.

---

## 📐 Data Architecture

```
D365 F&O (ERP)
    └── Manual Excel export (initial phase)
    └── SQL Server connection (scaled phase)
            └── Power Query (M) — data transformation & cleaning
                    └── Power BI Data Model
                            └── DAX Measures
                                    └── Dashboard Visualizations
```

### Data Sources
| Source | Description |
|--------|-------------|
| D365 F&O | Credit note transactions, customer data, invoice data |
| RMA Module | Return merchandise authorization data |
| Manual correction table | Excel-based lookup for missing/incorrect codes |

> ⚠️ **Note:** All data in this repository is **dummy/anonymized data** for demonstration purposes only.

---

## 📊 Dashboard Pages

### Page 1 — Credit Note Overview (2025)
- Total credit notes by month
- Breakdown by reason category: Dekort · Admin · Warehouse · Transport · Product defect
- Return volume by week number (trend analysis)
- KPI cards: total count and total DKK value

### Page 2 — All Years Historical View
- Multi-year comparison (2022–2025)
- Year-over-year trend analysis
- Reason category distribution across all years
- Pie chart: proportion of each error type

---

## 📏 DAX Measures

```dax
-- Total Credit Notes
Total Kreditnotat = COUNTROWS('CreditNoteTable')

-- Total DKK Value
Total DKK = SUM('CreditNoteTable'[Amount])

-- Dekort % of Total
Dekort % = 
DIVIDE(
    CALCULATE(COUNTROWS('CreditNoteTable'), 'CreditNoteTable'[Reason] = "Dekort"),
    [Total Kreditnotat],
    0
)

-- YoY Change
YoY Credit Notes = 
VAR CurrentYear = [Total Kreditnotat]
VAR PreviousYear = CALCULATE([Total Kreditnotat], SAMEPERIODLASTYEAR('DateTable'[Date]))
RETURN DIVIDE(CurrentYear - PreviousYear, PreviousYear, 0)
```

---

## 🔄 Power Query Transformations (M)

Key transformation steps applied:

```m
// Remove error rows and null values
= Table.SelectRows(Source, each [InvoiceAmount] <> null and [InvoiceAmount] <> 0)

// Normalize reason categories (trim + uppercase for consistent matching)
= Table.TransformColumns(Source, {{"ReasonCode", each Text.Upper(Text.Trim(_))}})

// Add missing code correction via lookup table
= Table.NestedJoin(Source, {"CustomerCode"}, CorrectionTable, {"CustomerCode"}, "Correction", JoinKind.LeftOuter)

// Extract date parts for time intelligence
= Table.AddColumn(Source, "WeekNumber", each Date.WeekOfYear([InvoiceDate]))
```

---

## 🏗️ Data Model

```
DateTable (1) ──────── (*) CreditNoteTable
                              |
CustomerTable (1) ──── (*) CreditNoteTable
                              |
ReasonCodeTable (1) ── (*) CreditNoteTable
```

**Model type:** Star schema  
**Relationships:** Single direction, many-to-one

---

## ✅ Business Impact

| Before | After |
|--------|-------|
| Manual printing of reports | Automated dashboard refresh |
| No error root cause analysis | Breakdown by 5 reason categories |
| Reactive customer handling | Proactive follow-up based on data |
| No historical trending | Multi-year trend analysis |
| No executive access | CEO and CFO weekly usage |

**First systematic customer follow-up** was conducted ~10 months after project initiation, resulting in positive customer feedback and stamdata corrections in the ERP system.

---

## 🛠️ Technologies Used

| Technology | Usage |
|------------|-------|
| Microsoft Power BI | Dashboard, visualizations, DAX |
| Power Query (M) | Data transformation, cleaning, loading |
| D365 F&O | Source ERP system |
| SQL Server | Production data connection (scaled phase) |
| Excel | Initial data extraction + manual correction table |

---

## 📁 Repository Structure

```
credit-note-analysis-powerbi/
├── README.md
├── dashboard/
│   └── CreditNote_Dashboard_DEMO.pbix      # Power BI file with dummy data
├── data/
│   └── dummy_creditnotes.xlsx              # Sample data structure
├── screenshots/
│   ├── page1_2025_overview.png
│   └── page2_all_years.png
└── docs/
    └── data_model.png                      # Star schema diagram
```

---

## 🚀 How to Use

1. Clone or download this repository
2. Open `CreditNote_Dashboard_DEMO.pbix` in Power BI Desktop
3. Data is pre-loaded with dummy data — no connection required
4. To connect your own data: update the Power Query source in `Transform Data`

---

## 👤 Author

**Michael Schannong**  
Finance Data Analyst | D365 F&O Superbruger | Power BI & Copilot Studio

- 🔗 LinkedIn: [linkedin.com/in/schannong](https://www.linkedin.com/in/schannong)
- 💻 GitHub: [github.com/MichaelSchannong](https://github.com/MichaelSchannong)
- 📧 misc@netassist.dk

---

*This project uses anonymized/dummy data. No confidential company information is included.*
