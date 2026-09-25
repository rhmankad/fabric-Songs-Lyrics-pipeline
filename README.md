# Microsoft Fabric End-to-End Data Pipeline: Songs & Lyrics

This repository showcases an end-to-end data engineering pipeline built entirely within **Microsoft Fabric**. The project implements a robust **Medallion Architecture** (Bronze, Silver, Gold) to ingest, extract, cleanse, and transform music metadata using PySpark and Fabric Data Pipelines.

## 🏗️ Architecture & Storage (Lakehouse)
The data is stored and processed in a Fabric Lakehouse, structured into distinct layers to ensure data quality and optimized reporting.

*   **Bronze Layer:** Raw data ingestion.
*   **Silver Layer (`silver_songs_metadata`):** Cleansed, deduplicated, and validated data.
*   **Gold Layer (Star Schema):** Business-ready dimensional model featuring a central fact table (`fact_songs`) and supporting dimension tables (`dim_category`, `dim_scale`).

![Lakehouse Structure](Pipeline%20and%20Outputs/Lakehouse.png)

## ⚙️ Orchestration & Dynamic Parameters
The entire workflow is orchestrated using a Fabric Data Pipeline (`PL_Master_Data_Ingestion`), which seamlessly chains together multiple PySpark notebooks and wait states. 

![Master Pipeline](Pipeline%20and%20Outputs/PL_Master_Data_Ingestion%20-.png)

The pipeline is designed to be highly dynamic, accepting runtime parameters (e.g., dynamically processing sub-categories like "Gujarati Ganesh" and "Marathi Aarti" under the "Devotional" main category).

![Pipeline Parameters](Pipeline%20and%20Outputs/Pipeline_Parameters.png)

## 🐍 Data Transformation & Codebase
The core transformation logic is handled via PySpark notebooks:
*   `01_Bronze_to_Silver.ipynb`: Cleanses raw metadata, standardizes nulls, and enforces data types.
*   `02_Silver_to_Gold.ipynb`: Models the cleansed data into a Star Schema, generating the final fact and dimension tables.
*   `Utility_Metadata_Overrides.ipynb`: Handles custom metadata overrides before final processing.

**Sample Output (`fact_songs`):**
![Fact Table Sample](Pipeline%20and%20Outputs/fact_songs_Sample_data.png)

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

## 📈 Power BI Dashboard
The transformed Gold layer data is connected via Direct Lake to a Power BI Semantic Model. This interactive dashboard allows end-users to filter songs by category, scale, and language, providing instant insights into the metadata collection.

![Power BI Dashboard](Pipeline%20and%20Outputs/Dashboard_Music_Catalog_Analytics.png)

## 📄 Automated PDF Generator & Environment
The pipeline concludes with an automated generation step powered by a custom workspace runtime environment (`PDF_Generator_Env`) and the `Utility_PDF_Generator.ipynb` notebook. This transforms structured Lakehouse records directly into formatted, hyperlinked PDF songbooks, utilizing custom Python logic to ensure unique file outputs and prevent overwrites.

![PDF Environment](Pipeline%20and%20Outputs/PDF_Generator_Env.png)
