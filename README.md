# Pizza-sales-analysis

## Table of contents
- [Project overview](#Project-overview)
- [Data sources](#Data-sources)
- [Tools](#Tool)
- [Data Cleaning/Preparation](#Data-Cleaning/Preparation)
- [Exploratory Data Analysis (EDA)](#Exploratory-Data-Analysis-(EDA))
- [Data analysis](#Data-analysis)
- [Results/Findings](#Results/Findings)
- [Recommendations](#Recommendations)

### Project overview

---
![Pizza sales Dashboard](https://github.com/user-attachments/assets/2f861817-4d89-41cf-ae1a-dcbd6792509f)

This project focuses on leveraging SQL, Python, and Tableau to analyze HR data, uncover actionable insights, and monitor key workforce metrics. The project involves building a data pipeline to acquire, clean, and process HR datasets, followed by exploratory data analysis (EDA) to identify trends. Finally, interactive KPI dashboards are created in Tableau to visualize and track essential workforce metrics, enabling data-driven decision-making in HR management.

### Data sources

Pizza Sales Data: The primary dataset used for this analysis is the 'pizza_sales_excel_file.xlsx' file, which contains detailed information about the company's workforce.

### Tool

- SQL Server - Data Analysis
- Tableau - Creating reports
- Python(Jupyter notebook) - Data cleaning and analysis


### Data Cleaning/Preparation

In the initial data preparation phase, we performed the following tasks:
1. Data loading and inspection.
2. Handling missing values.
3. Data Cleaning and formatting.

### Exploratory Data Analysis (EDA)
EDA involved exploring the company's workforce to answer various questions, such as;

### Data analysis

Includes some codes/features worked with

```sql
KPIs
1. Total Revenue
SELECT SUM(price * quantity) AS total_revenue FROM orders;
2. Total Orders
SELECT COUNT(DISTINCT order_id) AS total_orders FROM orders;
3. Average Order Value (AOV)
SELECT 
    (SUM(price * quantity) / COUNT(DISTINCT order_id)) AS avg_order_value FROM orders;
4. Total Pizzas Sold
SELECT SUM(quantity) AS total_pizzas_sold FROM orders;
5. Average Pizzas per Order
SELECT 
    (SUM(quantity) / COUNT(DISTINCT order_id)) AS avg_pizzas_per_order FROM orders;

Dashboard Visualizations
The queries for the dashboard visualizations remain the same, as they are not directly affected by the changes to the KPI formulas. For reference:
Daily Trends for Total Orders
SELECT DATE(order_date) AS order_date, COUNT(DISTINCT order_id) AS total_orders FROM orders 
GROUP BY DATE(order_date)
ORDER BY order_date;
Hourly Trends for Total Orders
SELECT HOUR(order_date) AS hour_of_day, COUNT(DISTINCT order_id) AS total_orders FROM orders
GROUP BY HOUR(order_date)
ORDER BY hour_of_day;
Percentage Sales by Pizza Category
SELECT pizza_category, 
       SUM(unit price * quantity) / (SELECT SUM(unit price * quantity) FROM orders) * 100 AS percentage_sales FROM orders
GROUP BY category;
Percentage Sales by Pizza Size
SELECT pizza_size, 
       SUM(price * quantity) / (SELECT SUM(price * quantity) FROM orders) * 100 AS percentage_sales FROM orders
GROUP BY pizza_size;
Total Pizzas Sold by Pizza Category
SELECT pizza_category, SUM(quantity) AS total_pizzas_sold FROM orders
GROUP BY pizza_category 
ORDER BY total_pizzas_sold DESC;
Top 5 Best Sellers by Pizza Category
SELECT pizza_category, pizza_id, SUM(quantity) AS total_sold FROM orders
GROUP BY pizza_category, pizza_id
ORDER BY pizza_category, total_sold DESC
LIMIT 5;
Bottom 5 Worst Sellers by Pizza Category
SELECT pizza_category, pizza_id, SUM(quantity) AS total_sold FROM orders
GROUP BY pizza_category, pizza_id
ORDER BY pizza_category, total_sold ASC
LIMIT 5;
```

### Python code

```import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
pizza=pd.read_excel('pizza_sales_excel_file.xlsx')


# checking for missing values
pizza.isnull().sum()
# checking for duplicated values
pizza.duplicated().sum()
# Data analysis

# Preview the dataset
print("Dataset Preview:")
print(pizza.head())

# Data Cleaning and Preparation
print("\nChecking for Missing Values:")
print(pizza.isnull().sum())  # Check for missing values

# Convert date column to datetime if necessary
pizza['order_date'] = pd.to_datetime(pizza['order_date'])

# KPI Calculations
print("\nKPIs:")
total_revenue = (pizza['unit_price'] * pizza['quantity']).sum()
print(f"Total Revenue: ${total_revenue:.2f}")

total_orders = pizza['order_id'].nunique()
print(f"Total Orders: {total_orders}")

total_pizzas_sold = pizza['quantity'].sum()
print(f"Total Pizzas Sold: {total_pizzas_sold}")

avg_order_value = total_revenue / total_orders
print(f"Average Order Value: ${avg_order_value:.2f}")

avg_pizzas_per_order = total_pizzas_sold / total_orders
print(f"Average Pizzas per Order: {avg_pizzas_per_order:.2f}")

# Sales by Pizza Category
sales_by_category = pizza.groupby('pizza_category')['unit_price'].sum()
print("\nSales by Pizza Category:")
print(sales_by_category)

# Sales by Pizza Size
sales_by_size = pizza.groupby('pizza_size')['unit_price'].sum()
print("\nSales by Pizza Size:")
print(sales_by_size)

# Visualizations
sns.set(style="whitegrid")


# 1. Daily Trends for Total Orders - Bar Chart
daily_orders = pizza.groupby(pizza['order_date'].dt.date)['order_id'].nunique()
plt.figure(figsize=(10, 5))
daily_orders.plot(kind='bar', color='skyblue')
plt.title("Daily Trends for Total Orders")
plt.xlabel("Date")
plt.ylabel("Number of Orders")
plt.xticks(rotation=45)
plt.show()

# 2. Hourly Trends for Total Orders - Line Chart
pizza['hour'] = pizza['order_date'].dt.hour
hourly_orders = pizza.groupby('hour')['order_id'].nunique()
plt.figure(figsize=(10, 5))
sns.lineplot(x=hourly_orders.index, y=hourly_orders.values, marker='o', color='orange')
plt.title("Hourly Trends for Total Orders")
plt.xlabel("Hour of the Day")
plt.ylabel("Number of Orders")
plt.xticks(range(0, 24))
plt.show()

# 3. Percentage Sales by Pizza Size - Pie Chart
sales_by_size_percentage = (sales_by_size / total_revenue) * 100
plt.figure(figsize=(7, 7))
sales_by_size_percentage.plot.pie(autopct='%1.1f%%', startangle=140, colors=sns.color_palette("pastel"))
plt.title("Percentage Sales by Pizza Size")
plt.ylabel("")
plt.show()


# 4. Percentage Sales by Pizza Category
sales_by_category_percentage = (sales_by_category / total_revenue) * 100
plt.figure(figsize=(7, 7))
sales_by_category_percentage.plot.pie(autopct='%1.1f%%', startangle=140, colors=sns.color_palette("pastel"))
plt.title("Percentage Sales by Pizza Category")
plt.ylabel("")
plt.show()

# 5. Total Pizzas Sold by Pizza Category
pizzas_by_category = pizza.groupby('pizza_category')['quantity'].sum()
plt.figure(figsize=(10, 5))
sns.barplot(x=pizzas_by_category.index, y=pizzas_by_category.values, palette="coolwarm")
plt.title("Total Pizzas Sold by Pizza Category")
plt.xlabel("Pizza Category")
plt.ylabel("Number of Pizzas Sold")
plt.show()

# 6. Top 5 Best Sellers by Pizza Name (Sorted by Total Quantity Sold)
top_5_best_sellers_by_quantity = (
    pizza.groupby('pizza_name')['quantity']  # Group by pizza name and quantity
    .sum()  # Sum the quantity sold for each pizza name
    .reset_index()  # Convert the series to a DataFrame
    .sort_values(by='quantity', ascending=False)  # Sort by total quantity (descending order)
    .head(5)  # Get the top 5
)

# Print top 5 best sellers by pizza name
print("\nTop 5 Best Sellers by Pizza Name (Sorted by Quantity Sold):")
print(top_5_best_sellers_by_quantity)

# 7. Bottom 5 Worst Sellers by Pizza Name (Sorted by Total Quantity Sold)
bottom_5_worst_sellers_by_quantity = (
    pizza.groupby('pizza_name')['quantity']  # Group by pizza name and quantity
    .sum()  # Sum the quantity sold for each pizza name
    .reset_index()  # Convert the series to a DataFrame
    .sort_values(by='quantity', ascending=True)  # Sort by total quantity (ascending order)
    .head(5)  # Get the bottom 5
)

# Print bottom 5 worst sellers by pizza name
print("\nBottom 5 Worst Sellers by Pizza Name (Sorted by Quantity Sold):")
print(bottom_5_worst_sellers_by_quantity)

# Plot Top 5 Best Sellers by Pizza Name (Bar Chart for Quantity Sold)
plt.figure(figsize=(10, 5))
sns.barplot(x=top_5_best_sellers_by_quantity['pizza_name'], y=top_5_best_sellers_by_quantity['quantity'], palette="viridis")
plt.title("Top 5 Best Sellers by Pizza Name (Quantity Sold)")
plt.xlabel("Pizza Name")
plt.ylabel("Total Quantity Sold")
plt.xticks(rotation=60)
plt.show()

# Plot Bottom 5 Worst Sellers by Pizza Name (Bar Chart for Quantity Sold)
plt.figure(figsize=(10, 5))
sns.barplot(x=bottom_5_worst_sellers_by_quantity['pizza_name'], y=bottom_5_worst_sellers_by_quantity['quantity'], palette="coolwarm")
plt.title("Bottom 5 Worst Sellers by Pizza Name (Quantity Sold)")
plt.xlabel("Pizza Name")
plt.ylabel("Total Quantity Sold")
plt.xticks(rotation=60)
plt.show()
