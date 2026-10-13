# Data Model

## Tables

This project uses a **single-table model** — there's no separate fact/dimension split, because the
source is one flat IBM HR dataset with one row per employee. There's nothing to join.

| Table | Source | Rows | Role |
|---|---|---|---|
| `CleanedData` | `02_Cleaned_Data` sheet in `HR_Attrition_Analytics.xlsx` | 1,470 | Single fact+dimension table — every measure and visual reads from here |

## Why no star schema

A star schema (fact table + dimension tables for Department, JobRole, etc.) makes sense when the same
dimension repeats across multiple fact tables, or when a dimension has its own attributes beyond what's
in the fact table. Here, Department and JobRole are just columns on the employee row — splitting them
into separate tables would add relationship overhead with no analytical benefit. If this project later
adds a second table (e.g. monthly headcount snapshots or exit-interview data), that's the point where a
proper Department/JobRole dimension table would start to earn its keep.

## Relationships

None. Single table, so no relationships to define.

## Column roles (for the Fields pane)

**Dimensions** (used to slice/filter): `Department`, `JobRole`, `Gender`, `MaritalStatus`,
`BusinessTravel`, `OverTime`, `EducationField`, `Education`, `JobLevel`, `JobSatisfaction`,
`EnvironmentSatisfaction`, `WorkLifeBalance`, `StockOptionLevel`, `JobInvolvement`,
`PerformanceRating`, `RelationshipSatisfaction`.

**Measures/facts** (used in calculations): `Age`, `MonthlyIncome`, `DailyRate`, `HourlyRate`,
`MonthlyRate`, `DistanceFromHome`, `TotalWorkingYears`, `YearsAtCompany`, `YearsInCurrentRole`,
`YearsSinceLastPromotion`, `YearsWithCurrManager`, `NumCompaniesWorked`, `TrainingTimesLastYear`,
`PercentSalaryHike`.

**Target variable**: `Attrition` (Yes/No text) and `AttritionFlag` (0/1 numeric equivalent).

**Key**: `EmployeeNumber` — unique per employee, verified with 0 duplicates in `04_Cleaning_Log`.

## Suggested calculated groups (optional, not required for v1)

If the dashboard grows, these are natural "bins" to add as calculated columns rather than DAX measures,
since they don't change per filter context:

- `Age Group`: 18-25 / 26-35 / 36-45 / 46-60
- `Tenure Group`: 0-2 / 3-5 / 6-10 / 10+ years
- `Income Quartile`: Q1–Q4 by `MonthlyIncome`

These already exist as computed groupings in `06_Data_Mining` (Excel) — replicate them as Power Query
custom columns if you want them as native slicers in Power BI, rather than recomputing them in DAX.
