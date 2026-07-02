# Hr-attrition-Dashboard-in-Tableau
Tableau dashboard analyzing employee attrition (74K+ records) — tracks attrition rate, tenure, satisfaction, overtime, and job role trends. Includes documented fixes for tenure unit conversion (months→years) and tenure-band bucketing bugs found during QA
Employee Attrition Dashboard

A Tableau dashboard analyzing employee attrition across a workforce of 74,498 employees. Tracks total headcount, average tenure, attrition rate, and attrition breakdowns by tenure band, job role, overtime status, and job satisfaction.

Data source

Employee_Attrition_Combined.csv — 29 fields, 74,498 rows, live connection.

Key fields:


Employee ID — unique identifier
Attrition — "Left" / "Stayed"
Company Tenure — raw tenure value stored in months
Job Satisfaction, Job Level, Job Role, Overtime, Work-Life Balance — categorical dimensions used in breakdown charts


Dashboard contents

SheetDescriptionKPI - Total EmployeesHeadcount cardKPI - Average TenureAverage tenure across all employees, in yearsKPI - Attrition RateCompany-wide attrition rateChart - Attrition By Tenure BandAttrition rate across 0-2 / 3-5 / 6-10 / 11-20 / 20+ year bandsChart - Attrition By Job RoleAttrition rate by seniority level (Entry / Mid / Senior)Chart - Attrition By OvertimeAttrition rate for employees with vs. without overtimeChart - Attrition By SatisfactionAttrition rate by job satisfaction level, with Work-Life Balance color breakdown

Known issues found & fixed during QA

1. Average Tenure showing 55 years

Cause: the underlying calculated field averaged tenure only for employees who had already left (AVG(IF [Attrition]="Left" THEN [Company Tenure] END)), and [Company Tenure] itself is stored in months, not years.

Fix:

AVG([Company Tenure]) / 12

Removed the leaver-only filter and added the months-to-years conversion.

2. Tenure Band bucketing was wrong

Cause: the band formula compared raw [Company Tenure] (months) against thresholds meant for years, so nearly every employee fell into the "20+ yrs" bucket.

Fix:

IF [Company Tenure]/12 <= 2 THEN "0-2 yrs"
ELSEIF [Company Tenure]/12 <= 5 THEN "3-5 yrs"
ELSEIF [Company Tenure]/12 <= 10 THEN "6-10 yrs"
ELSEIF [Company Tenure]/12 <= 20 THEN "11-20 yrs"
ELSE "20+ yrs"
END

3. Attrition Rate KPI (47.5%)

Status: verified correct. The formula

SUM([Attrition Flag]) / COUNTD([Employee ID])

is a properly constructed rate (leavers ÷ distinct headcount). The high value reflects the underlying dataset rather than a calculation bug — worth confirming whether the source data is real company data or a synthetic/sample dataset, since 47.5% is far above typical real-world attrition (10-20% annually).

4. Chart - Attrition By Satisfaction exceeding 1.0

Under investigation — bars on this chart currently exceed a valid 0-1 rate range, suggesting a summed rather than averaged measure. Not yet resolved as of this writing.
