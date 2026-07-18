# Client

`Client` is the entry point to the engine. Construct one with an API key — passed
directly or read from the `BACKTEST360_API_KEY` environment variable — and call its
methods to discover indicators, validate strategies, and run backtests.

```python
from backtest360 import Client

client = Client()   # reads BACKTEST360_API_KEY and BACKTEST360_ENGINE_URL
```

The base URL defaults to `https://api.backtest360.com` and can be overridden with the
`base_url` argument or the `BACKTEST360_ENGINE_URL` variable. See
[Authentication](../getting-started/authentication.md) to obtain a key.

## Methods at a glance

| Method | Purpose |
|--------|---------|
| `version()` | The engine version and contract. |
| `me()` | The calling key's identity, plan, and limits. |
| `list_indicators()` | The indicators the engine offers. |
| `list_templates(name=None, compact=True)` | The built-in strategy templates. |
| `sample_symbols()` | A short list of symbols with bundled sample data. |
| `sample_data(symbol="SPY")` | An OHLCV frame of sample data for quick experiments. |
| `validate_strategy(strategy)` | Validate a strategy document without running it. |
| `latest_signal(...)` | The most recent signal a strategy would produce. |
| `backtest(strategy, ohlcv, ...)` | Run a backtest from a `Strategy` and an OHLCV frame. Returns a `Result`. |
| `backtest_signals(signals, ohlcv, ...)` | Run a backtest from a precomputed signal series. Returns a `Result`. |
| `backtest_raw(payload)` | Run a backtest from a raw request envelope, for full control over the wire shape. |

The full signatures and argument detail follow.

::: backtest360.client.Client
    options:
      show_source: false
      members_order: source

---

## Result

Every backtest returns a `Result`. Its `stats` dictionary is keyed by stable metric ids
by default — `result.stats["sharpe"]`, `result.stats["max_drawdown"]` — and the rest of
its attributes expose the equity curve, trades, and signals as pandas objects.

::: backtest360.client.Result
    options:
      show_source: false
      members_order: source
