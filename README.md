# Enterprise Data Warehouse

A comprehensive SQL data project that demonstrates building a layered data warehouse (bronze → silver → gold), performing exploratory data analysis (EDA), and executing advanced data analytics (ADA) using plain SQL scripts and CSV data.

This repo provides sample source files, ingestion and transformation scripts, exploratory data analysis queries, advanced analytical queries, and quality checks to learn and prototype ETL/ELT processes, dimensional modeling, and data analytics on SQL Server.

**Table of Contents**

- [Project Structure](#project-structure)
- [Quick Start](#quick-start)
- [Data Warehousing](#data-warehousing)
- [Exploratory Data Analysis (EDA)](#exploratory-data-analysis-eda)
- [Advanced Data Analytics (ADA)](#advanced-data-analytics-ada)
- [ETL Flow](#etl-flow)
- [Running Quality Checks](#running-quality-checks)
- [Contributing](#contributing)
- [License](#license)

**Project Structure**

- `datasets/`
	- `csv-files/` — example output CSVs (gold-level tables) used for verification and demonstration.
	- `source-crm/`, `source-erp/` — sample raw source CSVs used as input to the pipeline.
- `scripts/`
	- `datawarehousing/` — main data warehouse scripts
		- `init_database.sql` — creates the database schema, roles, and basic objects.
		- `bronze/` — DDL and load procedures for the raw/ingest layer.
		- `silver/` — transformation DDL and procedures that clean and conform data.
		- `gold/` — dimensional models and final DDL for analytics-ready tables.
	- `eda/` — exploratory data analysis scripts.
	- `ada/` — analytical data analysis scripts (change over time, cumulative analysis, performance analysis, segmentation, and reporting).
- `tests/` — SQL-based quality checks (this folder is ignored by Git in this repo).

**Quick Start**

Prerequisites:

- Microsoft SQL Server (or a compatible SQL engine).
- `sqlcmd` (or use Azure Data Studio / SSMS to run scripts).

Initialize the database (replace server/user/password as needed):

```powershell
sqlcmd -S <SERVER> -U <USER> -P <PASSWORD> -i scripts\datawarehousing\init_database.sql
```

Load the bronze (raw ingest) layer (example):

```powershell
sqlcmd -S <SERVER> -U <USER> -P <PASSWORD> -i scripts\datawarehousing\bronze\proc_load_bronze.sql
```

Run silver transformations:

```powershell
sqlcmd -S <SERVER> -U <USER> -P <PASSWORD> -i scripts\datawarehousing\silver\proc_load_silver.sql
```

Apply gold modeling DDL:

```powershell
sqlcmd -S <SERVER> -U <USER> -P <PASSWORD> -i scripts\datawarehousing\gold\ddl.gold.sql
```

Adjust the above commands for your environment (Windows PowerShell shown).

## Data Warehousing

The `scripts/datawarehousing/` directory contains the core data warehouse implementation following the medallion architecture (Bronze → Silver → Gold layers).

### Overview

This section implements a complete data warehouse pipeline that:
- Ingests raw CSV data from multiple source systems (CRM and ERP)
- Transforms and cleans data through multiple layers
- Produces analytics-ready dimensional models

### Database Setup

The data warehouse uses a database named `DataWarehouse` with three schemas:
- **bronze**: Raw, unprocessed data ingested from source systems
- **silver**: Cleaned, conformed, and deduplicated data
- **gold**: Dimensional models optimized for analytics (facts and dimensions)

### Scripts

1. **`init_database.sql`**
   - Creates the `DataWarehouse` database
   - Sets up bronze, silver, and gold schemas
   - ⚠️ **Warning**: This script drops and recreates the database if it exists

2. **`bronze/`** - Raw Data Ingestion
   - `ddl_bronze.sql`: Creates staging tables for raw source data
     - `bronze.crm_cust_info`: Customer data from CRM system
     - `bronze.crm_prd_info`: Product data from CRM system
     - `bronze.crm_sales_details`: Sales transactions from CRM system
     - `bronze.erp_cust_az12`: Customer data from ERP system
     - `bronze.erp_loc_a101`: Location data from ERP system
     - `bronze.erp_px_cat_g1v2`: Product category data from ERP system
   - `proc_load_bronze.sql`: Stored procedure to load CSV files into bronze tables

3. **`silver/`** - Data Transformation
   - `ddl_silver.sql`: Creates cleaned and conformed tables
     - Standardizes data formats
     - Handles data quality issues
     - Creates canonical domain models
   - `proc_load_silver.sql`: Stored procedure to transform bronze data into silver layer

4. **`gold/`** - Dimensional Modeling
   - `ddl.gold.sql`: Creates star schema dimensional models
     - **Fact Tables**: `gold.fact_sales` - Sales transaction facts
     - **Dimension Tables**: 
       - `gold.dim_customers` - Customer dimension
       - `gold.dim_products` - Product dimension
     - Optimized for analytical queries and reporting

### Execution Order

1. Initialize database: `scripts\datawarehousing\init_database.sql`
2. Create bronze tables: `scripts\datawarehousing\bronze\ddl_bronze.sql`
3. Load bronze data: `scripts\datawarehousing\bronze\proc_load_bronze.sql`
4. Create silver tables: `scripts\datawarehousing\silver\ddl_silver.sql`
5. Transform to silver: `scripts\datawarehousing\silver\proc_load_silver.sql`
6. Create gold tables: `scripts\datawarehousing\gold\ddl.gold.sql`
7. Populate gold tables: Run transformation queries (typically part of silver→gold process)

## Exploratory Data Analysis (EDA)

The `scripts/eda/` directory contains SQL scripts for exploring and understanding your data before building analytical models.

### Overview

EDA scripts help you:
- Understand database structure and table schemas
- Explore data distributions and patterns
- Identify data quality issues
- Discover relationships between entities
- Analyze data ranges and magnitudes

### Scripts

1. **`0_init_database.sql`**
   - Creates the `DataWarehouseAnalytics` database
   - Sets up the `gold` schema for analytical queries
   - ⚠️ **Warning**: This script drops and recreates the database if it exists

2. **`1_database_exploration.sql`**
   - Lists all tables and their schemas using `INFORMATION_SCHEMA`
   - Inspects column metadata and data types
   - Helps understand the overall database structure

3. **`2_dimensions_exploration.sql`**
   - Explores dimension tables (customers, products)
   - Analyzes cardinality and distinct values
   - Identifies potential data quality issues in dimensions

4. **`3_date_range_exploration.sql`**
   - Examines date ranges in fact tables
   - Identifies gaps or outliers in temporal data
   - Helps understand the time coverage of your data

5. **`4_measures_exploration.sql`**
   - Analyzes numeric measures (sales amounts, quantities)
   - Calculates basic statistics (min, max, avg, sum)
   - Identifies outliers and data anomalies

6. **`5_magnitude_analysis.sql`**
   - Analyzes the scale and distribution of key metrics
   - Helps understand data volumes and patterns
   - Useful for capacity planning

7. **`6_ranking_analysis.sql`**
   - Ranks entities by various metrics
   - Identifies top performers (customers, products, etc.)
   - Uses window functions for ranking operations

### Usage

Run these scripts in sequence to systematically explore your data warehouse. They can be executed against the `DataWarehouse` database after the gold layer is populated, or against `DataWarehouseAnalytics` if you've set up a separate analytics database.

## Advanced Data Analytics (ADA)

The `scripts/ada/` directory contains advanced analytical queries for deep data analysis, reporting, and business intelligence.

### Overview

ADA scripts provide:
- Time-series analysis and trend identification
- Cumulative and running calculations
- Performance benchmarking and comparisons
- Customer and product segmentation
- Comprehensive reporting queries

### Scripts

1. **`1_change_over_time_analysis.sql`**
   - Tracks trends and changes in key metrics over time
   - Analyzes sales performance by year, month, quarter
   - Uses date functions (`DATEPART`, `DATETRUNC`, `FORMAT`)
   - Identifies seasonality and growth patterns
   - Calculates period-over-period comparisons

2. **`2_cumulative_analysis.sql`**
   - Calculates running totals and moving averages
   - Uses window functions (`SUM() OVER()`, `AVG() OVER()`)
   - Tracks cumulative performance over time
   - Useful for growth analysis and long-term trend identification

3. **`3_performance_analysis.sql`**
   - Benchmarks performance across different dimensions
   - Compares metrics between time periods, customers, or products
   - Calculates performance ratios and percentages
   - Identifies best and worst performers

4. **`4_data_segmentation.sql`**
   - Segments customers or products into groups
   - Uses clustering techniques and business rules
   - Creates actionable segments for marketing or operations
   - Analyzes segment characteristics and behaviors

5. **`5_data_segmentation.sql`**
   - Additional segmentation analysis
   - May focus on different segmentation criteria or methods

6. **`6_report_customers.sql`**
   - Comprehensive customer reporting
   - Customer lifetime value, purchase history, demographics
   - Customer ranking and segmentation reports
   - Ready-to-use queries for customer analytics

7. **`7_report_products.sql`**
   - Comprehensive product reporting
   - Product performance metrics, sales trends
   - Product ranking and profitability analysis
   - Ready-to-use queries for product analytics

### Usage

These scripts are designed to run against the gold layer of your data warehouse. They assume the dimensional model (fact_sales, dim_customers, dim_products) is populated and ready for analysis.

**ETL Flow**

- Bronze: ingest raw CSVs into staging tables (minimal transformations).
- Silver: clean, deduplicate, and conform data; produce canonical domain tables.
- Gold: build dimensional models and analytic-ready tables (facts and dims).

**Running Quality Checks**

To run the provided SQL quality checks:

```powershell
sqlcmd -S <SERVER> -U <USER> -P <PASSWORD> -i tests\quality_checks_silver.sql
```

Or open the file `tests/quality_checks_silver.sql` in your SQL editor to run checks selectively.

**License**

This project is provided as-is for learning and prototyping. See `LICENSE` for details.
