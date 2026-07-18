# Signal vs Execution

The engine separates two questions that backtests often conflate: *when a signal fires* and *how that signal is filled*. Your strategy document decides the first; an `Execution` config decides the second.

## Signal frequency

`signal_frequency` controls how often the engine re-evaluates your condition expressions:

- `"daily"` — evaluated once per day (the default, and most common).
- `"hourly"`, `"4h"`, `"weekly"` — other bar resolutions.

```python
from backtest360 import Execution

Execution(signal_frequency="daily")
```

## Entry and exit anchors

`entry` and `exit` set which price a fill uses:

| Anchor | Meaning |
|--------|---------|
| `"open"` | Fill at the next bar's open (the default for entry). |
| `"close"` | Fill at the current bar's close (the default for exit). |
| `"vwap"` | Fill at VWAP, when available. |

A `signal = 1` at bar D's close means: look for a fill on bar D+1 at the entry anchor price.

## The lag-1 rule

The engine enforces a strict no-look-ahead discipline: any value informing a decision at bar D uses only data through `close[D-1]`. Signals are shifted one bar before the fill loop runs. This closes off the most common source of backtest optimism — acting on information the strategy could not yet have had.

## Example: open-to-close daily

```python
from backtest360 import Client, Strategy, Execution

result = Client().backtest(
    Strategy.rsi_threshold_long(), df,
    execution=Execution(
        signal_frequency="daily",
        entry="open",
        exit="close",
    ),
)
```

## Fill model

`entry_fill` and `exit_fill` control how the fill price is drawn from within the bar. Each defaults to `"exact"`.

| Value | Behavior |
|-------|----------|
| `"exact"` | Exactly at the anchor price (default). |
| `"worst"` | The worst price in the bar — high for longs, low for shorts. |
| `"best"` | The best price in the bar. |
| `"random"` | A random price within the bar's range, for fill-sensitivity analysis. Seeded by `Settings(random_seed=...)` for reproducibility. |

## Entry and exit windows

`entry_window` and `exit_window` allow multi-bar fill attempts. A value of `2` means the engine retries for up to two bars after the signal before abandoning the trade. The default, `0`, fills on the target bar only.

## See also

- [Set stops and risk limits](../how-to/set-stops.md) — stop-losses, drawdown circuit-breakers, and re-entry rules.
- [Result anatomy](result-anatomy.md) — where the resulting fills and signals show up on a `Result`.
