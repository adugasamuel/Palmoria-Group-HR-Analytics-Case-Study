# Palmoria Group — HR Analytics Case Study (Power BI)

**Role:** HR Analytics Consultant (Capstone case study modeled as a real-world engagement)
**Deliverable:** Interactive Power BI dashboard + executive insights (salary & bonus analysis, gender distribution, pay-gap assessment, regulatory compliance check)

---

## Introduction

This project uses HR data from *Palmoria Group*, a manufacturing company, to diagnose gender-related risks (representation, pay equity, performance distribution) and to model annual bonus payouts. The analysis is implemented as a production-style Power BI dashboard that supports filter-driven exploration by **Region** and **Department**, and provides actionable recommendations to HR and leadership.

> **Note:** The dataset used in the public portfolio is a case study dataset created for the capstone exercise and has been handled with appropriate anonymization. The analysis and dashboard replicate a real-world HR analytics engagement.

---

## Project description

A reputational article raised concerns about gender inequality at Palmoria. The CHRO engaged an HR analyst to:

* Analyze gender representation across regions and departments
* Quantify and visualize any gender pay gaps
* Check compliance with a newly adopted \$90,000 minimum salary regulation
* Build a transparent, rule-based bonus allocation model and report total payouts by region and company-wide

The solution is delivered as a Power BI report (interactive dashboard + measures) and an executive summary of the findings and recommendations.

---

## Project context

* Source tables: **Employee** (primary) and **Bonus Rules** (lookup)
* Rows: >1,000 employee records (after cleaning)
* Key fields used: `EmployeeID`, `Gender`, `Region`, `Department`, `Salary`, `Rating` (performance), and bonus-rule fields (rating -> bonus rule)
* Tools: Power BI (Power Query + DAX), Excel (for quick checks / sample extracts)

**View the interactive report here:** `https://your-powerbi-report-url`
*(Replace the URL above with the published Power BI link or your GitHub Pages / embed link.)*

---

## Problem statement

Palmoria faces reputational and regulatory risk due to potential gender inequality and failure to meet a new \$90,000 minimum salary requirement. Leadership needs a clear, data-driven assessment of where the problems sit (regions/departments) and what financial exposure (salary + bonus) looks like.

---

## Aim of the project

* Quantify gender distribution and identify departments/regions with imbalances
* Detect and quantify any gender pay gap (overall, by region, and by department)
* Verify compliance with the \$90,000 minimum salary rule and show who falls below it
* Build and apply a transparent bonus allocation model by performance rating and report the resulting payouts by region and company total
* Produce an executive-ready dashboard to support decision-making

---

## My approach and steps

1. **Load data**

   * Load `Employee` and `Bonus Rules` (CSV / Excel) into Power BI (Power Query).
   * Confirm loaded row counts ( >1,000 rows pre-cleaning).

2. **Data cleaning & normalization** (see checklist below).

3. **Engineering & modeling**

   * Create salary band column (10k bands) in Power Query for banded visualizations.
   * Merge `Employee` with `Bonus Rules` (left join) so each employee is assigned the correct bonus rule for their rating / department (as applicable).
   * Where Power BI needed multi-column matching, I created a composite key (`RatingKey`) combining Department + Rating to enable relationships.

4. **Measures & calculations**

   * Implement DAX measures for total salary, total bonus, total pay (salary+bonus), average salaries by gender, count of employees above/below \$90,000, and more.
   * Build measures for regional aggregations and KPI cards.

5. **Visualizations**

   * KPI cards (Total Employees, % > \$90k, Avg Salary Female, Sum of Bonus, Total Pay)
   * Gender distribution (pie), gender by region/department (bar chart)
   * Rating distribution by gender (stacked bars)
   * Salary band distribution by region (10k bands)
   * Bonus payout table and regional totals

6. **Validation & storytelling**

   * Cross-check totals and band counts.
   * Prepare executive insights and targeted recommendations.

---

## Data cleaning checklist

* **Undisclosed Gender**

  * Assigned a neutral placeholder value: `"Unidentified"` for employees who did not disclose gender. This preserves count integrity while making gender-based aggregations explicit.
* **Employees without salary (no longer with company)**

  * Filtered out records where `Salary` = `0`, `NULL`, or missing. Marked as **inactive** and removed from active workforce and pay analyses.
* **Departments labelled `NULL`**

  * Removed or excluded records where `Department` is missing/`NULL` for department-based analysis (to prevent noisy group comparisons).
* **Basic checks**

  * Removed duplicates on `EmployeeID`.
  * Normalized categorical fields (trim, case, consistent spelling) for Region, Department, Rating.
  * Confirmed numeric types for Salary and numeric lookups for bonus rules.

---

## Analysis processes (summary of work done)

* Created DAX measures for:

  * `TotalSalary = SUM(Employee[Salary])`
  * `AvgFemaleSalary = CALCULATE(AVERAGE(Employee[Salary]), Employee[Gender] = "Female")`
  * `PctAbove90k = DIVIDE(CALCULATE(COUNTROWS(Employee), Employee[Salary] > 90000), COUNTROWS(Employee))`
  * `TotalBonus = SUMX(Employee, Employee[Salary] * RELATED('Bonus Rules'[BonusPct]))` *(or calculated in merged table)*
  * `TotalPay = [TotalSalary] + [TotalBonus]`
* Built salary bands (10k intervals) in Power Query to enable the “Employee grouped by band (10k)” visual.
* Merged Employee and Bonus Rules tables with a left join so all active employees receive bonus rules; used composite `RatingKey` where needed for multi-column mapping.
* Designed KPIs and cards for quick view (Total Employees, % > \$90k, Avg Female Salary, Sum of Bonus, Company Total Pay).
* Visualized regional totals for total pay (salary + bonus) to identify financial exposure by region.

**Example DAX snippets**

```DAX
AvgFemaleSalary = CALCULATE(AVERAGE(Employee[Salary]), Employee[Gender] = "Female")

PctAbove90k = 
DIVIDE(
  CALCULATE(COUNTROWS(Employee), Employee[Salary] > 90000),
  COUNTROWS(Employee),
  0
)

TotalPay = [TotalSalary] + [TotalBonus]
```

---

## Key findings & insights (snapshot from dashboard)

> *All numbers below are taken from the dashboard snapshot and reflect the cleaned dataset used for analysis.*

* **Workforce size:** **946** active employees (after removing inactive / salary = 0 records).
* **Gender distribution:**

  * Male: **465** (≈ **49.15%**)
  * Female: **441** (≈ **46.62%**)
  * Unidentified: **40** (≈ **4.23%**)
    The workforce is **near gender parity**, with a slight male majority. The `Unidentified` group is small but should be tracked.
* **Average female salary:** **\$72,140** (snapshot KPI `AvgSalary Female = $72.14K`).
* **Regulatory compliance check:** Only **30.9%** of employees earn **>\$90,000**, meaning **\~69.1%** are below the \$90k threshold — **Palmoria is not fully compliant** with the new minimum pay regulation and should prioritize remediation.
* **Total compensation (salary + bonus):** **\~\$71.92M** company-wide.
  Regional breakdown (total pay, salary + bonus):

  * **Kaduna:** **\$27,478,731.80**
  * **Abuja:** **\$24,917,424.20**
  * **Lagos:** **\$19,526,793.30**
* **Bonus pool:** Snapshot KPI indicates **\~\$2M** in total bonus payments allocated across employees (calculated via the bonus-rule merge).
* **Salary distribution:** Salary banding (10k bands) highlights where headcount clusters exist and allows quick identification of regions/departments with concentrations below \$90k.

**Interpretation:** while the overall gender split looks balanced, pay and compliance issues are material — large portions of the workforce fall below the \$90k regulatory threshold, and average female salary in the snapshot sits at \~\$72k. Regional totals show Kaduna has the largest total compensation spend (headcount or higher pay mix), which should be examined further for drivers (more employees, higher bonus rates, higher salaries).

---

## KPIs tracked

* Total active employees
* Gender distribution (% and counts)
* Average salary by gender
* % employees earning > \$90,000
* Total salary (company & region)
* Total bonus (company & region)
* Total pay (salary + bonus) (company & region)
* Employees per salary band (10k bands)
* Bonus payout by region and rating

---

## Key features of the dashboard

* Interactive filters for **Region** and **Department** (top-right slicers) so leadership can drill into trouble spots
* KPI cards for quick executive snapshot
* Gender distribution pie chart + gender by region/department bar charts
* Rating distribution segmented by gender
* Salary band visual (10k bins) with regional breakdown
* Bonus allocation table showing payouts per region and the company total
* Dynamic measures that recalc when slicers change

---

## Recommendations (actionable & prioritized)

1. **Immediate compliance review:** Because only \~30.9% of employees earn > \$90k, run a targeted compliance and remediation plan (salary adjustments or role reclassification) for roles and regions with large below-\$90k concentrations.
2. **Deep-dive pay equity analysis:** Use the dashboard filters to identify departments and job families with the largest pay gaps; examine median/mean pay by role level to control for experience and role mix.
3. **Address reporting & disclosure:** Reduce the `Unidentified` gender segment by improving HR data capture; this strengthens future analysis.
4. **Revisit bonus design:** Ensure the bonus allocation rules do not unintentionally widen pay gaps. Consider weighting bonus to support retention in underpaid groups if appropriate.
5. **Monitoring & governance:** Schedule monthly/quarterly refresh and a standing HR dashboard review so leadership can track remediation progress.
6. **Policy & communications:** Publish a public-facing pay-equity statement and an internal remediation timeline to preserve reputation while changes are implemented.

---

## How to reproduce / repository contents

* `Palmoria_PowerBI_Report.pbix` — Power BI report file (replace with your actual file name)
* `data/employee.csv` — cleaned/anon sample employee table ( >1,000 rows )
* `data/bonus_rules.csv` — bonus rules mapping used to compute bonuses
* `screenshots/` — dashboard screenshots for README
* `README.md` — this document

**To open / run locally**

1. Download the `.pbix` file and the `data` folder into the same working directory.
2. Open `Palmoria_PowerBI_Report.pbix` with Power BI Desktop.
3. On first open, if Power Query is configured to reference local CSVs, refresh data to load the sample dataset.
4. Interact with filters (Region / Department) to reproduce the snapshots.

---

## Skills & tools used

* **Power BI Desktop** (Power Query + Data Model + DAX measures) — primary report authoring tool
* **Power Query** — data cleaning, salary band creation, table merges (left join)
* **DAX** — measures for totals, averages, percentages, and financial calculations
* **Excel** — quick data validation and sample extracts (optional)
* Data modeling best practices — composite keys, single-direction relationships as required

---

## Notes on ethics & privacy

This readme and the public dashboard use anonymized/case-study data. For production deployments with real employee data, ensure you follow applicable data privacy regulations (e.g., local labor laws, GDPR where applicable), secure dashboards with row-level security, and obtain appropriate approvals before any public release.

---

## Contact

If you’d like to discuss this analysis, see the interactive report, or request an expanded study, contact:

**Your Name**
Email: `youremail@example.com`
LinkedIn: `https://www.linkedin.com/in/yourprofile`

---

### Final note

This capstone was executed as a practical HR analytics engagement and structured to mirror how an HR analytics team would diagnose representation, pay equity, and regulatory exposure. The dashboard is designed to be both an investigative tool and an executive communication artifact — it surfaces the issues, quantifies financial exposure, and points leadership to the most urgent remediation areas.

---

*Replace placeholder links and contact details with your live report URL and actual contact information before publishing.*
