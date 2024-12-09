# Data Portfolio: Newbie level 

> EASY Demand Forecasting & Inventory Optimization
> BECAUSE MY CURRENT LEVEL IS ONLY AT THIS STAGE...

## Demand Forecasting & Inventory Optimization

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
| Unnamed: 0 | Date       | Product_ID | Demand | Inventory |
|------------|------------|------------|--------|-----------|
| 0          | 2023-06-01 | P1         | 51     | 5500      |
| 1          | 2023-06-02 | P1         | 141    | 5449      |
| 2          | 2023-06-03 | P1         | 172    | 5308      |
| 3          | 2023-06-04 | P1         | 91     | 5136      |
| 4          | 2023-06-05 | P1         | 198    | 5045      |
```
### Dataset Columns Information
- *Unnamed*: Index (removed during preprocessing).
- *Date*: The observation date.
- *Product_ID*: The unique identifier for the product.
- *Demand*: The daily demand for the product.
- *Inventory*: The inventory level at the end of each day.

---
### Data Preprocessing
```python
# Remove unnecessary columns
data = data.drop(columns=['Unnamed: 0'])
data['Date'] = pd.to_datetime(data['Date'], format='%Y-%m-%d')

# Display processed data
print(data.head())
```
---
### Visualization: Demand Over Time 
```python
fig_demand = px.line(data, x='Date', y='Demand', title='Demand Over Time')
fig_demand.show()
```
###### Output: Interactive line chart displaying inventory trends over time 
![newplot](https://github.com/user-attachments/assets/d1674c08-3e4b-49a0-917a-eb86b4148173)
---
### Time Series Analysis for Demand Forecasting 
```python
# Prepare time series data
time_series = data.set_index('Date')['Demand']
differenced_series = time_series.diff().dropna()

# Plot ACF and PACF
fig, axes = plt.subplots(1, 2, figsize=(12, 4))
plot_acf(differenced_series, ax=axes[0])
plot_pacf(differenced_series, ax=axes[1])
plt.show()
```
##### Output: Plots for Auto-Correlation Function (ACF) and Partial Auto-Correlation Function (PACF) 
![Partial Correlation](https://github.com/user-attachments/assets/e1de57d7-1db8-40ac-87e2-71afa78e7386)

### Demand Forecasting with SARIMA (Seasonal Autoregressive Integrated Moving Average)
```python
# SARIMA model
order = (1, 1, 1)
seasonal_order = (1, 1, 1, 2)
model = SARIMAX(time_series, order=order, seasonal_order=seasonal_order)
model_fit = model.fit(disp=False)

# Predict the next 10 days
future_steps = 10
predictions = model_fit.predict(len(time_series), len(time_series) + future_steps - 1).astype(int)
print(predictions)

# Sample Prediction Output
| 2023-08-02    117 |                      |              |
|-------------------|----------------------|--------------|
| 2023-08-03    116 |                      |              |
| 2023-08-04    130 |                      |              |
| 2023-08-05    114 |                      |              |
| 2023-08-06    128 |                      |              |
| 2023-08-07    115 |                      |              |
| 2023-08-08    129 |                      |              |
| 2023-08-09    115 |                      |              |
| 2023-08-10    129 |                      |              |
| 2023-08-11    115 |                      |              |
 Freq: D            Name: predicted_mean  dtype: int32
```
---
### Inventory Optimization 
Optimize inventory according to the forecasted demand for the next 10 days
```python
# Create date indices for the future predictions
future_dates = pd.date_range(start=time_series.index[-1] + pd.DateOffset(days=1), periods=future_steps, freq='D')

# Create a pandas Series with the predicted values and date indices
forecasted_demand = pd.Series(predictions, index=future_dates)

# Initial inventory level
initial_inventory = 5500

# Lead time (number of days it takes to replenish inventory) 
lead_time = 1 # it's different for every business, 1 is an example

# Service level (probability of not stocking out)
service_level = 0.95 # it's different for every business, 0.95 is an example

# Calculate the optimal order quantity using the Newsvendor formula
z = np.abs(np.percentile(forecasted_demand, 100 * (1 - service_level)))
order_quantity = np.ceil(forecasted_demand.mean() + z).astype(int)

# Calculate the reorder point
reorder_point = forecasted_demand.mean() * lead_time + z

# Calculate the optimal safety stock
safety_stock = reorder_point - forecasted_demand.mean() * lead_time

# Calculate the total cost (holding cost + stockout cost)
holding_cost = 0.1  # different for every business, 0.1 is an example
stockout_cost = 10  # # different for every business, 10 is an example
total_holding_cost = holding_cost * (initial_inventory + 0.5 * order_quantity)
total_stockout_cost = stockout_cost * np.maximum(0, forecasted_demand.mean() * lead_time - initial_inventory)

# Calculate the total cost
total_cost = total_holding_cost + total_stockout_cost

# Results:
print("Optimal Order Quantity:", order_quantity)
print("Reorder Point:", reorder_point)
print("Safety Stock:", safety_stock)

| Optimal Order Quantity: 236   |
|-------------------------------|
| Reorder Point: 235.25         |
| Safety Stock: 114.45          |
| Total Cost: 561.8000000000001 |
```
---
## Conclusion

The analysis reveals critical inventory optimization insights:

Optimal Order Quantity: 236 units, representing the most cost-effective procurement volume
Reorder Point: 235.25 units, signaling when to initiate new stock procurement
Safety Stock: 114.45 units, providing a buffer against demand and supply uncertainties
Total Cost: 561.80 units, encompassing comprehensive inventory management expenses

> By implementing these precise inventory parameters, the organization can enhance supply chain responsiveness, reduce financial risk, and optimize > resource allocation.

--- 
## References
[Arman Kharmal](https://github.com/amankharwal)
---
## Acknowledgements 
- Tools Used: Python, Jupyeter Notebook
- Libraries: pandas, numpy, matplotlib, plotly, statsmodels

> THANK U FOR ANY EMPLOYERS READING UNTIL THIS PART, PLEASE HIRE ME     :sparkles::sparkles::sparkles:
