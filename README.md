CREATE DATABASE sales;
show DATABASES;
use sales;

CREATE TABLE Customers(
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(50),
    city VARCHAR(50)
);

CREATE TABLE Products(
    product_id INT PRIMARY KEY,
    product_name VARCHAR(50),
    category VARCHAR(30),
    price DECIMAL(10,2)
);

CREATE Table Orders(
    order_id INT PRIMARY KEY,
    customer_id INT,
    product_id INT,
    quantity INT,
    order_date DATE,
    Foreign Key (customer_id) REFERENCES Customers(customer_id),
    Foreign Key (product_id) REFERENCES Products(product_id)
);

INSERT INTO customers VALUES
(1,'Rahul','Hyderabad'),
(2,'Priya','Bangalore'),
(3,'Arjun','Chennai'),
(4,'Sneha','Mumbai'),
(5,'Kiran','Hyderabad');

INSERT INTO Products VALUES
(101,'Laptop','Electronics',55000),
(102,'Mouse','Accessories',500),
(103,'Keyboard','Accessories',1200),
(104,'Monitor','Electronics',12000),
(105,'Headphones','Accessories',2500);

INSERT INTO Orders VALUES
(1001,1,101,1,'2025-01-05'),
(1002,2,102,2,'2025-01-07'),
(1003,1,105,1,'2025-01-10'),
(1004,3,104,1,'2025-01-12'),
(1005,4,103,3,'2025-01-15'),
(1006,5,101,1,'2025-01-20'),
(1007,2,105,2,'2025-01-22'),
(1008,3,102,5,'2025-01-25'),
(1009,1,104,1,'2025-01-28');

SELECT * FROM customers;

select * from products;

select * from orders;

DESCRIBE sales.customers;
DESCRIBE sales.products;
DESCRIBE sales.orders;

select * from sales.products p
where p.category = 'Electronics';

select p.product_name, p.price from sales.products p
order by p.price desc;

select c.customer_id, sum(o.quantity) as 'Orders' from sales.Customers c join sales.orders o
on o.customer_id = c.customer_id
group by o.customer_id;

select p.product_id, sum(o.quantity) as 'Orders' from sales.orders o join sales.products p
on o.product_id = p.product_id
group by o.product_id;

select * from sales.customers c
where c.city='Hyderabad';

select c.Customer_Name, sum(o.Quantity) as 'Orders' from sales.customers c join sales.orders o
on c.customer_id = o.customer_id
group by c.customer_id;

select p.product_name, sum(o.quantity) as 'orders' from sales.products p inner join sales.orders o 
on p.product_id = o.product_id
group by o.product_id;

select p.product_name, sum(p.price*o.quantity) as 'revenue' from sales.products p inner join sales.orders o ON
p.product_id = o.product_id
group by p.product_id
order by revenue desc;

--      Business questions

-- 1. Which customer placed the most orders?
select c.customer_name,sum(o.quantity) as 'Most_Orders' from sales.customers c join sales.orders o 
ON c.customer_id = o.customer_id
group by o.customer_id
order by Most_Orders DESC
limit 1;
-- Arjun placed most orders..

-- 2. which product generated the highest revenue?
select p.product_name, sum(o.quantity*p.price) as 'Highest_revenue'
from sales.products p join sales.orders o on p.product_id = o.product_id
group by o.product_id
order by Highest_revenue DESC
limit 1;
-- Laptop generated highest revenue..

-- 3. Which city has the most customers?
SELECT c.City, sum(o.quantity) as 'total_Customers' from sales.customers c join sales.orders o
ON c.customer_id = o.customer_id
group by c.city
order by total_Customers DESC
limit 1;
-- chennai has most customers..

-- 4. Which category sold the most items?
select p.category as 'Most_sold_category' from sales.products p join sales.orders o
on p.product_id = o.product_id
group by Most_sold_category
order by sum(o.quantity) DESC
limit 1;
-- Category Accessories sold the most items..

-- 5. What is the total revenue of the store?
select sum(o.quantity*p.price) as 'Total revenue of store' from sales.orders o join sales.products p
on o.product_id = p.product_id;
-- The total revenue is 148600.

-- 6. Which product sold the highest quantity?
select p.product_name, sum(o.quantity) as 'Highest_quantity' FROM sales.orders o join sales.products p 
on o.product_id = p.product_id
group by o.product_id
order by Highest_quantity DESC
limit 1;
-- Mouse was sold in highest quantity.

-- 7. Show the top 3 customers by spending.
select c.customer_name, sum(o.quantity*p.price) as 'Spending'
from sales.customers c, sales.orders o, sales.products p
where o.customer_id = c.customer_id and
o.product_id = p.product_id
group by o.customer_id
order by Spending DESC
limit 3;
-- Rahul, Kiran, Arjun are the top 3 customers by spending.

--  8. Find customers who purchased more than one product.
select c.customer_name from sales.customers c join sales.orders o ON
c.customer_id = o.customer_id
group by c.customer_id
having count(DISTINCT o.product_id)>1;
-- Rahul, Priya, Arjun purchased more than one product.

-- 9. Find products that were never ordered.
select 'None' as 'Products_never_ordered'
where not exists (
    select p.product_name as 'Products_never_ordered' from sales.orders o LEFT JOIN sales.products p
    on o.product_id = p.product_id
    where o.product_id is null
);
-- There exists NO product which was never ordered.

-- 10. Calculate average order quantity
select avg(orders.quantity) as 'Average order quantity' from sales.orders;
