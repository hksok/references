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
