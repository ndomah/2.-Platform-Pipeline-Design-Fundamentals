# Data Modeling 2 Relational Data Modeling

## Introduction
From DAMA International:
- A *model* is an abstract representation of how something is built or how it works.
- *Data modeling* is the process of discovering, analyzing, and scoping data requirements, and then representing and communicating these data requirements in a precise form called the *data model*.
- Each model contains *a set of components*. Some examples of components are entities, relationships, facts, keys, and attributes.
 
## Data Modeling Schemas

|**Schema**|**Sample Notations**|
|---:|:---|
|Relational|Baker Notation, Chen|
|Dimensional|Dimensional|
|Object-Oriented|Unified Modeling Language (UML)|
|Time-Based|Data Vault, Anchor Modeling|
|NoSQL|Document, Column, Graph, Key-Value|

## Preparing the Environment
- Launch Docker Desktop
- Open the Command Prompt and change directories to the location of the [docker-compose.yaml]() file:
```yaml
services:
    db:
        image: mysql
        environment:
            MYSQL_ROOT_PASSWORD: root
            MYSQL_DATABASE: salesdb
            MYSQL_USER: user
            MYSQL_PASSWORD: password
        ports:
            - "42333:3306"
```
- Pull the [Official MySQL Docker Image](https://hub.docker.com/_/mysql/): `docker pull mysql`
- Run: `docker-compose up -d`

![fig1 - docker setup](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig1%20-%20docker%20setup.png)

- Launch MySQL Workbench and edit the configurations in the connection based on the contents of the yaml file, then test the connection
- Upon connecting, the sales database (`salesdb`) will have been created
- Prepare to use the database:
```mysql
USE salesdb;
```

![fig2 - workbench](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig2%20-%20workbench.png)

## Create the Conceptual Data Model
### Design Process

![fig3 - design process](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig3%20-%20design%20process.png)

### Discover the Entities and Attributes

#### Step 1: Review the Case Study
**Case Study: Kretz Store**
Kretz Store sells a wide range of mostly household items ranging from accessories such as photo frames to furniture. Until now the store has recorded the sales orders with the third-party Point of Sale (POS) sale system, however, it wants to implement the proprietary sales application to have a better control over data it collects.

The sales application must keep track of the following:
- The customers
- The sales orders placed by customers and the details of the orders
- The products being sold
- The sales employee who made the sale
- The store that made the sale
- The sales channel through which the sales was made
- Order fulfillment activities

#### Step 2: Detect All Nouns that Represent the Object of Interest

Review the following:
- An **entity** is a thing about which an organization collects information
- Entities can be derived from answering:

|**Question**|**Definition**|
|---:|---|
|**Who?**|...is important to the business?|
|**What?**|...does the organization make?/...service does it provide?|
|**When?**|...is the business in operation?|
|**Where?**|...is business conducted?|
|**Why?**|...is the business in business?|
|**How?**|...do we know that an event occured?|

- An **attribute** is a property that identifies, describes, or measures an entity
- *NOTE*: Don't confuse between object of interest and descriptions/details about details. 

**Problem Statement**
A company wants to keep track of its sales orders across sales channels and to track and manage each stage of the sales pipeline - from the order being placed to the good being delivered. 

The sales application must keep track of the following:
- The **customers**
- The **sales orders** placed by customers and the **details** of the orders
- The **products** being sold
- The **sales employee** who made the sale
- The **store** that made the sale
- The **sales channel** through which the sales was made
- Order **fulfillment activities**

![fig4 - entities and attributes](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig4%20-%20entities%20and%20attributes.png)

![fig5 - combined](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig5%20-%20combined.png)

### Define Relationships
- Place entities and attributes on a matrix:

![fig6 - matrix](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig6%20-%20matrix.png)

- Track **cardinality** and **optionality**:

||**Question**|**Yes**|**No**|
|---|---|---|---|
|**Cardinality**|Can an Entity A be related to more than one Entity B?|||
||Can an Entity B be relate to more than one Entity A?|||
|**Optionality**|Can an Entity A exist without an entity B?|||
||Can an Entity B exist without an Entity A?|||

![fig7 - c & o](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig7%20-%20c%20%26%20a.png)

- Create a relational conceptual model:

![fig8 - assertion model](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig8%20%20-%20assertion%20model.png)

- **Optionality** says what can and must happen in a relationship:
  - 0: Can (optional)
  - 1: Must (obligatory)
- **Cardinality** indicates the number of entity occurences in a relationship, which can be any number from zero to infinity:
  - 1: Only One
  - N: Many or At Least One  

### Normalize the Data
**Normalization** is a process of organizing tables in such a way so that they *reduce data redundancy and prevent inconsistent data dependencies.*

**Codd's Law**: A non-key field must provide a fact about the key, the whole key, and nothing but the key, so help me Codd.
- 1NF: the *key*
- 2NF: the *whole* key
- 3NF: and *nothing* but the key

#### 1NF: The *Key*

![fig9 - 1nfa](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig9%20-%201nfa.png)

![fig10- 1nfb](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig10%20-%201nfb.png)

#### 2NF: The *Whole* Key

![fig11 - 2nfa](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig11%20-%202nfa.png)

![fig12 - 2nfb](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig12%20-%202nfb.png)

#### 3NF: And *Nothing* But the Key

![fig13 - 3nfa](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig13%20-%203nfa.png)

![fig14 - 3nfb](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig14%20-%203nfb.png)

## Defining and Resolving Relationships

### One-to-One

![fig15 - 1:1 before](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig15%20-%201-1%20before.png)

![fig16 - 1:1 after](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig16%20-%201-1%20after.png)

### One-to-Many

![fig17 - 1:N before](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig17%20-%201-n%20before.png)

![fig18 - 1:N after](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig18%20-%201-n%20after.png)

### Many-to-Many

![fig19 - N:N before](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig19%20-%20n-n%20before.png)

![fig20 - N:N after](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig20%20-%20n-n%20after.png)

## Creating the Database

### ER Diagram
- An **index** is a database structure used to improve the database performance
    - Index all primary keys
    - Index all foreign keys
- Potentially:
    - Index columns used in `JOIN`s
    - Index columns used in `WHERE`
    - Index columns used in `ORDER BY`
    - Index columns used in `GROUP BY` 
- Create the ER diagrams

![fig22 - erd drawio](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig22%20-%20erd%20drawio.png)

![fig21 - erd mysql](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig21%20-%20erd.png)

- Right click on the tables on the ERD in MySQL Workbench to run the code and setup the database ([create_sales_tables_mysqldb_script.sql](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/assets_scripts/oltp/create_sales_tables_mysqldb_script.sql)):
```mysql
-- MySQL Workbench Forward Engineering

SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';

-- -----------------------------------------------------
-- Schema salesdb
-- -----------------------------------------------------

-- -----------------------------------------------------
-- Schema salesdb
-- -----------------------------------------------------
CREATE SCHEMA IF NOT EXISTS `salesdb` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci ;
USE `salesdb` ;

-- -----------------------------------------------------
-- Table `salesdb`.`state`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `salesdb`.`state` (
  `state_id` VARCHAR(50) NOT NULL,
  `area_code` VARCHAR(45) NULL,
  `state_code` VARCHAR(45) NULL,
  `state` VARCHAR(255) NULL,
  `country` VARCHAR(45) NULL,
  PRIMARY KEY (`state_id`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `salesdb`.`city`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `salesdb`.`city` (
  `city_id` VARCHAR(50) NOT NULL,
  `city_name` VARCHAR(255) NULL,
  `type` VARCHAR(45) NULL,
  `state_id` VARCHAR(50) NULL,
  PRIMARY KEY (`city_id`),
  INDEX `fk_city_state_idx` (`state_id` ASC) VISIBLE,
  CONSTRAINT `fk_city_state`
    FOREIGN KEY (`state_id`)
    REFERENCES `salesdb`.`state` (`state_id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `salesdb`.`store`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `salesdb`.`store` (
  `store_id` VARCHAR(50) NOT NULL,
  `store_name` VARCHAR(255) NULL,
  `latitude` DECIMAL NULL,
  `longitude` DECIMAL NULL,
  `location` VARCHAR(255) NULL,
  `city_id` VARCHAR(50) NULL,
  PRIMARY KEY (`store_id`),
  INDEX `fk_store_city_idx` (`city_id` ASC) VISIBLE,
  CONSTRAINT `fk_store_city`
    FOREIGN KEY (`city_id`)
    REFERENCES `salesdb`.`city` (`city_id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `salesdb`.`employee`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `salesdb`.`employee` (
  `employee_id` VARCHAR(50) NOT NULL,
  `employee_first_name` VARCHAR(255) NULL,
  `employee_last_name` VARCHAR(255) NULL,
  `store_id` VARCHAR(50) NULL,
  PRIMARY KEY (`employee_id`),
  INDEX `fk_employee_store_idx` (`store_id` ASC) VISIBLE,
  CONSTRAINT `fk_employee_store`
    FOREIGN KEY (`store_id`)
    REFERENCES `salesdb`.`store` (`store_id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `salesdb`.`product`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `salesdb`.`product` (
  `product_id` VARCHAR(50) NOT NULL,
  `product_name` VARCHAR(255) NULL,
  PRIMARY KEY (`product_id`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `salesdb`.`customer`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `salesdb`.`customer` (
  `customer_id` VARCHAR(50) NOT NULL,
  `customer_first_name` VARCHAR(255) NULL,
  `customer_last_name` VARCHAR(255) NULL,
  PRIMARY KEY (`customer_id`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `salesdb`.`sales_channel`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `salesdb`.`sales_channel` (
  `sales_channel_id` VARCHAR(50) NOT NULL,
  `sales_channel_name` VARCHAR(255) NULL,
  PRIMARY KEY (`sales_channel_id`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `salesdb`.`sales_order`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `salesdb`.`sales_order` (
  `order_num` VARCHAR(50) NOT NULL,
  `order_date` DATETIME NULL,
  `currency_code` VARCHAR(45) NULL,
  `order_quantity` DECIMAL NULL,
  `discount_applied` DECIMAL NULL,
  `ship_date` DATETIME NULL,
  `delivery_date` DATETIME NULL,
  `procure_date` DATETIME NULL,
  `total_cost` DECIMAL NULL,
  `total_price` DECIMAL NULL,
  `employee_id` VARCHAR(50) NULL,
  `product_id` VARCHAR(50) NULL,
  `sales_channel_id` VARCHAR(50) NULL,
  `customer_id` VARCHAR(50) NULL,
  PRIMARY KEY (`order_num`),
  INDEX `fk_salesorder_employee_idx` (`employee_id` ASC) VISIBLE,
  INDEX `fk_salesorder_product_idx` (`product_id` ASC) VISIBLE,
  INDEX `fk_salesorder_customer_idx` (`customer_id` ASC) VISIBLE,
  INDEX `fk_salesorder_channel_idx` (`sales_channel_id` ASC) VISIBLE,
  CONSTRAINT `fk_salesorder_employee`
    FOREIGN KEY (`employee_id`)
    REFERENCES `salesdb`.`employee` (`employee_id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_salesorder_product`
    FOREIGN KEY (`product_id`)
    REFERENCES `salesdb`.`product` (`product_id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_salesorder_customer`
    FOREIGN KEY (`customer_id`)
    REFERENCES `salesdb`.`customer` (`customer_id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_salesorder_channel`
    FOREIGN KEY (`sales_channel_id`)
    REFERENCES `salesdb`.`sales_channel` (`sales_channel_id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;
```

### Populate the MySQL DB with Data from a .xls File
- Review the data flow:

![fig23 - data flow](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/img/fig23%20-%20data%20flow.jpg)

- Using the [US_Regional_Sales_Data]() excel file, we write [etl_salesdb_oltp.py](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/5.%20Data%20Modeling%202%20Relational%20Data%20Modeling/assets_scripts/oltp/etl_salesdb_oltp.py):
```python
# Import libraries
import pandas as pd 
import numpy as np
from sqlalchemy import create_engine

# Establish the connection
conn = create_engine("mysql+pymysql://{user}:{pw}@localhost:{host}/{db}"
                       .format(user = "user",
                               pw = "password",
                               host = 42333,
                               db = "salesdb"))

# Change the path if you have your xls dataset somewhere else
file_location = r'C:\...\assets_files\US_Regional_Sales_Data.xlsx'
basic = pd.read_excel(file_location, sheet_name = 0)
stores = pd.read_excel(file_location, sheet_name = 2)
products = pd.read_excel(file_location, sheet_name = 3)
employees = pd.read_excel(file_location, sheet_name = 5)
customers = pd.read_excel(file_location, sheet_name = 1)

def customers_tbl(df):
    """The function saves customer details to mysql customers table"""
    #split the full name column to first name & last name
    df[['customer_first_name', 'customer_last_name']] = df['Customer Names'].str.split(' ', expand = True)
    
    #rename multiple column names by label
    df.rename(columns={'_CustomerID':'customer_id'}, inplace = True)
    
    #save to database
    df = df[['customer_id', 'customer_first_name', 'customer_last_name']]
    df.to_sql(name = 'customer', con = conn, if_exists = 'append', index = False)
    
    return df


def states_tbl(df):
    """The function saves state details to mysql states table"""
   
    df = df[["State", "StateCode", "AreaCode"]].drop_duplicates(subset = "State")

    #rename multiple column names by label
    df.rename(columns={'State':'state', 'StateCode':'state_code', 'AreaCode':'area_code'}, inplace = True)
    
    #generate the auto incremental ID as in the original file, States' attributes are a part of the stores table
    df['state_id'] = np.arange(0, 0 + len(df)) + 1

    #save to database
    df.to_sql(name = 'state', con = conn, if_exists = 'append', index = False)

    return df


def cities_tbl(df):
    """The function saves cities details to mysql cities table"""
    df = df[["City Name", "State", "Type"]].drop_duplicates(subset = "City Name")
       
    #generate the auto incremental ID as in the original file, Cities' attributes are a part of the stores table 
    df['city_id'] = np.arange(0, 0 + len(df)) + 1
    
    states = states_tbl(stores)
    
    #merge existing states data with cities data to get a state ID
    df = pd.merge(df, states,  left_on='State', right_on='state')
    
    #rename multiple column names by label
    df.rename(columns={'City Name':'city_name', 'Type':'type'}, inplace = True)
    
    #save to database
    df = df[['city_id', 'city_name', 'type', 'state_id']]
    df.to_sql(name = 'city', con = conn, if_exists = 'append', index = False)

    return df


def channel_tbl(df):
    """The function saves sales details to mysql sales channel table"""

    df = df[["Sales Channel"]].drop_duplicates(subset = "Sales Channel")

    #generate the auto incremental ID as in the original file, Sales Channels' attributes are a part of the main table
    df['sales_channel_id'] = np.arange(0, 0 + len(df)) + 1

    #rename multiple column names by label
    df.rename(columns={'Sales Channel':'sales_channel_name'}, inplace = True)
    
    #save to database
    df.to_sql(name = 'sales_channel', con = conn, if_exists = 'append', index = False)

    return df


def products_tbl(df):
    """The function saves products details to mysql products table"""

    #rename multiple column names by label
    df.rename(columns={'_ProductID':'product_id', 'Product Name':'product_name'}, inplace = True)
    
    #save to database
    df.to_sql(name = 'product', con = conn, if_exists = 'append', index = False)

    return df


def stores_tbl(df):
    """The function saves stores details to mysql stores table"""
    cities = cities_tbl(stores)

    #merge existing states data with cities data to get a state ID
    df = pd.merge(df, cities, left_on = 'City Name', right_on = 'city_name')
    
    #rename multiple column names by label
    df.rename(columns={'_StoreID':'store_id', 'County':'location', 'Latitude':'latitude', 'Longitude':'longitude'}, inplace = True)

    #save to database
    df = df[['store_id', 'latitude', 'longitude', 'location', 'city_id']]
    df.to_sql(name = 'store', con = conn, if_exists = 'append', index = False)

    return df


def employees_tbl(df, main_dataset):
    """The function saves employees details to mysql employees table"""
    main = main_dataset[["_SalesTeamID", "_StoreID"]].drop_duplicates(subset = "_SalesTeamID")

    #split the full name column to first name & last name
    df[['employee_first_name', 'employee_last_name']] = df['Sales Team'].str.split(' ', expand = True)
    
    #merge existing employee data with sales order data to get a store ID
    df = pd.merge(df, main, on = '_SalesTeamID')

    #rename multiple column names by label
    df.rename(columns={'_SalesTeamID':'employee_id', '_StoreID': 'store_id'}, inplace = True)
    
    #save to database
    df = df[['employee_id', 'employee_first_name', 'employee_last_name', 'store_id']]
    df.to_sql(name = 'employee', con = conn, if_exists = 'append', index = False)

    return df


def orders_tbl(df):
    """The function saves orders details to mysql salesorders table"""
    channels = channel_tbl(basic)

    #merge existing basic data with channels data to get a channel ID
    df = pd.merge(df, channels, left_on = 'Sales Channel', right_on = 'sales_channel_name' )    

    #rename multiple column names by label
    df.rename(columns={'OrderNumber':'order_num', 'Order Quantity':'order_quantity',
                       'OrderDate':'order_date','CurrencyCode':'currency_code',
                       'ShipDate':'ship_date','DeliveryDate':'delivery_date','TotalCost':'total_cost','TotalPrice':'total_price',
                                   'ProcuredDate':'procure_date',
                                   'Discount Applied':'discount_applied', '_SalesTeamID':'employee_id',
                                   '_CustomerID':'customer_id', '_ProductID':'product_id'}, inplace = True)
    
    #save to database
    df = df[['order_num', 'order_date', 'currency_code', 'order_quantity', 'discount_applied', 'ship_date',
                                        'delivery_date', 'procure_date', 'total_cost', 'total_price', 'employee_id',
                                          'customer_id', 'sales_channel_id', 'product_id']]
    df.to_sql(name = 'sales_order', con = conn, if_exists = 'append', index = False)

    return df


def main():
    customers_tbl(customers)
    products_tbl(products)
    stores_tbl(stores)
    employees_tbl(employees, basic)    
    orders_tbl(basic)
if __name__ == '__main__':
    main()
```
