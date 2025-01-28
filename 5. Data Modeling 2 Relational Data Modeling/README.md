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

![fig1 - docker setup]()

- Launch MySQL Workbench and edit the configurations in the connection based on the contents of the yaml file, then test the connection
- Upon connecting, the sales database (`salesdb`) will have been created
- Prepare to use the database:
```mysql
USE salesdb;
```

![fig2 - workbench]()

## Create the Conceptual Data Model
### Design Process

![fig3 - design process]()

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
|---:||
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

![fig4 - entities and attributes]()

![fig5 - combined]()

### Define Relationships
- Place entities and attributes on a matrix:

![fig6 - matrix]()

- Track **cardinality** and **optionality**:

||**Question**|**Yes**|**No**|
|---|---|---|---|
|**Cardinality**|Can an Entity A be related to more than one Entity B?|||
||Can an Entity B be relate to more than one Entity A?|||
|**Optionality**|Can an Entity A exist without an entity B?|||
||Can an Entity B exist without an Entity A?|||

![fig7 - c & o]()

- Create a relational conceptual model:

![fig8 - assertion model]()

- **Optionality** says what can and must happen in a relationship:
 - 0: Can (optional)
 - 1: Must (obligatory)
- **Cardinality** indicates the number of entity occurences in a relationship, which can be any number from zero to infinity:
 - 1: Only One
 - N: Many or At Least One  

### Normalize the Data
