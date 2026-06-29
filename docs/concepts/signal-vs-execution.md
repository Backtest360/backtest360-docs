# Signal vs Execution

The engine separates *when a signal fires* from *how that signal is filled*.

## Signal frequency

`signal_frequency` controls how often the engine re-evaluates your condition expressions:

- `"daily"` — conditions evaluated once per day (most common)
- `"hourly"`, `"4h"`, `"weekly"` — other bar resolutions

```python
from backtest360 import Execution

Execution(signal_frequency="daily")
```

## Entry and exit anchors

`entry` and `exit` control which price is used to fill:

| Anchor | Meaning |
|--------|---------|
| `"open"` | Fill at the next bar's open (default for entry) |
| `"close"` | Fill at the current bar's close (default for exit) |
| `"vwap"` | Fill at VWAP when available |

A `signal=1` at bar D's close means: look for a fill on bar D+1 at the entry anchor price.

## The lag-1 rule

The engine enforces a strict no-look-ahead rule: any value informing a decision at bar D uses only data through `close[D-1]`. Signals are shifted one bar before the fill loop runs. This prevents the most common form of backtest optimism.

## Example: open-to-close daily

```python
from backtest360 import Client, Strategy, Execution

result = Client(api_key="b360_...").backtest(
    Strategy.rsi_threshold_long(), df,
    execution=Execution(
        signal_frequency="daily",
        entry="open",
        exit="close",
    ),
)
```

## Fill model

`entry_fill` and `exit_fill` control how the fill price is simulated within the bar (each defaults to `"exact"`):

| Value | Behaviour |
|-------|-----------|
| `"exact"` | Exactly at the anchor price (default) |
| `"worst"` | Worst price in the bar (high for longs, low for shorts) |
| `"best"` | Best price in the bar |
| `"random"` | A random price within the bar's range — useful for fill-sensitivity analysis (seeded by `random_seed`) |

## Entry and exit windows

`entry_window` and `exit_window` allow multi-bar fill attempts. A value of `2` means the engine retries for up to 2 bars after the signal before skipping the trade.
