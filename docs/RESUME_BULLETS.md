# Resume Highlights

This document maps resume bullet points to their corresponding code implementations in this repository.

## Core Achievements

### Healthcare Analytics Workflow
Built an end-to-end healthcare analytics workflow on 55,500 synthetic encounters across 6 clinical conditions, turning raw clinical events into KPI-ready datasets for readmission, length-of-stay, and utilization reporting.

**Code Reference:** [`api/app/main.py`](../api/app/main.py)

---

### FastAPI Data Service
Productized data access with a FastAPI service exposing 11 GET endpoints (root, encounters, lookup dimensions, stats, and search) and dataset-level pagination/filtering for analyst and dashboard workflows.

**Code Reference:** [`api/app/main.py`](../api/app/main.py) — 11 route decorators

---

### dbt Data Warehouse
Engineered a dbt star-schema warehouse with 8 core mart models plus 3 custom SQL quality checks (negative LOS, discharge ordering, readmission-window validity), producing reproducible and auditable reporting outputs.

**Code Reference:**
- Models: [`dbt/models/marts/core/`](../dbt/models/marts/core/)
- Tests: [`dbt/tests/`](../dbt/tests/)

---

### Power BI Semantic Model
Delivered a code-first Power BI semantic model (TMDL/DAX) with table definitions, relationships, and reporting measures over encounter data, keeping KPI logic reviewable in source control.

**Code Reference:** [`powerbi/`](../powerbi/)

---

### Machine Learning (Optional)
Trained and tracked an XGBoost-based readmission workflow with MLflow metric logging (accuracy and AUC-ROC) and model artifact versioning, keeping model performance and lineage reviewable.

**Code Reference:** [`ml/src/train.py`](../ml/src/train.py)

---

## Data Notice

All data used in this project is **synthetic/simulated**. No real patient information is included.

## Visual Evidence

Screenshots demonstrating these implementations are available in the [`screenshots/`](../screenshots/) directory.
