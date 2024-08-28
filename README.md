# SQL-Pizza-Sales-Project

# Pizza Sales SQL Queries

Welcome to the Pizza Sales SQL Queries repository! This repository contains SQL scripts for managing and analyzing a pizza sales database. It includes scripts for creating the database, defining tables, and performing various analyses on the data.

## Table of Contents

- [Overview](#overview)
- [Database Schema](#database-schema)
- [Data Exploration ](#data-exploration)
- [Analysis Queries](#analysis-queries)
- [Findings](#findings)
- [Reports](#Reports)
- [Conclusion](#Conclusion)
- [Usage](#Usage)
- [Author](#Author)
- [Contact](#contact)


## Overview

The **Pizza Sales Database** project offers a set of SQL scripts to manage and analyze a database for pizza sales. It includes:

- **Database Setup**: Create tables for customers, pizzas, orders, and order items.
- **Data Exploration**: Basic queries to explore the data, like counting customers and listing pizza types.
- **Advanced Analysis**: Queries to analyze sales trends, customer spending, pizza popularity, and revenue insights.

### Key Features

- **Understand Customer Behavior**: Identify top customers and their spending habits.
- **Track Pizza Sales**: See which pizzas are most popular and profitable.
- **Analyze Revenue**: Review monthly and regional revenue to optimize strategies.

Use this project to gain insights into your pizza sales and make data-driven decisions.

For questions or more details, please refer to the [Contact](#contact) section below.

## Database Schema

The following tables are defined in the `pizza_sales` database:

### Customers Table

Stores customer information.

```sql
CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    FirstName VARCHAR(50) NOT NULL,
    LastName VARCHAR(50) NOT NULL,
    Email VARCHAR(100) NOT NULL UNIQUE,
    PhoneNumber VARCHAR(20),
    Address VARCHAR(100),
    City VARCHAR(50),
    State CHAR(2),
    ZipCode VARCHAR(10),
    JoinDate DATE
);
```
### Pizzas Table

Stores details about different pizzas.

```sql
CREATE TABLE Pizzas (
    PizzaID INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL,
    Size VARCHAR(20) NOT NULL,
    Price DECIMAL(10, 2) NOT NULL,
    Category VARCHAR(20) NOT NULL
);
```
### Orders Table

Stores information about customer orders

```sql
CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    CustomerID INT NOT NULL,
    OrderDate DATE NOT NULL,
    TotalAmount DECIMAL(10, 2) NOT NULL,
    DeliveryAddress VARCHAR(100),
    Status VARCHAR(20),
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
);
```
### Order Items Table

Stores details about the items in each order.

```sql
CREATE TABLE OrderItems (
    OrderID INT NOT NULL,
    PizzaID INT NOT NULL,
    Quantity INT NOT NULL,
    Price DECIMAL(10, 2) NOT NULL,
    PRIMARY KEY (OrderID, PizzaID),
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID),
    FOREIGN KEY (PizzaID) REFERENCES Pizzas(PizzaID)
);
```
## Data Exploration 

These queries help in exploring the data and understanding the contents of the tables.

### Query 1: Number of Customers
```sql
SELECT count(*) AS Cust_count FROM customers;
```

### Query 2: Types of Pizzas and Categories
```sql
SELECT Name, Category FROM pizzas
ORDER BY Category;
```

### Query 3: Total Number of Orders
```sql
SELECT count(orderid) AS Order_Count
FROM orders;
```
## Data Analysis / Findings 

These questions provide insights and analysis based on the pizza sales data.

### Q1: Retrieve Customer Names and Email Addresses
```sql
SELECT FirstName,LastName, Email FROM customers;
```

### Q2: Find all orders with a total amount greater than $150. Include FirstName ,LastName,OrderID, CustomerID, and TotalAmount in the results.
```sql
SELECT 
	ord.CustomerID,
	FirstName ,
	LastName,
	ord.OrderID,
	ord.TotalAmount 
FROM orders AS ord
JOIN customers AS cst
ON ord.CustomerID = cst.CustomerID
WHERE TotalAmount > 150;
```

### Q3: Calculate the total revenue generated for each customer where the total order is greater than 4. Include CustomerID, TotalOrders, and TotalRevenue in the results.
```sql
SELECT 
	CustomerID,
	count(OrderID)AS total_orders,
    	sum(TotalAmount)AS Total_revenue
FROM orders
GROUP BY CustomerID
HAVING total_orders > 4;
```

### Q4: Find the average price of pizzas in each category. Include Category and AveragePrice in the results.
```sql
SELECT
	Category,
	avg(price) AS avg_price
FROM pizzas
GROUP BY Category;
```
### Q5: Calculate the total revenue and total quantity for each type of pizza. Include the pizza name, size, category, Total revenue generated and Total quantity sold by each pizza type.
```sql
SELECT 
    P.Name,
    P.Size,
    p.category ,
    SUM(P.Price) AS TotalRevenue,
    SUM(oi.Quantity) AS TotalQuantity
FROM OrderItems AS oi
JOIN
    Pizzas AS P ON oi.PizzaID = P.PizzaID
GROUP BY 
	P.Name , P.Size ,p.category , P.Price;
```
### Q6: Retrieve the highest priced pizza in each category. Include Category, PizzaName, and Price in the results.
```sql
SELECT p.Category, 
       p.Name AS PizzaName, 
       p.Price
FROM Pizzas p
JOIN (
    SELECT Category, 
           MAX(Price) AS MaxPrice
    FROM Pizzas
    GROUP BY Category
) max_p
ON p.Category = max_p.Category AND p.Price = max_p.MaxPrice;
```
### Q7:Find the total revenue generated by month for the current year. Include Month and TotalRevenue in the results.
```sql
SELECT 
    MONTHNAME(OrderDate) AS CY_Month,
    SUM(TotalAmount) AS TotalRevenue
FROM orders
WHERE YEAR(orderdate) = 2024
GROUP BY CY_Month;
```
### Q8: Determine the average time between orders for each customer. Assume each order is placed on the same day and calculate the average days between consecutive orders.
```sql
SELECT CustomerID,
       AVG(DATEDIFF(next_order_date, OrderDate)) AS AvgDaysBetweenOrders
FROM (SELECT o1.CustomerID,
             o1.OrderDate,
             LEAD(o1.OrderDate) OVER (PARTITION BY o1.CustomerID ORDER BY o1.OrderDate) AS next_order_date
      FROM Orders o1) AS NewQuery
WHERE next_order_date IS NOT NULL
GROUP BY CustomerID;
```
### Q9: List the top 5 customers who have spent the most on pizza, including CustomerID, TotalSpent, and Rank. Use window functions to rank the customers.
```sql
SELECT CustomerID,
       SUM(TotalAmount) AS TotalSpent,
       RANK() OVER (ORDER BY SUM(TotalAmount) DESC) AS Rank_no
FROM Orders
GROUP BY CustomerID
LIMIT 5;
```
### Q10: Identify the pizzas that are ordered the most frequently, including PizzaID, PizzaName, and TotalQuantityOrdered. Use a subquery to aggregate the data.
```sql
SELECT 
    P.PizzaID,
    P.Name AS PizzaName,
    SUM(oi.Quantity) AS TotalQuantityOrdered
FROM pizzas P
JOIN orderitems OI ON p.pizzaid = oi.PizzaID
GROUP BY PizzaID , PizzaName;
```
### Q11. Identify the top 5 states with the highest total revenue generated from customer orders. For each state, include the total number of customers and the total revenue.
```sql
SELECT 
    C.state,
    COUNT(c.CustomerID) AS cst_count,
    SUM(O.TotalAmount) AS total_revenue
FROM
    customers AS C
        JOIN
    orders AS O ON O.CustomerID = C.CustomerID
GROUP BY State
ORDER BY total_revenue DESC
LIMIT 5; 
```
### Q12. Find all the details for a specific customer, including their full name, email address, phone number, and the total amount they have spent on orders. (CustomerID = 78)
```sql
SELECT 
    C.CustomerID,
    CONCAT(C.FirstName, ' ', C.LastName) AS FullName,
    C.Email,
    C.PhoneNumber,
    COALESCE(SUM(O.TotalAmount), 0) AS TotalAmountSpent
FROM
    customers AS C
LEFT JOIN 
    orders AS O ON C.CustomerID = O.CustomerID
WHERE 
    C.CustomerID = 78
GROUP BY 
    C.CustomerID, C.FirstName, C.LastName, C.Email, C.PhoneNumber;
```

## Findings

* Customer Base: The number of customers and their details are well recorded, with unique email addresses and complete information.

* Pizza Varieties: The database includes a range of pizza categories, sizes, and prices, allowing for diverse menu offerings.

* Order Insights: Orders with higher total amounts are easily identified, and insights into frequent orders and high spenders can be extracted.

* Revenue Analysis: Revenue trends by month and category, as well as the highest-priced pizzas and most frequently ordered items, are readily available.

* Customer Spending: Top customers and their spending patterns are tracked, providing valuable insights for targeted marketing.


## Reports

* Customer Spending Report: Identifies customers who spend the most and provides a ranking.

* Pizza Popularity Report: Shows which pizzas are ordered most frequently and their revenue contribution.

* Revenue Trends Report: Displays total revenue generated by month, helping identify seasonal trends.

* State-Wise Revenue Report: Highlights which states generate the most revenue, useful for regional marketing strategies.

* Order Frequency Report: Provides data on average days between orders for each customer, aiding in customer engagement strategies.

## Conclusions

* High-Value Customers: A small percentage of customers generate a significant portion of the revenue. Focusing on these high-value customers can yield higher returns.

* Popular Pizzas: Certain pizzas are more popular and generate higher revenue, indicating which items should be promoted or kept in stock.

* Seasonal Trends: Revenue varies by month, suggesting that marketing and inventory strategies should be adjusted according to seasonal trends.

* Regional Insights: Some states contribute more to revenue, indicating potential areas for expansion or targeted promotions.

* Customer Retention: Understanding the average time between orders can help tailor loyalty programs and promotional strategies to encourage repeat business.

## Usage
Feel free to use the dataset and the SQL queries.


### Set Up the Database

1. **Create the Database**: Run the provided SQL scripts to set up the `pizza_sales` database and create its tables.

2. **Import Data**: Use your database management tool’s import wizard to load data from CSV files into the corresponding tables in the database.

### Running Queries

1. **Explore Data**: Use the exploration queries to get insights into the data, such as counting customers and listing pizza types.

2. **Analyze Data**: Execute the analysis queries to gather detailed insights into sales trends, customer behavior, and revenue.

For detailed instructions on running queries and interpreting results, refer to the provided SQL scripts.

## Author 

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. If you have any questions, feedback, or would like to collaborate, feel free to get in touch. Thank you for visiting !!

## Contact 

* LinkedIN - 	(www.linkedin.com/in/jatinthakur2004)
* Discord  - 	(https://discord.com/channels/@me)
* Email     -	(jatinthakur.uk@gmail.com)

