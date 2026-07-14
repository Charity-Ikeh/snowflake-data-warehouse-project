# Snowflake Data Warehouse Project

Hands-on SQL work using Snowflake's TPC-H sample dataset (SNOWFLAKE_SAMPLE_DATA.TPCH_SF1). 
Covers data exploration, multi-table joins, and business-focused analysis.

## Environment

- Platform: Snowflake (Enterprise trial, Azure region)
- Dataset: TPC-H benchmark data, Scale Factor 1
- Tables used: CUSTOMER, ORDERS, LINEITEM, NATION, REGION, PART, PARTSUPP, SUPPLIER

## Project structure

- `/queries` — SQL files for each stage of the work
- `/screenshots` — result screenshots taken directly from Snowsight

## Work completed

### 1. Data exploration

Before writing any joins, I started by pulling raw sample rows from the three core tables 
(CUSTOMER, ORDERS, LINEITEM) to get a feel for the structure and what each column actually 
meant.

From there I checked row counts across all 8 tables to understand the scale I was working with:

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

LINEITEM is by far the biggest table, which tracks with roughly 4 line items per order on 
average. That matches the one-to-many relationship you'd expect between ORDERS and LINEITEM — 
order 2400001 alone had 5 line items in it when I looked at the raw data.

Query: [`queries/01_exploration.sql`](queries/01_exploration.sql)

### 2. Joins and aggregation

Once I had a feel for the tables individually, I moved into joining them to answer questions 
that actually mean something from a business standpoint.

**Nation-level order value**

Joined CUSTOMER, ORDERS, and NATION to see which nations were generating the most order value.

Top 4 by total order value:

| Nation     | Total orders | Total order value | Avg order value |
|------------|--------------|--------------------|--------------------|
| France     | 61,600       | $9.32B             | $151,277.86        |
| Indonesia  | 61,377       | $9.30B             | $151,536.08        |
| Russia     | 61,495       | $9.28B             | $150,944.36        |
| Mozambique | 61,267       | $9.25B             | $150,964.05        |

Order counts and total values stayed pretty close across all 25 nations, sitting between roughly 
58,000 and 61,600 orders each. That's really just a reflection of how evenly TPC-H generates its 
sample data — not a real regional pattern, so I'm not going to pretend it's a genuine business 
insight.

**Part-level revenue**

Joined LINEITEM and PART to pull the top 20 parts by revenue generated (extended price minus 
discount, summed across every line item).

Query: [`queries/02_joins_aggregation.sql`](queries/02_joins_aggregation.sql)

### 3. Analysis

**Customer value segmentation**

Segmented all 150,000 customers by total lifetime spend, using a LEFT JOIN from CUSTOMER to 
ORDERS so customers with zero orders would still show up in the results instead of quietly 
disappearing.

My first pass used thresholds of $500,000 and $200,000, but that put 99,091 of the 150,000 
customers into "high value" — not exactly a useful split. TPC-H order values run a lot higher 
than typical retail figures, so I went back, looked at the actual spend distribution, and reset 
the thresholds to $3,000,000 and $1,500,000.

Final segmentation:

| Segment    | Customers | Avg spend per customer |
|------------|-----------|--------------------------|
| High value | 24,921    | $3,654,671.08            |
| Mid value  | 48,301    | $2,203,167.17            |
| Low value  | 26,774    | $1,095,692.50            |
| No orders  | 50,004    | $0.00                    |

One thing that stood out here: about a third of all customers (50,004) have never placed an 
order. In a real business, that's the kind of number a sales or marketing team would actually 
want to see, not something to just skim past.

**Data quality check on ORDERS**

Ran a basic data quality pass on the ORDERS table, checking for nulls in key fields and any 
invalid price values:

| Check                               | Result    |
|---------------------------------------|-----------|
| Total orders                          | 1,500,000 |
| Non-null order key                    | 1,500,000 |
| Non-null customer key                 | 1,500,000 |
| Orders with zero or negative price    | 0         |
| Orders with null order date           | 0         |

No issues turned up, but that's expected — TPC-H is a clean, synthetic benchmark dataset by 
design. Nothing here was cleaned or fixed, this was just a check.

Query: [`queries/03_analysis.sql`](queries/03_analysis.sql)

## Notes

This project uses Snowflake's own built-in TPC-H sample dataset. Every query here was run and 
verified directly in Snowsight — the results shown are actual output, not made up for the sake 
of the write-up.
