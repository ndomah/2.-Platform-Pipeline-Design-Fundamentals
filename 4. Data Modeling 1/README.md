# Data Modeling 1

## Why Data Modeling is Important

**Data modeling** is important because it helps structure and organize data effectively to meet business and technical requirements.
- **Schema Design for NoSQL Stores:**
  - Even in NoSQL databases, having a well-thought-out schema design improves query performance, consistency, and scalability.
- **Achieving Business Goals:**
  - Proper data modeling aligns data storage and retrieval with business objectives, ensuring efficiency and clarity.
- **Defining Access Patterns:**
  - A well-designed model optimizes how data is accessed and queried, leading to faster and more efficient operations.
- **Maintainability:**
  - A structured model helps create a system that is easier to update, scale, and troubleshoot over time.
- **Preventing Data Swamps:**
  - Without a clear data model, unstructured and poorly managed data can accumulate, making it difficult to retrieve useful insights.
 
Overall, data modeling helps in creating a logical and consistent framework for data storage, ensuring scalability, performance, and maintainability across different database systems.

## The Dataset
We will be working with this [online retail dataset from kaggle](https://www.kaggle.com/datasets/tunguz/online-retail).

![dataset](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/4.%20Data%20Modeling%201/img/fig2%20-%20dataset.png)

## Entity Relationship

![entity relationship diagram](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/4.%20Data%20Modeling%201/img/fig2%20-%20entity%20relationship%20diagram.png)

### Customer and Invoice (One-to-Many Relationship)
**Entities Involved:**
- `Customer` (Primary Key: `Customer_id`)  
- `Invoice` (Primary Key: `InvoiceNo`, Foreign Key: `Customer_id`)

**Explanation:**  
Each customer can have multiple invoices (`1` to `*` relationship), but each invoice belongs to only one customer. This relationship is represented by linking the `Customer_id` field in both tables.

### Invoice and Invoice_Stock (One-to-Many Relationship)
**Entities Involved:**
- `Invoice` (Primary Key: `InvoiceNo`)  
- `invoice_stock` (Primary Key: `Id_invoice_stock`, Foreign Key: `InvoiceNo`)

**Explanation:**  
Each invoice can be linked to multiple stock items (`1` to `*` relationship), meaning an invoice can contain multiple stock items, but each entry in `invoice_stock` corresponds to a single invoice.


### Stock_Code and Invoice_Stock (One-to-Many Relationship)
**Entities Involved:**
- `stock_code` (Primary Key: `Id_stock`)  
- `invoice_stock` (Primary Key: `Id_invoice_stock`, Foreign Key: `Id_stock`)

**Explanation:**  
Each stock item can appear in multiple invoice records (`1` to `*` relationship), meaning a stock item can be associated with multiple invoices, but each `invoice_stock` record references a single stock item.

### Summary of Relationships
- A **Customer** can have multiple **Invoices** (1:M).
- An **Invoice** can contain multiple **Stock items** through the **invoice_stock** table (1:M).
- A **Stock item** can appear in multiple **Invoices** via the **invoice_stock** table (1:M).

### Purpose of the `invoice_stock` Table (Join Table)
This table acts as a bridge (junction table) between `Invoice` and `stock_code`, allowing a many-to-many relationship where:
- One invoice can have multiple stock items.
- One stock item can appear in multiple invoices.

## Wide Column Stores
**Wide Column Stores**, such as Apache Cassandra and Google Bigtable, are NoSQL databases that store data in tables, rows, and dynamic columns, offering high scalability and flexible schema designs. Unlike traditional relational databases, they optimize read and write operations for distributed workloads, making them ideal for handling large-scale datasets.

|**Column Names**|Invoice_No|CustomerID|Invoice Date|Country|Item_StockCode or StockCode|Item_22749 or 22749|
|---|---|---|---|---|---|---|
|**Column Values**|536367|13047|12/1/2010 8:34|United Kingdom|Description, Quantity, Price|Description, Quantity, Price|

**How This Structure Benefits a Wide Column Store:**
- **Efficient Read/Write Operations:**
  - Lookups are optimized using row keys, enabling fast retrieval of invoices by Invoice_No.
- **Scalability:**
  - Data can be distributed across nodes, with transactions (invoices) partitioned by row key.
- **Schema Flexibility:**
  - New products (stock codes) can be added without modifying the schema, reducing administrative overhead.
- **Sparse Data Optimization:**
  - Not every invoice will have the same products, and wide column stores handle empty values efficiently.

## Document Stores
**Document stores**, such as MongoDB and CouchDB, store data in a flexible, schema-less format using JSON, BSON, or XML. Unlike relational databases that require predefined schemas, document databases allow data to be stored in a nested and hierarchical format, making them highly adaptable to changing requirements.

```json
{
  "InvoiceNo": 536367,
  "CustomerID": 13047,
  "InvoiceDate": "2010-12-01T08:34:00",
  "Country": "United Kingdom",
  "Items": [
    {
      "StockCode": "22749",
      "Description": "ASSORTED COLOUR BIRD ORNAMENT",
      "Quantity": 32,
      "Price": 1.69
    },
    {
      "StockCode": "71053",
      "Description": "WHITE METAL LANTERN",
      "Quantity": 6,
      "Price": 3.39
    }
  ]
}
```
**Advantages of Using Document Stores for Your Dataset**
- **Better Data Organization:**
  - Invoices, customers, and item details are grouped in a single document, making data easier to access and manage.
- **Faster Reads:**
  - Since all related information is stored in one place, retrieving an invoice is faster without needing complex joins.
- **Easier Updates:**
  - Changing customer information or adding new products is straightforward with no schema constraints.
- **Distributed Architecture:**
  - Supports high availability and fault tolerance across clusters.

## Key Value Stores

**Key-Value Stores**, such as Redis, DynamoDB, and Riak, store data as a simple collection of key-value pairs. They are highly efficient for fast lookups, caching, and distributed data management due to their minimal structure and high scalability.

![key value stores](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/4.%20Data%20Modeling%201/img/fig3%20-%20key%20value%20stores.png)

**Key Section:**
- The key used in this example is a composite key: `CustomerID_Invoice_No`
- This key combines the customer ID and invoice number to uniquely identify each invoice entry.
- **Considerations:**
  - **Partial Key Scans:** A challenge in key-value stores is whether it's possible to query by part of the key (e.g., searching by CustomerID alone without knowing the Invoice_No).
  - **Missing Key Components:** If part of the key is missing, retrieving data can become complex unless indexed appropriately.
    
**Value Section:**
- The value associated with the key includes multiple attributes such as:
  - `StockCode`
  - `Description`
  - `Quantity`
  - `Price`
  - `Invoice Date`
  - `Country`
- In a key-value store, all these attributes are typically stored as a single value (e.g., JSON or a serialized object).
- **Challenges:**
  - **Sorting Issues:** Since the `Invoice Date` is stored inside the value, it is difficult to sort or filter by date efficiently without retrieving the entire value first.
  - **Handling Multiple Items:** A single invoice can contain multiple items, raising questions about how to model and retrieve such data efficiently.
 
Key-value stores provide a fast and scalable solution for simple lookups using a unique identifier. However, challenges arise when partial lookups, sorting, or complex queries are required, making them best suited for scenarios where access patterns are predictable and simple.

## Data Warehousing

A **data warehouse** is a centralized repository that integrates data from various sources to support business intelligence and analytics. The diagram you provided represents a star schema, which is a commonly used modeling approach in data warehousing.

![data warehousing](https://github.com/ndomah/2.-Platform-Pipeline-Design-Fundamentals/blob/main/4.%20Data%20Modeling%201/img/fig4%20-%20data%20warehousing.png)

**Fact Table (Central Table)**
- Named as `FACT` in the diagram.
- Contains foreign keys (`FK`) to link with dimension tables.
- Stores quantitative data such as:
  - `FK CustomerID` (reference to the customer dimension)
  - `FK InvoiceNo` (reference to the invoice dimension)
  - `FK StockCode` (reference to the items dimension)
  - `Item Price` (price of an individual item)
  - `(Total price)` (aggregated cost calculation)

The fact table captures transactional data and numerical values used for analysis (e.g., sales revenue, total items sold).

**Dimension Tables (Descriptive Context)**
- The dimension tables provide descriptive attributes related to the business entities involved in transactions. Each has a **Primary Key (PK)** and links to the fact table via foreign keys.
  - **Customer Dimension (`DIM Customer`)**
    - Contains `CustomerID` as the primary key.
    - Stores attributes like customer name, location, and demographics.
  - **Invoice Dimension (`DIM Invoice`)**
    - Contains `InvoiceNo` as the primary key.
    - Holds attributes like invoice date, billing address, and payment terms.
  - **Items Dimension (`DIM Items`)**
    - Contains `StockCode` as the primary key.
    - Provides item-specific details such as description, category, and supplier.

**Benefits of Using a Data Warehouse:**
  - **Improved Query Performance:** The star schema structure allows for fast analytical queries by pre-aggregating data.
  - **Historical Analysis:** The warehouse enables tracking of historical performance by storing data over time.
  - **Business Intelligence (BI):** Supports reporting and dashboarding tools to analyze sales trends, customer behavior, and inventory.
  - **Data Consistency:** Ensures a unified view of data across the business by integrating multiple data sources.
