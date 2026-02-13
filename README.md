# ECOMMERCE-SQL-ANALYSIS
🛒 E-Commerce SQL Business Analysis
📌 Project Overview

This project analyzes transactional data from an e-commerce company using SQL.
The goal is to extract actionable business insights related to:

Customer segmentation

Sales trends

Product performance

Inventory turnover

Customer acquisition

🗂 Database Tables Used

Orders

OrderDetails

Products

Customers

📊 Business Problems & Solutions
1️⃣ Customer Segmentation by Engagement Depth
🎯 Objective

Segment customers based on number of orders placed:

One-time buyers

Occasional shoppers

Regular customers

🧠 Business Value

Helps marketing team create targeted campaigns.

💻 SQL Query
WITH cte1 AS (
    SELECT customer_id, COUNT(order_id) AS NumberOfOrders
    FROM Orders
    GROUP BY customer_id
)
SELECT NumberOfOrders,
       COUNT(customer_id) AS CustomerCount
FROM cte1
GROUP BY NumberOfOrders
ORDER BY NumberOfOrders;

📈 Insight

Most customers are one-time buyers, indicating retention opportunity.

2️⃣ High-Value Product Identification
🎯 Objective

Identify products with average quantity per order = 2 but high revenue.

💻 SQL Query
SELECT product_id AS Product_Id,
       AVG(quantity) AS AvgQuantity,
       SUM(quantity * price_per_unit) AS TotalRevenue
FROM OrderDetails
GROUP BY product_id
HAVING AVG(quantity) = 2
ORDER BY TotalRevenue DESC;

📈 Insight

These products may represent premium category demand.

3️⃣ Customer Acquisition Trends
🎯 Objective

Analyze month-on-month new customer growth.

WITH cte1 AS (
    SELECT customer_id,
           MIN(order_date) AS first_order
    FROM Orders
    GROUP BY customer_id
)
SELECT DATE_FORMAT(first_order, '%Y-%m') AS FirstPurchaseMonth,
       COUNT(customer_id) AS TotalNewCustomers
FROM cte1
GROUP BY FirstPurchaseMonth
ORDER BY FirstPurchaseMonth;

📈 Insight

Shows effectiveness of marketing campaigns.

4️⃣ Low Engagement Products
🎯 Objective

Find products purchased by less than 40% of customers.

SELECT p.product_id,
       p.name,
       COUNT(DISTINCT o.customer_id) AS UniqueCustomerCount
FROM Products p
JOIN OrderDetails od ON p.product_id = od.product_id
JOIN Orders o ON od.order_id = o.order_id
GROUP BY p.product_id, p.name
HAVING COUNT(DISTINCT o.customer_id) <
       0.4 * (SELECT COUNT(DISTINCT customer_id) FROM Customers);

📈 Insight

Helps identify products needing promotion or removal.

5️⃣ Sales Trend Analysis (Month-on-Month Growth)
WITH cte1 AS (
    SELECT DATE_FORMAT(order_date, '%Y-%m') AS Month,
           SUM(total_amount) AS TotalSales
    FROM Orders
    GROUP BY DATE_FORMAT(order_date, '%Y-%m')
),
cte2 AS (
    SELECT Month,
           TotalSales,
           LAG(TotalSales) OVER (ORDER BY Month) AS PreviousMonth
    FROM cte1
)
SELECT Month,
       TotalSales,
       ROUND(((TotalSales - PreviousMonth)/PreviousMonth)*100,2) AS PercentChange
FROM cte2;

📈 Insight

Identifies growth acceleration or decline.

6️⃣ Inventory Refresh Rate
SELECT product_id,
       COUNT(*) AS SalesFrequency
FROM OrderDetails
GROUP BY product_id
ORDER BY SalesFrequency DESC
LIMIT 5;

📈 Insight

Top 5 fastest moving products.

7️⃣ Average Order Value Fluctuation
WITH cte1 AS (
    SELECT DATE_FORMAT(order_date, '%Y-%m') AS Month,
           ROUND(AVG(total_amount),2) AS AvgOrderValue
    FROM Orders
    GROUP BY DATE_FORMAT(order_date, '%Y-%m')
),
cte2 AS (
    SELECT Month,
           AvgOrderValue,
           LAG(AvgOrderValue) OVER (ORDER BY Month) AS previous
    FROM cte1
)
SELECT Month,
       AvgOrderValue,
       ROUND(AvgOrderValue - previous,2) AS ChangeInValue
FROM cte2
ORDER BY ChangeInValue DESC;

8️⃣ Peak Sales Period Identification
SELECT DATE_FORMAT(order_date, '%Y-%m') AS Month,
       SUM(total_amount) AS TotalSales
FROM Orders
GROUP BY Month
ORDER BY TotalSales DESC
LIMIT 3;

📌 Key Business Insights

High dependency on one-time buyers.

Few products dominate revenue.

Clear seasonal sales spikes.

Inventory optimization opportunity exists.

🎯 Skills Demonstrated

CTEs

Window Functions (LAG)

Aggregation

Joins

Subqueries

Business-driven SQL analysis
