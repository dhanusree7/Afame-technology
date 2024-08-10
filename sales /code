import pandas as pd

# Load the Excel file
file_path = r'C:\Users\sry91\Downloads\ECOMM DATA.xlsx'  # Update with your file path
xls = pd.ExcelFile(file_path)

# Load all sheets into dataframes
orders_df = pd.read_excel(xls, 'Orders')
returns_df = pd.read_excel(xls, 'Returns')
people_df = pd.read_excel(xls, 'People')

# Data Cleaning: Convert columns to appropriate data types
orders_df['Order Date'] = pd.to_datetime(orders_df['Order Date'])
orders_df['Ship Date'] = pd.to_datetime(orders_df['Ship Date'])

# Add Year and Month columns for time-based analysis
orders_df['Year'] = orders_df['Order Date'].dt.year
orders_df['Month'] = orders_df['Order Date'].dt.month

# 1. Sales and Profit Analysis Over Time
# Group by year and month to see trends over time
monthly_sales = orders_df.groupby(['Year', 'Month'])[['Sales', 'Profit']].sum().reset_index()

# 2. Performance by Product Category and Sub-Category
category_performance = orders_df.groupby('Category')[['Sales', 'Profit']].sum().sort_values(by='Sales', ascending=False).reset_index()
subcategory_performance = orders_df.groupby(['Category', 'Sub-Category'])[['Sales', 'Profit']].sum().sort_values(by='Sales', ascending=False).reset_index()

# 3. Returns Analysis
# Merge the returns data with the orders data to see which orders were returned
returns_df = returns_df.rename(columns={"Order ID": "Order ID"})
returned_orders = pd.merge(orders_df, returns_df, on='Order ID', how='left')
returned_orders['Returned'] = returned_orders['Returned'].fillna('No')

# Calculate return rates and impact on profit
return_rate = returned_orders['Returned'].value_counts(normalize=True) * 100
return_impact = returned_orders[returned_orders['Returned'] == 'Yes'][['Sales', 'Profit']].sum()

# 4. Regional Analysis (linking with People data)
regional_sales = orders_df.groupby('Region')[['Sales', 'Profit']].sum().sort_values(by='Sales', ascending=False).reset_index()

# Print insights
print("Sales and Profit Trends Over Time:")
print(monthly_sales.head())

print("\nTop Performing Product Categories:")
print(category_performance)

print("\nTop Performing Sub-Categories:")
print(subcategory_performance.head())

print("\nReturn Rates:")
print(return_rate)

print("\nImpact of Returns on Profit:")
print(return_impact)

print("\nSales and Profit by Region:")
print(regional_sales)

# Example of identifying any new pattern or trend
# Let's check if a specific product category is being returned more often
category_return_rate = returned_orders.groupby(['Category', 'Returned'])['Order ID'].count().unstack().fillna(0)
category_return_rate['Return Rate (%)'] = (category_return_rate['Yes'] / (category_return_rate['Yes'] + category_return_rate['No'])) * 100

print("\nCategory Return Rates:")
print(category_return_rate.sort_values(by='Return Rate (%)', ascending=False))

# Identifying if any new trend is found
new_trend = category_return_rate.sort_values(by='Return Rate (%)', ascending=False).head(1)
print("\nNew Pattern Found: Highest return rate observed in the category:", new_trend.index[0])
