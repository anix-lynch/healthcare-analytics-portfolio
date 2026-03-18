# Business Context: Why This Analytics Stack Exists

## The Problem Healthcare Organizations Face

Healthcare administrators, clinical leaders, and finance teams need to answer critical operational and clinical questions, but their data is scattered across multiple systems. Without a unified analytics layer, they can't:

- Identify high-cost readmission patterns
- Optimize bed utilization and length-of-stay
- Track quality metrics by provider or facility
- Forecast resource needs based on patient volume trends

## Who Uses This Analytics Stack?

### 1. **Hospital Administrators** (Primary Users)
**Questions They Ask:**
- Which hospitals have the highest readmission rates? (Quality metric)
- What's our average length-of-stay by condition? (Efficiency metric)
- Which insurance providers generate the most revenue? (Financial planning)
- Are we seeing seasonal patterns in admissions? (Capacity planning)

**What They Get:**
- KPI dashboards showing readmission rates, LOS trends, and financial metrics
- Drill-down capability by hospital, condition, and time period
- Alerts when metrics exceed thresholds (e.g., readmission rate > 15%)

### 2. **Clinical Quality Teams** (Secondary Users)
**Questions They Ask:**
- Which doctors have the best patient outcomes? (Performance review)
- Are certain medications associated with better recovery times? (Treatment optimization)
- Which conditions have the highest readmission risk? (Care pathway improvement)

**What They Get:**
- Provider-level performance scorecards
- Medication effectiveness analysis
- Risk stratification for post-discharge follow-up

### 3. **Finance/Revenue Cycle Teams** (Secondary Users)
**Questions They Ask:**
- What's our average billing amount by insurance provider? (Contract negotiation)
- Which conditions generate the most revenue? (Service line planning)
- Are we seeing payment delays from specific payers? (Collections strategy)

**What They Get:**
- Revenue analysis by payer, condition, and facility
- Billing cycle time metrics
- Profitability analysis by service line

## The Original Analysis Question

**Core Question:** *"Why are 18% of our patients being readmitted within 30 days, and which factors (doctor, hospital, condition, medication) are the strongest predictors?"*

This led to building:
1. **API Layer** → Fast data access for analysts and dashboards
2. **dbt Warehouse** → Clean, trustworthy star schema for reporting
3. **Power BI Dashboards** → Self-service analytics for stakeholders
4. **ML Model** → Predictive readmission risk scoring for care teams

## What the Star Schema Enables

### Fact Table: `fact_patient_encounters`
**Measures (What We Count):**
- Total encounters
- Total billing amount
- Average length-of-stay
- Readmission count (within 30 days)
- Readmission rate (%)

### Dimension Tables (How We Slice the Data):**
- **dim_patient** → Age, gender, blood type (demographic analysis)
- **dim_doctor** → Provider name (performance comparison)
- **dim_hospital** → Facility name (site benchmarking)
- **dim_diagnosis** → Medical condition (clinical analysis)
- **dim_medication** → Treatment prescribed (medication effectiveness)
- **dim_insurance** → Payer type (financial analysis)
- **dim_date** → Admission/discharge dates (trend analysis)

### Example Business Questions Answered:

| Question | Fact | Dimensions | Insight |
|----------|------|------------|---------|
| "Which hospital has the highest readmission rate for diabetes patients?" | Readmission rate (%) | Hospital + Diagnosis | Hospital A: 22% vs. Hospital B: 12% → Investigate Hospital A's discharge protocols |
| "What's our average LOS for cancer patients by insurance type?" | Avg LOS (days) | Diagnosis + Insurance | Medicare patients: 8.2 days vs. Private: 5.1 days → Potential discharge planning issue |
| "Which doctors have the shortest LOS without increasing readmissions?" | Avg LOS + Readmission rate | Doctor + Diagnosis | Dr. Smith: 4.2 days LOS, 9% readmit vs. Dr. Jones: 6.1 days LOS, 8% readmit → Dr. Smith is more efficient |
| "Are we seeing more hypertension admissions in winter months?" | Total encounters | Diagnosis + Date (month) | Dec-Feb: +35% hypertension admits → Plan for seasonal staffing |

## Why Data Analysts Are Hired for This

Healthcare organizations hire data analysts to:

1. **Build the infrastructure** (API + warehouse + dashboards) so stakeholders can self-serve
2. **Monitor KPIs** and alert leaders when metrics degrade (e.g., readmission spike)
3. **Investigate root causes** when problems surface (e.g., why did Hospital C's LOS increase 20%?)
4. **Predict future needs** using ML (e.g., which patients need extra discharge support?)
5. **Prove ROI** of clinical initiatives (e.g., did the new care pathway reduce readmissions?)

## Real-World Impact (If This Were Production Data)

**Scenario:** Dashboard shows Hospital A has a 22% readmission rate for diabetes patients (vs. 12% system average).

**Action Taken:**
1. Clinical team reviews discharge protocols at Hospital A
2. Discovers patients aren't getting follow-up appointments scheduled before discharge
3. Implements new workflow: schedule follow-up before patient leaves
4. 3 months later: Readmission rate drops to 14%
5. **Estimated savings:** 150 fewer readmissions/year × $15K/readmission = **$2.25M/year**

This is why healthcare organizations invest in analytics infrastructure — it directly improves patient outcomes and reduces costs.

## What This Portfolio Demonstrates

This project proves the candidate can:
- **Understand the business problem** (not just write SQL)
- **Build production-grade infrastructure** (API, warehouse, dashboards)
- **Translate data into insights** (not just tables, but answers to real questions)
- **Communicate with non-technical stakeholders** (executives don't care about dbt models, they care about readmission rates)

---

**Next Step:** See the Power BI dashboard that brings this data to life → [`docs/DASHBOARD_SPEC.md`](DASHBOARD_SPEC.md)
