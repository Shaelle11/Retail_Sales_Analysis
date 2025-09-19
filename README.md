# Retail Sales Analytics Project

## Project Overview
This project demonstrates an **end-to-end data analytics pipeline** using **SQL, Excel, and Power BI**.  
We analyze Walmart’s retail sales dataset to uncover insights into sales trends, seasonality, and the impact of holidays.

## Project Insight
Retailers like Walmart need to understand **how holidays, seasonality, and store differences** affect sales.  
This project was built to:  
- Explore the **impact of holidays** on weekly sales.  
- Identify **store and department performance trends**.  
- Provide a **dashboard for decision-making** that highlights key KPIs.  
- Practice a full **data analytics workflow** (ETL → Analysis → Visualization).
---

## 📂 Data Source
- **Dataset**: [Walmart Sales CSV](https://www.kaggle.com/c/walmart-recruiting-store-sales-forecasting/data)  
- Contains weekly sales data across multiple stores and departments.
- Holiday and non holiday Weekly sales for stores and departments.

---

## Tools & Technologies
- **SQL (MySQL)** - Data import, cleaning, feature engineering  
- **Excel** - Pivot tables & slicers for exploratory analysis  
- **Power BI** - Interactive dashboard with DAX measures
- **Python** - Python script for baatch import of large dataset for SQL.

---
## Project Workflow
1. **Got Data** - Walmart Kaggle sales dataset (CSV).  
2. **Python (ETL step)** → Batched import into SQL database because the dataset was too large for direct import.  
3. **SQL (MySQL)** → Data cleaning, schema creation, and feature engineering (`Year`, `Month`, `Week`).  
4. **Excel** → Exploratory analysis using pivot tables and slicers.  
5. **Power BI** → Interactive dashboards with DAX measures and KPIs.  

## SQL Steps
1. Create Database and Table.
   <img width="598" height="289" alt="SQL Create database and table script" src="https://github.com/user-attachments/assets/d9c7da30-cb90-41a4-a2e3-648203765afc" />
2. Imported the CSV into **MySQL** using .  
3. Cleaned data and converted date formats.  
4. Added new time-based columns:
   - `Year`
   - `Month`
   - `Week`  
4. Verified record counts after loading.

## Python Data Import script snippet
Because the dataset was too large for the MySQL wizard, I wrote a Python script with **pandas + SQLAlchemy + urllib.parse** to batch import in chunks of 50,000 rows.

```python data import script
import pandas as pd
from sqlalchemy import create_engine
import urllib.parse

# --- DB connection ---
user = "root"
password = urllib.parse.quote_plus("MySqlPassword@1")  # escape @ or special chars
host = "localhost:3306"
db = "walmart_sales"

engine = create_engine(f"mysql+pymysql://{user}:{password}@{host}/{db}")

# --- Read & insert in chunks ---
chunksize = 50000
for chunk in pd.read_csv("C:/Users/Hp/Desktop/chunks/train.csv", chunksize=chunksize):
    # Convert TRUE/FALSE to 1/0
    chunk['Holiday_Flag'] = chunk['IsHoliday'].map({True:"TRUE", False:"FALSE", "TRUE":"TRUE", "FALSE":"FALSE"})

    
    # Drop the old IsHoliday column
    chunk.drop(columns=['IsHoliday'], inplace=True)
    
    # Insert chunk into MySQL
    chunk.to_sql(name="sales", con=engine, if_exists="append", index=False)
    print(f"Inserted {len(chunk)} rows")
