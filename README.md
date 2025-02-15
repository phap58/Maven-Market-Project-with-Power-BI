# Maven-Market-Project-with-Power-BI
# Adding Calculated Columns and Measures using DAX

## 1. Adding Calculated Columns
### Calendar Table
- **Weekend** – Returns “Y” for Saturdays or Sundays, otherwise “N”
  ```DAX
  Weekend = IF('Calendar'[Day Name] = "Saturday" || 'Calendar'[Day Name] = "Sunday", "Y", "N")
  ```
- **End of Month** – Returns the last date of the current month for each row
  ```DAX
  End of Month = EOMONTH('Calendar'[date], 0)
  ```

### Customers Table
- **Current Age** – Calculates customer age using the birthdate column and `TODAY()`
  ```DAX
  Current Age = DATEDIFF(Customers[birthdate], TODAY(), YEAR)
  ```
- **Priority** – Returns "High" for customers who own homes and have Golden membership cards, otherwise "Standard"
  ```DAX
  Priority = IF(Customers[member_card] = "Golden" && Customers[homeowner] = "Y", "High", "Standard")
  ```
- **Short_Country** – Returns the first three uppercase characters of the customer's country
  ```DAX
  Short_Country = UPPER(LEFT(Customers[customer_country], 3))
  ```
- **House Number** – Extracts all characters/numbers before the first space in the customer address
  ```DAX
  House_Number = LEFT(Customers[customer_address], SEARCH(" ", Customers[customer_address]) - 1)
  ```

### Products Table
- **Price Tier** – Categorizes retail price into "High", "Mid", or "Low"
  ```DAX
  Price_Tier = IF(Products[product_retail_price] > 3, "High", IF(Products[product_retail_price] > 1, "Mid", "Low"))
  ```

### Stores Table
- **Years Since Remodel** – Calculates the years since the last remodel
  ```DAX
  Years_Since_Remodel = DATEDIFF(Stores[last_remodel_date], TODAY(), YEAR)
  ```

## 2. Adding Measures
### Basic Aggregations
- **Quantity Sold** – Total quantity of products sold
  ```DAX
  Quantity Sold = SUM(Transaction_Data[quantity])
  ```
- **Quantity Returned** – Total quantity of products returned
  ```DAX
  Quantity Returned = SUM(Return_Data[quantity])
  ```
- **Total Transactions** – Count of transactions
  ```DAX
  Total Transactions = COUNTROWS(Transaction_Data)
  ```
- **Total Returns** – Count of return transactions
  ```DAX
  Total Returns = COUNTROWS(Return_Data)
  ```

### Performance Metrics
- **Return Rate** – Percentage of products returned
  ```DAX
  Return Rate = [Quantity Returned] / [Quantity Sold]
  ```
- **Weekend Transactions** – Count of transactions occurring on weekends
  ```DAX
  Weekend Transactions = CALCULATE([Total Transactions], 'Calendar'[Weekend] = "Y")
  ```
- **% Weekend Transactions** – Percentage of transactions occurring on weekends
  ```DAX
  % Weekend Transactions = [Weekend Transactions] / [Total Transactions]
  ```
- **All Transactions** – Total transactions ignoring filters
  ```DAX
  All Transactions = CALCULATE([Total Transactions], ALL(Transaction_Data))
  ```
- **All Returns** – Total returns ignoring filters
  ```DAX
  All Returns = CALCULATE([Total Returns], ALL(Return_Data))
  ```

### Financial Metrics
- **Total Revenue** – Revenue from transactions
  ```DAX
  Total Revenue = SUMX(Transaction_Data, Transaction_Data[quantity] * RELATED(Products[product_retail_price]))
  ```
- **Total Cost** – Cost of sold products
  ```DAX
  Total Cost = SUMX(Transaction_Data, Transaction_Data[quantity] * RELATED(Products[product_cost]))
  ```
- **Total Profit** – Revenue minus cost
  ```DAX
  Total Profit = [Total Revenue] - [Total Cost]
  ```
- **Profit Margin** – Profit as a percentage of revenue
  ```DAX
  Profit Margin = [Total Profit] / [Total Revenue]
  ```
- **Unique Products** – Count of distinct product names
  ```DAX
  Unique Products = DISTINCTCOUNT(Products[product_name])
  ```

### Time-Based Metrics
- **YTD Revenue** – Year-to-date revenue
  ```DAX
  YTD Revenue = CALCULATE([Total Revenue], DATESYTD('Calendar'[date]))
  ```
- **60-Day Revenue** – Revenue over the past 60 days
  ```DAX
  60-Day Revenue = CALCULATE([Total Revenue], DATESINPERIOD('Calendar'[date], MAX('Calendar'[date]), -60, DAY))
  ```
- **Last Month Transactions** – Transactions from the previous month
  ```DAX
  Last Month Transactions = CALCULATE([Total Transactions], DATEADD('Calendar'[date], -1, MONTH))
  ```
- **Last Month Revenue** – Revenue from the previous month
  ```DAX
  Last Month Revenue = CALCULATE([Total Revenue], DATEADD('Calendar'[date], -1, MONTH))
  ```
- **Last Month Profit** – Profit from the previous month
  ```DAX
  Last Month Profit = CALCULATE([Total Profit], DATEADD('Calendar'[date], -1, MONTH))
  ```
- **Last Month Returns** – Returns from the previous month
  ```DAX
  Last Month Returns = CALCULATE([Total Returns], DATEADD('Calendar'[date], -1, MONTH))
  ```
- **Revenue Target** – Target revenue based on a 5% increase over the previous month
  ```DAX
  Revenue Target = [Last Month Revenue] * 1.05
  ```

