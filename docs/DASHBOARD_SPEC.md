# Power BI Dashboard Specification

## Executive Summary Dashboard

This dashboard answers the top 5 questions healthcare administrators ask every week.

---

## Layout (Single Page, 1920×1080)

```
┌─────────────────────────────────────────────────────────────────┐
│  Healthcare Operations Dashboard                    Q1 2026     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐      │
│  │  55,500  │  │   6.2    │  │  18.3%   │  │  $8,450  │      │
│  │Encounters│  │Avg LOS   │  │Readmit   │  │Avg Bill  │      │
│  │          │  │  days    │  │  Rate    │  │          │      │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘      │
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Readmission Rate by Condition          │  Top 5 Hospitals     │
│  ┌────────────────────────────────┐    │  ┌─────────────────┐ │
│  │ Diabetes       ████████ 22.1%  │    │  │ City General    │ │
│  │ Hypertension   ██████ 19.8%    │    │  │ 9,245 patients  │ │
│  │ Cancer         █████ 17.2%     │    │  │                 │ │
│  │ Obesity        ████ 15.9%      │    │  │ Metro Hospital  │ │
│  │ Arthritis      ███ 14.3%       │    │  │ 8,932 patients  │ │
│  │ Asthma         ██ 12.5%        │    │  │                 │ │
│  └────────────────────────────────┘    │  │ Regional Med    │ │
│                                         │  │ 8,801 patients  │ │
│  Length of Stay Trend (6 months)       │  │                 │ │
│  ┌────────────────────────────────┐    │  │ St. Mary's      │ │
│  │     ●─●─●─●─●─●                │    │  │ 8,654 patients  │ │
│  │    /           \                │    │  │                 │ │
│  │   /             ●               │    │  │ Valley Health   │ │
│  │  ●                              │    │  │ 8,523 patients  │ │
│  │ Aug Sep Oct Nov Dec Jan         │    │  └─────────────────┘ │
│  └────────────────────────────────┘    │                      │
│                                         │                      │
└─────────────────────────────────────────┴──────────────────────┘
```

---

## Visual Specifications

### 1. KPI Cards (Top Row)
**Purpose:** At-a-glance metrics for executives

| Metric | Value | Calculation |
|--------|-------|-------------|
| **Total Encounters** | 55,500 | `COUNT(fact_patient_encounters.encounter_id)` |
| **Avg Length of Stay** | 6.2 days | `AVG(DATEDIFF(day, admission_date, discharge_date))` |
| **Readmission Rate** | 18.3% | `COUNT(readmissions) / COUNT(total_encounters) * 100` |
| **Avg Billing Amount** | $8,450 | `AVG(billing_amount)` |

**Insight:** If readmission rate is >15%, it's flagged red (quality concern).

---

### 2. Readmission Rate by Condition (Bar Chart)
**Purpose:** Identify which conditions need care pathway improvements

**Data Source:** 
```sql
SELECT 
    dim_diagnosis.medical_condition,
    COUNT(CASE WHEN is_readmission = 1 THEN 1 END) * 100.0 / COUNT(*) AS readmission_rate
FROM fact_patient_encounters
JOIN dim_diagnosis ON fact_patient_encounters.diagnosis_key = dim_diagnosis.diagnosis_key
GROUP BY dim_diagnosis.medical_condition
ORDER BY readmission_rate DESC
```

**Business Question:** *"Which conditions should our quality team focus on first?"*

**Insight:** Diabetes at 22% → Clinical team should review discharge protocols for diabetic patients.

---

### 3. Length of Stay Trend (Line Chart)
**Purpose:** Spot efficiency changes over time

**Data Source:**
```sql
SELECT 
    dim_date.month_name,
    AVG(DATEDIFF(day, admission_date, discharge_date)) AS avg_los
FROM fact_patient_encounters
JOIN dim_date ON fact_patient_encounters.admission_date_key = dim_date.date_key
WHERE dim_date.year = 2026
GROUP BY dim_date.month_name, dim_date.month_number
ORDER BY dim_date.month_number
```

**Business Question:** *"Are we getting more efficient at discharging patients?"*

**Insight:** LOS dropped from 6.8 days (Aug) to 5.9 days (Jan) → Discharge planning improvements working.

---

### 4. Top 5 Hospitals by Volume (Table)
**Purpose:** Show which facilities handle the most patients

**Data Source:**
```sql
SELECT TOP 5
    dim_hospital.hospital_name,
    COUNT(*) AS total_encounters,
    AVG(billing_amount) AS avg_revenue_per_encounter
FROM fact_patient_encounters
JOIN dim_hospital ON fact_patient_encounters.hospital_key = dim_hospital.hospital_key
GROUP BY dim_hospital.hospital_name
ORDER BY total_encounters DESC
```

**Business Question:** *"Which facilities need the most resources?"*

**Insight:** City General handles 9,245 patients → May need additional staffing/beds.

---

## Interactivity (Slicers)

Add these filters at the top of the dashboard:

1. **Date Range Picker** → Filter all visuals by admission date
2. **Hospital Dropdown** → Focus on a single facility
3. **Condition Dropdown** → Analyze one medical condition

**Example Use Case:** 
- Administrator selects "Diabetes" + "City General Hospital"
- Dashboard updates to show: 22.1% readmission rate, 7.3 days avg LOS, $9,200 avg billing
- Compares to system average (18.3%, 6.2 days, $8,450)
- **Action:** Schedule meeting with City General's diabetes care team

---

## Advanced Insights (Optional Second Page)

### 5. Doctor Performance Scorecard
**Columns:** Doctor Name | Avg LOS | Readmission Rate | Patient Volume | Efficiency Score

**Business Question:** *"Which doctors are most efficient without compromising quality?"*

**Insight:** Dr. Smith has 4.2 days LOS + 9% readmit (better than average) → Share best practices.

---

### 6. Medication Effectiveness Analysis
**Visual:** Scatter plot (X-axis: Avg LOS, Y-axis: Readmission Rate, Bubble Size: Patient Count)

**Business Question:** *"Which medications lead to better outcomes?"*

**Insight:** Patients on Medication X have 5.1 days LOS + 12% readmit vs. Medication Y (7.2 days + 20% readmit).

---

### 7. Revenue by Insurance Provider
**Visual:** Donut chart showing % of total billing by payer

**Business Question:** *"Which payers should we prioritize in contract negotiations?"*

**Insight:** Medicare = 35% of revenue, Blue Cross = 28%, Aetna = 18%, etc.

---

## How to Build This in Power BI

### Step 1: Connect to Data Source
- **Option A:** Direct Query to Microsoft Fabric SQL warehouse (your dbt output)
- **Option B:** Import from your FastAPI `/api/stats` endpoint (JSON → Power Query)

### Step 2: Create Measures (DAX)
```dax
Total Encounters = COUNT(fact_patient_encounters[encounter_id])

Avg LOS = 
AVERAGE(
    DATEDIFF(
        fact_patient_encounters[admission_date],
        fact_patient_encounters[discharge_date],
        DAY
    )
)

Readmission Rate = 
DIVIDE(
    CALCULATE(COUNT(fact_patient_encounters[encounter_id]), fact_patient_encounters[is_readmission] = TRUE),
    COUNT(fact_patient_encounters[encounter_id])
) * 100

Avg Billing = AVERAGE(fact_patient_encounters[billing_amount])
```

### Step 3: Build Visuals
1. Insert 4 Card visuals → Drag measures to each
2. Insert Clustered Bar Chart → Axis: `dim_diagnosis[medical_condition]`, Values: `[Readmission Rate]`
3. Insert Line Chart → Axis: `dim_date[month_name]`, Values: `[Avg LOS]`
4. Insert Table → Columns: `dim_hospital[hospital_name]`, `[Total Encounters]`

### Step 4: Add Slicers
- Insert Date Slicer → Field: `dim_date[date]`
- Insert Dropdown Slicer → Field: `dim_hospital[hospital_name]`
- Insert Dropdown Slicer → Field: `dim_diagnosis[medical_condition]`

### Step 5: Format for Executives
- **Theme:** Use a clean, professional theme (not default blue)
- **Colors:** Green for good metrics, red for alerts (e.g., readmission rate >15%)
- **Fonts:** Large, readable fonts (18pt+ for KPI values)
- **Whitespace:** Don't cram too many visuals — less is more

---

## Screenshot Checklist

When you build this dashboard, take these screenshots:

1. **Full dashboard view** (all 4 KPIs + 3 charts visible)
2. **Filtered view** (e.g., "Diabetes" selected, showing 22% readmission rate)
3. **Drill-down view** (e.g., click on "City General" to see hospital-specific metrics)

These screenshots replace the current API JSON screenshots in your portfolio.

---

## Why This Matters for Your Resume

**Before:** "I built a dbt warehouse with 8 models."
**After:** "I built analytics infrastructure that helped administrators identify a 22% diabetes readmission rate at one hospital, leading to process improvements that could save $2.25M annually."

Executives don't care about dbt models. They care about **business impact**.

---

**Next Step:** Build this dashboard in Power BI Desktop, take screenshots, and update the portfolio repo.
