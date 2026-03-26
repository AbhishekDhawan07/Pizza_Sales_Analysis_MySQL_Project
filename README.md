# 🍕 Pizza Sales Analysis — MySQL Project

A structured SQL analytics project built on a real-world-style pizza restaurant dataset. This project covers fundamental to advanced MySQL querying — from basic aggregations and joins to subqueries, window functions, and category-wise revenue rankings — across four relational tables.

---

## 📋 Table of Contents

1. [Project Overview](#project-overview)
2. [Features](#features)
3. [Database Schema](#database-schema)
4. [Dataset Description](#dataset-description)
5. [Query Categories](#query-categories)
6. [Tech Stack](#tech-stack)
7. [Project Structure](#project-structure)
8. [Getting Started](#getting-started)
9. [Sample Queries](#sample-queries)
10. [Key Insights Unlocked](#key-insights-unlocked)
11. [Author](#author)

---

## Project Overview

This project simulates a Pizza Hut-style sales analytics system using MySQL. It models the relationships between customer orders, order line items, pizza variants (by size and price), and pizza type metadata (category, name, ingredients).

The goal is to practice and demonstrate real-world SQL skills — from simple counts and revenue totals to multi-table joins, percentage contribution calculations, hourly demand distribution, and category-wise revenue ranking using window functions.

---

## Features

- **13 SQL queries** spanning three difficulty tiers
- Multi-table `JOIN` operations across 4 normalized tables
- Revenue calculations: total sales, top earners, and category-wise contribution percentages
- **Window functions**: `RANK() OVER (PARTITION BY ...)` for category-wise revenue ranking
- **Subqueries** for percentage share computation and average daily order volume
- Order distribution analysis by hour of day
- Pizza size popularity ranking
- Top-N analysis: most ordered pizzas, highest revenue types, priciest item
- Data quality-aware design with `ROUND()` and `NULLIF` patterns

---

## Database Schema

```
orders
    └── order_id (PK)
    └── date
    └── time

order_details
    └── order_details_id (PK)
    └── order_id (FK → orders)
    └── pizza_id (FK → pizzas)
    └── quantity

pizzas
    └── pizza_id (PK)
    └── pizza_type_id (FK → pizza_types)
    └── size
    └── price

pizza_types
    └── pizza_type_id (PK)
    └── name
    └── category
    └── ingredients
```

---

## Dataset Description

| File | Description | Key Columns |
|---|---|---|
| `orders.csv` | One record per customer order | `order_id`, `date`, `time` |
| `order_details.csv` | Line items for each order | `order_details_id`, `order_id`, `pizza_id`, `quantity` |
| `pizzas.csv` | Pizza variants by size and price | `pizza_id`, `pizza_type_id`, `size`, `price` |
| `pizza_types.csv` | Pizza type metadata | `pizza_type_id`, `name`, `category`, `ingredients` |

---

## Query Categories

### 🟢 Basic Queries (Q1–Q5)
Foundational SQL: counts, aggregations, sorting, and simple joins.

- **Q1** — Retrieve the total number of orders placed
- **Q2** — Calculate total revenue generated from pizza sales
- **Q3** — Identify the highest-priced pizza
- **Q4** — Identify the most common pizza size ordered
- **Q5** — List the top 5 most ordered pizza types along with their quantities

---

### 🔵 Intermediate Queries (Q6–Q10)
Multi-table joins, grouping, date/time functions, and subqueries.

- **Q6** — Find the total quantity of each pizza category ordered
- **Q7** — Determine the distribution of orders by hour of the day
- **Q8** — Find the category-wise distribution of pizzas (count per category)
- **Q9** — Calculate the average number of pizzas ordered per day (subquery + `AVG`)
- **Q10** — Determine the top 3 most ordered pizza types based on revenue

---

### 🔴 Advanced Queries (Q11–Q13)
Percentage contributions, cumulative revenue trends, and window functions.

- **Q11** — Calculate the percentage contribution of each pizza category to total revenue (subquery-based share)
- **Q12** — Analyze the cumulative revenue generated over time (running total by date)
- **Q13** — Determine the top 3 most ordered pizza types based on revenue **for each category** (`RANK() OVER PARTITION BY`)

---

## Tech Stack

- **Database**: MySQL 8.0+
- **Language**: SQL (DDL + DML + Subqueries + Window Functions)
- **Data Format**: CSV (imported via MySQL Workbench or `LOAD DATA INFILE`)
- **Tools**: MySQL Workbench / DBeaver / any MySQL-compatible client

---

## Project Structure

```
Pizza_Sales_Analysis_MySQL_Project/
│
├── README.md
│
└── Pizza Sales Analysis - MySQL Project/
    ├── Pizza Sales Analysis - MySQL Project.sql    # All 13 queries + DDL
    ├── orders.csv
    ├── order_details.csv
    ├── pizzas.csv
    └── pizza_types.csv
```

---

## Getting Started

### 1. Create the database and table

```sql
CREATE DATABASE pizzahut;
USE pizzahut;

CREATE TABLE order_details (
    order_details_id INT NOT NULL,
    order_id         INT NOT NULL,
    pizza_id         TEXT NOT NULL,
    quantity         INT NOT NULL,
    PRIMARY KEY (order_details_id)
);
```

Repeat `CREATE TABLE` for `orders`, `pizzas`, and `pizza_types` following the schema above.

### 2. Import the CSV files

Use MySQL Workbench's **Table Data Import Wizard**, or:

```sql
LOAD DATA INFILE '/path/to/orders.csv'
INTO TABLE orders
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

Repeat for each dataset file.

### 3. Run the queries

Open `Pizza Sales Analysis - MySQL Project.sql` in your MySQL client and execute queries individually or as a batch.

---

## Sample Queries

**Total revenue from pizza sales:**
```sql
SELECT ROUND(SUM(order_details.quantity * pizzas.price), 2) AS total_sales
FROM order_details
JOIN pizzas ON pizzas.pizza_id = order_details.pizza_id;
```

**Average pizzas ordered per day:**
```sql
SELECT ROUND(AVG(quantity), 0) AS avg_pizza_ordered_per_day
FROM (
    SELECT orders.date, SUM(order_details.quantity) AS quantity
    FROM orders
    JOIN order_details ON orders.order_id = order_details.order_id
    GROUP BY orders.date
) AS order_quantity;
```

**Top 3 revenue-generating pizzas per category:**
```sql
SELECT category, name, revenue
FROM (
    SELECT category, name, revenue,
           RANK() OVER (PARTITION BY category ORDER BY revenue DESC) AS rn
    FROM (
        SELECT pizza_types.category, pizza_types.name,
               SUM(order_details.quantity * pizzas.price) AS revenue
        FROM pizza_types
        JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
        GROUP BY pizza_types.category, pizza_types.name
    ) AS a
) AS b
WHERE rn <= 3;
```

---

## Key Insights Unlocked

- Total order volume and overall revenue — a quick business health snapshot
- Which pizza size drives the most orders (demand by format)
- Peak ordering hours — useful for staffing and kitchen capacity planning
- Which pizza categories (Classic, Chicken, Supreme, Veggie) lead in quantity and revenue
- Percentage revenue share per category — identifies the most profitable segment
- The single highest-priced item on the menu
- Top 3 best-sellers both overall and within each category
- Cumulative revenue trend over time to track growth

---

## Author

Built as a MySQL portfolio project to demonstrate SQL proficiency across data retrieval, multi-table joins, aggregation, subqueries, and window functions on a realistic relational sales dataset.

---

> ⭐ If you found this project helpful, feel free to star or fork the repository!
