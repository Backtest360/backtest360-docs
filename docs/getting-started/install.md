# Installation

## Requirements

- Python 3.9 or newer.
- A Backtest360 API key. Create a free account at [backtest360.com/sign-up](https://backtest360.com/sign-up); a free-tier key comes with the account. See [Authentication](authentication.md).

## Install the client

```bash
pip install backtest360
```

The client depends only on `httpx` and `pandas`. There are no other runtime dependencies, and it bundles no data provider — you supply price data as a `pandas.DataFrame`, or use the engine's bundled sample datasets.

## Verify the install

With your key in the `BACKTEST360_API_KEY` environment variable (see [Authentication](authentication.md)):

```python
from backtest360 import Client

print(Client().version())
# {'version': '0.6.1', 'engine': '0.12.1',
#  'api_contract': '4', 'expected_client_contract': '1'}
```

A successful response confirms the client is installed and can reach the engine; the exact numbers track the deployed engine. To confirm your key specifically, call `Client().me()`, which returns your account and plan and fails with an auth error if the key is missing or invalid.

## Optional: a data source

The client works with any OHLCV `DataFrame` you provide. For a self-contained first run you can use the bundled sample data (`client.sample_data("SPY")`) with no external package. To pull your own market data, common choices are:

```bash
pip install yfinance           # Yahoo Finance
pip install tiingo             # Tiingo
pip install pandas-datareader  # FRED, and other sources
```

See [Use your own data](../how-to/use-your-own-data.md) for the required frame shape.

## Next

[Get and configure your API key →](authentication.md)
