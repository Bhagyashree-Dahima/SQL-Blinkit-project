# SQL-Blinkit-project
This project analyzes Blinkit dataset using SQL to generate business insights. The focus is on data cleaning, KPI creation, and outlet-based sales analysis. Queries are designed to handle inconsistencies, improve data quality, and provide clear decision-making insights.
Perfect 👍 I see your uploaded doc (`Query Doc.docx`) is about **Blinkit Sales Data Analysis with SQL** — including data cleaning, KPIs, and advanced queries like PIVOT, group-wise sales, outlet analysis, etc.&#x20;

Here’s a **GitHub-friendly README.md** draft with project description, queries, and structure:

---

# 📊 Blinkit Sales Data Analysis (SQL Project)

## 🔹 Project Overview

This project analyzes **Blinkit (Big Basket / Grocery Sales) dataset** using **SQL** to generate business insights. The focus is on **data cleaning, KPI creation, and outlet-based sales analysis**. Queries are designed to handle inconsistencies, improve data quality, and provide clear decision-making insights.

---

## 🔹 Objectives

* Clean raw data (remove inconsistencies in `Item_Fat_Content`).
* Create **Key Performance Indicators (KPIs)** such as total sales, average sales, total orders, and ratings.
* Analyze sales by **item category, fat content, outlet type, location, and size**.
* Perform **pivot transformations** for multi-dimensional reporting.
* Provide business-ready insights through structured SQL queries.

---

## 🔹 Dataset

The dataset (`blinkit_data`) includes:

* **Item Details:** Item\_Type, Item\_Fat\_Content, Item\_Visibility, Rating
* **Sales Information:** Total\_Sales, Outlet\_Establishment\_Year, Outlet\_Size, Outlet\_Location\_Type, Outlet\_Type
* **Order Details:** Count of items/orders

---

## 🔹 Data Cleaning

Standardized inconsistent values in **Item\_Fat\_Content** field:

```sql
UPDATE blinkit_data
SET Item_Fat_Content = 
    CASE 
        WHEN Item_Fat_Content IN ('LF', 'low fat') THEN 'Low Fat'
        WHEN Item_Fat_Content = 'reg' THEN 'Regular'
        ELSE Item_Fat_Content
    END;

SELECT DISTINCT Item_Fat_Content FROM blinkit_data;
```

---

## 🔹 Key Insights (SQL Queries)

### 1️⃣ KPIs

* **Total Sales** (in million)
* **Average Sales**
* **No. of Items Sold**
* **Average Rating**

```sql
SELECT CAST(SUM(Total_Sales) / 1000000.0 AS DECIMAL(10,2)) AS Total_Sales_Million FROM blinkit_data;
SELECT CAST(AVG(Total_Sales) AS INT) AS Avg_Sales FROM blinkit_data;
SELECT COUNT(*) AS No_of_Orders FROM blinkit_data;
SELECT CAST(AVG(Rating) AS DECIMAL(10,1)) AS Avg_Rating FROM blinkit_data;
```

---

### 2️⃣ Sales by Fat Content

```sql
SELECT Item_Fat_Content, CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales
FROM blinkit_data
GROUP BY Item_Fat_Content;
```

---

### 3️⃣ Sales by Item Type

```sql
SELECT Item_Type, CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales
FROM blinkit_data
GROUP BY Item_Type
ORDER BY Total_Sales DESC;
```

---

### 4️⃣ Fat Content by Outlet (Pivot Table)

```sql
SELECT Outlet_Location_Type, 
       ISNULL([Low Fat], 0) AS Low_Fat, 
       ISNULL([Regular], 0) AS Regular
FROM (
    SELECT Outlet_Location_Type, Item_Fat_Content, 
           CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales
    FROM blinkit_data
    GROUP BY Outlet_Location_Type, Item_Fat_Content
) AS SourceTable
PIVOT (
    SUM(Total_Sales) 
    FOR Item_Fat_Content IN ([Low Fat], [Regular])
) AS PivotTable
ORDER BY Outlet_Location_Type;
```

---

### 5️⃣ Sales by Outlet Establishment Year

```sql
SELECT Outlet_Establishment_Year, CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales
FROM blinkit_data
GROUP BY Outlet_Establishment_Year
ORDER BY Outlet_Establishment_Year;
```

---

### 6️⃣ Sales Percentage by Outlet Size

```sql
SELECT Outlet_Size, 
       CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales,
       CAST((SUM(Total_Sales) * 100.0 / SUM(SUM(Total_Sales)) OVER()) AS DECIMAL(10,2)) AS Sales_Percentage
FROM blinkit_data
GROUP BY Outlet_Size
ORDER BY Total_Sales DESC;
```

---

### 7️⃣ Sales by Outlet Location Type

```sql
SELECT Outlet_Location_Type, CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales
FROM blinkit_data
GROUP BY Outlet_Location_Type
ORDER BY Total_Sales DESC;
```

---

### 8️⃣ Metrics by Outlet Type

```sql
SELECT Outlet_Type, 
       CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales,
       CAST(AVG(Total_Sales) AS DECIMAL(10,0)) AS Avg_Sales,
       COUNT(*) AS No_Of_Items,
       CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_Rating,
       CAST(AVG(Item_Visibility) AS DECIMAL(10,2)) AS Item_Visibility
FROM blinkit_data
GROUP BY Outlet_Type
ORDER BY Total_Sales DESC;
```

---

## 🔹 Key Business Findings

* **Regular items dominate sales** compared to Low Fat.
* **Top-selling categories** can be identified from Item\_Type analysis.
* **Large outlets contribute the most** to total sales percentage.
* **Sales trend across outlet establishment years** helps track long-term performance.
* **Tier-wise location analysis** shows urban outlets outperform rural ones.

---

## 🔹 Tools Used

* **SQL (SQL Server)** – Data Cleaning, Querying, Analysis

---

## 📌 How to Use

1. Import dataset into your SQL database.
2. Run the queries from `/sql_queries` folder.
3. Explore insights and visualize results in Power BI/Excel.

---

