# SQL

- Structured Query Language (SQL) is a standard computer language for relational database management and data manipulation.
- Used to query, insert, update and modify data.
- read, write, and update data.

### The SELECT statement

```sql
SELECT prod_name, prod_id, prod_price
FROM Products;
```

request all columns by using the asterisk (*)  
wild card character instead of column names

```sql
SELECT *
FROM Products;
```

```sql
SELECT prod_name
FROM Products
LIMIT 5;
```

### Creating tables

```sql
CREATE TABLE Shoes
(
Id    char(10)     PRIMARY KEY,
Brand char(10)     NOT NULL,
Type  char(250)    NOT NULL,
Color char(250)    NOT NULL,
Price decimal(8,2) NOT NULL,
Desc  Varchar(750) NULL
);
```

```sql
INSERT INTO Shoes
VALUES ('14535974',
        'Gucci',
        'Slippers',
        'Pink',
        '695.00',
        NULL);
```

```sql
INSERT INTO Shoes
       (Id,
        Brand,
        Type,
        Color,
        Price,
        Desc
       )
VALUES ('14535974',
        'Gucci',
        'Slippers',
        'Pink',
        '695.00',
        NULL);
```

### Creating temporary tables

- temporary tables will be deleted when current session is terminated
- faster than creating a real table
- useful for complex queries using subsets and joins

```sql
CREATE TEMPORARY TABLE Sandals AS
(
    SELECT *
    FROM shoes
    WHERE shoe_type = 'sandals'
)
```

### Adding comments to SQL

- single line

```sql
SELECT shoe_id
--,brand_id
,shoe_name
from shoes
```

- section

```sql
SELECT shoe_id
/*,brand_id
,shoe_name
*/
from shoes
```

### filtering

```sql
SELECT column_name, column_name
FROM table_name
WHERE column_name operator value;
```

Operator | Description
--- | ---
= | Equal
<> | Not equal. Note: In some versions of SQL this operator may be written as !=
\> | Greater than
< | Less than
\>= | Greater than or equal
<= | Less than or equal
BETWEEN | Between an inclusive range
IS NULL | is a null value

```sql
SELECT ProductName,
UnitPrice,
SupplierID
FROM Products
WHERE ProductName = 'Tofu';
```

```sql
SELECT ProductName,
UnitPrice,
SupplierID,
UnitsInStock
FROM Products
WHERE UnitsInStock BETWEEN 15 AND 80;
```

```sql
SELECT ProductName,
UnitPrice,
SupplierID,
UnitsInStock
FROM Products
WHERE ProductName IS NULL;
```

### Advanced filtering : IN, OR, and NOT

```sql
SELECT ProductID,
UnitPrice,
SupplierID
FROM Products
WHERE SupplierID IN (9, 10, 11);
```

```sql
SELECT ProductName,
ProductID,
UnitPrice,
SupplierID
FROM Products
WHERE ProductName = 'Tofu' OR 'Konbu';
```

IN works the same as OR

Benefits of IN:

- Long list of options
- IN executes faster than OR
- Don't have to think about the order with IN
- Can contain another SELECT

```sql
SELECT
ProductID,
UnitPrice,
SupplierID
FROM Products
WHERE SupplierID = 9 OR SupplierID = 11
AND UnitPrice > 15;
```

Order Of Operations

- can contain AND and OR Operators
- SQL processes AND before OR
- Use ()

```sql
SELECT *
FROM Employees
WHERE NOT City='London' AND NOT City='Seattle';
```

### Using wildcards in SQL

- Special character used to match parts of a value
- Search pattern made from literal text, wildcard character, or a combination
- Uses LIKE as an operator (though technically a predicate)
- can only be used for strings
- cannot be used for non-text datatypes
- helpful for data scientists as they explore string variables

Wildcard | Action
--- | ---
'%Pizza' | Grabs anything ending with the word pizza
'Pizza%' | Grabs anything after the word pizza
'%Pizza%' | Grabs anything before and after the word pizza

### Sorting with ORDER BY

```sql
SELECT something
FROM database
ORDER BY characteristic
```

rules for ORDER BY

- takes the name of one or more columns
- add a comma after each additional column name
- can sort by a column not retrieved
- must always be the last clause in a select statement

Sort direction

- DESC descending order
- ASC ascending order
- Only applies to the column names it directly precedes

### Math Operations

- perform basic math calculations using your data
- discuss the order of operations
- describe analysis possibilities of using math operators and SQL together

Operator | Description
--- | ---
\+ | Addition
\- | Subtraction
\* | Multiplication
/ | Division

```sql
SELECT 
ProductID,
UnitsOnOrder,
UnitPrice,
UnitsOnOrder * UnitPrice AS Total_Order_Cost
FROM Products
```

```sql
SELECT 
ProductID,
Quantity,
UnitPrice,
Discount,
(UnitPrice - Discount)*Quantity AS Total_Cost
FROM Products
```

### Aggregate Functions

- AVERAGE
> - rows containing NULL values are ignored by the AVERAGE function
- COUNT
> - COUNT(*) - Counts all the rows in a table containing values or NULL values
> - COUNT(column) - Counts all the rows in a specific column ignoring NULL values
- MIN
> - NULL values are ignored by the MIN function
- MAX
> - NULL values are ignored by the MAX function
- SUM
- DISTINCT

```sql
SELECT AVG(UnitPrice) AS avg_price
FROM products
```

```sql
SELECT SUM(UnitPrice*UnitsInStock) AS total_price
FROM Products
WHERE SupplierID = 23;
```

```sql
SELECT COUNT(DISTINCT CustomerID)
FROM Customers
```

### Grouping Data with SQL

- Perform some additional aggregations using the GROUP BY and HAVING clauses
- Discuss how NULLs are or aren't affected by the GROUP BY and HAVING clauses
- Use the GROUP BY and ORDER BY clauses together to better sort your data
- GROUP BY clauses can contain multiple columns
- Every column in your SELECT statement must be present in a GROUP BY clause, except for aggregated calculations
- NULLs will be grouped together if your GROUP BY column contains NULLs

```sql
SELECT 
Region,
COUNT(CustomerID) AS total_customers
FROM Customers
GROUP BY Region;
```

```sql
SELECT
CustomerID,
COUNT(*) AS orders
FROM Orders
GROUP BY CustomerID
HAVING COUNT(*) >= 2;
```

WHERE filters before data is grouped
HAVING filters after data is grouped
Rows eliminated by the WHERE clause will not be included in the group



