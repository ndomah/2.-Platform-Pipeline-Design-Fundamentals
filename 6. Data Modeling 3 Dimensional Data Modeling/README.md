# Data Modeling 3 Dimensional Data Modeling
## Introduction
- Management Information Systems (MIS) or Decision Support Systems (DSS) were introduced in the 80s to drive management decisions.
- Ralph Kimball and Bill Inmon are considered to be pioneers of the most frequently used approaches to data warehousing design.

## Dimensional Modeling (Kimball): Architecture

![fig1 - dwi layers]()

**Staging (ETL systems)**:
1. Transform from source-to-target
2. Conform dimensions
3. Normalization optional

**Dimensional**:
1. Atomic and summary data
2. Organized by business process
3. Uses conforomed dimensions

**BI Applications**:
1. Ad hoc queries
2. Standard reports
3. Analytic apps

## ER Modeling (Inmon): Architecture

![fig2 - cif]()

**Operational**:
1. Detailed
2. Current valued
3. Application-oriented

**Enterprise Data Warehouse**:
1. Most granular
2. Time variant
3. Integrated
4. Subject-oriented
5. Some summary

**Data Marts**:
1. Some data is derived, some data is primitive

**Individual**:
1. Temporary
2. Ad hoc

## Dimensional Modeling Basics
### Approaches to Data Warehouses

![fig3 - plot]()

**Data-Driven Approach**:
- Adopted by IT-led teams avoiding early user involvement
- Based directly on source data
- Highly normalized, often in 3NF

**Reporting-driven Approach**:
- Based on BI users' reporting requirements
- Uses dimensional modeling comprised of facts and dimensions

### Dimensional Modeling (Kimball): Design Process

![fig4 - kimball lifecycle]()

### Dimension Tables Explained
A dimension table is a table in a database that stores descriptive attributes, often text values, that provide context to the data in a fact table.

#### Slowly Changing Dimensions (SCD): Type 0, 1, 2, and 3

![fig5 - scd table]()

***SCD Type 0 Example**

![fig6 - type 0]()

***SCD Type 1 Example**

![fig7 - type 1]()

***SCD Type 2 Example**

![fig8 - type 2]()

***SCD Type 3 Example**

![fig9 - type 3]()

### Fact Tables Explained

#### Types of Facts

||**Transaction**|**Periodic Snapshot**|**Accumulating Snapshot**|
|---:|---|---|---|
|**Periodicity**|Discrete transaction points in time|Recurring snapshots at regular, predictable intervals|Evolving pipeline/workflow|
|**Grain**|1 row per transaction or transaction line|1 row per snapshot period plus other dimensions|1 row per pipeline occurence|
|**Date Dimension(s)**|Transaction date|Snapshot date|Multiple dates for pipeline's key milestones|
|**Fact**|Transaction performance|Cumulative performance for time interval|Performance for pipeline occurrence|
|**Fact Table Updates**|No updates, unless error correction|No updates, unless error correction|Updated whenever pipeline activity occurs|

#### Types of Measures in Facts

![fig10 - additive]()

**Additive** facts are facts that can be summed up/aggregated through all of the dimensions in the fact table.

![fig11 - semiadditive]()

**Semi-Additive** facts are facts that can be summed up/aggregated for some of the dimensions in the fact table but not the others.

![fig12 - nonadditive]()

**Non-Additive** facts are facts that cannot be summed up/aggregated for any of the dimensions. 

## Data Warehouse Setup

### DuckDB Introduction
- Change directories to `assets_scripts` folder
- Create conda environment: `conda env create -f modelingenv.yml`
- Verify environment was created: `conda info --envs`
- Cleanup downloaded libraries: `conda clean -tp`
- Run `jupyter notebook` and open [`duckdb_practical_exercise.ipynb`]()

### Creating Tables in DuckDB
- Run [`create_tables_salesdb_olap.py`] to create the tables

```python
#importing libraries
import duckdb
from sql_queries_salesdb_olap import create_table_queries

def create_tables(dest):
    for query in create_table_queries:
        dest.execute(query)
        dest.commit()

def main():
    dest = destDatabase.cursor()
    create_tables(dest)

if __name__ == "__main__":
    destDatabase = duckdb.connect(r'C:\Users\niles\OneDrive\Desktop\assets_scripts\salesdwh.duckdb')
    main()
```

- This script imports `create_table_queries` from [`sql_quries_salesdb_olap.py`]()

```python

create_stores=('''CREATE TABLE IF NOT EXISTS dim_store (
      store_id varchar(50),
      latitude DECIMAL(18, 2),
      longitude DECIMAL(18, 2),
      location varchar(255),
      city_name varchar(255),
      type varchar(45),
      area_code varchar(45),
      state_code varchar(45),
      state varchar(255)) ''')    

create_employees_seq=('''CREATE SEQUENCE employee_sk START 1;''')
create_employees_scd2=('''CREATE TABLE IF NOT EXISTS dim_employee_scd2 (
      employee_id VARCHAR(50),
      employee_name VARCHAR(255),
      valid_from DATE,
      valid_to DATE,
      store_id VARCHAR(50),
      version integer,
      employee_sk  integer primary key default nextval('employee_sk')) ''')
                       
create_employees_scd1=('''CREATE TABLE IF NOT EXISTS dim_employee_scd1 (
      employee_id VARCHAR(50) ,
      employee_name VARCHAR(255),
      store_id VARCHAR(50))''')

create_customers=('''CREATE TABLE IF NOT EXISTS dim_customer (
      customer_id varchar(50),
      customer_name varchar(255))''')    

create_products=('''CREATE TABLE IF NOT EXISTS dim_product (
      product_id varchar(50) ,
      product_name varchar(255))''')  

create_channels=('''CREATE TABLE IF NOT EXISTS dim_sales_channel (
      sales_channel_id varchar(50),
      sales_channel_name varchar(255))''')

create_sales_scd2=('''CREATE TABLE IF NOT EXISTS fact_sales_scd2 (
      order_num varchar(50),
      employee_sk integer,
      employee_id varchar(50),
      customer_id varchar(50),
      product_id varchar(50),
      sales_channel_id varchar(50),
      currency_code varchar(45),
      order_quantity DECIMAL(18, 2),
      total_cost DECIMAL(18, 2),
      total_price DECIMAL(18, 2),
      order_date date,
      ship_date date,
      delivery_date date,
      procure_date date)''')

create_sales_scd1=('''CREATE TABLE IF NOT EXISTS fact_sales_scd1 (
      order_num varchar(50),
      employee_id varchar(50),
      customer_id varchar(50),
      product_id varchar(50),
      sales_channel_id varchar(50),
      currency_code varchar(45),
      order_quantity DECIMAL(18, 2),
      total_cost DECIMAL(18, 2),
      total_price DECIMAL(18, 2),
      order_date date,
      ship_date date,
      delivery_date date,
      procure_date date)''')

create_sales_acc=('''CREATE TABLE IF NOT EXISTS fact_sales_acc (
      order_num varchar(50),
      employee_id varchar(50),
      customer_id varchar(50),
      product_id varchar(50),
      sales_channel_id varchar(50),
      currency_code varchar(15),
      order_quantity DECIMAL(18, 2),
      total_cost DECIMAL(18, 2),
      total_price DECIMAL(18, 2),
      shipment_lag integer,
      delivery_lag integer,
      order_date date,
      ship_date date,
      delivery_date date,
      procure_date date)''')


create_table_queries = [create_stores, create_employees_seq, create_employees_scd2, create_employees_scd1, create_customers, create_products, create_channels, create_sales_scd2, create_sales_scd1, create_sales_acc]
```

## Working with the Data Warehouse

### Exploring SCD0 and SCD1


### Exploring SCD2


### Exploring Transaction Fact Table


### Exploring Accumulating Fact Table


