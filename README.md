# Data Portfolio: Newbie level 

# EASY Demand Forecasting & Inventory Optimization

# Demand Forecasting & Inventory Optimization

## Overview
This project focuses on forecasting demand and optimizing Product_ID `P1` inventory using time series analysis and inventory management strategies.

---

## Project Tasks
1. Utilize time series analysis to forecast future Product_ID `P1` demand.
2. Generate predictions for the next 10 days.
3. Calculate and implement safety stock levels to account for demand variability.
4. Determine the reorder point to identify when to replenish inventory.
5. Apply the Economic Order Quantity (EOQ) concept to minimize inventory costs.
6. Define the reorder cycle, representing the time interval between placing orders.

---

## Code Walkthrough

### Import Libraries and Load Data
```python
import pandas as pd
import numpy as np
import plotly.express as px
from statsmodels.graphics.tsaplots import plot_acf, plot_pacf
import matplotlib.pyplot as plt
from statsmodels.tsa.statespace.sarimax import SARIMAX

# Load the dataset
data = pd.read_csv(r"C:\Users\Admin\Downloads\demand_inventory.csv")

# Display initial rows
print(data.head())
```
---
### Data Preprocessing
```python
# Remove unnecessary columns
data = data.drop(columns=['Unnamed: 0'])
data['Date'] = pd.to_datetime(data['Date'], format='%Y-%m-%d')

# Display processed data
print(data.head())
```
