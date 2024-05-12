---
layout: default
---

# Abstruct 
This is the S&P 500 stock heatmap created using Tableau. It offers a comprehensive visualization of the performance and correlations among the stocks comprising the S&P 500 index. And the data collected by Python's yfinance library.

### About how to collect the daily S&P500 stock price data
```ruby
# import yfinance library
import pandas as pd
import yfinance as yf
```
```ruby
# then use the wikipedia to collect the lastest S&P500 stocks' list
sp500url = 'https://en.wikipedia.org/wiki/List_of_S%26P_500_companies'
data_table = pd.read_html(sp500url)
tickers = data_table[0]['Symbol'].tolist()
```
```
# As I am not in the US, if I collect the stocks' close price and make the difference to calculate the percentage, so I need to lag two days, you can adjust these codes to refer to your timezone, or you can set no limit to the time and collect all time data
from datetime import date, timedelta
yesterday = date.today() - timedelta(days=2)
start_day = yesterday.strftime('%Y-%m-%d')
today = date.today()
end_day = today.strftime('%Y-%m-%d')
```

```
# as some stock list formats no meet the yfinance library list, we need to rename the unmatch name
for i in range(len(tickers)):
    if tickers[i] == 'BF.B':
        tickers[i] = 'BF-B'
    elif tickers[i] == 'BRK.B':
        tickers[i] = 'BRK-B'
```

```
import warnings
warnings.filterwarnings('ignore')
#get the price data of SP500 from yfinance
df = pd.DataFrame()
for ticker in tickers:
    prices = yf.download(tickers=ticker, start=start_day, end=end_day)
    prices['Ticker'] = ticker
    df = df.append(prices)
df.reset_index(inplace=True)
df = df[['Ticker', 'Date', 'Open', 'High', 'Low', 'Close', 'Adj Close', 'Volume']]
```
```
# get the market cap
df2 = pd.DataFrame(columns=['Ticker', 'Market Cap'])
for ticker in tickers:
    ticker_obj = yf.Ticker(ticker)
    market_cap = ticker_obj.info.get('marketCap', 'N/A')
    df2 = df2.append({'Ticker': ticker, 'Market Cap': market_cap}, ignore_index=True)
```

```
merged_table = pd.merge(df, df2, on='Ticker', how='left')
```

```
# the final merged document that collects all the information we need: Ticker, Date, Open, High, Low, Close, Adj Close, Volume, Market Cap, Security, GICS Sector, GICS, Sub-Industry, Headquarters, Location, Date, added, CIK, Founded
df3 = data_table[0].rename(columns={'Symbol': 'Ticker'})
merged_table_final = pd.merge(merged_table, df3, on='Ticker', how='left')
merged_table_final.to_csv('SP500_heatmap_industry.csv', index=False)
```





