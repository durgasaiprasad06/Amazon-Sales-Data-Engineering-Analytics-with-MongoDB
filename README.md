# 📊 Amazon Sales Analytics with MongoDB

A comprehensive end-to-end data engineering and analytics pipeline using **MongoDB** and **Python**. This project ingests, validates, manages, and analyzes over **128,000+** Amazon India e-commerce sales transactions from raw CSV format into MongoDB documents, providing structured schema validation, transactional CRUD operations, and advanced analytical aggregation pipelines.

---


## 🎯 Overview

The purpose of this project is to model and analyze e-commerce sales data using MongoDB's document-oriented database. Key objectives include:

- **Connectivity**: Seamless integration with local MongoDB or MongoDB Atlas via `pymongo`.
- **Data Integrity**: Enforcing JSON schema validation (`$jsonSchema`) at the database level.
- **High-Throughput Ingestion**: Batch processing and cleaning 128,000+ transaction records from CSV.
- **Operational CRUD**: Safe querying, updating, and maintenance of order records.
- **Business Intelligence**: Rich analytics using MongoDB Aggregation Framework to calculate revenue, category breakdowns, fulfillment performance, and geographic distribution.

---

## 🏗️ Project Architecture

```
+--------------------------------+
|    Amazon Sale Report.csv      |  Raw E-Commerce Sales Data (~128k rows)
+--------------------------------+
               |
               v
+--------------------------------+
|     data_migration.py          |  Data Cleaning & Type Casting (Pandas / PyMongo)
+--------------------------------+
               |
               v  (Enforced by schema_validation.py)
+--------------------------------+
|        MongoDB Database        |  Database: amazon_sales
|   Collection: sales_orders     |  Indexes: Order ID, Category, Date, Status
+--------------------------------+
          /            \
         /              \
        v                v
+------------------+   +----------------------------+
| crud_operations  |   | revenue_aggregation.py     |
| .py (App Ops)    |   | (Analytics & KPI Metrics)  |
+------------------+   +----------------------------+
```

---

## 📁 Repository Structure

```
Mongo/
│
├── Amazon Sale Report.csv      # Raw dataset containing 128,975 sales records
├── test_connection.py         # MongoDB connection health-check script
├── schema_validation.py       # MongoDB collection creation & $jsonSchema validation rules
├── data_migration.py          # ETL script for batch ingestion and data cleaning
├── crud_operations.py         # Demonstrations of Create, Read, Update, and Delete actions
├── revenue_aggregation.py     # Aggregation pipelines for revenue, category, and regional KPIs
└── README.md                  # Project documentation and user guide
```


## 📊 Dataset Overview

The dataset (`Amazon Sale Report.csv`) contains **128,975 transaction records** covering sales across multiple product categories, fulfillment channels, and regions:

| Field Name | Type | Description |
| :--- | :--- | :--- |
| `Order ID` | String | Unique Amazon order tracking ID (e.g., `405-8078784-5731545`) |
| `Date` | Date/String | Transaction date (e.g., `04-30-22`) |
| `Status` | String | Order status (`Shipped`, `Delivered to Buyer`, `Cancelled`, etc.) |
| `Fulfilment` | String | Fulfillment type (`Merchant` or `Amazon`) |
| `Sales Channel` | String | Channel name (`Amazon.in`, etc.) |
| `ship-service-level` | String | Shipping tier (`Standard`, `Expedited`) |
| `Category` | String | Product category (`Set`, `kurta`, `Western Dress`, `Top`, etc.) |
| `Size` | String | Apparel size (`S`, `M`, `L`, `XL`, `XXL`, `3XL`, etc.) |
| `Qty` | Integer | Quantity ordered |
| `currency` | String | Currency code (`INR`) |
| `Amount` | Double/Float | Total transaction monetary value |
| `ship-city` | String | Destination delivery city |
| `ship-state` | String | Destination delivery state |
| `ship-postal-code` | String/Number | Destination postal zip code |
| `B2B` | Boolean | Flag indicating whether order is Business-to-Business |
| `fulfilled-by` | String | Fulfillment partner (`Easy Ship`, etc.) |

---

## ⚙️ Prerequisites

1. **Python**: Python 3.8 or higher installed on your system.
2. **MongoDB Instance**:
   - **Local**: [MongoDB Community Server](https://www.mongodb.com/try/download/community) running locally at `localhost:27017`.
   - **Cloud**: [MongoDB Atlas](https://www.mongodb.com/atlas/database) cloud cluster with an active connection string.

---

## 🔧 Configuration

By default, scripts can read the connection string from an environment variable or default to a local MongoDB instance.

Create a `.env` file in the root directory (optional but recommended):
```env
MONGO_URI=mongodb://localhost:27017/
DATABASE_NAME=amazon_sales
COLLECTION_NAME=sales_orders
```

---

## ⚡ Performance Optimization & Indexing

To ensure lightning-fast aggregations and queries on large collections, recommend creating the following compound and single indexes:

```python
# Create indexes for optimal query speeds
db.sales_orders.create_index([("order_id", 1)], unique=True)
db.sales_orders.create_index([("category", 1), ("status", 1)])
db.sales_orders.create_index([("date", -1)])
db.sales_orders.create_index([("shipping_address.state", 1)])
```


