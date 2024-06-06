# SQL-Sales Analysis


## Introduction
### Project Overview
This SQL project demonstrates my skills in data management and analysis using PostgreSQL. It involves creating and managing several relational tables like orders, customers, products, branches, shipping, returns, and payments. These tables cover transactional data, customer details, product information, logistics, and financial transactions within a business setting. The dataset includes over one million transactions from 15,000+ customers over an 11-year period from 2009 to 2019, providing a solid foundation for detailed analysis and insights.
### Table of Contents
- **orders**: Order_ID, Customer_ID, Order_Date, Branch_ID, Product_ID, Price, Quantity, Total, Shipping_Address
- **customers**: Customer_ID, Country, City, Address, Company_name, Contact_Person, Contact_Numbers, email, Payment_Method
- **products**: Product_ID, Product, Brand, Price
- **branch**: Branch_ID, City, Address
- **shipping**: Shipment_ID, Order_ID, Shipping_Company, Tracking_Number, Shipment_Date, Expected_Delivery_Date, Actual_Delivery_Date, Shipping_Address, Shipping_Cost, Delay_Charge, Reason_for_the_delay
- **return**: Return_ID, Order_ID, Return_Date, Return_Reason, Return_Shipping_Cost, Return_Items, Return_quantity, Refund_Amount, Refund_Date
- **payments**: Payment_ID, Order_ID, Customer_ID, Paid_Amount, Payment_Date

### Dataset Creation
The initial phase of this project involved creating the databases(sales) and tables within PostgreSQL.

#### creating database named "Sales" in postgresql

![Screenshot_16](https://github.com/sasmithaadhikari/SQL-Sales-Analysis/assets/165268051/d71bd363-0830-4f4d-b8a3-abc3fd6239d5)

#### creating each tables and establishing relations with each tables in sales database 

![combined](https://github.com/sasmithaadhikari/SQL-Sales-Analysis/assets/165268051/fd0c5ba9-84d6-4251-aa0f-f9e9cd1c4224)

```sql
create table customers(
       customer_id serial primary key,
       country text,
       city text,
       address text,
       company_name text,
       contact_person text,
       contact_numbers text,
       email text );

create table branch(
       branch_id serial primary key,
       city text,
       address text);

create table products(
       product_id serial primary key,
       product text,
       brand text,
       price decimal(10, 2));

create table orders(
       order_id serial primary key,
       customer_id int,
       order_date date,
       branch_id int,
       product_id int,
       price decimal(10, 2),
       quantity int,
       total decimal(10,2),
       shipping_address text,
       foreign key(customer_id) references customers(customer_id),
       foreign key(branch_id) references branch(branch_id),
       foreign key(product_id) references products(product_id) );

create table shipping(
       shipment_id serial primary key,
       order_id int,
       shipping_company text,
       tracking_number text,
       shipment_date date,
       expected_delivery_date date,
       actual_delivery_date date,
       shipping_address text,
       shipping_cost decimal(10, 2),
       delay_charge decimal(10, 2),
       reason_for_the_delay text,
       foreign key(order_id) references orders(order_id) );

create table return(
       return_id serial primary key,
       order_id int,
       return_date date,
       return_reason text,
       return_shipping_cost decimal(10, 2),
       return_items text,
       return_quantity int,
       refund_amount decimal(10, 2),
       refund_date date,
       foreign key(order_id) references orders(order_id) );

create table payments(
       payment_id serial primary key,
       order_id int,
       customer_id int,
       paid_amount decimal(10, 2),
       payment_date date,
       foreign key(order_id) references orders(order_id),
       foreign key(customer_id) references customers(customer_id) );
    
```

#### Importing data into each table in the sales database through CSV files.

![Screenshot_20](https://github.com/sasmithaadhikari/SQL-Sales-Analysis/assets/165268051/729a5d84-544d-4c34-bc53-3cd77d764527)

>**Since there is a 25 MB size limitation for uploading files to GitHub, I separately saved all the CSV files in another location for referencing purposes**
>**You can access them using the following link.**
  
## Analyzing Phase

1.*What are the yearly transaction counts, along with the projected sales for the next year based on current trends, and how do these years rank in terms of transaction volume?*

![Screenshot_3](https://github.com/sasmithaadhikari/SQL-Sales-Analysis/assets/165268051/829f65a1-230f-4a47-b586-750ecd89e0b7)

>**????**

``` sql
select extract(year from order_date) as Year,
       count(order_id) as Transaction_Count,
       lead(count(order_id),1,0) over(order by extract(year from order_date)) as Next_Year_transaction_count,
       dense_rank() over(order by count(order_id) desc ) as Rank_Based_On_Transaction_Count
      from orders
      group by extract(year from order_date)
```



 




