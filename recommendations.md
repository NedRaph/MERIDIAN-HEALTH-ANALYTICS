# Recommendations

Findings and recommended actions derived from the Financial Performance and Operational Performance dashboards. Each item follows a **Finding → Impact → Action** structure.

---

## Financial Performance

### 1. Revenue is heavily concentrated in Inpatient visits
**Finding:** Inpatient visits generate 80.66% of total revenue from just 14.62% of visit volume, while Telehealth (14.45% of visits) generates only 0.67% of revenue.
**Impact:** The facility's financial health is disproportionately dependent on a single visit type. Any disruption to inpatient capacity — bed shortages, staffing gaps, a payer policy change — would affect revenue far more severely than visit volume alone would suggest.
**Action:** Diversify revenue exposure by reviewing reimbursement rates for Telehealth and Outpatient (currently high-volume, low-revenue, which may signal underpricing or an unfavorable payer mix), and explicitly monitor inpatient capacity as a financial risk rather than only an operational one.

### 2. Orthopedics leads in both revenue and outstanding balance
**Finding:** Orthopedics is the top-performing department by revenue ($742,208) and also carries the highest outstanding balance ($162,776).
**Impact:** On its own, this pairing is ambiguous — a high outstanding balance in the top-revenue department could simply reflect higher billing volume (not concerning), or a genuine collections problem disproportionate to its size (concerning). The current dashboard can't distinguish between the two.
**Action:** Add outstanding-balance-as-a-percentage-of-department-revenue as a follow-up metric to normalize for scale before concluding whether Orthopedics has a collections issue.

### 3. Private insurance is the largest outstanding balance exposure
**Finding:** Private insurance accounts for 41.67% of total outstanding balance — nearly double Medicare (30.8%) and over 3x Medicaid (11.34%).
**Impact:** This runs counter to the common assumption that uninsured patients drive most collections risk. Private payers are the largest single exposure.
**Action:** Conduct a claims-denial audit specifically for Private payers. This is likely the highest-leverage collections target, since Private also generates the facility's highest denial dollar amount.

### 4. The 2026 revenue decline is a partial-year artifact
**Finding:** Revenue rose from $1.52M (2024) to $2.02M (2025), then dropped to $1.26M (2026).
**Impact:** Presented without context, this reads as a real decline. It is more likely an incomplete-year effect.
**Action:** Annualize or prorate 2026 figures for a fair year-over-year comparison, or add an explicit "partial year" caveat directly on the chart to avoid a misleading trend read.

---

## Operational Performance

### 5. Internal Medicine combines the highest volume with the highest denial count
**Finding:** Internal Medicine has the highest visit volume (296) and, per the financial dashboard, also the highest denied-claim count.
**Impact:** High patient throughput may be straining claims documentation accuracy, converting operational load directly into financial leakage.
**Action:** Prioritize a denial-reason review for Internal Medicine specifically — it is simultaneously a top operational and top financial risk point, making it the highest-leverage department for a targeted fix.

### 6. ER staffing does not appear aligned to actual demand curve
**Finding:** ER visit volume is low overnight/morning (5–12 visits) and rises sharply through the afternoon and evening, peaking around hour 19–20 (46 visits).
**Impact:** If ER staffing is scheduled evenly across all 24 hours, the department is likely overstaffed overnight and understaffed during the evening peak.
**Action:** Shift ER staffing ratios to match the observed hourly demand curve, concentrating coverage in the 13:00–22:00 window.

### 7. Visit volume trend mirrors the revenue trend
**Finding:** Visit volume also rose 2024→2025 (656 → 693) and dropped in 2026 (693 → 450).
**Impact:** The parallel movement between visit volume and revenue is a positive internal consistency check — it confirms the two dashboards are measuring a coherent underlying reality rather than diverging due to a data or calculation error.
**Action:** Apply the same partial-year caveat used in the financial trend. No corrective action needed beyond annotation — this finding validates rather than flags a problem.

### 8. Diagnosis volume points to chronic disease management as the core demand driver
**Finding:** Hypertension (28.96%) and Type 2 Diabetes (21%) alone account for roughly half of the top-5 diagnosis volume; the remainder skews toward routine/chronic conditions rather than acute or emergency cases.
**Impact:** The facility's real demand center appears to be ongoing chronic care management, not acute/emergency treatment — a fact that should inform staffing and resource allocation.
**Action:** Cross-reference this diagnosis mix against Staff specialty counts to confirm Internal Medicine and Cardiology have adequate capacity relative to demonstrated chronic-care demand.

---

## Summary Priority List

If only three actions could be taken immediately, in order of estimated impact:

1. **Audit Private insurance denials** — largest single outstanding-balance exposure with a clear root-cause path.
2. **Realign ER staffing to the 13:00–22:00 demand peak** — directly actionable, low-cost, clear data support.
3. **Investigate Internal Medicine's denial drivers** — the one finding that connects both dashboards, and the department where a fix compounds across both operational and financial metrics.