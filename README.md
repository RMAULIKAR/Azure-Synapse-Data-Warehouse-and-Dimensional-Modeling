# Azure Synapse Data Warehouse & Dimensional Modeling

##  Overview

A hands-on **Azure Synapse Analytics** data warehousing project that demonstrates an end-to-end flow from raw data ingestion to a **dimensional data warehouse and business views**.

The project uses **Azure Synapse Pipelines, Mapping Data Flow, ADLS/Blob Storage, Synapse Serverless SQL, Parquet, external tables, and Star Schema dimensional modeling**.

### Key Highlights

* Cloud-based data warehouse solution using **Azure Synapse Analytics**
* Raw data ingestion into **ADLS/Blob Storage**
* Transformation using **Synapse Mapping Data Flow**
* **Bronze/Raw → Silver → Gold** data processing layers
* Parquet-based data storage
* **External tables and external data sources** using Synapse Serverless SQL
* Fact and Dimension modeling using **Star Schema**
* SQL-based business views for sales analysis

---

##  Architecture

```text
                    ┌─────────────────────┐
                    │     Raw CSV Data    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Raw Ingestion      │
                    │  Synapse Pipeline   │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   BRONZE / RAW      │
                    │   ADLS / Parquet    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Mapping Data Flow │
                    │   Transformation    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │       SILVER        │
                    │   Parquet / ADLS    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Synapse Serverless  │
                    │     SQL Layer       │
                    └──────────┬──────────┘
                               │
                  ┌────────────┴────────────┐
                  ▼                         ▼
             Dimensions                  Fact
                  │                         │
                  └────────────┬────────────┘
                               ▼
                    ┌─────────────────────┐
                    │       GOLD          │
                    │  Star Schema / DWH  │
                    └──────────┬──────────┘
                               │
                               ▼
                    Business Views / Analytics
```

---

##  Data Flow

The project follows a simple warehouse processing flow:

**Raw CSV → Bronze → Transformation → Silver → Dimensional Modeling → Gold → Business Views**

The repository contains separate Synapse pipeline definitions for **raw ingestion** and **Silver-layer processing**, along with datasets, linked services, integration runtime configuration, data flow, and SQL scripts.

---

##  Bronze / Raw Layer

The `rawIngestion` pipeline reads raw delimited/CSV data from Azure Storage and writes it to **Parquet** in the Data Lake.

```text
Raw CSV
   ↓
Synapse Pipeline
   ↓
Bronze / Parquet
```

---

##  Silver Layer

The `SilverIngestion` pipeline executes the `TransfromLayer` Mapping Data Flow and, after successful completion, executes a SQL script to create the Silver external table.

The Mapping Data Flow performs transformations including:

* Filtering records based on `UnitPrice`
* Creating a domain from `CustomerEmail`
* Selecting the required domain value
* Writing the transformed data as Parquet

```text
Bronze / Parquet
      ↓
Mapping Data Flow
      ↓
Filtering + Derived Columns
      ↓
Silver / Parquet
```

---

##  Dimensional Modeling

The project builds a **Star Schema** in the Gold layer.

### Dimension Tables

* `DimCustomer`
* `DimProduct`
* `DimGeography`
* `DimOrders`

### Fact Table

* `FactOrders`

Surrogate keys are generated for the dimension tables using `ROW_NUMBER()`, and the fact table is created by joining the Silver data with the corresponding dimensions.

```text
DimCustomer ──────┐
DimProduct ───────┤
DimGeography ─────┼──► FactOrders
DimOrders ────────┘
```

---

##  Synapse Serverless SQL & External Tables

The project uses **Synapse Serverless SQL** to work with data stored externally in Azure Storage.

External data sources are configured using **managed identity credentials**, with Parquet and Delta file formats defined for external access.

The Silver and Gold schemas expose the Data Lake data through **external tables**, allowing SQL-based querying without physically loading the data into traditional database storage.

---

##  Business View

A business view is created on top of `FactOrders` to provide sales-level metrics such as:

* Total Orders
* Total Quantity
* Total Sales
* Average Order Value
* Minimum Order Value
* Maximum Order Value

---

##  Synapse Components

| Component               | Purpose                                                         |
| ----------------------- | --------------------------------------------------------------- |
| **Synapse Pipelines**   | Data ingestion and orchestration                                |
| **Mapping Data Flow**   | Transformation of raw data                                      |
| **ADLS / Blob Storage** | External data storage                                           |
| **Serverless SQL Pool** | SQL-based querying and warehouse layer                          |
| **External Tables**     | Accessing Data Lake data through SQL                            |
| **SQL Scripts**         | Creating schemas, external tables, dimensions, facts, and views |
| **Datasets**            | Source and target dataset definitions                           |
| **Linked Services**     | Connectivity to Azure resources                                 |

---

##  Technologies & Concepts

**Cloud:** Microsoft Azure · Azure Synapse Analytics · Azure Data Lake Storage

**Processing:** Synapse Pipelines · Mapping Data Flow · Serverless SQL

**Storage:** Azure Storage · Parquet

**Data Warehouse:** External Tables · SQL Schemas · Fact & Dimension Modeling

**Architecture:** Bronze/Raw → Silver → Gold · Star Schema

**Analytics:** Business Views · SQL Aggregations

---

##  Outcome

This project provides practical exposure to building a **cloud-based data warehouse using Azure Synapse Analytics**, covering the complete workflow from raw data ingestion and transformation to **Parquet-based Data Lake storage, Serverless SQL external tables, Star Schema dimensional modeling, and business-level SQL views**.
