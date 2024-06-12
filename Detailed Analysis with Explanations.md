# SQL-Sales Analysis


## Introduction
### Project Overview
This SQL project demonstrates my skills in data management and analysis using PostgreSQL. It involves creating and managing several relational tables like orders, customers, products, branches, shipping, returns, and payments. These tables cover transactional data, customer details, product information, logistics, and financial transactions within a business setting. The dataset includes over one million transactions from 15,000+ customers over an 11-year period from 2009 to 2019, providing a solid foundation for detailed analysis and insights.

## How this Analysis is documented
In this analysis, I will begin by attaching a screenshot of the result where I executed the SQL query and obtained the results. Following that, I will provide a brief explanation based on the screenshot. Finally, I will include the query used to fetch the data within the PostgreSQL database.

## Problem Statements



### TOOLS USED
- EXCEL
- POSTGRE SQL DATABASE

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

2. *Can the order value be considered as the total revenue the company earned?*

![Screenshot_5](https://github.com/sasmithaadhikari/SQL-Sales-Analysis/assets/165268051/090735f7-5e8f-42b6-847f-2f5e60e6d962)

>**I tried to fetch data related to sales transactions on a yearly and quarterly basis. Gross sales amount cannot be considered as revenue earned by the company, as each sales transaction includes expenses. Therefore, it is necessary to deduct these expenses to determine the exact amount that the company actually earned**

```sql
with Quarterly_Revenue as(
select extract(year from o.order_date) as Year,
       extract(quarter from o.order_date) as quarter,
       sum(o.total) as gross_Quarterly_revenue,
       sum(o.total-(o.total-coalesce(p.paid_amount,0))-coalesce(s.shipping_cost,0)
       -coalesce(s.delay_charge,0)-coalesce(r.refund_amount,0)-coalesce(r.return_shipping_cost,0)) 
       as Quarterly_Net_Revenue
 from orders o 
 left join shipping s on o.order_id=s.order_id
 left join return r on o.order_id=r.order_id
 left join payments p on o.order_id=p.order_id
group by extract(year from o.order_date),
         extract(quarter from o.order_date)
order by extract(year from o.Order_Date), extract(quarter from o.Order_Date))
select Year,quarter,
       gross_Quarterly_revenue,
       Quarterly_Net_Revenue,
      sum(gross_Quarterly_revenue) over(partition by Year) as Yearly_Gross_sales,
      sum(Quarterly_Net_Revenue) over(partition by Year) as Yearly_Net_sales
    from Quarterly_Revenue
    order by Year,Quarter
```

3.*What are the costs associated with sales transactions?*

![Screenshot_6](https://github.com/sasmithaadhikari/SQL-Sales-Analysis/assets/165268051/1387ced0-2aa0-443e-8e56-b894fdbc84c9)

>**???**

```sql
with costs as(
select extract(year from o.order_date) as year,
       sum(s.shipping_cost) as total_shipping_cost,
       sum(s.delay_charge) as total_delay_chargers,
       sum(r.refund_amount) as total_refund_cost,
       sum(r.return_shipping_cost) as total_return_cost,
       sum(o.total-coalesce(p.paid_amount,0)) as bad_debts
 from orders o 
  left join shipping s on o.order_id=s.order_id
  left join return r on o.order_id=r.order_id
  left join payments p on o.order_id=p.order_id
 group by extract(year from o.order_date))
 select  year,
        total_shipping_cost,total_delay_chargers,total_refund_cost,
        total_return_cost,bad_debts,
       sum(total_shipping_cost+total_delay_chargers+total_refund_cost+total_return_cost+bad_debts) as total_cost
       from costs
 group by total_shipping_cost,total_delay_chargers,total_refund_cost,total_return_cost,bad_debts,year
order by Year
```

4. *costs as percentages of gross revenue*

![Screenshot_7](https://github.com/sasmithaadhikari/SQL-Sales-Analysis/assets/165268051/4aa3a142-6c23-4cae-8af7-0b1a54dec3a8)

>**??**


5. *How much delay cost could have been saved?*

![Screenshot_16](https://github.com/sasmithaadhikari/SQL-Sales-Analysis/assets/165268051/6fe4ae5d-f246-425d-acc9-768b6142655e)

> **???** 

```sql
 
select 
    Reason_for_the_delay,
    count(*) as delay_count,
    ROUND((count(*)::decimal / total_transactions.total_count) * 100, 2) as delay_percentage_of_totaltransactions,
    sum(Delay_Charge) as total_delay_cost,
    ROUND((sum(Delay_Charge) / total_delay_costs.total_cost) * 100, 2) as delay_cost_percentage
from 
    shipping,
    (select count(*) as total_count from shipping) as total_transactions,
    (select sum(Delay_Charge) as total_cost from shipping where Actual_Delivery_Date > Expected_Delivery_Date) as total_delay_costs
where 
    actual_Delivery_Date > expected_Delivery_Date
GROUP BY 
    reason_for_the_delay, total_transactions.total_count, total_delay_costs.total_cost;
```










 




