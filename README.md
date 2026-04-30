
End-to-End Banking Data Pipeline (Medallion Architecture)

Project Overview

This project demonstrates a high-scale data engineering solution designed to process 25 million banking records using a Medallion Architecture (Bronze, Silver, Gold). The pipeline is built entirely within Azure Data Factory (ADF) using Mapping Data Flows for a low-code, scalable transformation process. 



Architecture Diagram





Pipeline Stages
1. Bronze Layer (Raw Landing)

* Purpose: To ingest raw, immutable data from the source systems. 
* Activities: Three parallel Copy Activities (usersData, TranscationsData, CardsData). 
* Data Format: Delimited Text (CSV). 
* Storage: ADLS Gen2 bronze container. 

2. Silver Layer (Cleansing & Standardization)

* Purpose: Filter invalid data and standardize formats for downstream integration. 
* Transformation Engine: ADF Mapping Data Flow (dataflow2). 
* Key Logic:
o Filtered users to a valid age range (18–100) and removed null IDs. 
o Standardized gender and card_brand to uppercase. 
o Handled nulls in merchant_state by defaulting to 'Online'. 
* Storage: Optimized Parquet files in the silver container. 

3. Gold Layer (Business Ready)

* Purpose: Provide a flattened, enriched dataset for BI reporting. 
* Transformation Engine: ADF Mapping Data Flow (dataflow3). 
* Key Logic:
o Left Join: Users joined with Cards on client_id. 
o Left Join: Enriched User-Card data joined with Transactions on card_id. 
o Select: Removal of duplicate columns and final schema mapping. 
* Storage: Final SQL Table / Sink for analysis. 



Technical Specifications

* Orchestration: Azure Data Factory Pipelines. 
* Compute: Managed Integration Runtime (8-core General Purpose). 
* Scaling: Handles 25M+ records using Spark-based execution under the hood of Mapping Data Flows. 
* Error Handling: Configured to stopOnFirstError for the Gold sink to ensure data integrity. 


Project Structure

├── /Documentation
│   ├── Step_1_Bronze_Ingestion.md
│   ├── Step_2_Silver_Transformation.md
│   └── Step_3_Gold_Layer_Consolidation.md
├── /Code
│   ├── BronzeIngestion.json        # Ingestion logic
│   ├── dataflow2_silver.json       # Cleaning script
│   ├── dataflow3_gold.json         # Join & Load script
│   └── MasterPipeline.json         # Full ADF Orchestration
└── README.md
??? README.md



How to Deploy

1. Storage: Create an ADLS Gen2 account with bronze and silver containers. 
2. Datasets: Define ADF datasets for CSV (Bronze), Parquet (Silver), and your SQL Sink (Gold). 
3. Import: Upload the JSON files from the /Code folder into your Azure Data Factory instance. 
4. Trigger: Execute the BankingDataPipeline to run the end-to-end flow.


