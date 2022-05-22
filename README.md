# Shopify Data Science Challenge Fall 2022

### Question 1
Q1.a)<br>
At first glance, I imagine that some high-end outliers in our dataset are skewing the AOV.<br>
Q1.b)<br>
In that case the mean should work a lot better for us in approximating AOV.<br>
Q1.c)<br>
The mean order amount is 284.00<br>
The detailed reasoning behind my answers to question 1 consists can be found in a [Jupyter notebook](question1.ipynb) with a series of annotations. The provided dataset for this question is uploaded and renamed here as `data_sneakers.csv`. Please run the notebook and read the annotations to get the answers.

### Question 2
The answers and queries for question 2 are in the text file [here](question2.txt) and are listed below Queries can be tested at at [this link](https://www.w3schools.com/SQL/TRYSQL.ASP?FILENAME=TRYSQL_SELECT_ALL). I have made the assumption that whatever SQL engine I'm using optimizes better with JOIN rather than sub-queries. With that assumption, I've opted to minimize sub-queries except for at the end for selecting a final answer.

#### Q1.a
**How many orders were shipped by Speedy Express in total?**
```MySQL
SELECT Count(*) as SpeedyExpress_OrderCount
FROM   Shippers as shp
       INNER JOIN Orders O
               ON shp.ShipperID = O.ShipperID
WHERE  shp.ShipperName = "Speedy Express"
```
| **SpeedyExpress_OrderCount**  |
|---|
|  54 |

#### Q1.b
**What is the last name of the employee with the most orders?**
```MySQL
SELECT EmployeeOfTheMonth
FROM   (SELECT LastName as EmployeeOfTheMonth, Max(count) as TopNumberSales
        FROM   (SELECT Employees.LastName, Count(Orders.EmployeeID) as count
                FROM   Orders
                       JOIN Employees
                         ON Employees.EmployeeID = Orders.EmployeeID
                GROUP  BY Orders.EmployeeID))  
```
| **EmployeeOfTheMonth**  |
|---|
|  Peacock |

#### Q1.c
**What product was ordered the most by customers in Germany?**<br>
NOTE: I'm assuming this is asking for total quantity of product ordered, not total number of orders placed for a product.
i.e: If there was 1 order of 100 product A, and 10 orders of 1 product B; the query should ouput product A. 
```MySQL
SELECT ProductName as GermanysFavouriteProduct
FROM   (SELECT ProductName, Max(total_quant) AS TotalProductQuantity
        FROM   (SELECT Products.ProductName, Sum(Quantity) AS total_quant
                FROM   Products
                       INNER JOIN Customers
                               ON Customers.CustomerID = Orders.CustomerID
                       INNER JOIN Orders
                               ON OrderDetails.OrderID = Orders.OrderID
                       INNER JOIN OrderDetails
                               ON Products.ProductID = OrderDetails.ProductID
                WHERE  Customers.country = "Germany"
                GROUP  BY Products.ProductID)) 
```
| **GermanysFavouriteProduct**  |
|---|
|  Boston Crab Meat |
