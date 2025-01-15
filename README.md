# End-to-End Data Analysis Project

This project is a comprehensive data analysis workflow that involves downloading a dataset, cleaning it in Python, and analyzing it using SQL queries in MySQL Workbench. The project is inspired by a YouTube tutorial and was adapted to practice data analysis skills using Python and MySQL Workbench instead of SQL Server.

## Project Overview

# Objective

The primary objective of this project is to demonstrate the process of performing data analysis from start to finish, including:

- Accessing a dataset via the Kaggle API.

- Cleaning and preprocessing the data in Python using Jupyter Notebook.

- Connecting Python to MySQL Workbench.

- Writing SQL queries to answer business questions.

# Tools Used

- Python: Data cleaning and preprocessing.

- MySQL Workbench: Database management and SQL query execution.

- Kaggle API: Downloading the dataset programmatically.

# Dataset Information

 - Source: Kaggle

 - Dataset Name: [Retail Orders]

# Key Features:
- order_id: Unique identifier for each order.

- order_date: Date of the order.

- ship_mode: Shipping mode for the order.

- segment: Customer segment.

- country: Country of the order.

- city: City of the order.

- state: State of the order.

- postal_code: Postal code of the order location.

- region: Region of the order.

- category: Product category.

- sub_category: Product sub-category.

- product_id: Identifier for each product.

- cost_price: Cost price of the product.

- list_price: List price of the product.

- quantity: Quantity of items sold.

- discount_percent: Discount percentage applied.

- discount: Discount amount applied.

- sale_price: Revenue generated from the order


# Workflow

**Step 1**: Downloading the Dataset

The dataset was downloaded using the Kaggle API. Below is the Python script used for this step:

```py 
!pip install kaggle
import kaggle
!kaggle datasets download ankitbansal06/retail-orders -f orders.csv
```
**Step 2**:Extracting the Zip file
```py
import zipfile
zip_ref=zipfile.ZipFile('orders.csv.zip')
zip_ref.extractall()
zip_ref.close()
```

**Step 3**: Data Cleaning in Python
Here is an example of the Python code used:

```py
import pandas as pd
df=pd.read_csv("orders.csv",na_values=['Not Available', 'unknown'])
df.columns=df.columns.str.lower()
df.columns=df.columns.str.replace(' ','_')
f['discount']=df['list_price']*df['discount_percent']*.01
df['sale_price']=df['list_price']-df['discount']
df['profit']=df['sale_price']-df['cost_price']
df['order_date']=pd.to_datetime(df['order_date'],format='%Y-%m-%d')
```

Step 3: Connecting Python to MySQL Workbench

The cleaned dataset was uploaded to MySQL Workbench using the mysql.connector library. Below is the Python script used for the connection and data insertion:
```py
!pip install pymysql
!pip install sqlalchemy pymysql pandas
import mysql.connector

# Establish connection to MySQL
from sqlalchemy import create_engine

engine = create_engine("mysql+pymysql://root:1234@localhost/my_database")
df.to_sql(name='df_orders',con=engine,index=False, if_exists='append')

**Step 4**: SQL Analysis

SQL queries were written to answer various business questions. Examples include:

- Query 1: find top 10 highest revenue generating products
```sql
 SELECT product_id,ROUND(sum(sale_price)) AS Revenue
 FROM df_orders
 GROUP BY product_id
 ORDER BY Revenue DESC
 LIMIT 10;
```
 - Query 2: find top 5 highest selling product in each region
```sql
 WITH cte AS (SELECT region,ROUND(sum(sale_price)) AS Sales
 FROM df_orders
 GROUP BY region,product_id)
 SELECT * 
 FROM ( SELECT * ,
 RANK() OVER(PARTITION BY region ORDER BY Sales DESC) AS ranks
 FROM cte) AS A
 WHERE ranks<=5;
```
 - Query 3: find month over month growth comparision for 2022 and 2023 sales eg:jan 2022 vs jan 2023
 
 ```sql
 with cte as 
 (select year(order_date) as order_year,month(order_date) as order_month,sum(sale_price) as sales
 from df_orders
 GROUP BY order_year,order_month)
 Select  order_month,
 sum(case when order_year=2022 then sales else 0 end) as sales_2022,
 sum(case when order_year=2023 then sales else 0 end) as sales_2023
 from cte
 group by order_month
 order by order_month;
 ```
- Query 4:for each category which month has highest sales
 ```sql  
 with cte as (select category,DATE_FORMAT(order_date, '%Y%m') as order_year_month,sum(sale_price)as total_sales
 from df_orders
 group by category,DATE_FORMAT(order_date, '%Y%m')
 )
 select * from
 (select *,
 rank() over(partition by category order by total_sales desc)as ranks
 from cte)as a
 where ranks=1;
```
 - Query 5: which sub category had highest growth by profit in 2023 compare to 2022
```sql
with cte as 
 (select sub_category,year(order_date) as order_year,sum(sale_price) as sales
 from df_orders
 GROUP BY sub_category,year(order_date)),
 cte2 as(
 Select  sub_category,
 sum(case when order_year=2022 then sales else 0 end) as sales_2022,
 sum(case when order_year=2023 then sales else 0 end) as sales_2023
 from cte
 group by sub_category)
 select *,(sales_2023-sales_2022)*100/(sales_2022)as sales_growth
 from cte2
 order by sales_growth desc
 limit 1;
```

Project Highlights

Learning Experience: This project was inspired by a **Ankit Bansal**
[(youtubelink)](https://www.youtube.com/@ankitbansal6) where the original implementation used SQL Server. In this adaptation, I used MySQL Workbench.

Hands-On Practice: It provided valuable experience in data cleaning, SQL querying, and integrating Python with MySQL.

Adaptation: Modified the approach to suit the MySQL platform while replicating the analysis.

Disclaimer

This project is not original work. It is adapted from a  **Ankit Bansal**
[(youtubelink)](https://www.youtube.com/@ankitbansal6) for learning purposes. The modifications, including using MySQL Workbench and additional analysis, were performed independently.

Acknowledgments

Special thanks to  **Ankit Bansal** for the detailed tutorial that inspired this project.
