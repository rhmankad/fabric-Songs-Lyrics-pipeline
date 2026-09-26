# 🎵 End-to-End Music Catalog & Song Lyrics Data Engineering Pipeline
### *Enterprise Medallion Architecture & Automated Hyperlinked Book Publishing on Microsoft Fabric*

[![Microsoft Fabric](https://img.shields.io/badge/Platform-Microsoft%20Fabric-0078D4?logo=microsoftazure&logoColor=white)](#)
[![Apache Spark](https://img.shields.io/badge/Engine-PySpark%20%2F%20Delta%20Lake-E25A1C?logo=apachespark&logoColor=white)](#)
[![Data Modeling](https://img.shields.io/badge/Modeling-Star%20Schema%20(Gold)-005A9E)](#)
[![Orchestration](https://img.shields.io/badge/Orchestration-Fabric%20Pipelines-blue)](#)
[![Visualization](https://img.shields.io/badge/BI-Power%20BI%20Direct%20Lake-F2C811?logo=powerbi&logoColor=black)](#)

---

## 🎯 Business Problem & Project Objective
The core requirement of this project was to organize a massive, unstructured collection of song lyrics. The source files were raw PDFs containing only the lyrics—lacking any metadata, categorization, or indexing. 

To solve this, the pipeline was built to:
1. **Ingest Metadata:** Read external CSV files containing the categorizations, scales, and singer details for the raw lyric pages.
2. **Transform & Model:** Cleanse and map this data using PySpark, storing it in a query-optimized Star Schema (Gold Layer).
3. **Automate Book Generation:** Programmatically stitch the raw PDF pages together based on user-selected categories and dynamically generate a custom, hyperlinked Table of Contents (Index) that instantly navigates to the correct lyric page.

---

## 🏛️ System Architecture

```text
[ Raw PDF Catalogs & CSV Metadata ]
                │
                ▼ (OneLake / Files)
┌────────────────────────────────────────────────────────┐
│                   BRONZE LAYER                         │
│  - Raw file ingestion & landing validation             │
│  - Schema detection and quarantine tracking            │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼ (PySpark Transformation & Cleansing)
┌────────────────────────────────────────────────────────┐
│                   SILVER LAYER                         │
│  - silver_songs_metadata (Standardized strings)        │
│  - manual_metadata_overrides (Catalog corrections)     │
│  - dead_letter_songs (Quarantined schema failures)     │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼ (Star Schema Dimensional Modeling)
┌────────────────────────────────────────────────────────┐
│                    GOLD LAYER                          │
│  - fact_songs (Metrics, keys, page mapping)            │
│  - dim_category (Main & sub-category hierarchy)        │
│  - dim_scale (Musical scale keys & standardization)    │
└───────────────┬────────────────────────┬───────────────┘
                │                        │
                ▼                        ▼
┌──────────────────────────────┐  ┌──────────────────────────────────┐
│    POWER BI ANALYTICS        │  │     AUTOMATED PDF COMPILER       │
│ - Direct Lake Catalog KPIs   │  │ - Parameterized Fabric Pipeline  │
│ - Language & Scale Breakdown │  │ - Dynamic Hyperlinked Index TOC  │
│ - Metadata Quality Auditing  │  │ - Multi-file Production Books    │
└──────────────────────────────┘  └──────────────────────────────────┘
```

---

## 🗄️ Architecture & Storage (Lakehouse)
The data is stored and processed in a Fabric Lakehouse, structured into distinct layers to ensure data quality and optimized reporting.

*   **Bronze Layer:** Raw data ingestion. The raw files are securely landed in a structured hierarchical namespace format within the Lakehouse.
![Bronze Folder Structure](Pipeline%20and%20Outputs/Bronze%20Folder%20Structure.png)

*   **Silver Layer (`silver_songs_metadata`):** Cleansed, deduplicated, and validated data.
*   **Gold Layer (Star Schema):** Business-ready dimensional model featuring a central fact table (`fact_songs`) and supporting dimension tables (`dim_category`, `dim_scale`).
![Lakehouse Structure](Pipeline%20and%20Outputs/Lakehouse.png)

---

## ⚙️ Orchestration & Dynamic Parameters
The entire workflow is orchestrated using a Fabric Data Pipeline (`PL_Master_Data_Ingestion`), which seamlessly chains together multiple PySpark notebooks and wait states. 

![Master Pipeline](Pipeline%20and%20Outputs/PL_Master_Data_Ingestion%20-.png)

The pipeline executes through sequential stages, ensuring metadata overrides and cleansing steps validate successfully before passing data to the final reporting and PDF generation stages.

![Pipeline Execution Success](Pipeline%20and%20Outputs/Pipeline_Execution_success.png)

The pipeline is designed to be highly dynamic, accepting runtime parameters (e.g., dynamically processing sub-categories like "Gujarati Ganesh" and "Marathi Aarti" under the "Devotional" main category).

![Pipeline Parameters Settings](Pipeline%20and%20Outputs/Parameter_settings_PL.png)
![Pipeline Runtime Parameters](Pipeline%20and%20Outputs/Pipeline_Parameters.png)

---

## 🐍 Data Transformation & Codebase
The core transformation logic is handled via PySpark notebooks:
*   `01_Bronze_to_Silver.ipynb`: Cleanses raw metadata, standardizes nulls, and enforces data types.
*   `Apply_Metadata_Overrides.ipynb` & `Utility_Metadata_Overrides.ipynb`: Handles custom metadata overrides before final processing.
*   `Silver_to_Gold_StarSchema.ipynb`: Models the cleansed data into a Star Schema, generating the final fact and dimension tables.

**Sample Output (`fact_songs`):**
![Fact Table Sample](Pipeline%20and%20Outputs/fact_songs_Sample_data.png)

---

## 📊 Data Modeling & Analytics
To validate the Star Schema and ensure the Gold layer is perfectly optimized for downstream BI tools, the following SQL analyses were performed directly on the Fabric SQL Analytics Endpoint.

### 1. Star Schema Join Validation
Successfully connecting the fact table to dimension tables to aggregate song counts by category and scale.
![Star Schema Join](Pipeline%20and%20Outputs/Star%20Schema%20Join.png)

### 2. Data Profiling
Aggregating the cleansed data to profile the dataset by language distribution.
![Data Profiling](Pipeline%20and%20Outputs/Data%20Profiling%20Aggregation.png)

### 3. Data Quality & Exception Handling
Monitoring the pipeline's handling of missing or incomplete source data (standardized to "Unknown" during the Silver transformation).
![Data Quality Check](Pipeline%20and%20Outputs/Data%20Quality%20Check.png)

---

## 📈 Power BI Dashboard
The transformed Gold layer data is connected via Direct Lake to a Power BI Semantic Model. This interactive dashboard allows end-users to filter songs by category, scale, and language, providing instant insights into the metadata collection.

![Power BI Dashboard](Pipeline%20and%20Outputs/Dashboard_Music_Catalog_Analytics.png)

---

## 📄 Automated PDF Generator & Environment
The pipeline concludes with an automated generation step powered by a custom workspace runtime environment (`PDF_Generator_Env`) and the `Utility_PDF_Generator.ipynb` notebook. This transforms structured Lakehouse records directly into formatted, hyperlinked PDF songbooks, utilizing custom Python logic to ensure unique file outputs and prevent overwrites.

![PDF Environment](Pipeline%20and%20Outputs/PDF_Generator_Env.png)

The engine successfully outputs dynamic, collision-proof catalog books based exclusively on the parameters passed during the pipeline run.

![Generated PDF Books](Pipeline%20and%20Outputs/Sample_Generated_PDFs.png)

---

## 📁 Repository Structure

```text
├── README.md
├── Sample_Data/
│   ├── Bollywood.csv
│   ├── Garba.csv
│   └── Prarthna_Sabha.csv
├── Notebooks/
│   ├── 01_Bronze_to_Silver.ipynb
│   ├── Apply_Metadata_Overrides.ipynb
│   ├── Utility_Metadata_Overrides.ipynb
│   ├── Silver_to_Gold_StarSchema.ipynb
│   └── Utility_PDF_Generator.ipynb
└── Pipeline and Outputs/
    
---

## 🛠️ Tech Stack & Skills Highlighted

* **Cloud Platform:** Microsoft Fabric, OneLake, Lakehouse architecture
* **Data Processing & ETL:** Apache Spark (PySpark), Delta Lake, Fabric Data Pipelines
* **Data Modeling:** Dimensional Modeling (Star Schema), Slowly Changing Dimensions principles, Data Quality Audits
* **Querying & Profiling:** Spark SQL, T-SQL / SQL Endpoint
* **PDF Manipulation & Publishing:** PyMuPDF (fitz), ReportLab Canvas & Flowables
* **Business Intelligence:** Microsoft Power BI (Direct Lake / DirectQuery, Data Modeling, DAX)
