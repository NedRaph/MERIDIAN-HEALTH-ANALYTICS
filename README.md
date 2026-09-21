# MERIDIAN-HEALTH-ANALYTICS
## Healthcare Facility Performance Analytics

![Financial performance](images/Financial%20Performance%20Dashboard.png)

![Operational performance](images/Operational%20Performance.png)

End-to-end data cleaning, business-rule validation, and two-page Power BI dashboard (Financial Performance & Operational Performance) built on a synthetic healthcare facility dataset consisting of five relational tables: **Patients, Visits, Diagnoses, Billing, and Staff**.

This project simulates a real-world analyst workflow: raw data → business rule definition → data quality validation → reconciliation → dashboard build → stakeholder recommendations.

---

## 1. Project Objective

In this project, i assumed the role of Data Analytics Manager for this healthcare facility, the goal was to answer 10 core business questions spanning financial and operational performance, while establishing and enforcing data quality standards before any analysis was trusted.

**Financial questions covered:**
- What is our outstanding balance exposure by insurance type, and how much is realistically collectible?
- Which departments generate the most revenue?
- How does revenue trend over time, and how is it distributed across visit types?

**Operational questions covered:**
- What are our visit volumes and trends by department and over time?
- What are our peak ER hours, and is staffing aligned to them?
- What are our top diagnoses by frequency, and does specialty staffing match demand?

---

## 2. Data Quality Business Rules

Before any cleaning was performed, business rules were defined for each table to establish what "clean" actually means in this context (full rule set documented separately in `data_quality_business_rules.md`). Key rules included:

- `patient_id`, `visit_id`, `physician_id` must be unique and never null (primary keys)
- `registration_date` must be ≥ `date_of_birth`
- `visit_date` must be ≥ `date_of_birth` (no legitimate exception — a visit cannot precede a birth)
- `visit_date` may precede `registration_date` in limited cases (e.g., ER walk-ins treated before formal registration) — **flagged, not automatically removed**
- `insurance_type` must be populated (must be a defined value, not blank)
- `attending_physician_id` must be populated on every visit
- `outstanding_balance` must always equal `total_charge − insurance_paid − patient_paid`, independent of `claim_status`
- `claim_status = "N/A"` (reserved for uninsured patients) must be treated as a distinct, valid value — **never conflated with a true missing/blank value**

---

## 3. Data Cleaning & Reconciliation Process

### Staff
✅ 100% clean — no violations found against any business rule.

### Patients
- **33 records flagged and removed from clean final dataset**: `registration_date` earlier than `date_of_birth` — a logical impossibility indicating a data entry or system error. Flagged for source-system investigation.
- **14 records flagged**: `insurance_type` unknown/blank, violating the "insurance type must be populated" rule. Retained but flagged for resolution rather than deleted, since the patient record itself remained otherwise valid.

### Visits
- **121 records flagged and removed**: `patient_id` not found in the cleaned Patients table — a direct downstream consequence of the 33 patients removed in the prior step (orphaned records).
- **3 records flagged and removed**: `visit_date` earlier than the patient's `date_of_birth` — a hard data integrity violation with no valid business exception.
- **40 records flagged**: missing `attending_physician_id`, violating the rule that every visit must have an attending physician on record. Flagged for correction at the source rather than deleted outright, since the clinical/financial data on those rows remained usable.

### Billing & Diagnoses
- Reconciled against the cleaned Visits table — billing and diagnosis records with no matching `visit_id` in the cleaned dataset were flagged and excluded from final reporting to preserve referential integrity across the model.

### Final Reconciled Row Counts

| Table | Raw Rows | Clean Rows |
|---|---|---|
| Patients | 505 | 467 |
| Visits | 2,000 | 1,836 |
| Billing | 2,000 | 1,799 |
| Diagnoses | 2,594 | 2,379 |
| Staff | 50 | 50 |

All five tables were then merged on their common keys (`patient_id`, `visit_id`, `attending_physician_id`) to form the base model powering both dashboards.

### Key Definitional Decisions
A few judgment calls were made explicit and documented rather than left ambiguous:
- **Revenue** is defined as `insurance_paid + patient_paid` (cash actually collected), **not** `total_charge` (amount billed) — since billed charges include pending/disputed amounts with unknown final resolution, using them as "revenue" would overstate actual facility income.
- **Outstanding balance** is treated as independent of `claim_status` — it reflects money currently unpaid as of today, regardless of where a claim sits in the insurer's process.

---

## 4. Dashboards

### Page 1 — Financial Performance
**KPIs:** Total Revenue, Total Outstanding Balance, % Outstanding Balance (vs. total charges)

**Visualizations:**
1. Revenue collected by department — column chart
2. Outstanding balance by department — bar chart
3. Revenue trend over time — line chart
4. Revenue by visit type — treemap
5. Outstanding balance composition by insurance type — pie chart

### Page 2 — Operational Performance
**KPIs:** Total Visits, Average Inpatient Length of Stay, % ER Visits, Average Visits per Physician

**Visualizations:**
1. Visit volume by department — column chart
2. Visit type distribution — pie chart
3. Visit volume trend over time — line chart
4. ER visit volume by hour of day — area chart
5. Top 5 diagnoses by frequency — treemap

---

## 5. Key Findings

- **Revenue concentration risk:** Inpatient visits generate 80.66% of total revenue from only 14.62% of visit volume — the facility's financial health is heavily dependent on a single visit type.
- **Orthopedics leads in both revenue and outstanding balance** — the top revenue-generating department also carries the highest raw outstanding balance, warranting a normalized (percent-of-revenue) view as a next step.
- **Private insurance carries the largest outstanding balance share (41.67%)** — counter to the common assumption that uninsured patients drive the bulk of exposure.
- **Internal Medicine is both the highest-volume department and a top denial-count department** — a direct link between operational load and financial leakage.
- **ER visit volume peaks sharply between hours 13–22**, suggesting current staffing patterns may not be aligned to actual demand.
- **Chronic disease management (hypertension, diabetes) dominates diagnosis volume**, suggesting the facility's core demand is ongoing chronic care rather than acute/emergency treatment.
- **2026 figures (revenue and visit volume) reflect a partial year**, not a real decline — flagged explicitly to avoid a misleading trend read.

Full recommendations tied to each finding are documented in `recommendations.md`.

---

## 6. Tools Used

- **Excel** — initial cleaning, pivot table exploration, business rule validation
- **Power BI** — dashboard build, DAX measures, KPI cards

---

## 7. Repo Structure

```
├── data/
│   ├── Healthcare Data (Clean).xlsx
│   ├── Healthcare Data (Flagged).xlsx
│   ├── Healthcare Data (Raw).xlsx
├── images/
|   ├── Financial Performance Dashboard.png
|   ├── Operational Performance Dashboard.png
├── analysis_findings.md
├── data_quality_business_rules.md
├── recommendations.md
├── Meridian Healthcare Dashboard.pbix
└── README.md
```

---

## 8. Notes on Data Provenance

This dataset is synthetic, generated for practice purposes. It intentionally includes realistic data quality issues (missing values, inconsistent formatting, referential integrity violations, and date-logic errors) to simulate a real-world cleaning and validation workflow.