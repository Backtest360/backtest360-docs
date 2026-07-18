# Strategy

`Strategy` builds the strategy document the engine runs. Start from a template — for
example `Strategy.rsi_threshold_long()` or `Strategy.ma_crossover()` — or compose one
from indicators with `Strategy.indicator(...)`. The configuration dataclasses below
(`Execution`, `Costs`, `Risk`, `Sizing`) tune how a strategy is executed and are passed
to `Client.backtest()` as keyword arguments.

::: backtest360.strategy.Strategy
    options:
      show_source: false
      members_order: source

---

## Execution

::: backtest360.strategy.Execution
    options:
      show_source: false

---

## Costs

::: backtest360.strategy.Costs
    options:
      show_source: false

---

## Risk

::: backtest360.strategy.Risk
    options:
      show_source: false

---

## Sizing

::: backtest360.strategy.Sizing
    options:
      show_source: false
