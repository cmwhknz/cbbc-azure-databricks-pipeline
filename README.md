# CBBC Azure Databricks ETL Pipeline

This project is a portfolio showcase designed to demonstrate practical exposure to Azure-based data engineering. It highlights hands-on work with Azure Databricks, Azure Data Lake Storage Gen2, PySpark, and medallion architecture through a simple end-to-end pipeline built across bronze, silver, and gold layers.

## What this project demonstrates

- Azure Databricks notebook development
- PySpark-based data ingestion and transformation
- Medallion architecture design using bronze, silver, and gold layers
- Azure Data Lake Storage Gen2 integration for raw source files
- Delta table-based processing in Databricks
- Basic cloud authentication and storage connectivity patterns in Azure

## Technologies used

- Azure Databricks
- PySpark
- Delta Lake
- Azure Data Lake Storage Gen2
- Databricks notebooks
- SQL

## Architecture overview

This project follows a medallion-style pipeline:

- **Bronze**: ingest raw CBBC files from Azure Data Lake Storage into Delta tables and capture ingestion logs
- **Silver**: clean, standardize, and combine the latest bronze records into a curated dataset
- **Gold**: aggregate the silver dataset into a business-ready analytical output

```
Azure Data Lake Storage Gen2
          ↓
bronze.cbbc_list
bronze.cbbc_summary
          ↓
silver.cbbc_stake_cureted
          ↓
gold.cbbc_stake_distribution
```

## Repository structure
```
cbbc-azure-databricks-pipeline/
├── notebooks/
│   ├── bronze_cbbc_list_ingestion.ipynb
│   ├── bronze_cbbc_summary_ingestion.ipynb
│   ├── silver_transformation.ipynb
│   └── gold_aggregation.ipynb
│
└── sql/
    ├── create_tables_bronze.ipynb
    ├── create_tables_silver.ipynb
    └── create_tables_gold.ipynb
```

## Pipeline components

### Bronze layer

#### `bronze_cbbc_list_ingestion.ipynb`

Reads raw CBBC list files from Azure Data Lake Storage and writes them to the bronze table.

**Outputs:**
- `bronze.cbbc_list`
- `bronze.cbbc_list_ingestion_log`

**Main responsibilities:**
- Configure ADLS access
- List incoming source files
- Identify files not yet processed successfully
- Ingest source data into the bronze table
- Record success or failure in the ingestion log

#### `bronze_cbbc_summary_ingestion.ipynb`

Reads raw CBBC summary Excel files from Azure Data Lake Storage and writes them to the bronze summary table.

**Outputs:**
- `bronze.cbbc_summary`
- `bronze.cbbc_summary_ingestion_log`

**Main responsibilities:**
- Configure ADLS access
- List incoming Excel files
- Identify new files to process
- Extract trade date and issuer information
- Write bronze data and ingestion log entries

### Silver layer

#### `silver_transformation.ipynb`

Builds the silver dataset for a selected trade date by joining the latest bronze records.

**Output:**
- `silver.cbbc_stake_cureted`

**Main responsibilities:**
- Load the latest records from `bronze.cbbc_list`
- Load the latest records from `bronze.cbbc_summary`
- Standardize CBBC codes
- Clean numeric columns
- Create derived metrics such as:
  - `net_contracts_change`
  - `net_cash_flow`
- Write the curated silver dataset

### Gold layer

#### `gold_aggregation.ipynb`

Aggregates the silver dataset into call level ranges for downstream analysis.

**Output:**
- `gold.cbbc_stake_distribution`

**Main responsibilities:**
- Read `silver.cbbc_stake_curated`
- Group by call level buckets
- Aggregate total net cash flow
- Write the gold analytical output

## SQL setup

The `sql/` folder contains notebooks used to create the Delta tables required by the pipeline.

### `create_tables_bronze.ipynb`
Creates bronze tables and ingestion log tables.

### `create_tables_silver.ipynb`
Creates the silver target table.

### `create_tables_gold.ipynb`
Creates the gold target table.
