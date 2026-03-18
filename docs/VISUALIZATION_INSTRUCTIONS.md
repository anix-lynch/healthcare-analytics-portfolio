# Power BI Visualization Instructions (For GUI Agents)

**Context:** This is a healthcare analytics portfolio project. We need to create executive-friendly Power BI dashboards to show business impact, not just technical proof.

---

## What We Have Already

✅ **Data Infrastructure:**
- Microsoft Fabric workspace: `HealthcareAnalytics`
- Semantic model: `Healthcare Semantic Model` (TMDL/DAX)
- Data: 55,500 synthetic patient encounters
- Dimensions: Patient, Doctor, Hospital, Condition, Medication, Insurance, Date
- Measures: Total Encounters, Avg LOS, Readmission Rate, Avg Billing

✅ **Documentation:**
- Business context: [`BUSINESS_CONTEXT.md`](BUSINESS_CONTEXT.md) — Why this analytics exists
- Dashboard spec: [`DASHBOARD_SPEC.md`](DASHBOARD_SPEC.md) — What visuals to create
- CLI automation: [`../scripts/README_POWERBI_CLI.md`](../scripts/README_POWERBI_CLI.md) — How to create report via API

---

## What We Need You To Do

### Task: Create Power BI Dashboard with Visuals

**Goal:** Build an executive-friendly dashboard that shows:
1. **4 KPI Cards** (Total Encounters, Avg LOS, Readmission Rate, Avg Billing)
2. **Readmission Rate by Condition** (Bar Chart)
3. **Length of Stay Trend** (Line Chart)
4. **Top 5 Hospitals by Volume** (Table)

**Access:**
- Workspace URL: `https://app.powerbi.com/groups/577de43f-21b4-479e-99b6-ea78f32e5216`
- Semantic Model ID: `8b5a0e39-7978-41f9-bbc1-420e1e51c059`

---

## Step-by-Step Instructions

### Option 1: Web UI (Recommended)

1. **Navigate to Workspace:**
   - Go to: `https://app.powerbi.com`
   - Sign in with Azure credentials
   - Select workspace: `HealthcareAnalytics`

2. **Create New Report:**
   - Click "+ New" → "Report"
   - Select semantic model: `Healthcare Semantic Model`
   - You're now in the visual editor

3. **Add KPI Cards (Row 1):**
   - Click "Card" visual icon
   - Drag `Total Encounters` measure to the card
   - Repeat for: `Avg LOS`, `Readmission Rate`, `Avg Billing`
   - Position cards horizontally at the top

4. **Add Bar Chart (Row 2, Left):**
   - Click "Clustered Bar Chart" icon
   - Drag `dim_diagnosis.medical_condition` to **Axis**
   - Drag `Readmission Rate` measure to **Values**
   - Sort descending by Readmission Rate

5. **Add Line Chart (Row 2, Right):**
   - Click "Line Chart" icon
   - Drag `dim_date.month_name` to **Axis**
   - Drag `Avg LOS` measure to **Values**
   - Show last 6 months

6. **Add Table (Row 3):**
   - Click "Table" icon
   - Drag `dim_hospital.hospital_name` to **Columns**
   - Drag `Total Encounters` measure to **Columns**
   - Sort descending, show top 5

7. **Format & Save:**
   - Apply clean theme (not default blue)
   - Use large fonts for KPI values (24pt+)
   - Add title: "Healthcare Operations Dashboard"
   - Click "Save" → Name: `Healthcare Operations Dashboard`

8. **Export Screenshot:**
   - File → Export → PNG
   - Save as: `healthcare-dashboard-powerbi.png`
   - Upload to: `screenshots/` folder in this repo

---

### Option 2: CLI (For Automation Nerds)

Run the bash script to create report shell:
```bash
cd scripts
./create_powerbi_dashboard.sh
```

Then follow the URL it prints to add visuals manually (one-time setup).

---

## Expected Output

### Screenshot Should Show:

```
┌─────────────────────────────────────────────────────────┐
│ Healthcare Operations Dashboard              Q1 2026   │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  [55,500]    [6.2 days]    [18.3%]    [$8,450]        │
│ Encounters    Avg LOS    Readmissions  Avg Billing     │
│                                                         │
│  ┌──────────────────┐  ┌──────────────────────────┐   │
│  │ Readmission by   │  │ Top 5 Hospitals          │   │
│  │ Condition        │  │                          │   │
│  │ ████ Diabetes    │  │ City General    9,245    │   │
│  │ ███ Hypertension │  │ Metro Hospital  8,932    │   │
│  │ ██ Cancer        │  │ Regional Med    8,801    │   │
│  └──────────────────┘  └──────────────────────────┘   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │ Length of Stay Trend                            │   │
│  │     ●───●───●───●───●───●                       │   │
│  │    /             \                              │   │
│  │   /               ●                             │   │
│  │  ●                                              │   │
│  │ Aug  Sep  Oct  Nov  Dec  Jan                   │   │
│  └─────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

---

## Business Questions This Dashboard Answers

1. **"How many patients did we treat?"** → Total Encounters card
2. **"Are we efficient at discharge?"** → Avg LOS card + trend chart
3. **"Which conditions need care improvement?"** → Readmission bar chart
4. **"Which facilities need more resources?"** → Top hospitals table
5. **"What's our quality benchmark?"** → Readmission rate card

---

## DAX Measures (Already Defined in Semantic Model)

If you need to create custom measures:

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
    CALCULATE(
        COUNT(fact_patient_encounters[encounter_id]),
        fact_patient_encounters[is_readmission] = TRUE
    ),
    COUNT(fact_patient_encounters[encounter_id])
) * 100

Avg Billing = AVERAGE(fact_patient_encounters[billing_amount])
```

---

## Troubleshooting

### "I can't see the semantic model"
- Verify you're signed in with the correct Azure account
- Check workspace permissions (you need Contributor or Admin)
- Workspace ID: `577de43f-21b4-479e-99b6-ea78f32e5216`

### "Measures aren't showing up"
- The semantic model should have pre-defined measures
- If not, create them using the DAX above
- Right-click on fact table → New Measure → Paste DAX

### "Can't export screenshot"
- Use browser screenshot tool (Cmd+Shift+4 on Mac)
- Or: File → Export → PNG (requires Premium capacity)

---

## Questions to Ask Me

If you get stuck, ping with:
- Screenshot of what you see
- Specific error message
- Which step you're on

I'll help debug.

---

## Why This Matters

This dashboard transforms the portfolio from:
- ❌ "I wrote SQL and dbt models" (technical proof)
- ✅ "I deliver business insights that drive decisions" (business impact)

Executives don't care about dbt. They care about **"Why is our readmission rate 22%?"**

---

## Deliverables

When done, commit to this repo:
1. `screenshots/healthcare-dashboard-powerbi.png` (clean screenshot)
2. Optional: `powerbi/healthcare_dashboard.pbix` (if you download the report)
3. Update `README.md` to show the new screenshot

---

**Ready? Go build! 🚀**
