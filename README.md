# Blinkit-Analysis-SQL-Power BI


## Project Overview



This project showcases SQL skills for exploring, cleaning, and analyzing Blinkit data, involving database setup, EDA, and answering business queries—ideal for beginners in data analysis.

## Objectives
1. **Prepare Blinkit Data**: Clean and format the Blinkit dataset for SQL analysis.
2. **Set Up SQL Database**: Write SQL scripts to create tables and load data.
3. **Data Cleaning**: Remove missing values and inconsistencies.
4. **EDA & Business Queries**: Perform exploratory analysis and answer business-driven questions.

## Project Structure

### 1. Database Setup

 **Database Creation**: The project starts by creating a database named `blinkit_data`.
 **Table Creation**: A table named `blinkit_data` is created to store the blinkit data. 
The table structure includes columns for transaction Item Fat Content,Item Identifier,Item Type,Outlet Establishment Year,Outlet Identifier,Outlet Location Type,Outlet Size,Outlet Type,Item Visibility,Item Weight,Total Sales,Rating





```sql
CREATE DATABASE blinkit
use blinkit

CREATE TABLE Blinkit (
    Item_Fat_Content VARCHAR(50),
    Item_Identifier VARCHAR(50) PRIMARY KEY,
    Item_Type VARCHAR(100),
    Outlet_Establishment_Year INT,
    Outlet_Identifier VARCHAR(50),
    Outlet_Location_Type VARCHAR(50),
    Outlet_Size VARCHAR(50),
    Outlet_Type VARCHAR(50),
    Item_Visibility DECIMAL(10,4),
    Item_Weight DECIMAL(10,2),
    Total_Sales DECIMAL(12,2),
    Rating DECIMAL(5,2)
);
```

### 2. Data Exploration & Cleaning

**Clean Value**: Standardize Item_Fat_Content values.

```sql
UPDATE Blinkit
SET Item_Fat_Content = 
    CASE 
        WHEN Item_Fat_Content IN ('LF', 'low fat') THEN 'Low Fat'
        WHEN Item_Fat_Content = 'reg' THEN 'Regular'
        ELSE Item_Fat_Content
    END;

```

### 3. Data Analysis & Findings


```sql
Retrieve all data from the Blinkit table
SELECT * FROM Blinkit
```


1 **How many total sales we have in Millions**?

```sql
SELECT CONCAT(CAST(SUM(Total_Sales) / 1000000 AS DECIMAL(10,1)), 'M') AS Total_Sales_Millions FROM Blinkit
```



2 **How many unique Items we have**?

```sql
SELECT COUNT(*) AS No_Of_Items FROM Blinkit
```


3 **How many unique Outlet_Identifie we have**?

```sql
SELECT COUNT(DISTINCT outlet_identifier) FROM Blinkit;
```




---------------------------BUSINESS PROBLEMS---------------------------------------

The following SQL queries were developed to answer specific business questions:


1 **What is the average sales'**?

```sql
SELECT CAST(AVG(Total_Sales) AS DECIMAL(10,1)) AS Avg_Sales FROM Blinkit;
```


	
2  **What is the average rating of all items**?

```sql
SELECT CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_Rating FROM Blinkit;
```



3 **What is the total sales, average sales, number of items, and average rating for each Item_Fat_Content type**?

```sql
SELECT Item_Fat_Content,
       CONCAT(CAST(SUM(Total_Sales) / 1000 AS DECIMAL(10,1)), 'K') AS Total_Sales_Thousand, 
       CAST(AVG(Total_Sales) AS DECIMAL(10,1)) AS Avg_Sales,
       COUNT(*) AS No_Of_Items,
       CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_Rating
FROM Blinkit
GROUP BY Item_Fat_Content
ORDER BY Total_Sales_Thousand DESC;
```



4 **What is the total sales, average sales, number of items, and average rating for each Item_Type**?

```sql
SSELECT Item_Type,
       CAST(SUM(Total_Sales) AS DECIMAL(10,1)) AS Total_Sales, 
       CAST(AVG(Total_Sales) AS DECIMAL(10,1)) AS Avg_Sales,
       COUNT(*) AS No_Of_Items,
       CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_Rating
FROM Blinkit
GROUP BY Item_Type
ORDER BY Total_Sales DESC;
```



5  **What is the total sales, average sales, number of items, and average rating for each combination of Outlet_Location_Type and Item_Fat_Content**?

```sql
SELECT Outlet_Location_Type, Item_Fat_Content,
       CAST(SUM(Total_Sales) AS DECIMAL(10,1)) AS Total_Sales, 
       CAST(AVG(Total_Sales) AS DECIMAL(10,1)) AS Avg_Sales,
       COUNT(*) AS No_Of_Items,
       CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_Rating
FROM Blinkit
GROUP BY Outlet_Location_Type, Item_Fat_Content
ORDER BY Total_Sales DESC;
```



6 **What is the total sales for Low Fat and Regular items in each Outlet_Location_Type**?

```sql
SELECT Outlet_Location_Type, 
       COALESCE(SUM(CASE WHEN Item_Fat_Content = 'Low Fat' THEN Total_Sales END), 0) AS Low_Fat,
       COALESCE(SUM(CASE WHEN Item_Fat_Content = 'Regular' THEN Total_Sales END), 0) AS Regular
FROM Blinkit
GROUP BY Outlet_Location_Type
ORDER BY Outlet_Location_Type;
```



7 **What is the total sales, average sales, number of items, and average rating for each Outlet_Establishment_Year**?

```sql
SELECT Outlet_Establishment_Year, 
       CAST(SUM(Total_Sales) AS DECIMAL(10,1)) AS Total_Sales,
       CAST(AVG(Total_Sales) AS DECIMAL(10,1)) AS Avg_Sales,
       COUNT(*) AS No_Of_Items,
       CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_Rating
FROM Blinkit
GROUP BY Outlet_Establishment_Year
ORDER BY Outlet_Establishment_Year ASC;
```




8 **What is the total sales and percentage contribution of each Outlet_Size**?

```sql
SELECT Outlet_Size, 
       CAST(SUM(Total_Sales) AS DECIMAL(10,1)) AS Total_Sales,
       CONCAT(CAST(SUM(Total_Sales) * 100.0 / SUM(SUM(Total_Sales)) OVER () AS DECIMAL(10,1)), '%') AS Percentage_Size
FROM Blinkit
GROUP BY Outlet_Size
ORDER BY Percentage_Size DESC
```


9 **What is the total sales, percentage contribution, average sales, number of items, and average rating for each Outlet_Location_Type**?
```sql
SELECT Outlet_Location_Type, 
       CAST(SUM(Total_Sales) AS DECIMAL(10,1)) AS Total_Sales,
       CONCAT(CAST(SUM(Total_Sales) * 100.0 / SUM(SUM(Total_Sales)) OVER () AS DECIMAL(10,1)), '%') AS Percentage_Type,
       CAST(AVG(Total_Sales) AS DECIMAL(10,1)) AS Avg_Sales,
       COUNT(*) AS No_Of_Items,
       CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_Rating
FROM Blinkit
GROUP BY Outlet_Location_Type
ORDER BY Total_Sales DESC;
```

10 **What is the total sales, average sales, number of items, average rating, and average item visibility for each Outlet_Type**?
```sql
SELECT Outlet_Type, 
       CAST(SUM(Total_Sales) AS DECIMAL(10,1)) AS Total_Sales,
       CAST(AVG(Total_Sales) AS DECIMAL(10,1)) AS Avg_Sales,
       COUNT(*) AS No_Of_Items,
       CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_Rating,
       CAST(AVG(Item_Visibility) AS DECIMAL(10,2)) AS Avg_Item_Visibility
FROM Blinkit
GROUP BY Outlet_Type
ORDER BY Total_Sales DESC;
```


11 **Top 5 Outlets by Sales**?
```sql
SELECT Outlet_Identifier,
       CAST(SUM(Total_Sales) AS DECIMAL(10,1)) AS Total_Sales  
FROM sales_data 
GROUP BY Outlet_Identifier 
ORDER BY Total_Sales DESC 
LIMIT 5;
```



## Findings

► **Total Sales Volume**: The total sales amount is in the millions, showcasing significant transaction activity across Blinkit outlets.

► **Product & Outlet Diversity**: The dataset contains a variety of unique items and outlets, emphasizing a diverse product and store mix.

► **Sales Performance by Fat Content**: Regular and Low Fat items contribute significantly to total sales, with noticeable variations across outlet locations.

► **Top-Selling Item Types**: Certain item types outperform others in total revenue, highlighting consumer preferences for specific product categories.

► **Impact of Outlet Location & Size**: Sales vary by outlet location type, with urban and high-traffic locations generating more revenue, and Larger outlets contribute a higher percentage of total sales, reinforcing their importance in revenue generation

► **Sales Trends by Establishment Year**: Older outlets tend to have higher sales, suggesting a strong customer base and brand loyalty.

► **Outlet Type Insights**: Supermarkets outperform smaller outlets in both sales volume and customer engagement, and Outlet type also influences item visibility, impacting product reach and purchase behavior.

► **Top-Performing Outlets**: The top 5 outlets generate the highest revenue, indicating possible strategic advantages such as location, pricing, or customer preferences.










## Conclusion

This Blinkit sales analysis project showcases SQL skills in data cleaning, EDA, and business insights to optimize sales and customer strategies.


## Author - Vipin Karunakaran

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. 



**END**


