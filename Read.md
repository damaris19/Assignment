FINANACIAL_ANALYTICS ASSIGNMENT

This is a SQL project to analyze data from a fictional company.

This is a readme file for the Finance database. The Finance database is a Microsoft SQL Server database that contains the DimCustomer and DimProduct, DimSalesTerritory, DimInvoices, and FactResellers tables.

TABLE OF CONTENTS

1. Prerequisites
2. Create Database

3. Create Tables & Exploratory Data Analysis
	3.1 DimCustomer
		 3.1.1Exploratory Data Analysis
	3.2 DimProduct 
		 3.2.1Exploratory Data Analysis
	3.3 DimSalesTerritory 
	     3.3.1Exploratory Data Analysis
	3.4 DimInvoices
		 3.4.1Exploratory Data Analysis
	3.5 FactResellerSales
		 3.5.1Exploratory Data Analysis
4. SQL Questions

-----------------------------------------------------------------------------------------------------------=
Prerequisites
Microsoft SQL Server Management Studio (SSMS)
Sample dataset provided in CSV format

Steps
	Create database
	Create tables: DimProduct, DimSalesTerritory, DimInvoices, DimInvoices FactResellersTable
	Import data from CSV files into the tables
	Conduct exploratory data analysis
	Write SQL queries to answer  questions



------------------------------------------------------------------------------------------------------------
1. Create database
Create a new database in SSMS called "Finance".
------------------------------------------------------------------------------------------------------------
2. Create tables and import data 
Use the following SQL codes to create each of the necessary tables:
------------------------------------------------------------------------------------------------------------
Dimcustomer 

DROP TABLE DimProduct;
--Create table DimProduct table fields:
CREATE TABLE DimProduct (
    ProductKey INT PRIMARY KEY,
    ProductAlternateKey NVARCHAR(50),
    ProductSubcategoryKey NVARCHAR(10) DEFAULT NULL,
    WeightUnitMeasureCode NVARCHAR(10) DEFAULT NULL,
    SizeUnitMeasureCode NVARCHAR(10)   DEFAULT NULL,
    ProductName NVARCHAR(50),
    StandardCost NVARCHAR(20), -- Convert to numeric as needed
    FinishedGoodsFlag BIT,
    Color NVARCHAR(15),
    SafetyStockLevel INT,
    ReorderPoint INT,
    ListPrice NVARCHAR(20), -- Convert to numeric as needed
    Size NVARCHAR(50),
    SizeRange NVARCHAR(50),
    Weight NVARCHAR(20), -- Convert to numeric as needed,
    DaysToManufacture INT,
    ProductLine NVARCHAR(50)          DEFAULT NULL,
    DealerPrice NVARCHAR(20), -- Convert to numeric as needed
    Class NVARCHAR(50)                DEFAULT NULL,
    Style NVARCHAR(50)                DEFAULT NULL,
    ModelName NVARCHAR(50)            DEFAULT NULL,
    StartDate DATETIME,
    EndDate NVARCHAR(50), -- Convert to DATETIME as needed,   
    Status NVARCHAR(50)               DEFAULT NULL
);

Inserting  Data from CSV

BULK INSERT DimProduct
FROM 'C:\Users\DAMARIS\Documents\PRODUCT.csv'
WITH (
  FORMAT = 'CSV',
  FIRSTROW = 2,
  FIELDTERMINATOR = ',',
  ROWTERMINATOR = '\n'
);
------------------------------------------------------------------------------------------------------------
DimCustomers Table Exploratory  Data Analysis
------------------------------------------------------------------------------------------------------------
The following are some exploratory data analysis queries on the DimCustomer table:

Total number of customers: 
SELECT COUNT(*) FROM DimCustomer; 

Number of unique customers: 
SELECT COUNT(DISTINCT CustomerKey) FROM DimCustomer;

Average yearly income: 
SELECT AVG(YearlyIncome) FROM DimCustomer;

Customers earnings by gender: 

SELECT Gender, SUM(YearlyIncome) 
FROM DimCustomer 
GROUP BY Gender 
ORDER BY SUM(YearlyIncome) ASC;
-- men earn more than women overall but other factors need to be checked)

Percentage of male customers:
 SELECT 
    (COUNT(CASE WHEN Gender ='M' THEN 1 END)/CAST(COUNT(*) AS float)*100.00) AS Percentage_Male_Customers 
 FROM DimCustomer; (50.58%)

Average income by gender: 
SELECT Gender, AVG(YearlyIncome) 
FROM DimCustomer 
GROUP BY Gender; (female customers earn slightly more than male customers on average)

Customers  distribution by education level
SELECT EnglishEducation, COUNT(*) AS Numbner_of_Customers, 
       COUNT(*) / ROUND(CAST(SUM(COUNT(*)) OVER() AS float),2)* 100 AS Percent_Contribution
FROM DimCustomer
GROUP BY EnglishEducation
GO
--HIghest number of customers have  some college or Bachelors 

Looking at the Commute Distance or locality for Customers
SELECT CommuteDistance, 
       COUNT(*) AS Numbner_of_Customers, COUNT(*) / ROUND(CAST(SUM(COUNT(*)) OVER() AS float),2)* 100 AS Percent_Contribution
FROM DimCustomer
GROUP BY CommuteDistance
GO
-- Majority of the customers(51%)  live within 2 miles


Number of average number of children per customer
SELECT AVG(TotalChildren)
FROM DimCustomer
GO 
--1 child on average per customer

Checking Marital status of customers
SELECT MaritalStatus, COUNT(*) AS Numbner_of_Customers, 
       COUNT(*) / ROUND(CAST(SUM(COUNT(*)) OVER() AS float),2)* 100 AS Percent_Contribution
FROM DimCustomer
GROUP BY MaritalStatus
GO
---54% of Customers are married.

SELECT HouseOwnerFlag, COUNT(*) AS Numbner_of_Customers, COUNT(*) / ROUND(CAST(SUM(COUNT(*)) OVER() AS float),2)* 100 AS Percent_Contribution
FROM DimCustomer
GROUP BY HouseOwnerFlag -- 67% of customers own a house

SELECT (SELECT COUNT(*)
               FROM DimCustomer 
	           WHERE NumberCarsOwned > 0) / ROUND(CAST(SUM(COUNT(*)) OVER() AS float),2)* 100 AS Percentage_owning_cars
FROM DimCustomer
GROUP BY NumberCarsOwned 
GO 
-- 77% of customers own a car

-- In Summary the best customer target market is middle class 2 parent families who live less than 2 miles from the store, have atleast 1 child and have some form of college education.They also own a house and a car

------------------------------------------------------------------------------------------------------------
DimProduct

DROP TABLE DimProduct;
Table is created by runnig the following query :

CREATE TABLE DimProduct (
    ProductKey INT PRIMARY KEY,
    ProductAlternateKey NVARCHAR(50),
    ProductSubcategoryKey NVARCHAR(10) DEFAULT NULL,
    WeightUnitMeasureCode NVARCHAR(10) DEFAULT NULL,
    SizeUnitMeasureCode NVARCHAR(10)   DEFAULT NULL,
    ProductName NVARCHAR(50),
    StandardCost NVARCHAR(20),
    FinishedGoodsFlag BIT,
    Color NVARCHAR(15),
    SafetyStockLevel INT,
    ReorderPoint INT,
    ListPrice NVARCHAR(20),
    Size NVARCHAR(50),
    SizeRange NVARCHAR(50),
    Weight NVARCHAR(20),
    DaysToManufacture INT,
    ProductLine NVARCHAR(50) DEFAULT NULL,
    DealerPrice NVARCHAR(20),
    Class NVARCHAR(50) DEFAULT NULL,
    Style NVARCHAR(50) DEFAULT NULL,
    ModelName NVARCHAR(50) DEFAULT NULL,
    StartDate DATETIME,
    EndDate NVARCHAR(50),
    Status NVARCHAR(50) DEFAULT NULL
);

Inserting Data from CSV

BULK INSERT DimProduct
FROM 'C:\Users\DAMARIS\Documents\PRODUCT.csv'
WITH (
  FORMAT = 'CSV',
  FIRSTROW = 2,
  FIELDTERMINATOR = ',',
  ROWTERMINATOR = '\n'
);
------------------------------------------------------------------------------------------------------------
DimProduct Table Exploratory Data Analysis:
------------------------------------------------------------------------------------------------------------
The following queries are used to perform Exploratory Data Analysis (EDA) on the DimProduct table:

Count the number of records:
SELECT COUNT(*) FROM DimProduct;

Check for duplicates:
SELECT COUNT(DISTINCT ProductKey) FROM DimProduct;

Convert some fields to float data types for aggregation and analysis. A backup of the original DimProduct table is created before making changes:

DROP TABLE DimProduct_Backup;

SELECT * INTO DimProduct_Backup FROM DimProduct;

Standard Cost
UPDATE DimProduct SET StandardCost = 0 WHERE StandardCost = 'NULL';
ALTER TABLE DimProduct ALTER COLUMN StandardCost FLOAT NOT NULL;

ListPrice
UPDATE DimProduct SET ListPrice = 0 WHERE ListPrice = 'NULL';
ALTER TABLE DimProduct ALTER COLUMN ListPrice FLOAT NOT NULL;

Weight 
UPDATE DimProduct SET Weight = 0 WHERE Weight = 'NULL';
ALTER TABLE DimProduct ALTER COLUMN Weight FLOAT NOT NULL;

DealerPrice
UPDATE DimProduct SET DealerPrice = 0 WHERE DealerPrice = 'NULL';
ALTER TABLE DimProduct ALTER COLUMN DealerPrice FLOAT NOT NULL;


Calculate the average standard cost of production:
SELECT AVG(StandardCost) 
FROM DimProduct WHERE StandardCost > 0;

Calculate the average standard cost of production for each product:
SELECT ProductName,AVG(StandardCost) AS Avg_Standard_Cost
FROM DimProduct
WHERE StandardCost > 0

Rank Products  by their list prices and obtain top 10 list prices
The result will include the product key, name, list price, and the ranking of the list price.

SELECT*
FROM (SELECT ProductKey, 
             ProductName, 
			 ListPrice, 
             DENSE_RANK () OVER (
ORDER BY  ListPrice DESC) Price_Ranking
FROM DimProduct) ListPrice_Ranking
WHERE Price_Ranking <= 10
GO --Hight product list price = 3578.27


Checking Profits by finished products
This SQL query checks the profitability of the products based on their list price, dealer price, and standard cost. It also filters the results to show only finished goods.

SELECT ProductName, 
       SUM(ListPrice - StandardCost) as List_Price_Profit, 
	   SUM(DealerPrice - StandardCost) as DealerPrice_Profit
FROM DimProduct
WHERE FinishedGoodsFlag  = 1
GROUP BY ProductName
ORDER BY SUM(ListPrice - StandardCost) DESC,  SUM(DealerPrice - StandardCost)  DESC
--List Price has more profitabity
------------------------------------------------------------------------------------------------------------
DimSalesTerritory 

DROP TABLE DimSalesTerritory;

CREATE TABLE DimSalesTerritory (
    SalesTerritoryKey INT PRIMARY KEY,
    SalesTerritoryAlternateKey INT,
    SalesTerritoryRegion VARCHAR(50),
    SalesTerritoryCountry VARCHAR(50),
    SalesTerritoryGroup VARCHAR(50)
);

-- Import data from csv file and bulk insert into the DimCustomer table
BULK INSERT DimSalesTerritory
FROM 'C:\Users\DAMARIS\Documents\SALESTERRITORY.csv'
WITH (
  FORMAT = 'CSV',
  FIRSTROW = 2,
  FIELDTERMINATOR = ',',
  ROWTERMINATOR = '\n'
);

-- Checking that the fields records are inserted in DimSalesTerritory table
SELECT *  
FROM DimSalesTerritory;

------------------------------------------------------------------------------------------------------------
DimSalesTerritory Exploratory Data Analysis
------------------------------------------------------------------------------------------------------------
This section contains SQL code for cleaning up the DimSalesTerritory table and performing exploratory data analysis.

Drop the last row since it has null or blank values

DELETE FROM DimSalesTerritory
WHERE SalesTerritoryKey = 11;

Select distinct values for SalesTerritoryRegion

SELECT DISTINCT SalesTerritoryRegion
FROM DimSalesTerritory;

Select count of distinct SalesTerritoryCountry values for each SalesTerritoryRegion

SELECT SalesTerritoryRegion, COUNT(DISTINCT SalesTerritoryCountry) AS 'Distinct Countries'
FROM DimSalesTerritory
GROUP BY SalesTerritoryRegion;

Select count of distinct SalesTerritoryGroup values for each SalesTerritoryRegion

SELECT SalesTerritoryRegion, COUNT(DISTINCT SalesTerritoryGroup) AS 'Distinct Groups'
FROM DimSalesTerritory
GROUP BY SalesTerritoryRegion;
------------------------------------------------------------------------------------------------------------
DimInvoices Table 

DROP TABLE DimInvoices;

-- Creating the Invoices table
CREATE TABLE DimInvoices(
  invoice_id VARCHAR(255) NOT NULL PRIMARY KEY,
  invoice_date DATE NOT NULL,
  product_type VARCHAR(20),
  is_recurring BIT  NULL,-- Refer to yes or no later
  invoice_status VARCHAR(10) NOT NULL,
  discount DECIMAL(10,2),
  discounted_amount_usd DECIMAL(10,0),
  amount_usd DECIMAL(10,0) NOT NULL
);
------------------------------------------------------------------------------------------------------------
DimInvoices Table  Exploratory Data Analyis
------------------------------------------------------------------------------------------------------------
Checking that the fields records are inserted in DimInvoices table

SELECT *
FROM DimInvoices
GO

Checking how many received in 2022 . The whole data is based on 2022 so no need to apply a where clause

SELECT COUNT (*)
FROM DimInvoices
GO

Analysing invoices by status(cancelled, invoiced, pending)

SELECT invoice_status,  COUNT(invoice_id) as Number_of_invoices, COUNT(*) /ROUND(CAST(SUM(COUNT(*)) OVER() AS float),2)* 100 AS Percent_Total
FROM DimInvoices
GROUP BY invoice_status
GO
------------------------------------------------------------------------------------------------------------
FactRessellerSales Table 

DROP TABLE FactResellerSales;

--Creating the resellers table

CREATE TABLE FactResellerSales (
	ProductKey INT, --FOREIGN KEY REFERENCES DimProduct(ProductKey)
	OrderDateKey INT,
	DueDateKey INT,
	ShipDateKey INT,
	ResellerKey INT,-- 
	EmployeeKey INT,--employee table missing
	PromotionKey INT,
	CurrencyKey INT,-- NO CURRENCY TABLE GIVEN, ASSUMPTION CURRENCY WILL 
	SalesTerritoryKey INT,  --FOREIGN KEY REFERENCES DimSalesTerritory(SalesTerritoryKey),
	SalesOrderNumber VARCHAR(20),
	SalesOrderLineNumber INT,
	RevisionNumber INT,
	OrderQuantity INT,
	UnitPrice DECIMAL(18,4),
	ExtendedAmount DECIMAL(18,4),
	UnitPriceDiscountPct DECIMAL(18,4),
	DiscountAmount DECIMAL(18,4),
	ProductStandardCost DECIMAL(18,4),
	TotalProductCost DECIMAL(18,4),
	SalesAmount DECIMAL(18,4),
	TaxAmt DECIMAL(18,4),
	Freight DECIMAL(18,4),
	CarrierTrackingNumber VARCHAR(20),
	CustomerPONumber VARCHAR(20),
	OrderDate DATE,
	DueDate DATE,
	ShipDate DATE
);
-- Import data from csv file and bulk insert into the FactReseller table
BULK INSERT FactResellerSales
FROM 'C:\Users\DAMARIS\Documents\RESELLERSALES.csv'
WITH (
  FORMAT = 'CSV',
  FIRSTROW = 2,
  FIELDTERMINATOR = ',',
  ROWTERMINATOR = '\n'
)
GO
------------------------------------------------------------------------------------------------------------
FactResellers Table  Exploratory Data Analyis
------------------------------------------------------------------------------------------------------------

Counting records in FactResellers table
SELECT count(*) as total
FROM FactResellerSales -- there are 60855 total records in this table
GO

Sales Profit made by  currencyKey
SELECT CurrencyKey, SUM(SalesAmount) AS Sales,SUM(TotalProductCost) AS Production_Cost, (SUM(SalesAmount) - SUM(TotalProductCost)) as Profit
FROM FactResellerSales
GROUP BY CurrencyKey
GO

Calculating the Reseller Performance based in the quantiy of goods ordered.

SELECT ResellerKey,  SUM(OrderQuantity) AS Orders
FROM FactResellerSales
GROUP BY ResellerKey
ORDER BY Orders DESC
GO

--Ranking Months  with by Orders, starting with the month with the high order quantities

SELECT  CONVERT(VARCHAR(7), OrderDate, 120) AS 'Month', SUM(OrderQuantity) AS Orders
FROM FactResellerSales
GROUP BY CONVERT(VARCHAR(7), OrderDate, 120)
ORDER BY Orders DESC;
------------------------------------------------------------------------------------------------------------
SQL QUESTIONS
------------------------------------------------------------------------------------------------------------

-----------------------------------------------------------------------------------------------------------
Question 1: What is the highest transaction of each month in 2012 for the product Sport-100 Helmet, Red?
------------------------------------------------------------------------------------------------------------

Select the 3 fields (MM-YYYY), SalesAmount and Order Date based on the query below
--Selects all sales amount records for the 'Sport-100 Helmet, Red' in 2012, splits the results amounts by    Year-Month 

SELECT 
    rankd_2012.[Month],
    rankd_2012.SalesAmount,
	rankd_2012.OrderDate
	FROM
    (
        SELECT 
            CONVERT(VARCHAR(7), f.OrderDate, 120) AS 'Month',      
            f.SalesAmount,f.OrderDate,
            RANK() OVER (
                PARTITION BY CONVERT(VARCHAR(7), f.OrderDate, 120) --Split by Month-Year
                ORDER BY SalesAmount DESC --rank from the highest sales amount 
            ) AS rank
        FROM 
            FactResellerSales f
        LEFT JOIN 
            DimProduct p ON f.ProductKey = p.ProductKey 
        WHERE 
            p.ProductName = 'Sport-100 Helmet, Red' AND YEAR(OrderDate) = 2012
    ) rankd_2012
WHERE 
    rankd_2012.rank = 1

GO



------------------------------------------------------------------------------------------------------------
--Question 2: Find all the products and their total sales amount by month. Only consider products that had at least one sale in 2012.
------------------------------------------------------------------------------------------------------------

Select product name from DimProduct table, SalesAmount, Month from the FactResellers table 
Obtain ProductName from the Dimproduct table and append on the FactResellerTable
Obtain the records  from the Year 2012 , but only where the a product had atleat 1 sale

SELECT  
       p.ProductName AS ProductName, 
	   ROUND(SUM(f.SalesAmount),2)AS SalesAmount, 
	   CONVERT(VARCHAR(7), f.OrderDate, 120) AS 'Month'
FROM FactResellerSales f

LEFT JOIN DimProduct p 
ON f.ProductKey = p.ProductKey
----------------------------------------------------------------------------------------------------------------------------------
WHERE YEAR(OrderDate) = '2012'
GROUP BY  p.ProductName, CONVERT(VARCHAR(7), f.OrderDate, 120)
HAVING COUNT(f.ProductKey) >= 1
GO



------------------------------------------------------------------------------------------------------------
Question 3: What are the age groups of customers categorised by marital status and gender? 
-----------------------------------------------------------------------------------------------------------

Create a back up  table to store original records

DROP TABLE DimCustomer_Backup
SELECT *
INTO DimCustomer_Backup
FROM DimCustomer
GO

In DimCustomer table Add a new column called Age , which calculate the  ages of customers 

ALTER TABLE DimCustomer
ADD Age INT DEFAULT 0;

Update the new table column Age based on the difference between between the birthdate and '2070-01-01'

UPDATE DimCustomer
SET Age = DATEDIFF("year",[BirthDate], '2070-01-01') + 
                        CASE
                           WHEN DATEADD("year", DATEDIFF("year", [BirthDate], '2070-01-01'), [BirthDate]) > '2070-01-01' THEN -1
                           ELSE 0
                        END

Check the updated Dimcustomer fields

SELECT * 
FROM DimCustomer
GO

Select MaritalStatus, Gender and categorise the ages using case when statement


SELECT MaritalStatus, Gender, 
       SUM(CASE WHEN Age < 35 THEN 1 ELSE  0 END) AS AgeBelow35,
	   SUM(CASE WHEN Age BETWEEN 35 AND 50  THEN 1 ELSE 0 END) AS AgeBetween35and50,
	   SUM(CASE WHEN Age > 50 THEN 1 ELSE 0 END)  AS AgeAbove50 

FROM DimCustomer
GROUP BY  MaritalStatus, Gender;

--All customers fall in the age 35+ years old at 2070-01-01




------------------------------------------------------------------------------------------------------------
 Question 4: What is the combination of product & country with the lowest amount of sales per month in 2022? 
------------------------------------------------------------------------------------------------------------
 Check for Sales in 2022 first in FactResellerSales

 SELECT Year(OrderDate), SUM(SalesAmount)
 FROM FactResellerSales
 --WHERE Year(OrderDate) = '2022'
 GROUP BY Year(OrderDate);

There are no sales records in 2022 only from 2010 to 2012

If there were sales in 2022 . I would use a CTE instead of a nested subquery for efficiency and readbility

WITH cte_sales AS (
    SELECT  
	    CONVERT(VARCHAR(7), f.OrderDate, 120) AS 'Month',
        p.ProductName AS ProductName, 
        s.SalesTerritoryCountry,
        ROUND(SUM(f.SalesAmount),2) AS SalesAmount
    FROM FactResellerSales f
    LEFT JOIN DimProduct p
        ON f.ProductKey = p.ProductKey
    LEFT JOIN DimSalesTerritory s 
        ON f.SalesTerritoryKey = s.SalesTerritoryKey
	WHERE YEAR(f.OrderDate) = 2022

    GROUP BY
       CONVERT(VARCHAR(7), f.OrderDate, 120),
	   p.ProductName, 
        s.SalesTerritoryCountry
) 
SELECT 
    Month,
    ProductName, 
    SalesTerritoryCountry, 
    SalesAmount
FROM cte_sales
WHERE SalesAmount = (SELECT MIN(SalesAmount) FROM cte_sales)
ORDER BY SalesAmount

CTE : cte_sale  calculates the total sales amount for each combination of product and country in the year 2022.
The CTE joins the FactResellerSales, DimProduct, and DimSalesTerritory tables and filters the results for the year 2012 using a WHERE clause. 
The results are then grouped by product name and sales territory country. The sales amount for each group is calculated by summing the SalesAmount column and rounding the result to 2 decimal places.


----------------------------------------------------------------------------------------------------------
Question 5: What is 2022 monthly basis Annual Recurring Revenue (ARR) of products named EOR Monthly and EOR Yearly? 
------------------------------------------------------------------------------------------------------------
SELECT *
FROM DimInvoices
GO



Select  invoice month , and define  MRR_usd, and Monthly_ARR_usd  using case statement.
Narrow down to the 2 products : EOR Monthly and EOR Yearly,
Aggregate results by date

	 SELECT 
    CONVERT(VARCHAR(7), invoice_date, 120) AS 'InvoiceMonth',
  
    SUM(CASE 
        WHEN is_recurring = '1' AND invoice_status = 'invoiced' 
        THEN amount_usd 
        ELSE 0 
    END) AS MRR_usd,
   (12*
    SUM(CASE 
        WHEN is_recurring = '1' AND invoice_status = 'invoiced' 
        THEN amount_usd 
        ELSE 0 
		END)) AS Monthly_ARR_usd
    
FROM 
    DimInvoices
WHERE product_type IN('EOR Monthly', 'EOR Yearly')
GROUP BY 
    CONVERT(VARCHAR(7), invoice_date, 120)
------------------------------------------------------------------------------------------------------------