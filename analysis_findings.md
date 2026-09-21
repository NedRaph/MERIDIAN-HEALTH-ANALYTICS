# Analysis Findings

Summary of key results from the Financial Performance and Operational Performance dashboards, built on the reconciled dataset (467 patients, 1,836 visits, 1,799 billing records, 2,379 diagnoses, 50 staff).

---

## Financial Performance

**KPIs**
- Total Revenue Collected: **$4.80M**
- Total Outstanding Balance: **$1.03M**
- Outstanding Balance as % of Total Charges: **17.67%**

**Revenue by Department**
Top performers: Orthopedics ($742,208), Internal Medicine ($716,658), Behavioral Health ($632,799).
Bottom performers: Emergency ($334,754), Cardiology ($290,699), Neurology ($147,041 — lowest).

**Outstanding Balance by Department**
Highest: Orthopedics ($162,776), Internal Medicine ($135,642), Pediatrics ($130,895).
Lowest: Cardiology ($52,974), Neurology ($44,652).

**Revenue Trend Over Time**
- 2024: $1,516,957
- 2025: $2,019,092 (increase)
- 2026: $1,260,850 (decline — likely a partial-year effect, as 2026 has not yet concluded)

**Revenue by Visit Type**
- Inpatient: 80.66%
- ER: 13.71%
- Outpatient: 4.96%
- Telehealth: 0.67%

**Outstanding Balance Composition by Insurance Type**
- Private: 41.67%
- Medicare: 30.8%
- Uninsured: 12.75%
- Medicaid: 11.34%
- Unknown: 3.44%

---

## Operational Performance

**KPIs**
- Total Visits: **1,799**
- Average Inpatient Length of Stay: **4.08 days**
- % of Visits that are ER: **25%**
- Average Visits per Physician: **~36**

**Visit Volume by Department**
Highest: Internal Medicine (296), Orthopedics (251), Pediatrics (225).
Lowest: Cardiology (116), Radiology (113), Neurology (79).

**Visit Type Distribution**
- Outpatient: 45.97%
- ER: 24.96%
- Inpatient: 14.62%
- Telehealth: 14.45%

**Visit Volume Trend Over Time**
- 2024: 656 visits
- 2025: 693 visits (slight increase)
- 2026: 450 visits (decline — same partial-year effect as revenue trend)

**ER Visits by Hour of Day**
- Low volume, hours 0–12: roughly 5–12 visits/hour
- Rising volume, hours 13–22: ranging from 17–46 visits/hour
- Peak: hour 19–20 (46 visits)

**Top 5 Diagnoses by Frequency**
1. Essential (primary) hypertension — 28.96%
2. Type 2 diabetes mellitus without complications — 21.00%
3. Acute upper respiratory infection, unspecified — 17.92%
4. Encounter for general adult medical exam without abnormal findings — 17.38%
5. Hyperlipidemia, unspecified — 14.75%

---

## Cross-Dashboard Observations

- **Internal Medicine** appears at or near the top on both dashboards — highest visit volume operationally, and among the highest revenue and denial counts financially — indicating a direct link between patient load and financial leakage in this department.
- **Revenue trend and visit volume trend move in parallel** across all three years, which supports the internal consistency of the two dashboards (both reflect the same partial 2026 data).
- **Cardiology and Neurology are consistently low performers** across both revenue and visit volume, suggesting either genuinely low demand for these specialties or a capacity/referral constraint worth investigating separately.

Recommended actions tied to these findings are documented in `recommendations.md`.