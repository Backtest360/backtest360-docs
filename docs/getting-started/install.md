# Installation

## Requirements

- Python 3.9+
- An API key from [backtest360.com](https://backtest360.com)

## Install the client

```bash
pip install backtest360
```

The client depends only on `httpx` and `pandas`. No other runtime dependencies.

## Verify the install

```python
from backtest360 import Client
print(Client(api_key="b360_...").version())
# {'version': 'x.y.z', 'engine': '...', 'api_contract': '...', 'expected_client_contract': '...'}
```

## Optional: data fetching

The client does not bundle a data provider — you supply your own OHLCV DataFrame. Common choices:

```bash
pip install yfinance           # Yahoo Finance
pip install tiingo             # Tiingo
pip install pandas-datareader  # FRED, Quandl, etc.
```

## Next

[Get your API key →](authentication.md)
