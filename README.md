# ICO Data Protection Complaints — Interactive Dashboard

An interactive, self-contained dashboard exploring **140,820 completed data protection complaint cases** handled by the UK Information Commissioner's Office (ICO), covering 12 consecutive quarters from 2023/24 Q2 through 2026/27 Q1.

**[Open the dashboard → `index.html`]**

The single file `index.html` contains all data and code. No server, no dependencies, no internet connection required — open it directly in any modern browser.

---

## Data sources

All underlying data is published by the ICO under the [Open Government Licence v3.0](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/).

### Case-level quarterly datasets

Downloaded from the ICO's [Data Protection Complaints data sets page](https://ico.org.uk/action-weve-taken/complaints-and-concerns-data-sets/data-protection-complaints/). Each file represents cases *completed* (closed) during that quarter, not cases received.

| File | Period | Rows |
|------|--------|------|
| `dp-dataset-q2-202324.csv` | 2023/24 Q2 (Jul–Sep 2023) | 8,177 |
| `dp-complaints-q3-2023-24.csv` | 2023/24 Q3 (Oct–Dec 2023) | 8,241 |
| `q4-2023-24-datasets-dp.csv` | 2023/24 Q4 (Jan–Mar 2024) | 9,063 |
| `q1-2024-2025-datasets-dp.csv` | 2024/25 Q1 (Apr–Jun 2024) | 8,986 |
| `q2-2024-2025-dataset-for-dp.csv` | 2024/25 Q2 (Jul–Sep 2024) | 8,852 |
| `data-protection-complaints-q3-2024-2025.csv` | 2024/25 Q3 (Oct–Dec 2024) | 7,920 |
| `q4-datasets-for-dp-run-262-amended.csv` | 2024/25 Q4 (Jan–Mar 2025) | 8,588 |
| `dp-proactive-disclosure-completed-cases-september-2025.csv` | 2025/26 Q1 (Apr–Jun 2025) | 9,528 |
| `dp-q2-2025-2026.csv` | 2025/26 Q2 (Jul–Sep 2025) | 9,361 |
| `dp-q3-2025-2026-dataset.csv` | 2025/26 Q3 (Oct–Dec 2025) | 8,646 |
| `dp-complaints-q4-2025-26.csv` | 2025/26 Q4 (Jan–Mar 2026) | 31,799 |
| `dp-q1-2026-27.csv` | 2026/27 Q1 (Apr–Jun 2026) | 24,132 |

### Supporting documents

Also sourced from the ICO website:

- **ICO25 strategy scorecards** — quarterly KPI tables (90-day response rate, 6-month response rate, data breach backlog) for 2023/24 Q4 through 2025/26 Q2
- **Performance and Impact Reports** (Q3 2025/26, Q4 2025/26, Q1 2026/27) — narrative context including caseload volumes, average case ages, and operational commentary
- **Case outcome descriptions PDF** — ICO's internal guidance on the three-tier outcome taxonomy (Decision → Decision Detail 1 → Decision Detail 2)

---

## How the data was processed

### Normalisation

The 12 quarterly CSV files use inconsistent column naming across releases (e.g. `ICOCaseRef` vs `Case Reference`, `CaseStatus` vs `Case Status`, `DecisionDetail1` vs `Decision Detail1`). All files were normalised to a common 21-column schema:

```
case_reference, case_status, legislation,
received_date, received_fy, received_quarter, received_month,
completed_date, completed_fy, completed_quarter, completed_month,
sector, sub_sector, primary_reason,
submitted_about_account, decision, decision_detail_1, decision_detail_2,
cctv, source_file, source_period
```

Encoding: all source files use Latin-1; the combined output is UTF-8. A `Completed Cases` title row present in some files is skipped automatically. The `cctv` column exists only in the 2024/25 Q4 file and is blank for all other periods.

Where the same `case_reference` appears in more than one quarterly file (2,473 cases across all 12 files — 1.8% of the total), the later file's version is kept, as it represents the most up-to-date record.

### Combined dataset

`ico_dp_complaints_combined.csv` — 140,820 rows, 21 columns, UTF-8. One row per completed case. The dashboard's data is pre-aggregated from this file.

### Dashboard aggregation

For performance, the dashboard embeds a pre-computed cube rather than the raw 140k-row CSV. The cube aggregates on: **completed period × sector × decision × decision detail 1 × decision detail 2 × primary reason**, yielding 22,891 aggregate rows embedded as JSON in `index.html`.

---

## Dashboard features

The dashboard has three filter controls (Period, Sector, Decision) and nine chart panels:

- **KPI tiles** — total cases, unique sectors, infringement rate, top sector
- **Case volume by quarter** — bar chart of completed cases per period
- **Decision mix over time** — stacked bar: No further action, Informal action taken, Regulatory action taken
- **Top 20 sectors** — horizontal bar, sortable
- **Decision Detail 1 breakdown** — Infringement / Potential infringement / No action / No infringement
- **Decision Detail 2 breakdown** — granular outcome (e.g. Insufficient information, Complaint recorded for information purposes)
- **Top complaint reasons** — primary legislation/right cited (Art 15, Art 17, Art 32, etc.)
- **Infringement rate by sector** — % of cases resulting in an infringement finding per sector
- **Sector summary table** — sortable table with total, action rate, infringement %, and NFA %

All charts update dynamically when filters are applied. Chart.js v4.5.1 is inlined; no internet connection is required.

---

## Key findings

### Demand surge and backlog crisis (2025/26)

Complaint volumes roughly doubled year-on-year during 2025/26. The ICO received 20,320 cases in Q3 2025/26 compared to 10,090 in Q3 2024/25. By December 2025 the open caseload had grown to approximately 37,000 cases — nearly three times the 13,587 recorded at the same point the previous year — and average case age increased from 73 days to 116 days over the same period.

The ICO's 90-day response target (covering 80% of complaints) fell from 64.8% in 2023/24 Q4 to a low of 12.3% in 2024/25 Q3, recovering only after the introduction of the Data Protection Complaints Framework in February 2026.

### The NFA rate trajectory

No Further Action (NFA) consistently accounts for the majority of outcomes but has risen materially over the period analysed:

| Period | NFA rate |
|--------|----------|
| 2023/24 Q2 | 56.1% |
| 2024/25 Q3 | 64.9% |
| 2025/26 Q3 | 69.9% |
| 2025/26 Q4 | **91.1%** |
| 2026/27 Q1 | **91.0%** |

The jump to 91% in Q4 2025/26 reflects two overlapping mechanisms:

**1. Backlog clearance via "Insufficient information to proceed"**
In Q4 2025/26, 15,858 cases (51% of all completions) were closed as NFA / Insufficient information. These cases had a median age of 173 days at closure, and 82% had been received in 2025/26 Q1–Q2 (April–September 2025). The flat age distribution — in contrast to the tight 100–119 day clustering seen in normal operation — indicates many were cleared as a cohort rather than having completed the standard initial-contact-then-90-day-wait process.

**2. New framework: "Complaint recorded for information purposes"**
A new outcome type, absent from all pre-2026 data, emerges in Q4 2025/26 (9,746 cases) and becomes the *dominant* outcome in Q1 2026/27 (16,083 cases — 67% of all completions). Under this classification, cases are closed with DD1 = "No action" and the organisation complained about is not formally engaged. Median closure time fell from 92 days in Q4 to 55 days in Q1, with 91% of Q1 cases closed within 30–90 days. Subject access requests (Art 15) account for 48% of these cases, compared to ~25% historically.

### Right of access (SAR) complaints dominate throughout

Art 15 (right of access / subject access requests) is consistently the single largest complaint type, accounting for roughly 20–25% of all cases across 2023–2025, rising to 48% of "complaint recorded for information purposes" closures in Q1 2026/27. This disproportionate uptick in SAR cases being triaged under the lighter-touch outcome is a notable feature of the post-DPCF data.

### Sector patterns

The top five sectors by complaint volume throughout the period are consistently: Finance/insurance/credit, Health, Online Technology and Telecoms, Local Government, and General business. Infringement rates (where the ICO finds a likely breach) are concentrated in Health and Justice, which show above-average infringement findings relative to complaint volume.

### Data Protection Complaints Framework (February 2026)

The ICO published an impact assessment on its complaints handling approach in Q4 2025/26, coinciding with the framework's introduction. The 90-day response KPI improved dramatically from 23.2% in Q4 2025/26 to 76.9% in Q1 2026/27. However, the improvement is substantially driven by the shift to lighter-touch "recorded for information purposes" closures, rather than a proportionate increase in investigated outcomes.

---

## Data limitations and caveats

- **Completed ≠ received**: each quarterly file contains cases *completed* in that quarter, which may have been received in earlier quarters. The received-date range in the combined dataset spans 2019 to June 2026.
- **Case-level data only**: the dataset records outcomes but not the details of what happened within a case — whether the ICO contacted the respondent organisation, what representations were made, or whether the complainant was satisfied.
- **"Complaint recorded for information purposes"** is a new outcome type introduced with the DPCF in early 2026. Its precise meaning is not defined in the published case outcome descriptions PDF, which predates the framework.
- **Duplicate handling**: 2,473 case references appear in more than one quarterly file. These are retained once, using the later file's version.
- **Legislation column**: describes the legislative regime under which the case was handled (UK GDPR, DPA 2018 Part 3, hybrid, etc.) but is populated inconsistently in earlier files.
- The combined CSV and scripts used to produce it are included in this repository for transparency. The dashboard itself is self-contained and does not require the CSV or scripts to operate.

---

## Repository structure

```
index.html        Self-contained interactive dashboard (open this)
README.md         This file
```

The repository contains only the dashboard and documentation. All source data files, the combined CSV, and build scripts are held locally and excluded via `.gitignore`. The data sources are publicly available from the ICO's website (links above) and can be used to regenerate the combined dataset if needed.

---

## Licence

Source data: © Information Commissioner's Office, published under the [Open Government Licence v3.0](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/).

Analysis, dashboard code, and this README are released under the [MIT Licence](https://opensource.org/licenses/MIT).
