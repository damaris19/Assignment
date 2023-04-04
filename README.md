# Assignment
To show case the Senior Data Analyst Project Assignment

## Database Connection

### I used a Microsoft SQL Server Management Database. Connection Steps as follows:

+ Open Microsoft SQL Server Management Studio (SSMS), 
+ Connect to your SQL Server instance(I am using a local instance ),
+ Create New Query 
+ Save Query as "Exploratory Analysis with SQL.sql".
+ Create the finance Database as per the query below:

DROP DATABASE finance;

CREATE DATABASE Finance;

USE Finance;

## Exploratory Analysis with SQL
-- Creating the finance database
DROP DATABASE finance;

CREATE DATABASE Finance;

USE Finance;
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
-- Creating Tables 
-- Create the DimCustomer table 
DROP TABLE DimCustomer;

CREATE TABLE DimCustomer (
    CustomerKey INT PRIMARY KEY,
    GeographyKey INT,
    CustomerAlternateKey NVARCHAR(15),
    Title NVARCHAR(8),
    FirstName NVARCHAR(50),
    MiddleName NVARCHAR(50),
    LastName NVARCHAR(50),
    NameStyle BIT,
    BirthDate DATE,
    MaritalStatus NVARCHAR(1),
    Suffix NVARCHAR(10),
    Gender NVARCHAR(1),
    EmailAddress NVARCHAR(50),
    YearlyIncome INT,
    TotalChildren INT,
    NumberChildrenAtHome INT,
    EnglishEducation NVARCHAR(40),
    SpanishEducation NVARCHAR(40),
    FrenchEducation NVARCHAR(40),
    EnglishOccupation NVARCHAR(100),
    SpanishOccupation NVARCHAR(100),
    FrenchOccupation NVARCHAR(100),
    HouseOwnerFlag BIT,
    NumberCarsOwned INT,
    AddressLine1 NVARCHAR(60),
    AddressLine2 NVARCHAR(60),
    Phone NVARCHAR(25),
    DateFirstPurchase DATE,
    CommuteDistance NVARCHAR(15)
	);

####Data Cleanup before import : 
+ Convert Birthdate and DateFirstPurchase fields into YYYY/MM/DD format. Both fields had date and number data types before conversion

####Import data from csv file and bulk insert into the DimCustomer table
+ BULK INSERT DimCustomer
+ FROM 'C:\Users\DAMARIS\Documents\CUST.csv'
+ WITH (
  + FORMAT = 'CSV',
  + FIRSTROW = 2,
  + FIELDTERMINATOR = ',',
  + ROWTERMINATOR = '\n'
+ )
+ GO
-- Checking that the field records are inserted in DimCustomer table
SELECT * 
  FROM DimCustomer 
GO
--EXPLORATORY DATA ANALYSIS ON THE CUSTOMERS

--Checking total count
SELECT COUNT(*)
  FROM DimCustomer
GO -- there are  18484 records
--Check for duplicates in the customer table
SELECT COUNT(distinct DimCustomer.CustomerKey) -- total of 18484 unique customer ids, hence no duplicates in this table
    FROM DimCustomer
GO
---Looking for customer country using the geography key
--SELECT 
--  Geographykey, 
--  CAST(Geographykey AS geography).STAsText() AS location_name 
--FROM DimCustomer;

--Looking at Average Yearly income 
SELECT AVG(YearlyIncome)
FROM DimCustomer
GO ---Average customer earns  $57305

--Checking customers earnings by gender
SELECT Gender, SUM(YearlyIncome) -- Men earn more than women overall but other factor need to be checked. I.e more men than women? Avg income by gender
FROM DimCustomer
GROUP BY Gender
ORDER BY SUM(YearlyIncome) ASC
GO

--Checking customers  split by gender to check if there are more men that women
SELECT Gender, COUNT(CustomerKey) 
FROM DimCustomer
GROUP BY Gender
GO

--Checking % of Male customers
SELECT 
(COUNT(CASE WHEN Gender ='M' THEN 1 END)/CAST(COUNT(*) AS float)*100.00) AS Percentage_Male_Customers -- Male customers make 50.58% of total customers
FROM DimCustomer
GO

-- Checking Average income by Gender 
SELECT Gender, AVG(YearlyIncome)
FROM DimCustomer
GROUP BY Gender
GO -- Avg yearly income is higher for female customers @ 57369, and Male @57243

--Customers  distribution by education level
SELECT EnglishEducation, COUNT(*) AS Numbner_of_Customers, COUNT(*) / ROUND(CAST(SUM(COUNT(*)) OVER() AS float),2)* 100 AS Percent_Contribution
FROM DimCustomer
GROUP BY EnglishEducation
GO-- HIghest number of customers have  some college or Bachelors 

--Looking at the Commute Distance or locality for Customers
SELECT CommuteDistance, COUNT(*) AS Numbner_of_Customers, COUNT(*) / ROUND(CAST(SUM(COUNT(*)) OVER() AS float),2)* 100 AS Percent_Contribution
FROM DimCustomer
GROUP BY CommuteDistance
GO-- Majority of the customers(51%)  live within 2 miles
-- Number of average number of children per customer
SELECT AVG(TotalChildren)
FROM DimCustomer-- 1 child on average per customer
GO 
--Checking Marital status of customers
SELECT MaritalStatus, COUNT(*) AS Numbner_of_Customers, COUNT(*) / ROUND(CAST(SUM(COUNT(*)) OVER() AS float),2)* 100 AS Percent_Contribution
FROM DimCustomer
GROUP BY MaritalStatus---54% of Customers are married.

SELECT HouseOwnerFlag, COUNT(*) AS Numbner_of_Customers, COUNT(*) / ROUND(CAST(SUM(COUNT(*)) OVER() AS float),2)* 100 AS Percent_Contribution
FROM DimCustomer
GROUP BY HouseOwnerFlag -- 67% of customers own a house

SELECT (SELECT COUNT(*)
               FROM DimCustomer 
	           WHERE NumberCarsOwned > 0) / ROUND(CAST(SUM(COUNT(*)) OVER() AS float),2)* 100 AS Percentage_owning_cars
FROM DimCustomer
GROUP BY NumberCarsOwned -- 77% of customers own a car

-- In Summary the best customer target market is middle class 2 parent families who live less than 2 miles from the store, have atleast 1 child and have some form of college education.They also own a house and a car
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
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

-- Import data from csv file and bulk insert into the Dim Product table
BULK INSERT DimProduct
FROM 'C:\Users\DAMARIS\Documents\PRODUCT.csv'
WITH (
  FORMAT = 'CSV',
  FIRSTROW = 2,
  FIELDTERMINATOR = ',',
  ROWTERMINATOR = '\n'
)
GO
-- Checking that the field records are inserted in DimProduct table
SELECT* 
 FROM  DimProduct
GO
---------------------------------------------------------------------------------------
--EXPLORATORY DATA ANALYSIS ON THE PRODUCTS

SELECT COUNT(*)
 FROM  DimProduct
GO -- No of records =569 

--Checking for duplicates
SELECT COUNT(DISTINCT ProductKey)
 FROM  DimProduct
GO 
-- Product unit codes
SELECT WeightUnitMeasureCode, COUNT(ProductKey) AS Product_Count
FROM DimProduct
GROUP BY WeightUnitMeasureCode
GO
-- Converting quantity tables from VARCHAR to Float DATA TYPES for aggregation and anaysis
-- Create a backup of the original DimProduct table to retain original table values and data types.
DROP TABLE DimProduct_Backup;
SELECT *
INTO DimProduct_Backup
FROM DimProduct
GO
--Check that the back up table has the inserted fields 
SELECT * 
FROM DimProduct_Backup 
GO

SELECT* 
 FROM  DimProduct
GO
--EDA dealing with null values and changing varchat data types 
---Changing some fields to allow for aggregation and calculation the fields 

--Standard Cost --   
UPDATE DimProduct SET StandardCost = 0 WHERE StandardCost = 'NULL'; --Replacing Null nvarchar values with 0 first to allow for data type conversion,to avoid errors .
ALTER TABLE DimProduct ALTER COLUMN StandardCost FLOAT NOT NULL; -- Converting nvachar data type to float for aggregation and calculation 
--ALTER TABLE DimProduct ADD CONSTRAINT DF_DimProduct_StandardCost DEFAULT 0 FOR StandardCost; -- Adding the default constraint of 0 
 
--ListPrice
UPDATE DimProduct SET ListPrice = 0 WHERE ListPrice = 'NULL';
ALTER TABLE DimProduct ALTER COLUMN ListPrice FLOAT NOT NULL;
--ALTER TABLE DimProduct ADD CONSTRAINT DF_DimProduct_ListPrice DEFAULT 0 FOR ListPrice;

--Weight
UPDATE DimProduct SET Weight = 0 WHERE Weight = 'NULL';
ALTER TABLE DimProduct ALTER COLUMN Weight FLOAT NOT NULL;
--ALTER TABLE DimProduct ADD CONSTRAINT DF_DimProduct_Weight DEFAULT 0 FOR Weight;

--DealerPrice
UPDATE DimProduct SET DealerPrice = 0 WHERE DealerPrice = 'NULL';
ALTER TABLE DimProduct ALTER COLUMN DealerPrice FLOAT NOT NULL;
--ALTER TABLE DimProduct ADD CONSTRAINT DF_DimProduct_DealerPrice DEFAULT 0 FOR DealerPrice;

--
SELECT AVG(StandardCost)
FROM DimProduct
WHERE StandardCost > 0 -- exclude values where  standard cost = 0
GO -- avg cost of production is 395.96
--Aver
SELECT ProductName,AVG(StandardCost) AS Avg_Standard_Cost
FROM DimProduct
WHERE StandardCost > 0 -- exclude values where  standard cost 
GROUP BY ProductName
ORDER BY AVG(StandardCost) DESC
GO
-- Product Ranking by their list prices top 10 list prices
SELECT*
FROM (SELECT ProductKey, 
             ProductName, 
			 ListPrice, 
             DENSE_RANK () OVER (
ORDER BY  ListPrice DESC) Price_Ranking
FROM DimProduct) ListPrice_Ranking
WHERE Price_Ranking <= 10
GO --Hight product list price = 3578.27
--Checking Profits by products 
SELECT ProductName, 
       SUM(ListPrice - StandardCost) as List_Price_Profit, 
	   SUM(DealerPrice - StandardCost) as DealerPrice_Profit
FROM DimProduct
WHERE FinishedGoodsFlag  = 1
GROUP BY ProductName
ORDER BY SUM(ListPrice - StandardCost) DESC,  SUM(DealerPrice - StandardCost)  DESC
--List Price has more profitabity

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--Creating The DimSalesTerritory table
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
)
GO
-- Checking that the fields records are inserted in DimSalesTerritory table
SELECT *  
FROM DimSalesTerritory
GO

-----------------------------------------------------------
--EXPLORATORY DATA ANALYSIS SALES TERITORIES
--Clean up 
-- Drop the last row since it has null or blank values
DELETE FROM DimSalesTerritory
WHERE SalesTerritoryKey = 11
GO

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
DROP Table DimInvoices;
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
-- Convert invoice date into yyyy/mm/dd date type format
-- Convert discount to decimal 
-- Convert Is_recurring column  into boolean . Can be changed later to Yes or No
-- Import data from csv file and bulk insert into the DimInvoices table
BULK INSERT DimInvoices
FROM 'C:\Users\DAMARIS\Documents\INVOICES.csv'
WITH (
  FORMAT = 'CSV',
  FIRSTROW = 2,
  FIELDTERMINATOR = ',',
  ROWTERMINATOR = '\n'
)
GO
-- Checking that the fields records are inserted in DimInvoices table
SELECT *
FROM DimInvoices
GO
-----------------------------------------------------------------------------------------------------------------------------------------------------
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
-- Checking that the fields records are inserted in DimInvoices table

SELECT *
FROM FactResellerSales
GO
----------------------------------------------------------------------------------------------------------------------
--EXPLORATORY DATA ANALYSIS ON THE RESELLERSSALES TABLE
---Counting records in FactResellers table-- 
SELECT count(*) as total
FROM FactResellerSales -- there are 60855 total records in this table
GO


Select  count(distinct ProductKey), count(distinct OrderDateKey), count(distinct DueDateKey),count(distinct ShipDateKey),count(distinct ResellerKey), 
        count(distinct EmployeeKey),count(distinct PromotionKey),count(distinct CurrencyKey),count(distinct SalesTerritoryKey),count(distinct SalesOrderNumber),
		count(distinct SalesOrderLineNumber),count(distinct RevisionNumber),count(distinct CarrierTrackingNumber),count(distinct CustomerPONumber),
		count(distinct OrderDate),count(distinct DueDate),count(distinct ShipDate)
from FactResellerSales
GO  -- No primary  key detected.

-- Inserting Adding new field name FactResellerID and making it a primary   key in the FactResellers table
ALTER TABLE FactResellerSales
ADD FactResellerID INT IDENTITY(1,1) NOT NULL
GO

ALTER TABLE FactResellerSales
ADD CONSTRAINT PK_FactResellerSales PRIMARY KEY(FactResellerID)

## SQL Questions
