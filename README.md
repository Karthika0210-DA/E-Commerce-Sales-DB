# E-Commerce-Sales-DB
An E-Commerce Sales Database is typically designed to manage customers, products, orders, payments, and sales transactions for an online store.

**ER Diagram** 

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/24f5b3df-daac-44ce-b03b-50af2b308bff" />


**Database Schema:**

**Customers Table:**

CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    CustomerName VARCHAR(100),
    City VARCHAR(50),
    Country VARCHAR(50)
);

**Products Table:**

CREATE TABLE Products (
    ProductID INT PRIMARY KEY,
    ProductName VARCHAR(100),
    Category VARCHAR(50),
    Price DECIMAL(10,2)
);

**Orders Table:**

CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    CustomerID INT,
    OrderDate DATE,
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
);

**OrderDetails Table:**

CREATE TABLE OrderDetails (
    OrderDetailID INT PRIMARY KEY,
    OrderID INT,
    ProductID INT,
    Quantity INT,
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID),
    FOREIGN KEY (ProductID) REFERENCES Products(ProductID)
);

**Dataset:**

**Customers**

INSERT INTO Customers VALUES
(1,'John Smith','New York','USA'),
(2,'Emma Brown','London','UK'),
(3,'Raj Kumar','Chennai','India'),
(4,'Sophia Lee','Sydney','Australia'),
(5,'David Wilson','Toronto','Canada');

**Products**

INSERT INTO Products VALUES
(101,'Laptop','Electronics',800),
(102,'Mobile','Electronics',500),
(103,'Headphones','Accessories',100),
(104,'Keyboard','Accessories',50),
(105,'Monitor','Electronics',300);

**Orders**

INSERT INTO Orders VALUES
(1001,1,'2024-01-10'),
(1002,2,'2024-01-12'),
(1003,3,'2024-02-05'),
(1004,1,'2024-02-15'),
(1005,4,'2024-03-01'),
(1006,5,'2024-03-05');

**OrderDetails**

INSERT INTO OrderDetails VALUES
(001,1001,101,1),
(002,1001,103,2),
(003,1002,102,1),
(004,1002,104,3),
(005,1003,105,1),
(006,1004,102,2),
(007,1005,101,1),
(008,1005,104,2),
(009,1006,103,5),
(010,1006,105,1);

**Queries :**

**-- Display all customers**

SELECT * FROM Customers;

**-- Display all products**

SELECT * FROM Products;

**-- Display all orders**

select * from Orders;

**-- Display all orderdetails**

select * from OrderDetails;

**-- Find all orders placed by John Smith**

SELECT o.*, c.CustomerName
FROM Orders o
JOIN Customers c
ON o.CustomerID = c.CustomerID
WHERE c.CustomerName = 'John Smith';  

<img width="675" height="444" alt="image" src="https://github.com/user-attachments/assets/e85ace11-a581-4b3f-aaa2-526ab4abd961" />


**-- Total number of customers**

SELECT COUNT(*) AS TotalCustomers
FROM Customers;

**-- Average product price**

SELECT AVG(Price) AS AvgPrice
FROM Products;

**-- Most expensive product**

SELECT *
FROM Products
ORDER BY Price DESC
LIMIT 1;

<img width="661" height="425" alt="image" src="https://github.com/user-attachments/assets/05d422f9-6507-413a-b669-684a91e0bd71" />


**-- Total sales revenue**

SELECT SUM(od.Quantity * p.Price) AS Revenue
FROM OrderDetails od
JOIN Products p
ON od.ProductID = p.ProductID;

**-- Revenue by product**

SELECT p.ProductName,
       SUM(od.Quantity * p.Price) AS Revenue
FROM OrderDetails od
JOIN Products p
ON od.ProductID = p.ProductID
GROUP BY p.ProductName;

<img width="664" height="424" alt="image" src="https://github.com/user-attachments/assets/e09d1571-b8f9-4117-b4f9-437339d374b9" />


**-- Top-selling product**

SELECT p.ProductName,
       SUM(od.Quantity) AS Soldunits
FROM OrderDetails od
JOIN Products p
ON od.ProductID = p.ProductID
GROUP BY p.ProductName
ORDER BY Soldunits DESC;

**-- Customer-wise revenue**

SELECT c.CustomerName,
       SUM(od.Quantity * p.Price) AS Revenue
FROM Customers c
JOIN Orders o
ON c.CustomerID = o.CustomerID
JOIN OrderDetails od
ON o.OrderID = od.OrderID
JOIN Products p
ON od.ProductID = p.ProductID
GROUP BY c.CustomerName;

<img width="678" height="422" alt="image" src="https://github.com/user-attachments/assets/fca489e7-ee1b-4ff9-846b-aff313c2f760" />


**-- Top 3 customers by revenue**

SELECT c.CustomerName,
       SUM(od.Quantity * p.Price) AS Revenue
FROM Customers c
JOIN Orders o
ON c.CustomerID = o.CustomerID
JOIN OrderDetails od
ON o.OrderID = od.OrderID
JOIN Products p
ON od.ProductID = p.ProductID
GROUP BY c.CustomerName
ORDER BY Revenue DESC
LIMIT 3;

**-- Products never ordered**

SELECT p.ProductName
FROM Products p
LEFT JOIN OrderDetails od
ON p.ProductID = od.ProductID
WHERE od.ProductID IS NULL;

**-- Rank products by revenue**

SELECT
    p.ProductName,
    SUM(od.Quantity * p.Price) Revenue,
    RANK() OVER(
        ORDER BY SUM(od.Quantity * p.Price) DESC
    ) AS RevenueRank
FROM Products p
JOIN OrderDetails od
ON p.ProductID = od.ProductID
GROUP BY p.ProductName;

<img width="689" height="423" alt="image" src="https://github.com/user-attachments/assets/f9d26c41-48a5-4e4f-8b51-01f822998bb7" />



**-- Running revenue total**

SELECT
    o.OrderDate,
    SUM(od.Quantity * p.Price) DailyRevenue,
    SUM(SUM(od.Quantity * p.Price))
      OVER(ORDER BY o.OrderDate)
      AS RunningRevenue
FROM Orders o
JOIN OrderDetails od
ON o.OrderID = od.OrderID
JOIN Products p
ON od.ProductID = p.ProductID
GROUP BY o.OrderDate;
ON od.ProductID = p.ProductID
GROUP BY o.OrderDate;

<img width="673" height="417" alt="image" src="https://github.com/user-attachments/assets/a6aadf8f-c004-4342-8365-7aff1941fe76" />
