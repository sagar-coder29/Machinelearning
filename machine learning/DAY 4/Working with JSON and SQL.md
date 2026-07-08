# Working with JSON and SQL | Reading & Writing Data

---

## Overview

Beyond CSV, **JSON** and **SQL databases** are two of the most common data sources in real-world ML projects.

```mermaid
graph LR
    JSON["JSON File<br/>data.json"] --> Pandas["Pandas"]
    SQL["SQL Database"] --> Pandas
    Pandas --> DF["DataFrame"]
```

---

## Part 1: Working with JSON

**JSON (JavaScript Object Notation)** is a lightweight data format widely used in APIs and web applications.

### JSON Structure

```json
{
  "users": [
    {"id": 1, "name": "Alice", "age": 25, "city": "Mumbai"},
    {"id": 2, "name": "Bob", "age": 30, "city": "Delhi"},
    {"id": 3, "name": "Charlie", "age": 35, "city": "Bangalore"}
  ]
}
```

---

### Reading JSON

```python
import pandas as pd

# Basic read
df = pd.read_json('data.json')

# Read from URL (API response)
df = pd.read_json('https://api.example.com/data')

# Read inline JSON string
json_str = '{"name": ["Alice", "Bob"], "age": [25, 30]}'
df = pd.read_json(json_str)
```

### JSON Orientation Formats

JSON can be structured in different ways. Use the `orient` parameter to specify.

```python
# records: [{col: val}, {col: val}, ...] — most common
df = pd.read_json('data.json', orient='records')

# index: {row: {col: val}}
df = pd.read_json('data.json', orient='index')

# columns: {col: {row: val}}
df = pd.read_json('data.json', orient='columns')

# values: [[val, val], [val, val]]
df = pd.read_json('data.json', orient='values')

# table: {schema, data} — includes data types
df = pd.read_json('data.json', orient='table')
```

| Orient | Format | When to Use |
|--------|--------|-------------|
| `records` | List of dicts | Most common API format |
| `columns` | Dict of lists | Simple columnar data |
| `index` | Dict of dicts | Row-oriented data |
| `values` | List of lists | No column names, just data |
| `table` | Schema + data | When you need type info |

### Nested JSON — Normalization

Real-world JSON is often nested. Use `json_normalize` to flatten it.

```python
from pandas import json_normalize

# Nested JSON
data = {
    "orders": [
        {
            "order_id": 1,
            "customer": {"name": "Alice", "email": "alice@email.com"},
            "items": [
                {"product": "Laptop", "price": 800},
                {"product": "Mouse", "price": 20}
            ]
        },
        {
            "order_id": 2,
            "customer": {"name": "Bob", "email": "bob@email.com"},
            "items": [
                {"product": "Keyboard", "price": 50}
            ]
        }
    ]
}

# Flatten nested dicts
df = json_normalize(data['orders'], sep='_')
print(df.columns)
# ['order_id', 'customer_name', 'customer_email', 'items']

# Flatten nested lists (explode)
df_exploded = df.explode('items')
df_exploded[['product', 'price']] = pd.DataFrame(
    df_exploded['items'].tolist(), index=df_exploded.index
)
print(df_exploded)
```

### Writing JSON

```python
# Basic write
df.to_json('output.json')

# Different orientations
df.to_json('output.json', orient='records')     # Default
df.to_json('output.json', orient='index')
df.to_json('output.json', orient='columns')
df.to_json('output.json', orient='table')        # Includes schema

# Pretty print (indented)
df.to_json('output.json', indent=2)

# Compressed
df.to_json('output.json.gz', compression='gzip')
```

### API Interaction

```python
import requests

# Fetch JSON from API
response = requests.get('https://api.github.com/repos/pandas-dev/pandas/issues')
data = response.json()

# Directly into DataFrame
df = pd.DataFrame(data)

# Flatten if nested
df_flat = json_normalize(data)
```

---

### Key JSON vs CSV Tradeoffs

| Aspect | JSON | CSV |
|--------|------|-----|
| **Structure** | Nested allowed (hierarchical) | Flat (tabular only) |
| **Data Types** | Supports strings, numbers, arrays, objects | All stored as text |
| **Human Readable** | Moderate | Very readable |
| **File Size** | Larger (redundant keys) | Smaller |
| **Parsing Speed** | Slower | Faster |
| **API Friendly** | ✅ Yes (standard API format) | ❌ Rarely used in APIs |
| **ML Friendly** | Needs flattening | ✅ Ready to use |

> **Rule of Thumb:** Use JSON for APIs and hierarchical data. Convert to CSV/DataFrame for ML.

---

## Part 2: Working with SQL Databases

SQL databases store data in **relational tables**. Pandas can read/write directly to databases.

### Setup

```python
import pandas as pd
from sqlalchemy import create_engine

# SQLite (local file)
engine = create_engine('sqlite:///database.db')

# PostgreSQL
engine = create_engine('postgresql://user:password@host:port/dbname')

# MySQL
engine = create_engine('mysql+pymysql://user:password@host:port/dbname')

# PostgreSQL (with psycopg2)
engine = create_engine('postgresql+psycopg2://user:password@host/dbname')
```

---

### Reading from SQL

```python
# Read entire table
df = pd.read_sql_table('table_name', engine)

# Read with SQL query
df = pd.read_sql_query('SELECT * FROM table_name WHERE age > 25', engine)

# read_sql automatically detects type
df = pd.read_sql('SELECT * FROM table_name', engine)

# Read only specific columns
df = pd.read_sql('SELECT name, age, salary FROM employees', engine)

# Read with parameters (safe from SQL injection)
query = 'SELECT * FROM employees WHERE city = %s'
df = pd.read_sql(query, engine, params=('Mumbai',))
```

### Writing to SQL

```python
# Write DataFrame to SQL table
df.to_sql('employees', engine, if_exists='replace', index=False)

# Append to existing table
df.to_sql('employees', engine, if_exists='append', index=False)

# Write only specific columns
df.to_sql('employees', engine, if_exists='replace', 
          columns=['name', 'age', 'salary'])

# Write in chunks (for large DataFrames)
df.to_sql('employees', engine, if_exists='replace', chunksize=1000)
```

| Parameter | Options | Description |
|-----------|---------|-------------|
| `name` | String | Table name |
| `con` | Engine | Database connection |
| `if_exists` | `'fail'`, `'replace'`, `'append'` | Behavior if table exists |
| `index` | True/False | Write DataFrame index as column |
| `chunksize` | Integer | Rows per batch write |
| `dtype` | Dict | SQL column types override |

---

### SQL Joins in Pandas

Instead of writing complex SQL joins, you can read tables into DataFrames and use pandas.

```python
# Read multiple tables
orders = pd.read_sql('SELECT * FROM orders', engine)
customers = pd.read_sql('SELECT * FROM customers', engine)
products = pd.read_sql('SELECT * FROM products', engine)

# Merge (join) in pandas
df = orders.merge(customers, on='customer_id', how='left')
df = df.merge(products, on='product_id', how='left')

# Equivalent SQL: 
# SELECT * FROM orders 
# LEFT JOIN customers ON orders.customer_id = customers.id
# LEFT JOIN products ON orders.product_id = products.id
```

---

### Advanced SQL Operations

```python
# Aggregation
df = pd.read_sql("""
    SELECT city, 
           COUNT(*) as count, 
           AVG(salary) as avg_salary,
           MAX(salary) as max_salary
    FROM employees
    GROUP BY city
    HAVING COUNT(*) > 10
    ORDER BY avg_salary DESC
""", engine)

# Window functions
df = pd.read_sql("""
    SELECT name, department, salary,
           RANK() OVER (PARTITION BY department ORDER BY salary DESC) as rank
    FROM employees
""", engine)

# Date filtering
df = pd.read_sql("""
    SELECT * FROM sales
    WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31'
""", engine)
```

---

### Best Practices

```python
# 1. Always close connections
engine.dispose()

# 2. Use context manager
from sqlalchemy import create_engine
from contextlib import contextmanager

@contextmanager
def get_engine():
    engine = create_engine('sqlite:///database.db')
    try:
        yield engine
    finally:
        engine.dispose()

with get_engine() as engine:
    df = pd.read_sql('SELECT * FROM table', engine)

# 3. Parameterized queries (prevent SQL injection)
df = pd.read_sql(
    'SELECT * FROM employees WHERE city = %s AND age > %s',
    engine, params=('Mumbai', 30)
)

# 4. Chunk large reads
chunks = pd.read_sql('SELECT * FROM massive_table', engine, chunksize=10000)
for chunk in chunks:
    process(chunk)
```

---

### SQL vs NoSQL Considerations

| | SQL (Relational) | NoSQL (MongoDB, etc.) |
|--|-----------------|----------------------|
| **Structure** | Tables, rows, columns | Documents (JSON-like) |
| **Schema** | Fixed (defined upfront) | Flexible |
| **Relationships** | Foreign keys, joins | Embedded or references |
| **Pandas Support** | ✅ Excellent (read_sql, to_sql) | ⚠️ Needs special library |
| **ML Use** | ✅ Great for structured data | ⚠️ Needs flattening |

---

## Quick Reference Cheatsheet

| Task | Code |
|------|------|
| Read JSON | `pd.read_json('file.json')` |
| Read JSON from API | `pd.read_json(url)` |
| Flatten nested JSON | `json_normalize(data)` |
| Write JSON | `df.to_json('out.json')` |
| Connect to SQLite | `create_engine('sqlite:///db.sqlite')` |
| Read SQL table | `pd.read_sql_table('table', engine)` |
| Read SQL query | `pd.read_sql('SELECT * FROM t', engine)` |
| Write to SQL | `df.to_sql('table', engine, if_exists='replace')` |

---

## Summary

```mermaid
graph TD
    subgraph "JSON"
        J1["pd.read_json()"] --> J2["json_normalize()<br/>For nested data"]
        J2 --> J3["DataFrame"]
        J3 --> J4["df.to_json()"]
    end
    
    subgraph "SQL"
        S1["create_engine()"] --> S2["pd.read_sql()"]
        S2 --> S3["DataFrame"]
        S3 --> S4["df.to_sql()"]
    end
```

```
JSON:
  READ   → pd.read_json('data.json')
  FLATTEN → json_normalize(data)
  WRITE  → df.to_json('output.json')

SQL:
  CONNECT → engine = create_engine('sqlite:///db.sqlite')
  READ    → df = pd.read_sql('SELECT * FROM table', engine)
  WRITE   → df.to_sql('table', engine, if_exists='replace')
```

---

*Based on CampusX videos: "Working with JSON Files" & "Working with SQL Databases | SQL for Data Science"*
