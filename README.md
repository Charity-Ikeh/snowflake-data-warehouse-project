# Snowflake Data Warehouse Project

Hands-on SQL analysis using Snowflake's TPC-H sample dataset (SNOWFLAKE_SAMPLE_DATA.TPCH_SF1), 
covering data exploration, multi-table joins, and business-focused aggregation.

## Environment

- Platform: Snowflake (Enterprise trial, Azure region)
- Dataset: TPC-H benchmark data, Scale Factor 1
- Tables used: CUSTOMER, ORDERS, LINEITEM, NATION, REGION, PART, PARTSUPP, SUPPLIER

## Project structure

- `/queries` — SQL files for each stage of the analysis
- `/screenshots` — result screenshots from Snowsight

## Work completed

### 1. Data exploration

Started by looking at raw sample rows from the three core tables (CUSTOMER, ORDERS, LINEITEM) 
to understand structure and column meaning before doing any joins.

Then checked row counts across all 8 tables to understand scale:

| Table     | Row count |
|-----------|-----------|
| CUSTOMER  | 150,000   |
| ORDERS    | 1,500,000 |
| LINEITEM  | 6,001,215 |
| SUPPLIER  | 10,000    |
| PART      | 200,000   |
| PARTSUPP  | 800,000   |
| NATION    | 25        |
| REGION    | 5         |

LINEITEM is by far the largest table, roughly 4 line items per order on average, which makes 
sense given the one-to-many relationship between ORDERS and LINEITEM confirmed in the raw data 
(order 2400001 alone had 5 line items).

Query: [`queries/01_exploration.sql`](queries/01_exploration.sql)


### 2. Joins and aggregation
(to be filled in)

### 3. Analysis
(to be filled in)

## Notes

This project uses Snowflake's own built-in sample dataset. All queries were run and 
verified directly in Snowsight; results shown reflect actual output, not illustrative examples.
