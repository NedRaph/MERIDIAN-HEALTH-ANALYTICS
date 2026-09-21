# Data Quality Business Rules — Healthcare Facility Dataset

**Owner:** Data Analytics Manager
**Purpose:** Define what "clean" means for each table so cleaning decisions are consistent, defensible, and repeatable — not ad hoc.

---

## 1. PATIENTS Table

| Column | Business Rule | Nulls Acceptable? |
|---|---|---|
| `patient_id` | Must be unique. Format: `PT#####`. This is the primary key — every other table references it. No two rows may share one (a duplicate `patient_id` is a data integrity violation, not just "messy"). | **No** — never null, never duplicated |
| `first_name` / `last_name` | Free text, letters/hyphens/apostrophes only (no numbers, no blank strings). | **No** — a patient record without a name is not usable |
| `date_of_birth` | Must be a valid date, in the past, and result in a plausible age (0–120 years). Cannot be later than `registration_date`. | **No** — required for age-based clinical/billing logic |
| `gender` | Must be standardized to a **fixed value list** (e.g., `Male`, `Female`, `Other`, `Unknown`) — not free text. `M`, `m`, `MALE` etc. are **not acceptable values**, they are formatting defects that must be mapped to the standard list. | **No** — use `Unknown` as the standardized null-equivalent, not a blank |
| `race_ethnicity` | Must match a fixed value list aligned to a recognized standard (e.g., OMB race/ethnicity categories). | **Yes, conditionally** — acceptable if patient declined to disclose, but should be coded as `Not Disclosed`, not left blank |
| `insurance_type` | Must match fixed list: `Private`, `Medicare`, `Medicaid`, `Uninsured`. This field drives billing logic downstream, so it is a **high-priority required field**. | **No** — if truly unknown, must be flagged as `Unknown` and routed for follow-up, never left blank silently |
| `zip_code` | Must be a valid 5-digit format for the country of operation. | **Yes, conditionally** — a blank is acceptable only if documented as "patient declined" |
| `registration_date` | Must be a valid date, cannot be in the future, cannot precede `date_of_birth`. | **No** |

**Rule for duplicates:** A "duplicate patient" is defined as two records matching on (`first_name` + `last_name` + `date_of_birth`) — not on name alone. Duplicate `patient_id` values are never acceptable and indicate a system error, not just messy data.

---

## 2. VISITS Table

| Column | Business Rule | Nulls Acceptable? |
|---|---|---|
| `visit_id` | Unique primary key, format `V######`. | **No** |
| `patient_id` | Must exist in the Patients table (referential integrity). A visit with no matching patient is an **orphan record** and should be quarantined for review, not silently dropped. | **No** |
| `visit_date` | Valid date, cannot be in the future, cannot precede the patient's `date_of_birth`. | **No** |
| `visit_type` | Must match fixed list: `Outpatient`, `ER`, `Inpatient`, `Telehealth`. | **No** |
| `department` | Must match a fixed list of recognized hospital departments, and should logically match the assigned physician's specialty (a Cardiology visit with an Orthopedics physician is a business-rule violation, not just a typo). | **No** |
| `attending_physician_id` | Must exist in the Staff table. | **No** — a visit with no attending physician on record is incomplete and should be flagged, not assumed |
| `length_of_stay_days` | Required **only if** `visit_type = Inpatient`; must be null for all other visit types (a null here for Inpatient, or a value here for Outpatient, is a rule violation). Must be ≥ 1 when present. | **Conditional** — null is *correct* for non-inpatient visits, and a defect for inpatient visits |
| `discharge_disposition` | Must match fixed list. Logically, `Home` is the expected default for Outpatient/Telehealth; other values are primarily expected for Inpatient/ER. | **No** |

---

## 3. DIAGNOSES Table

| Column | Business Rule | Nulls Acceptable? |
|---|---|---|
| `diagnosis_id` | Unique primary key. | **No** |
| `visit_id` | Must exist in the Visits table. | **No** |
| `icd10_code` | Must be a valid, properly formatted ICD-10 code (not free text description standing in for a code). | **No** |
| `diagnosis_description` | Must match the official description tied to the `icd10_code` — a mismatch between code and description is a data integrity error, not a style issue. | **No** |
| `is_primary_diagnosis` | Boolean. Exactly **one** primary diagnosis is allowed per `visit_id` — zero or multiple primary flags on the same visit is a rule violation. | **No** |

---

## 4. BILLING Table

| Column | Business Rule | Nulls Acceptable? |
|---|---|---|
| `visit_id` | Must exist in the Visits table, and should appear **exactly once** (one billing record per visit — duplicates here mean double-billing risk). | **No** |
| `total_charge` | Must be > 0. Should fall within an expected range for the associated `visit_type` (e.g., an Outpatient visit charging $50,000 is a statistical outlier requiring review, not automatically an error, but should be flagged). | **No** |
| `insurance_paid` | Must be ≥ 0 and ≤ `total_charge`. Must be $0 when `insurance_type = Uninsured`. | **No** — should be `0`, not blank, when not applicable |
| `patient_paid` | Must be ≥ 0. | **No** — should be `0`, not blank, if nothing has been paid yet |
| `outstanding_balance` | **Must always equal** `total_charge − insurance_paid − patient_paid`. Any row where this equation doesn't hold is a calculation-integrity failure and takes priority over all other cleaning. This field is independent of `claim_status` — it reflects money owed *today*, not a future-resolved state. | **No** |
| `claim_status` | Must match fixed list: `Paid`, `Pending`, `Denied`, `N/A`. `N/A` is a **valid value**, reserved specifically for `insurance_type = Uninsured` — it is not the same as a missing/blank value and must never be treated interchangeably with one. | **No** — a true blank here means the claim status genuinely wasn't recorded and should be routed to Revenue Cycle for follow-up, not assumed to be any specific status |

**Critical technical note for whoever builds the pipeline:** when importing this table into a tool (Excel, pandas, SQL), explicitly define `N/A` as a *literal text value*, not a null marker — several tools (including pandas by default) will silently convert the text `"N/A"` into a true null, which corrupts the distinction between "no insurer" and "status unknown."

---

## 5. STAFF Table

| Column | Business Rule | Nulls Acceptable? |
|---|---|---|
| `physician_id` | Unique primary key, format `PHY####`. | **No** |
| `first_name` / `last_name` | Same text rules as Patients. | **No** |
| `specialty` | Must match a fixed, approved list of medical specialties. | **No** |
| `department` | Must map consistently 1:1 (or documented many:1) to `specialty` — the same specialty should never appear under two different department labels. | **No** |
| `years_experience` | Integer ≥ 0, and realistically ≤ ~60. | **No** |

---

## Cross-Table (Referential) Rules

1. Every `patient_id` in Visits/Billing must exist in Patients — no orphans.
2. Every `visit_id` in Diagnoses/Billing must exist in Visits — no orphans.
3. Every `attending_physician_id` in Visits must exist in Staff — no orphans.
4. Sums and joins must reconcile: total billing rows should equal total visit rows (1:1), unless the business rule explicitly allows multiple bills per visit (currently it does not).

## General Null-Handling Principle

A blank is only "acceptable" when the business has explicitly defined what that blank *means* (e.g., "patient declined to answer"). An unexplained blank is not clean data — it's undocumented data, and should always be flagged for follow-up rather than assumed, imputed, or dropped silently.