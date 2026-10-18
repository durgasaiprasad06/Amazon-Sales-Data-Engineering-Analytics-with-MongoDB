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

## 🔄 Execution Pipeline

Run the scripts in the following sequence for a smooth end-to-end setup:

### 1. Test Connection (`test_connection.py`)
Verifies that MongoDB is accessible and prints server ping status:
```powershell
python test_connection.py
```
*Expected Output:*
> `Pinged your deployment. You successfully connected to MongoDB!`

---

### 2. Apply Schema Validation (`schema_validation.py`)
Configures the collection `sales_orders` with strict `$jsonSchema` rules to enforce valid data types and prevent corrupt or malformed document insertion:
```powershell
python schema_validation.py
```
*Key Validations:*
- Required fields: `order_id`, `date`, `status`, `qty`
- `amount`: Must be a positive numeric value or null (for cancelled orders)
- `b2b`: Must be boolean

---

### 3. Migrate & Ingest Data (`data_migration.py`)
Reads `Amazon Sale Report.csv`, cleans missing and inconsistent fields, transforms dates and numbers, and uses PyMongo's `insert_many(ordered=False)` for high-speed batch insertion:
```powershell
python data_migration.py
```
*Features:*
- Batch ingestion (chunks of 5,000 to 10,000 documents)
- Handles `NaN` values and missing geographic data
- Cleans and formats date strings into `ISODate` objects

---

### 4. Perform CRUD Operations (`crud_operations.py`)
Demonstrates database manipulation capabilities:
```powershell
python crud_operations.py
```
- **Create**: Insert a newly placed customer order.
- **Read**: Query orders by `order_id`, filter orders by status (`Cancelled`, `Shipped`), or search high-value transactions (`amount > 2000`).
- **Update**: Update courier shipping status or delivery timestamps.
- **Delete**: Remove test or obsolete cancelled records.

---

### 5. Run Revenue & Sales Aggregations (`revenue_aggregation.py`)
Executes complex analytical pipelines using the MongoDB Aggregation Framework:
```powershell
python revenue_aggregation.py
```

Outputs metrics including:
- **Total Revenue & Volume**: Total gross revenue and count of completed orders.
- **Category-wise Revenue**: Revenue, average order value, and items sold grouped by `Category`.
- **Top 10 Selling SKUs**: Highest revenue-generating stock keeping units.
- **State-wise Sales**: Top Indian states by order volume and total sales value.
- **Fulfillment Channel Comparison**: Performance analysis between Amazon FBA and Merchant fulfillment.
- **B2B vs. B2C Breakdown**: Revenue share and order volume distribution.

---

## 📋 Document Schema & Validation Rules

A sample ingested document inside the `sales_orders` collection:

```json
{
  "_id": { "$oid": "661f4b8f1c8e9b001a123456" },
  "index": 1,
  "order_id": "171-9198151-1101146",
  "date": { "$date": "2022-04-30T00:00:00.000Z" },
  "status": "Shipped - Delivered to Buyer",
  "fulfilment": "Merchant",
  "sales_channel": "Amazon.in",
  "ship_service_level": "Standard",
  "style": "JNE3781",
  "sku": "JNE3781-KR-XXXL",
  "category": "kurta",
  "size": "3XL",
  "asin": "B09K3WFS32",
  "courier_status": "Shipped",
  "qty": 1,
  "currency": "INR",
  "amount": 406.0,
  "shipping_address": {
    "city": "BENGALURU",
    "state": "KARNATAKA",
    "postal_code": "560085",
    "country": "IN"
  },
  "b2b": false,
  "fulfilled_by": "Easy Ship"
}
```

---

## 🔍 Key Aggregation Pipelines

### Category Revenue Pipeline Example
```python
[
    { "$match": { "status": { "$nin": ["Cancelled"] }, "amount": { "$gt": 0 } } },
    {
        "$group": {
            "_id": "$category",
            "total_revenue": { "$sum": "$amount" },
            "total_items_sold": { "$sum": "$qty" },
            "average_order_value": { "$avg": "$amount" }
        }
    },
    { "$sort": { "total_revenue": -1 } },
    { "$limit": 10 }
]
```

### State-wise Sales Distribution Example
```python
[
    { "$match": { "status": { "$nin": ["Cancelled"] } } },
    {
        "$group": {
            "_id": "$shipping_address.state",
            "order_count": { "$sum": 1 },
            "gross_sales": { "$sum": "$amount" }
        }
    },
    { "$sort": { "gross_sales": -1 } },
    { "$limit": 10 }
]
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


