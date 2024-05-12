---
layout: default
---

<div class='tableauPlaceholder' id='viz1715484213325' style='position: relative'>
  <noscript>
    <a href='#'>
      <img alt='Daily S&P500 Stock Price HeatMap' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Da&#47;DailySP500HeatMap&#47;Dashboard1&#47;1_rss.png' style='border: none' />
    </a>
  </noscript>
  <object class='tableauViz' style='display:none;'>
    <param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' />
    <param name='embed_code_version' value='3' />
    <param name='site_root' value='' />
    <param name='name' value='DailySP500HeatMap/Dashboard1' />
    <param name='tabs' value='no' />
    <param name='toolbar' value='yes' />
    <param name='static_image' value='https://public.tableau.com/static/images/Da/DailySP500HeatMap/Dashboard1/1.png' />
    <param name='animate_transition' value='yes' />
    <param name='display_static_image' value='yes' />
    <param name='display_spinner' value='yes' />
    <param name='display_overlay' value='yes' />
    <param name='display_count' value='yes' />
    <param name='language' value='en-US' />
  </object>
</div>
<script type='text/javascript'>
  var divElement = document.getElementById('viz1715484213325');
  var vizElement = divElement.getElementsByTagName('object')[0];
  if (divElement.offsetWidth > 800) {
    vizElement.style.width = '100%';
    vizElement.style.maxWidth = '1230px';
    vizElement.style.height = (divElement.offsetWidth * 0.75) + 'px';
    vizElement.style.maxHeight = '755px';
  } else if (divElement.offsetWidth > 500) {
    vizElement.style.width = '100%';
    vizElement.style.maxWidth = '1230px';
    vizElement.style.height = (divElement.offsetWidth * 0.75) + 'px';
    vizElement.style.maxHeight = '755px';
  } else {
    vizElement.style.width = '100%';
    vizElement.style.height = '827px';
  }
  var scriptElement = document.createElement('script');
  scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';
  vizElement.parentNode.insertBefore(scriptElement, vizElement);
</script>


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
