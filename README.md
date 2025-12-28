# sql-data-warehouse-project

A compact example project that demonstrates building a layered SQL data warehouse (bronze → silver → gold) using plain SQL scripts and CSV data.

This repo provides sample source files, ingestion and transformation scripts, and simple quality checks to learn and prototype ETL/ELT processes and dimensional modeling on SQL Server.

**Table of Contents**

- [Project Structure](#project-structure)
- [Quick Start](#quick-start)
- [ETL Flow](#etl-flow)
- [Running Quality Checks](#running-quality-checks)
- [Contributing](#contributing)
- [License](#license)

**Project Structure**

- `datasets/`
	- `csv-files/` — example output CSVs (gold-level tables) used for verification and demonstration.
	- `source-crm/`, `source-erp/` — sample raw source CSVs used as input to the pipeline.
- `scripts/`
	- `init_database.sql` — creates the database schema, roles, and basic objects.
	- `bronze/` — DDL and load procedures for the raw/ingest layer.
	- `silver/` — transformation DDL and procedures that clean and conform data.
	- `gold/` — dimensional models and final DDL for analytics-ready tables.
	- `datawarehouse/`, `eda/` — helper scripts and exploratory SQL.
- `tests/` — SQL-based quality checks (this folder is ignored by Git in this repo).

**Quick Start**

Prerequisites:

- Microsoft SQL Server (or a compatible SQL engine).
- `sqlcmd` (or use Azure Data Studio / SSMS to run scripts).

Initialize the database (replace server/user/password as needed):

```powershell
sqlcmd -S <SERVER> -U <USER> -P <PASSWORD> -i scripts\init_database.sql
```

Load the bronze (raw ingest) layer (example):

```powershell
sqlcmd -S <SERVER> -U <USER> -P <PASSWORD> -i scripts\bronze\proc_load_bronze.sql
```

Run silver transformations:

```powershell
sqlcmd -S <SERVER> -U <USER> -P <PASSWORD> -i scripts\proc_load_silver.sql
```

Apply gold modeling DDL:

```powershell
sqlcmd -S <SERVER> -U <USER> -P <PASSWORD> -i scripts\gold\ddl.gold.sql
```

Adjust the above commands for your environment (Windows PowerShell shown).

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

**Contributing**

- Add scripts under `scripts/` following the bronze→silver→gold layering.
- Keep sample CSVs in `datasets/source-*` for reproducible runs.
- Open an issue or PR for enhancements or to add new checks/data.

**License**

This project is provided as-is for learning and prototyping. See `LICENSE` for details.

---

If you want, I can also add example connection config, parameterized run scripts (PowerShell), or CI steps to run these SQL checks automatically. Which would you prefer next?
