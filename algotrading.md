---
title: "ACTL1101 Assignment Part A"
author: "Siri Sampalli"
date: "2024 T2"
output:
  html_document:
    df_print: paged
  pdf_document: default
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Algorithmic Trading Strategy

## Introduction

In this assignment, you will develop an algorithmic trading strategy by incorporating financial metrics to evaluate its profitability. This exercise simulates a real-world scenario where you, as part of a financial technology team, need to present an improved version of a trading algorithm that not only executes trades but also calculates and reports on the financial performance of those trades.

## Background

Following a successful presentation to the Board of Directors, you have been tasked by the Trading Strategies Team to modify your trading algorithm. This modification should include tracking the costs and proceeds of trades to facilitate a deeper evaluation of the algorithm’s profitability, including calculating the Return on Investment (ROI).

After meeting with the Trading Strategies Team, you were asked to include costs, proceeds, and return on investments metrics to assess the profitability of your trading algorithm.

## Objectives

1. **Load and Prepare Data:** Open and run the starter code to create a DataFrame with stock closing data.

2. **Implement Trading Algorithm:** Create a simple trading algorithm based on daily price changes.

3. **Customize Trading Period:** Choose your entry and exit dates.

4. **Report Financial Performance:** Analyze and report the total profit or loss (P/L) and the ROI of the trading strategy.

5. **Implement a Trading Strategy:** Implement a trading strategy and analyze the total updated P/L and ROI. 

6. **Discussion:** Summarise your finding.


## Instructions

### Step 1: Data Loading

Start by running the provided code cells in the "Data Loading" section to generate a DataFrame containing AMD stock closing data. This will serve as the basis for your trading decisions. First, create a data frame named `amd_df` with the given closing prices and corresponding dates. 

```{r load-data}

# Load data from CSV file
amd_df <- read.csv("AMD.csv")

# Convert the date column to Date type and Adjusted Close as numeric
amd_df$date <- as.Date(amd_df$Date)
amd_df$close <- as.numeric(amd_df$Adj.Close)

amd_df <- amd_df[, c("date", "close")]
```


##Plotting the Data
Plot the closing prices over time to visualize the price movement.
```{r plot}
plot(amd_df$date, amd_df$close,'l')
```


## Step 2: Trading Algorithm
Implement the trading algorithm as per the instructions. You should initialize necessary variables, and loop through the dataframe to execute trades based on the set conditions.

- Initialize Columns: Start by ensuring dataframe has columns 'trade_type', 'costs_proceeds' and 'accumulated_shares'.
- Change the algorithm by modifying the loop to include the cost and proceeds metrics for buys of 100 shares. Make sure that the algorithm checks the following conditions and executes the strategy for each one:
  - If the previous price = 0, set 'trade_type' to 'buy', and set the 'costs_proceeds' column to the current share price multiplied by a `share_size` value of 100. Make sure to take the negative value of the expression so that the cost reflects money leaving an account. Finally, make sure to add the bought shares to an `accumulated_shares` variable.
  - Otherwise, if the price of the current day is less than that of the previous day, set the 'trade_type' to 'buy'. Set the 'costs_proceeds' to the current share price multiplied by a `share_size` value of 100.
  - You will not modify the algorithm for instances where the current day’s price is greater than the previous day’s price or when it is equal to the previous day’s price.
  - If this is the last day of trading, set the 'trade_type' to 'sell'. In this case, also set the 'costs_proceeds' column to the total number in the `accumulated_shares` variable multiplied by the price of the last day.



```{r trading}
# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA  # Corrected column name
amd_df$accumulated_shares <- 0  # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

for (i in 1:nrow(amd_df)) {
# Fill your code here

# On the last day of the period, we sell all the accumulated shares
  
  if (i == 1259) {
    amd_df$trade_type[i] <- 'sell'
    amd_df$costs_proceeds[i] <- accumulated_shares*amd_df$close[i]

# If the previous buying price was less than the current buying price, 
# we will buy the share and update the cost proceeds and accumulated shares 
    
  } else if (previous_price == 0 || amd_df$close[i] < previous_price) {
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -share_size*amd_df$close[i]
    accumulated_shares <- accumulated_shares + share_size
    amd_df$accumulated_shares[i] <- accumulated_shares

# If neither of the above statements are fulfilled, we will not update the cost 
# proceeds or accumulated shares (they remain the same)
    
    } else {
    amd_df$costs_proceeds[i] <- 0
    amd_df$accumulated_shares[i] <- accumulated_shares
  }

# updating the previous_price variable to the current day's price
  previous_price <- amd_df$close[i]
  
}
```


## Step 3: Customize Trading Period
- Define a trading period you wanted in the past five years 
```{r period}
# Fill your code here
# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA  # Corrected column name
amd_df$accumulated_shares <- 0  # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

# Period from 2020-12-31 to 2021-12-31

for (i in 410:662) {

# On the last day of the period, we sell all the accumulated shares
  if (i == 662) {
    amd_df$trade_type[i] <- 'sell'
    amd_df$costs_proceeds[i] <- accumulated_shares*amd_df$close[i]

# If the previous buying price was less than the current buying price, 
# we will buy the share and update the cost proceeds and accumulated shares
    
  } else if (previous_price == 0 || amd_df$close[i] < previous_price) {
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -share_size*amd_df$close[i]
    accumulated_shares <- accumulated_shares + share_size
    amd_df$accumulated_shares[i] <- accumulated_shares

# If neither of the above statements are fulfilled, we will not update the cost 
# proceeds or accumulated shares (they remain the same)
  } else {
    amd_df$costs_proceeds[i] <- 0
    amd_df$accumulated_shares[i] <- accumulated_shares
  }

# updating the previous_price variable to the current day's price 
  previous_price <- amd_df$close[i]
  
}
```


## Step 4: Run Your Algorithm and Analyze Results
After running your algorithm, check if the trades were executed as expected. Calculate the total profit or loss and ROI from the trades.

- Total Profit/Loss Calculation: Calculate the total profit or loss from your trades. This should be the sum of all entries in the 'costs_proceeds' column of your dataframe. This column records the financial impact of each trade, reflecting money spent on buys as negative values and money gained from sells as positive values.
- Invested Capital: Calculate the total capital invested. This is equal to the sum of the 'costs_proceeds' values for all 'buy' transactions. Since these entries are negative (representing money spent), you should take the negative sum of these values to reflect the total amount invested.
- ROI Formula: $$\text{ROI} = \left( \frac{\text{Total Profit or Loss}}{\text{Total Capital Invested}} \right) \times 100$$

```{r}
# Fill your code here
total_profit_loss <- 0
capital_invested <- 0
ROI <- 0

# We loop through all the values and sum up the cost proceeds to find the total
# profit/loss

for (i in 410:662) {
  total_profit_loss <- total_profit_loss + amd_df$costs_proceeds[i]

# Negative sum of all purchases to find total capital invested
  
  if (amd_df$costs_proceeds[i] < 0) {
    capital_invested = capital_invested - amd_df$costs_proceeds[i]
  }
  
}

ROI <- (total_profit_loss/capital_invested)*100
print('Profit/Loss:')
print(total_profit_loss)
print('ROI:')
print(ROI)

```

## Step 5: Profit-Taking Strategy or Stop-Loss Mechanisum (Choose 1)
- Option 1: Implement a profit-taking strategy that you sell half of your holdings if the price has increased by a certain percentage (e.g., 20%) from the average purchase price.
- Option 2: Implement a stop-loss mechanism in the trading strategy that you sell half of your holdings if the stock falls by a certain percentage (e.g., 20%) from the average purchase price. You don't need to buy 100 stocks on the days that the stop-loss mechanism is triggered.


```{r option}
# Fill your code here
# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA  # Corrected column name
amd_df$accumulated_shares <- 0  # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0
total_purchase_prices <- 0
total_days_purchased <- 0
average_purchase_price <- 0

# Period from 2020-12-31 to 2021-12-31

for (i in 410:662) {
  
# On the last day of the period, we sell all the accumulated shares
  
  if (i == 662) {
    amd_df$trade_type[i] <- 'sell'
    amd_df$costs_proceeds[i] <- accumulated_shares*amd_df$close[i]
  
# If the previous buying price was less than the current buying price, 
# we will buy the share and update the cost proceeds and accumulated shares
    
  } else if (previous_price == 0 || amd_df$close[i] < previous_price) {
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -share_size*amd_df$close[i]
    accumulated_shares <- accumulated_shares + share_size
    amd_df$accumulated_shares[i] <- accumulated_shares

# Updating the following to be used for average purchase price calculations
    total_purchase_prices <- total_purchase_prices + amd_df$close[i]
    total_days_purchased <- total_days_purchased + 1
    
# If neither of the above statements are fulfilled, we will not update the cost 
# proceeds or accumulated shares (they remain the same) but we will check if the 
# conditions of the profit-taking strategy are fulfilled
    
  } else {
    amd_df$costs_proceeds[i] <- 0
    amd_df$accumulated_shares[i] <- accumulated_shares
    average_purchase_price <- total_purchase_prices/total_days_purchased

# If the current share price is greater than 20% higher than the average purchase 
# price, we will sell half the shares and update the cost proceeds and 
# accumulated shares 
    
      if (amd_df$close[i] > average_purchase_price*1.2) {
        amd_df$trade_type[i] <- 'sell'
        accumulated_shares <- accumulated_shares*0.5
        amd_df$costs_proceeds[i] <- accumulated_shares*amd_df$close[i]
        
      }
  }
  
  # updating the previous_price variable to the current day's price
  previous_price <- amd_df$close[i]
  
}


```


## Step 6: Summarize Your Findings
- Did your P/L and ROI improve over your chosen period?
- Relate your results to a relevant market event and explain why these outcomes may have occurred.


```{r}
# Fill your code here and Discuss
total_profit_loss <- 0
capital_invested <- 0
ROI <- 0

# We loop through all the values within the specified period and sum up the cost  the total
# proceeds to find profit/loss

for (i in 410:662) {
  total_profit_loss <- total_profit_loss + amd_df$costs_proceeds[i]

# Negative sum of all purchases to find total capital invested
  
  if (amd_df$costs_proceeds[i] < 0) {
    capital_invested = capital_invested - amd_df$costs_proceeds[i]
  }
  
}

ROI <- (total_profit_loss/capital_invested)*100

print('Profit/Loss:')
print(total_profit_loss)
print('ROI:')
print(ROI)

```

Sample Discussion: On Wednesday, December 6, 2023, AMD CEO Lisa Su discussed a new graphics processor designed for AI servers, with Microsoft and Meta as committed users. The rise in AMD shares on the following Thursday suggests that investors believe in the chipmaker's upward potential and market expectations; My first strategy earned X dollars more than second strategy on this day, therefore providing a better ROI.

Discussion: 

During the period from 2020-12-31 to 2021-12-31, AMD experienced fluctuations in share price but an overall increase was observed. This can be attributed to several market events which likely boosted investor confidence and led to an increase in AMD share demand, driving buying prices up from $91.71 at the beginning of the period, to $143.90 at the end of the period. 

The company demonstrated strong financial performance during the second quarter of 2021, generating $3.85 billion in revenue. This is due, in part, to the successful launch of products including the Ryzen 5000G Series APUs. Moreover, despite supply chain difficulties resulting in chip shortages for a significant portion of the specified period, AMD managed to remain relatively stable and meet demand where required. These market events are likley to have boosted shareholder confidence and attracted a large number potential investors, increasing demand for AMD shares and driving the more significant increase in share price experienced in the third quarter of 2021. 

Overall, the profit-taking strategy proved to result in a significantly lower profit and ROI than the initial strategy. It proved more beneficial to wait to sell shares at the end of the period as they, overall, experienced an increase in price much higher than 1.2 times the average buying price.  






