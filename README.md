# Customer Segmentation Analysis Using SQL

## Project Overview

This project uses SQL to segment customers based on their purchasing behavior and identify different levels of customer value.

The analysis focuses on completed orders between **January 1 and September 30, 2026** and evaluates customers using:

- Total revenue
- Number of orders
- Average order value
- Most recent order date

## Customer Segmentation

Customers are classified into five groups:

| Segment | Criteria |
|---|---|
| VIP | Revenue ≥ $5,000 and 10+ orders |
| High Value | Revenue ≥ $3,000 |
| Loyal | 8+ orders |
| Regular | Revenue ≥ $1,000 |
| Low Value | Remaining customers |

## SQL Approach

The analysis uses:

- **CTEs** to organize the analysis into logical steps
- **WHERE** to filter completed transactions within the analysis period
- **SUM(), COUNT(), AVG(), and MAX()** to calculate customer-level metrics
- **GROUP BY** to aggregate transactions by customer
- **CASE WHEN** to apply customer segmentation rules

## Business Objective

The purpose of the analysis is not only to classify customers, but to understand **which customer groups contribute the most revenue and how their purchasing behaviors differ**.

These insights can support decisions around customer retention, targeted marketing, loyalty programs, and revenue growth.

## Next Step

The next stage of the analysis will compare the segments by customer count, total revenue, average revenue per customer, and contribution to overall revenue to identify the segments with the greatest business impact.
```-- Step 1: Calculate customer-level purchasing metrics
WITH Customer_metrics AS (SELECT 
        customer_id,
        SUM(order_amount) AS total_revenue, #Total amount the customer spent during the analysis period
        COUNT(order_id) AS total_orders,  # Number of completed orders placed by the customer
        AVG(order_amount) AS avg_order_value, # Average amount spent per order
        MAX(order_date) AS last_order_date  # Most recent completed order for the customer
    FROM orders
    WHERE order_status = 'Completed' # Only analyze completed orders within the specified period
      AND order_date BETWEEN '2026-01-01' AND '2026-09-30'
    GROUP BY customer_id #Aggregate multiple order records into one record per customer),
-- Step 2: Assign each customer to a segment
Customer_segment AS (SELECT 
        customer_id,
        total_revenue,
        total_orders,
        avg_order_value,
        last_order_date,
        CASE         #CASE when to define customer segmentation rules
            WHEN total_revenue >= 5000 
                 AND total_orders >= 10 
                THEN 'VIP'  # High spending AND high order frequency
            WHEN total_revenue >= 3000 
                THEN 'High Value' # High spending customers
            WHEN total_orders >= 8 
                THEN 'Loyal' # Frequent customers who may not have high total spending
            WHEN total_revenue >= 1000 
                THEN 'Regular' # Average-value customers
            -- Customers who do not meet any of the above criteria
            ELSE 'Low Value' # the remaining customers that doest fit in other criteria
        END AS customer_segment
    FROM Customer_metrics)
-- Step 3: Return the final customer segmentation dataset
SELECT
    customer_id,
    total_orders,
    total_revenue,
    avg_order_value,
    last_order_date,
    customer_segment
FROM Customer_segment;
```

