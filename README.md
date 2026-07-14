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

Moved from single-table exploration into multi-table joins to answer real business questions.

**Nation-level order value**

Joined CUSTOMER, ORDERS, and NATION to see which nations generate the most order value.

Top 4 by total order value:

| Nation     | Total orders | Total order value | Avg order value |
|------------|--------------|--------------------|--------------------|
| France     | 61,600       | $9.32B             | $151,277.86        |
| Indonesia  | 61,377       | $9.30B             | $151,536.08        |
| Russia     | 61,495       | $9.28B             | $150,944.36        |
| Mozambique | 61,267       | $9.25B             | $150,964.05        |

Order counts and total values stayed fairly close across all 25 nations (roughly 58,000–61,600 
orders each). This reflects TPC-H's evenly distributed sample data rather than any real regional 
skew, worth noting rather than overstating as a genuine business insight.

**Part-level revenue**

Joined LINEITEM and PART to find the top 20 parts by revenue generated (extended price minus 
discount, summed across all line items).

Query: [`queries/02_joins_aggregation.sql`](queries/02_joins_aggregation.sql)


### 3. Analysis
(to be filled in)

## Notes

This project uses Snowflake's own built-in sample dataset. All queries were run and 
verified directly in Snowsight; results shown reflect actual output, not illustrative examples.
