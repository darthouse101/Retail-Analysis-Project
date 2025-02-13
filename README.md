# Retail-Analysis-Project

This project involves an in-depth analysis of eCommerce data to uncover the most purchased items, identify trends, and discover valuable insights. The analysis focuses on understanding the purchasing behavior and revenue patterns of specific items within the dataset.

## Table of Contents

- [Introduction](#introduction)
- [Data](#data)
- [Setup](#setup)
- [Analysis](#analysis)
  - [Data Loading](#data-loading)
  - [Top Purchased Items](#top-purchased-items)
  - [Monthly Revenue Trends](#monthly-revenue-trends)
- [Visualizations](#visualizations)
- [Conclusion](#conclusion)

## Introduction

The purpose of this project is to analyze retail data to find the most purchased items and identify trends over time. By visualizing the data, we aim to uncover patterns that can provide insights into customer behavior and revenue generation.

## Data

The dataset used in this project is stored in an SQLite database named `retail.db`. The key columns in the `retail` table include:

- `StockCode`: Unique identifier for each item
- `Description`: Description of the item
- `Quantity`: Number of units sold
- `UnitPrice`: Price per unit of the item
- `Total`: Total revenue generated from the item
- `Month`: Month of the transaction

## Setup

To set up the project, follow these steps:

1. **Clone the repository:**

   ```bash
   git clone https://github.com/your-username/ecommerce-analysis.git
   cd ecommerce-analysis
   ```

2. **Install the required libraries:**

   ```bash
   pip install pandas matplotlib seaborn sqlite3
   ```

3. **Ensure the SQLite database is in the project directory:**

   - The `retail.db` file should be in the same directory as your Python scripts.

## Analysis

### Data Loading

Load the necessary libraries and establish a connection to the SQLite database:

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import sqlite3

# Establish the database connection
conn = sqlite3.connect('retail.db')
```

### Top Purchased Items

Identify the most purchased items and calculate the total quantity and revenue generated:

```python
query = '''
SELECT 
    StockCode,
    Description,
    TotalQuantity,
    UnitPrice,
    ROUND(TotalQuantity * UnitPrice, 2) AS YearlyRevenue
FROM (
    SELECT 
        StockCode,
        Description,
        SUM(Quantity) AS TotalQuantity,
        UnitPrice
    FROM retail
    GROUP BY StockCode
)
ORDER BY YearlyRevenue DESC
LIMIT 10
'''
df_top_items = pd.read_sql_query(query, conn)
print(df_top_items)
```

### Monthly Revenue Trends

Analyze the monthly revenue trends for the top items:

```python
query = '''
SELECT SUM(Total) AS Total_Sum, Month, Description 
FROM retail 
WHERE Description IN ('PARTY BUNTING', 'DOTCOM POSTAGE') 
GROUP BY Month, Description
ORDER BY Month
'''
df_monthly_trends = pd.read_sql_query(query, conn)
print(df_monthly_trends)
```

## Visualizations

Create visualizations to present the findings. For example, to visualize the monthly revenue trends of "PARTY BUNTING" and "DOTCOM POSTAGE":

```python
# Pivot and melt the data for plotting
df_pivot = df_monthly_trends.pivot(index='Month', columns='Description', values='Total_Sum').reset_index()
df_melted = pd.melt(df_pivot, id_vars='Month', value_vars=['PARTY BUNTING', 'DOTCOM POSTAGE'], 
                    var_name='Description', value_name='Total_Sum')

# Create the bar plot
plt.figure(figsize=(12, 6))
sns.barplot(x='Month', y='Total_Sum', hue='Description', data=df_melted)
plt.xticks(rotation=90)
plt.xlabel('Months')
plt.ylabel('Monthly Revenue')
plt.title('Impact of PARTY BUNTING and DOTCOM POSTAGE on Monthly Revenue')
plt.tight_layout()
plt.show()
```

## Conclusion

This project demonstrates how to perform an eCommerce analysis to identify the most purchased items and trends. The insights gained from this analysis can help businesses understand customer behavior, and optimize inventory.
