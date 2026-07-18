# Backtest360

Backtest360 is a rigour-first backtesting engine with a typed API. You describe a strategy as signals and execution rules, the engine runs it over historical price data, and you get a full performance report — returns, risk, trade-level detail, and benchmark-relative metrics — computed server-side with a strict no-look-ahead discipline.

The engine is deliberately conservative about the things that make backtests lie: signals are lagged one bar before they can fill, costs and slippage are modelled explicitly, and every run reports the data-quality issues it found. The result is a number you can defend.

## Three ways in, one API key

The same engine sits behind three interfaces, and one API key works across all of them.

- **Python client** — `pip install backtest360`. A typed client for scripts and notebooks. These docs cover it in full.
- **REST API** — call `https://api.backtest360.com` from any language with an `X-API-Key` header. See the [API Reference](api-reference/index.md).
- **AI assistants (MCP)** — point any MCP-compatible assistant at the hosted MCP server and run backtests in conversation, no install required. See [Connect over MCP](mcp/connect.md).

Create a free account at [backtest360.com/sign-up](https://backtest360.com/sign-up); a free-tier API key comes with the account. See [Authentication](getting-started/authentication.md) for how to configure and rotate it.

## Quick example

This runs end to end with nothing but the client and a key — the engine serves a small set of bundled sample datasets, so no external data feed is needed.

```python
from backtest360 import Client, Strategy

client = Client()  # reads BACKTEST360_API_KEY from the environment

df = client.sample_data("BTC")                      # bundled daily OHLCV
result = client.backtest(Strategy.rsi_threshold_long(), df)

print(result.stats["sharpe"])       # e.g. 1.42
print(result.stats["max_drawdown"]) # e.g. -0.42
result.summary()                    # compact human-readable report
```

Statistics are keyed by stable metric id (`sharpe`, `max_drawdown`, `cagr`, …). See [Result Anatomy](concepts/result-anatomy.md) for the full shape of a result.

## Where to start

- **[Installation](getting-started/install.md)** — install the client and verify it.
- **[Authentication](getting-started/authentication.md)** — get, configure, and rotate your API key.
- **[Your first backtest](getting-started/first-backtest.md)** — an end-to-end walkthrough.
- **[Client Reference](client-reference/client.md)** — the full `Client`, `Strategy`, and `Result` API.
- **[API Reference](api-reference/index.md)** — the interactive REST browser.

## Core concepts

- **[Strategy shape](concepts/strategy-shape.md)** — how a strategy document is put together.
- **[Signal vs execution](concepts/signal-vs-execution.md)** — how a signal becomes a fill, and the lag-1 rule.
- **[Result anatomy](concepts/result-anatomy.md)** — every field on a `Result`, and how the metrics are keyed.

## Need help?

Questions or feedback? Email [hello@backtest360.com](mailto:hello@backtest360.com). Found a bug or want a feature? [Open an issue on GitHub](https://github.com/Backtest360/backtest360/issues).
