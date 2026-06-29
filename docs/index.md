# Backtest360

**Backtest your trading strategy in minutes. Institutional grade.**

Backtest360 provides a cloud-based backtesting engine with a clean Python client, a REST API, and an MCP server. Plug in your OHLCV data, define a strategy with plain boolean expressions, and get a full performance report in seconds.

## Three ways in, one API key

The same API key works across every interface:

- **Python client** — `pip install backtest360`; drive the engine from scripts and notebooks. *(These docs.)*
- **REST API** — call `https://api.backtest360.com` directly from any language with an `X-API-Key` header. See the [API Reference](api-reference/index.md).
- **AI assistants (MCP)** — point any MCP-compatible assistant at our hosted MCP server and run backtests in conversation, no install. See [Connect over MCP](mcp/connect.md).

Get a key at [backtest360.com](https://backtest360.com).

## Quick example

```python
import yfinance as yf
from backtest360 import Client, Strategy

df = yf.download("BTC-USD", period="3y", interval="1d",
    auto_adjust=False, multi_level_index=False, progress=False)
df.columns = df.columns.str.lower()

result = Client(api_key="b360_...").backtest(
    Strategy.rsi_threshold_long(), df
)
print(result.stats["Sharpe"])
result.strategy_equity.plot(title="Equity curve")
```

## Where to start

- **[Installation](getting-started/install.md)** — `pip install backtest360`
- **[Authentication](getting-started/authentication.md)** — get and use your API key
- **[Your first backtest](getting-started/first-backtest.md)** — end-to-end walkthrough
- **[Client Reference](sdk-reference/client.md)** — full `Client`, `Strategy`, and helper class docs
- **[API Reference](api-reference/index.md)** — interactive REST API browser

## Need help?

Questions or feedback? Email us at [hello@backtest360.com](mailto:hello@backtest360.com)
— we read everything.

Found a bug, or want a feature? [Open an issue on GitHub](https://github.com/Backtest360/backtest360/issues).
