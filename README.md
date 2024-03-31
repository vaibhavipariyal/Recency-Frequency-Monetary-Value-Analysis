# Recency-Frequency-Monetary-Value-Analysis
RFM is a method used for analyzing customer value and segmenting customers which is commonly used in database marketing and direct marketing.

RFM Analysis is a marketing analytics tool used to identify a company’s most valuable customers by examining three specific purchasing behaviours:

1. How recently a customer has made a purchase (Recency),
2. How often they make purchases (Frequency),
3. How much money they spend (Monetary value).

Recency (R): Calculate how recently each customer made a purchase.
    -> SELECT CustomerID, MAX(PurchaseDate) AS LastPurchaseDate,
         DATEDIFF(CURRENT_DATE, MAX(PurchaseDate)) AS Recency
       FROM YourTableName
         GROUP BY CustomerID;

Frequency (F): Calculate how often each customer makes a purchase.
    -> SELECT CustomerID, COUNT(*) AS Frequency
       FROM YourTableName
        GROUP BY CustomerID;

Monetary (M): Calculate the total money spent by each customer.
    -> SELECT CustomerID, SUM(TransactionAmount) AS Monetary
       FROM YourTableName
          GROUP BY CustomerID;


Combining in a single query
    -> SELECT R.CustomerID, R.Recency, F.Frequency, M.Monetary
        FROM (SELECT CustomerID, DATEDIFF(CURRENT_DATE, MAX(PurchaseDate)) AS Recency
              FROM YourTableName
              GROUP BY CustomerID) R
        JOIN (SELECT CustomerID, COUNT(*) AS Frequency
              FROM YourTableName
              GROUP BY CustomerID) F ON R.CustomerID = F.CustomerID
        JOIN (SELECT CustomerID, SUM(TransactionAmount) AS Monetary
              FROM YourTableName
              GROUP BY CustomerID) M ON R.CustomerID = M.CustomerID;

With the RFM scores available, classify customers into segments 
      -> SELECT CustomerID,
         CASE
           WHEN Recency <= 30 THEN 'High'
           WHEN Recency BETWEEN 31 AND 60 THEN 'Medium'
           ELSE 'Low'
         END AS RecencyScore,
         CASE
           WHEN Frequency >= 10 THEN 'High'
           WHEN Frequency BETWEEN 5 AND 9 THEN 'Medium'
           ELSE 'Low'
         END AS FrequencyScore,
         CASE
           WHEN Monetary >= 500 THEN 'High'
           WHEN Monetary BETWEEN 200 AND 499 THEN 'Medium'
           ELSE 'Low'
         END AS MonetaryScore
         FROM CombinedRFMTable;
