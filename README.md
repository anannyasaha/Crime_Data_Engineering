# Ontario Crime Data Pipeline – Microsoft Fabric

## Project Summary
This repository contains six Microsoft Fabric notebooks that build an end-to-end data engineering pipeline for Statistics Canada crime data. Using the **Fabric Lakehouse + PySpark + Delta** architecture, the project ingests raw StatCan data, processes it through the **Medallion Architecture**, and produces a **Gold-layer star schema** ready for Power BI analytics.

The pipeline also generates **three aggregated Gold tables** optimized for high-performance visualization in Power BI.

---

## Notebook Structure

| Order | Notebook Name | Purpose |
|------|----------------|---------|
| **1** | `raw_ingestion.ipynb` | Pulls latest data from Statistics Canada (REST API / CSV download) → saves to `/Files/Raw/` |
| **2** | `raw_to_landing.ipynb` | Cleans raw CSVs, validates schema, removes corrupted rows → saves to `/Files/Landing/` |
| **3** | `landing_to_bronze.ipynb` | Loads Landing data → creates Bronze Delta table `tblcrime_bronze` |
| **4** | `silver_transformation.ipynb` | Cleans, standardizes, and reshapes Bronze data → creates `tblcrime_silver` |
| **5** | `gold_transformation_v2.ipynb` | Builds Fact + Dimension star schema tables + aggregated Gold tables for optimized Power BI reporting |
---

## Final Data Model (Gold Layer)

### **Fact Table**
- `fact_crime_incidents`  
  Contains incident counts, clearance rates, adult/youth charges, violation categories, and more.

### **Dimension Tables**
- `dim_municipality`
- `dim_violations`
- `dim_time`

###  **Aggregated Tables (Gold v2)**  
Used to speed up Power BI visuals:
- `vw_comparative_analysis`
- `vw_municipality_deep_dive`
- `vw_overview_kpis`



---

## 🛠 Requirements

Before running the notebooks, ensure you have:

- A **Microsoft Fabric Workspace**
- A **Lakehouse** created (recommended name: `lakehouse_crime`)
- The following directories in the Lakehouse:
/Files/Raw/
/Files/Landing/
/Tables/

Fabric automatically includes:
- PySpark  
- Delta Lake  
- Pandas  
- Requests  

No additional installation is required.

---

##  How to Run This Project in Microsoft Fabric

> Follow this sequence **exactly** for a clean pipeline run.

---

### **1️ Raw Ingestion – `raw_ingestion.ipynb`**
This notebook:
- Calls the StatCan API to download the latest dataset ZIP  
- Extracts CSVs  
- Writes them to `/Files/Raw/` inside your Lakehouse  

No manual downloads required.

---

### **2️ Raw → Landing – `raw_to_landing.ipynb`**
This notebook:
- Reads Raw CSVs  
- Cleans column names  
- Normalizes schema  
- Writes clean data to `/Files/Landing/`

---

### **3️ Landing → Bronze – `landing_to_bronze.ipynb`**
This notebook:
- Loads Landing data  
- Creates the Bronze Delta table **`tblcrime_bronze`**  
- Uses MERGE logic to avoid duplicate inserts  

---

### **4️ Silver Transformation – `silver_transformation.ipynb`**
This notebook:
- Removes null values  
- Standardizes violation names  
- Joins metadata  
- Creates **`tblcrime_silver`**, the clean analytics-ready dataset  

---

### **5️ Gold Transformation – `gold_transformation.ipynb`**
Creates the **Star Schema**:

#### Fact Table
- `fact_crimestats`

#### Dimension Tables
- `dim_municipality`  
- `dim_violations`  
- `dim_year`

---

### **6️ Gold Transformation V2 – `gold_transformation_v2.ipynb`**
Creates performance-optimized aggregated tables:

- Top crime categories  
- Highest clearance rates  
- Youth vs Adult charge comparisons  
- Yearly crime trends  

These significantly improve Power BI refresh performance.

---

##  Connecting Power BI

1. Open **Power BI Desktop**  
2. Go to **OneLake Data Hub**  
3. Connect to your Fabric Lakehouse  
4. Select **Gold Layer Tables**  
5. Start building dashboards using:
   - Dimensional filters  
   - Municipality slicers  
   - Crime category drilldowns  
   - YOY heatmaps and trend visuals  

---

##  Refreshing the Pipeline

When new StatCan data is released:

1. Run **`raw_ingestion.ipynb`**  
2. Re-run notebooks **2 → 6**  
3. Refresh the Power BI dataset  

The Medallion architecture ensures clean, traceable, repeatable updates.

---

## Folder Paths Used in This Project

### **Files Layer**
/Files/Raw/
/Files/Landing/

### **Tables Layer**
- `tblcrime_bronze`
- `tblcrime_silver`
- `fact_crimestats`
- `dim_municipality`
- `dim_violations`
- `dim_years`
- `vw_comparative_analysis`
- `vw_municipality_deep_dive`
- `vw_overview_kpis`
