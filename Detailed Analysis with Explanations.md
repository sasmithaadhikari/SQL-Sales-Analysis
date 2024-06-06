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
