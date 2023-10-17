# SQL-project---Northwind-data
This is a version of the Microsoft Access 2000 Northwind sample database, re-engineered for MySQL.

The Northwind sample database was provided with Microsoft Access as a tutorial schema for managing small business customers, orders, inventory, purchasing, suppliers, shipping, and employees. Northwind is an excellent tutorial schema for a small-business ERP, with customers, orders, inventory, purchasing, suppliers, shipping, employees, and single-entry accounting.

All the TABLES and VIEWS from the MSSQL-2000 version have been converted to MySQL and included here.

-- ----BEGINNER QUESTIONS---

use northwind_db;

-- 1.Which shippers do we have?;

select
*
from
shippers;

-- 2.Certain fields from Categories;

select
category_name,
description
from
categories;

-- 3. Now we’d like to see the same columns as above, but only for those employees that both have the title of Sales Representative, and also are in the United States.-- 

select
first_name,
last_name,
hire_date
from employees
where title = "Sales Representative";

-- 4. Now we’d like to see the same columns as above, but only for those
-- employees that both have the title of Sales Representative, and also are
-- in the United States.;

select
first_name,
last_name,
hire_date
from employees
where title = "Sales Representative" and country = "USA" ;

-- 5.Show all the orders placed by a specific employee. The EmployeeID for
-- this Employee (Steven Buchanan) is 5 ;

select
* from
orders
where employee_id = 5 ;

-- 6 In the Suppliers table, show the SupplierID, ContactName, and
-- ContactTitle for those Suppliers whose ContactTitle is not Marketing
-- Manager;

select
supplier_id,
contact_name,
contact_title
from suppliers
where contact_title != "Marketing Manager" ;

-- --or;

select
supplier_id,
contact_name,
contact_title
from suppliers
where contact_title <> "Marketing Manager" ;

-- 7. Products with “queso” in ProductName ;

select
product_id,
product_name
from
products
where product_name like "%queso%" ;

-- 8. Looking at the Orders table, there’s a field called ShipCountry. Write a
-- query that shows the OrderID, CustomerID, and ShipCountry for the
-- orders where the ShipCountry is either France or Belgium.;

select
order_id,
customer_id,
ship_country
from orders
where 
ship_country in ("France","Belgium") ;

-- --or;

select
order_id,
customer_id,
ship_country
from orders
where 
ship_country = "France" or "Belgium";

-- 9 .Orders shipping to any country in Latin America;

select
order_id,
customer_id,
ship_country
from orders
where 
ship_country in ("Brazil","Mexico", "Venezeula" , "Argentina") ;

-- 10 Employees, in order of age & date_of_birth ;

select
first_name,
last_name,
title,
date(birth_date) as date_of_birth
from
employees
order by date_of_birth desc  ;

-- 11 Employees full name - Show the FirstName and LastName columns from the Employees table,and then create a new column called FullName, showing FirstName and 
-- LastName joined together in one column, with a space in-between.;

select
first_name,
last_name,
concat(first_name," ",last_name) as full_name
from
employees ;

-- 12 OrderDetails amount per line item - In the OrderDetails table, we have the fields UnitPrice and Quantity.
-- Create a new field, TotalPrice, that multiplies these two together;

select
order_id,
product_id,
unit_price,
quantity,
(unit_price*quantity) as total_price
from order_details 
order by order_id,product_id ;

-- 13 How many customers? ;

select
count(customer_id)
from 
customers;

-- 14 When was the first order? ;

select
min(order_date) as first_order
from
orders;

-- 15 Countries where there are customers;

select
country
from
customers
group by country;


-- 16 Contact titles for customers - Show a list of all the different values in the Customers table for
-- ContactTitles. Also include a count for each ContactTitle.;

select
contact_title,
count(contact_title) as No_of_contact_titles
from customers
group by contact_title ;

-- 17 Products with associated supplier names;

select
product_id,
product_name,
company_name
from products
left join suppliers
on products.supplier_id = suppliers.supplier_id ;

-- 18 Orders and the Shipper that was used - We’d like to show a list of the Orders that were made, including the
-- Shipper that was used. Show the OrderID, OrderDate (date only), and
-- CompanyName of the Shipper, and sort by OrderID ;

select
order_id,
date(order_date),
company_name
from orders
left join shippers
on orders.ship_via = shippers.shipper_id 
 where order_id < 10300 
 order by order_id;
 
 -- ----INTERMEDIATE QUESTIONS----- 

use northwind_db ;

-- 19.Categories, and the total products in each category - Sort the results by the total number of products, in descendingorder;

select
A.category_name,
count(B.product_id) as total_products
from categories as A
left join products as B
on A.category_id = B.category_id 
group by category_name
order by total_products desc ;

-- --20. Total customers per country/city ;

select
country,
city,
count(customer_id) as numberofcustomers
from customers
group by country,city 
order by numberofcustomers desc ;


-- 21 . Products that need reordering- For now, just use the fields UnitsInStock and ReorderLevel, where UnitsInStock is less than the ReorderLevel, ignoring the fields
-- UnitsOnOrder and Discontinued.Order the results by ProductID.;

select
product_id,
product_name,
units_in_stock,
reorder_level
from products
where units_in_stock < reorder_level
order by product_id ;

-- 22, Now we need to incorporate these fields—UnitsInStock, UnitsOnOrder,ReorderLevel, Discontinued—into our calculation. We’ll define
-- “products that need reordering” with the following:
-- UnitsInStock plus UnitsOnOrder are less than or equal to ReorderLevel &
-- The Discontinued flag is false (0).;

select
product_id,
product_name,
units_in_stock,
units_on_order,
reorder_level,
discontinued
from products
where units_in_stock + units_on_order <= reorder_level and discontinued = 0 ;

-- 23.Customer list by region - A salesperson for Northwind is going on a business trip to visit customers, and would like to see a list of all customers, sorted by
-- region, alphabetically.
-- However, he wants the customers with no region (null in the Region field) to be at the end, instead of at the top, where you’d normally find
-- the null values. Within the same region, companies should be sorted by
-- CustomerID ;

select
customer_id,
company_name,
region
from customers 
order by 
case 
when region is null then 1
else 0
end,
region,customer_id ;

-- --or

select
customer_id,
company_name,
region,
case when region is null then 1
else 0
end as region_1
from customers 
order by 
region_1,
region,customer_id ;

-- 24.High freight charges = Return the three ship countries with the highest average freight overall, in descending order by average freight.;

select 
ship_country,
avg(freight)
from orders
group by ship_country
order by avg(freight) desc 
limit 3;

-- -25 High freight charges - 1997 ;

select 
ship_country,
avg(freight)
from orders
where year(order_date)= 1997
group by ship_country
order by avg(freight) desc ;

-- 26--High freight charges - last year - We now want to get the three ship countries with the highest average freight charges. But
-- instead of filtering for a particular year, we want to use the last 12
-- months of order data ;

select 
ship_country,
avg(freight)
from orders
where order_date >= date_sub((select max(order_date)from orders),interval 12 month)
group by ship_country
order by avg(freight) desc
limit 3 ;

-- 27.Inventory list ;

select
A.employee_id,
A.last_name,
B.order_id,
C.product_name,
D.quantity
from employees as A
join orders as B
on A.employee_id = B.employee_id
join order_details as D
on B.order_id = D.order_id
join products as C
on D.product_id = C.product_id 
order by order_id , D.product_id ;

-- 28.Customers with no orders;

select
customers.customer_id,
orders.order_id
from customers
left join orders
on customers.customer_id = orders.customer_id
where order_id is null ;

-- 29.Customers with no orders for EmployeeID 4;

select
customers.customer_id,
orders.order_id
from customers
left join orders
on customers.customer_id = orders.customer_id
and employee_id = 4
where order_id is null  ;

-- ADVANCED QUESTIONS----

use northwind_db;

--  30 High-value customers -We're defining high-value customers as those who've made at least 1 order with a total value (not including the discount) equal to $10,000 or
-- more. We only want to consider orders made in the year 1998;

select
customers.customer_id,
customers.company_name,
orders.order_id,
sum(unit_price*quantity) as total_amount
from customers
left join orders
on customers.customer_id = orders.customer_id
left join order_details
on orders.order_id = order_details.order_id 
where  year(order_date) = 1998
group by customers.customer_id,
customers.company_name,
orders.order_id
having sum(unit_price*quantity) >= 10000  ;

----- 31.High-value customers - total orders- Instead of requiring that customers have at least one individual orders totaling $10,000 or more, he wants to
-- define high-value customers as those who have orders totaling $15,000 or more in 1998. How would you change the answer to the problem above?;

select
customers.customer_id,
customers.company_name,
orders.order_id,
sum(unit_price*quantity) as total_amount
from customers
left join orders
on customers.customer_id = orders.customer_id
left join order_details
on orders.order_id = order_details.order_id 
where  year(order_date) = 1998
group by customers.customer_id,
customers.company_name,
orders.order_id
having sum(unit_price*quantity) >= 15000  ;

-- ---32. High-value customers - with discount - Change the above query to use the discount when calculating high-value customers. 
-- Order by the total amount which includes the discount;

select
customers.customer_id,
customers.company_name,
orders.order_id,
sum(unit_price*quantity* (1-discount)) as total_amount
from customers
left join orders
on customers.customer_id = orders.customer_id
left join order_details
on orders.order_id = order_details.order_id 
where  year(order_date) = 1998
group by customers.customer_id,
customers.company_name,
orders.order_id
having total_amount >= 15000 
order by total_amount desc;

-- ---33.Month-end orders -At the end of the month, salespeople are likely to try much harder to get orders, to meet their month-end quotas. Show all orders made on the last
-- day of the month. Order by EmployeeID and OrderID;

select
employee_id,
order_id,
order_date
from orders
where order_date = last_day(order_date)
order by employee_id , order_id ;
----

-- 34.Orders with many line items ;

select
orders.order_id,
count(order_details.order_id)
from orders 
join order_details 
on orders.order_id =order_details.order_id
group by orders.order_id
order by count(order_details.order_id) desc
limit 10;

-- --or;

select
A.order_id,
count(B.order_id)
from orders as A
join order_details as B
on A.order_id =B.order_id
group by A.order_id
order by count(B.order_id) desc
limit 10;
-----
-- 35. Orders - accidental double-entry - one of the salespeople,e thinks that she accidentally double-entered a line item on an order, 
-- with a different ProductID, but the same quantity. She remembers that the quantity was 60 or more. Show all the OrderIDs with line items that
-- match this, in order of OrderID.;

select
order_details.order_id,
quantity
from order_details
where quantity >= 60 
group by order_details.order_id,quantity
having count(*)>1 ; 

-- ---36 Orders - accidental double-entry details - Based on the previous question, we now want to show details of the
-- order, for orders that match the above criteria.;

with cte as (
select
* from order_details 
)
select 
order_details.order_id,
quantity
from order_details
where quantity >= 60 
group by order_details.order_id,quantity
having count(*)>1 ;

 -- 37 Late orders - Some customers are complaining about their orders arriving late. Which orders are late?

select
order_id,
order_date,
required_date,
shipped_date
from orders
where shipped_date >= required_date ;

-- 38 Late orders - which employees? -

select
employees.employee_id,
employees.last_name,
count(order_id) as late_orders
from orders
join employees
on employees.employee_id = orders.employee_id
where shipped_date >= required_date
group by employees.employee_id,
employees.last_name
order by late_orders desc ;

-- 39 Late orders vs. total orders - compared against the total number of orders per salesperson;

with

all_orders as 
(select 
employee_id,
count(order_id) as totalorders
from orders
group by employee_id ),

late_orders as 
(select 
employee_id,
count(order_id) as lateorders
from
orders
where shipped_date >= required_date
group by employee_id ) 

select 
employees.employee_id,
employees.last_name,
all_orders.totalorders,
late_orders.lateorders
from employees
join all_orders
on all_orders.employee_id = employees.employee_id
join late_orders
on late_orders.employee_id = employees.employee_id  ;

-- 40 Late orders vs. total orders - percentage - Now we want to get the percentage of late orders over total orders.;

with

all_orders as 
(select 
employee_id,
count(order_id) as totalorders
from orders
group by employee_id ),

late_orders as 
(select 
employee_id,
count(order_id) as lateorders
from
orders
where shipped_date >= required_date
group by employee_id ) 

select 
employees.employee_id,
employees.last_name,
all_orders.totalorders,
late_orders.lateorders,
late_orders.lateorders/all_orders.totalorders * 100 as percentage_orders
from employees
join all_orders
on all_orders.employee_id = employees.employee_id
join late_orders
on late_orders.employee_id = employees.employee_id ;


-- 41 Late orders vs. total orders - fix decimal ;

with

all_orders as 
(select 
employee_id,
count(order_id) as totalorders
from orders
group by employee_id ),

late_orders as 
(select 
employee_id,
count(order_id) as lateorders
from
orders
where shipped_date >= required_date
group by employee_id ) 

select 
employees.employee_id,
employees.last_name,
all_orders.totalorders,
late_orders.lateorders,
format((late_orders.lateorders/all_orders.totalorders * 100),2) as percentage_orders
from employees
join all_orders
on all_orders.employee_id = employees.employee_id
join late_orders
on late_orders.employee_id = employees.employee_id ;

-- 42 Customer grouping - categorize customers into groups, based on how much they ordered in 1998 .
-- The customer grouping categories are 0 to 1,000, 1,000 to 5,000, 5,000 to 10,000, and over 10,000.
-- A good starting point for this query is the answer from the problem “High-value customers - total orders. 
-- We don’t want to show customers who don’t have any orders in 1998 ;


with cte as(select
customers.customer_id,
customers.company_name,
sum(unit_price*quantity) as amount
from customers
left join orders
on orders.customer_id = customers.customer_id
left join order_details
on orders.order_id = order_details.order_id
where year(order_date) = 1998 
group by customers.customer_id
order by customers.customer_id) 

select *,
case
when amount between 0 and 1000 then "low"
when amount between 1000 and 5000 then "medium"
when amount between 5000 and 10000 then "high"
when amount > 10000 then "very high"
end as customer_group
from cte
order by amount desc
;

-- 43. Customer grouping with percentage ;

with 
cte as(select
customers.customer_id,
customers.company_name,
sum(unit_price*quantity) as amount
from customers
left join orders
on orders.customer_id = customers.customer_id
left join order_details
on orders.order_id = order_details.order_id
where year(order_date) = 1998 
group by customers.customer_id
order by customers.customer_id) ,

cte2 as(
select *,
case
when amount between 0 and 1000 then "low"
when amount between 1000 and 5000 then "medium"
when amount between 5000 and 10000 then "high"
when amount > 10000 then "higho"
end as customer_group
from cte
order by amount desc)

select
customer_group,
count(*) as each_category,
count(*)/(select count(*) from cte2) * 100 as percentage_each_category
from cte2 
group by customer_group ;

-- 44.Countries with suppliers or customers -list of all countries where suppliers and/or customers are based ;

select
country
from suppliers

union

select
country
FROM customers ;

-- 45.First order in each country ;

 with cte1 as (select
 ship_country,
 min(date(order_date)) as first_order
 from orders
 group by ship_country),
 
 cte2 as (select
 customer_id,
 order_id,
 order_date
 from orders)
 
 select
 cte2.customer_id,
 cte2.order_id,
 cte1.ship_country,
 cte1.first_order
 from cte1
 left join cte2
 on cte1.first_order = cte2.order_date ;


-- ---END-----

