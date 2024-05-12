---
layout: default
---

# The codes that collect the data
```ruby
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

```ruby
# get the market cap
df2 = pd.DataFrame(columns=['Ticker', 'Market Cap'])
for ticker in tickers:
    ticker_obj = yf.Ticker(ticker)
    market_cap = ticker_obj.info.get('marketCap', 'N/A')
    df2 = df2.append({'Ticker': ticker, 'Market Cap': market_cap}, ignore_index=True)
```

```ruby
merged_table = pd.merge(df, df2, on='Ticker', how='left')
```

```ruby
# the final merged document that collects all the information we need: Ticker, Date, Open, High, Low, Close, Adj Close, Volume, Market Cap, Security, GICS Sector, GICS, Sub-Industry, Headquarters, Location, Date, added, CIK, Founded
df3 = data_table[0].rename(columns={'Symbol': 'Ticker'})
merged_table_final = pd.merge(merged_table, df3, on='Ticker', how='left')
merged_table_final.to_csv('SP500_heatmap_industry.csv', index=False)
```
