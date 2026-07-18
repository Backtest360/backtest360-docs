# Use Your Own Data

The engine runs on OHLCV data you provide as a `pandas.DataFrame`. It bundles no data feed, so you are responsible for fetching and preparing the frame. (For a quick run with no external source, the engine also serves bundled sample datasets via `client.sample_data("SPY")` — see [Your first backtest](../getting-started/first-backtest.md).)

## Required format

A `pandas.DataFrame` with:

- **Index:** a `pd.DatetimeIndex` (timezone-naive or UTC).
- **Columns:** `open`, `high`, `low`, `close`, lowercase. `volume` is optional.

```python
import pandas as pd

df = pd.DataFrame({
    "open":  [...],
    "high":  [...],
    "low":   [...],
    "close": [...],
}, index=pd.to_datetime([...]))
```

The same frame goes to `client.backtest()`, `client.backtest_signals()`, and `client.latest_signal()`.

## Yahoo Finance

```python
import yfinance as yf

df = yf.download(
    "AAPL", period="5y", interval="1d",
    auto_adjust=False, multi_level_index=False, progress=False,
)
df.columns = df.columns.str.lower()
```

## CSV or Excel

```python
df = pd.read_csv("data.csv", index_col=0, parse_dates=True)
df.columns = df.columns.str.lower()
```

## Tiingo

```python
import tiingo

tiingo_client = tiingo.TiingoClient({"api_key": "..."})
raw = tiingo_client.get_ticker_price(
    "AAPL", startDate="2020-01-01", endDate="2025-01-01", frequency="daily",
)
df = pd.DataFrame(raw).set_index("date")
df.index = pd.to_datetime(df.index)
df = df.rename(columns={
    "adjOpen": "open", "adjHigh": "high", "adjLow": "low", "adjClose": "close",
})
```

## Data quality

Whatever the source, check `result.data_quality` after a run — the engine reports bad prices, missing bars, and other issues it found while preparing your frame. See [Result anatomy](../concepts/result-anatomy.md).
