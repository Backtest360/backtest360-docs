# Use Your Own Data

The engine accepts OHLCV data from any source — you are responsible for fetching and preparing it.

## Required format

A `pandas.DataFrame` with:

- **Index:** `pd.DatetimeIndex` (timezone-naive or UTC)
- **Columns:** `open`, `high`, `low`, `close` (lowercase). `volume` is optional.

```python
import pandas as pd

df = pd.DataFrame({
    "open":  [...],
    "high":  [...],
    "low":   [...],
    "close": [...],
}, index=pd.to_datetime([...]))
```

## Yahoo Finance

```python
import yfinance as yf

df = yf.download("AAPL", period="5y", interval="1d",
    auto_adjust=False, multi_level_index=False, progress=False)
df.columns = df.columns.str.lower()
```

## CSV / Excel

```python
df = pd.read_csv("data.csv", index_col=0, parse_dates=True)
df.columns = df.columns.str.lower()
```

## Tiingo

```python
import tiingo
client = tiingo.TiingoClient({"api_key": "..."})
raw = client.get_ticker_price("AAPL", startDate="2020-01-01",
    endDate="2025-01-01", frequency="daily")
df = pd.DataFrame(raw).set_index("date")
df.index = pd.to_datetime(df.index)
df = df.rename(columns={"adjOpen": "open", "adjHigh": "high",
                         "adjLow": "low", "adjClose": "close"})
```
