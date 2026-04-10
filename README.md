# Customer-Value-and-Churn-Analysis
15% of customers contributed to a £4.39M revenue loss, accounting for approximately 41% of total revenue. This dashboard explains that this loss did not occur suddenly, it resulted from gradual customer disengagement over time. This highlights the importance on focusing on customer retention.
Process:

RFM analysis was conducted using an SQL query to create a customer segmentation table.
This table was then combined with the main sales dataset in Power BI to develop the dashboard.

Customers were segmented based on:

Recency (R): How recently a customer made a purchase
Frequency (F): How often a customer makes purchases
Monetary (M): How much a customer spends

SQL Query:

WITH base AS (
    SELECT 
        CustomerID,
        MAX(InvoiceDate) AS LastPurchaseDate,
        COUNT(DISTINCT InvoiceNo) AS Frequency,
        SUM(Quantity * UnitPrice) AS Monetary
    FROM SalesTable
    WHERE CustomerID IS NOT NULL
    GROUP BY CustomerID
),

rfm AS (
    SELECT *,
        DATEDIFF(day, LastPurchaseDate, (SELECT MAX(InvoiceDate) FROM SalesTable)) AS Recency
    FROM base
),

scored AS (
    SELECT *,
        NTILE(5) OVER (ORDER BY Recency ASC) AS R_score,
        NTILE(5) OVER (ORDER BY Frequency DESC) AS F_score,
        NTILE(5) OVER (ORDER BY Monetary DESC) AS M_score
    FROM rfm
)

SELECT *,
    CASE 
        WHEN R_score = 5 AND F_score >= 4 AND M_score >= 4 THEN 'Champions'
        WHEN R_score >= 4 AND F_score >= 4 THEN 'Loyal Customers'
        WHEN R_score >= 4 THEN 'Recent Customers'
        WHEN R_score <= 2 AND F_score >= 3 THEN 'At Risk'
        WHEN R_score = 1 THEN 'Churned'
        ELSE 'Average'
    END AS Segment
FROM scored;

Key Insights
Churn % vs Churn Risk %

These two values are almost identical, suggesting the business is at risk of losing as many customers as it has already lost if no action is taken.

High-Value Customer Risk

The highest-value customer is currently classified as “At Risk.”
This highlights a direct threat to revenue and indicates the potential for further financial loss.

Revenue Concentration Risk

Although churned customers represent only 15.6% of the customer base, they account for the largest share of revenue.
This confirms that the business is losing high-value customers, not just a high number of customers.

Behavioral Churn Pattern

Customers who churn are concentrated in low-frequency segments (scatter plot).
The line chart shows that purchase frequency declines over time for these customers, confirming that churn is driven by gradual disengagement rather than sudden drop-off.

Early-Stage Churn (0–30 Days)

Most churn occurs within the first 30 days.
This indicates that while customer acquisition is effective, early-stage retention is weak.

Recommendations
Focus on retaining high-value customers through targeted engagement, rewards, and proactive support.
Monitor purchase frequency and intervene early when customers begin to disengage.
Improve onboarding and early customer experience to reduce churn within the first 30 days.
Final Thought

The role of data analysis extends beyond visualization.
It involves identifying the underlying drivers of business performance and providing clear, actionable insights to support decision-making.
