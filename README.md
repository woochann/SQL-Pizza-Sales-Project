# SQL-Pizza-Sales-Project

# Pizza Sales SQL Queries

Welcome to the Pizza Sales SQL Queries repository! This repository contains SQL scripts for managing and analyzing a pizza sales database. It includes scripts for creating the database, defining tables, and performing various analyses on the data.

## Table of Contents

- [Overview](#overview)
- [Database Schema](#database-schema)
- [Data Exploration Queries](#data-exploration-queries)
- [Analysis Queries](#analysis-queries)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

## Overview

This repository provides SQL scripts for:
- Creating and setting up a `pizza_sales` database.
- Defining tables for customers, pizzas, orders, and order items.
- Performing data exploration and analysis on pizza sales data.

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
### Data Exploration Queries

These queries help in exploring the data and understanding the contents of the tables.

Query 1: Number of Customers



