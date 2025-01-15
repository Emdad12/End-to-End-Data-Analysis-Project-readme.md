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

 - Dataset Name: [Insert Dataset Name Here]

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

import mysql.connector
from sqlalchemy import create_engine

# MySQL connection
connection = mysql.connector.connect(
    host='localhost',
    user='your_username',
    password='your_password',
    database='your_database'
)

# Uploading the data
df.to_sql('df_orders', con=create_engine('mysql+mysqlconnector://your_username:your_password@localhost/your_database'),
          index=False, if_exists='replace')

Step 4: SQL Analysis

SQL queries were written to answer various business questions. Examples include:

Query 1: Top 5 Highest-Selling Products in Each Region

SELECT region, product_id, SUM(sale_price) AS total_sales
FROM df_orders
GROUP BY region, product_id
ORDER BY region, total_sales DESC
LIMIT 5;

Query 2: Highest Sales by Category Each Month

WITH cte AS (
    SELECT category, DATE_FORMAT(order_date, '%Y-%m') AS order_month, SUM(sale_price) AS total_sales
    FROM df_orders
    GROUP BY category, DATE_FORMAT(order_date, '%Y-%m')
)
SELECT category, order_month, total_sales
FROM (
    SELECT category, order_month, total_sales,
           RANK() OVER (PARTITION BY category ORDER BY total_sales DESC) AS rank
    FROM cte
) ranked_sales
WHERE rank = 1;

Results and Insights

Top-Selling Products: Identified the highest revenue-generating products in each region.

Seasonal Trends: Determined monthly sales trends for each category.

Profitability: Gained insights into which product categories yielded the highest profits.

Project Highlights

Learning Experience: This project was inspired by a [YouTube tutorial](insert link) where the original implementation used SQL Server. In this adaptation, I used MySQL Workbench.

Hands-On Practice: It provided valuable experience in data cleaning, SQL querying, and integrating Python with MySQL.

Adaptation: Modified the approach to suit the MySQL platform while replicating the analysis.

Disclaimer

This project is not original work. It is adapted from a [YouTube tutorial](insert link) for learning purposes. The modifications, including using MySQL Workbench and additional analysis, were performed independently.

Acknowledgments

Special thanks to [YouTube Channel Name](insert link) for the detailed tutorial that inspired this project.
