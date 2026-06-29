# Handle Errors

All client errors raise `Backtest360Error`. The `status` and `code` attributes tell you what went wrong.

## Pattern

```python
from backtest360 import Client, Backtest360Error

try:
    result = client.backtest(strategy, df)
except Backtest360Error as e:
    if e.status == 401:
        print("Authentication failed:", e.code)
    elif e.status == 422:
        print("Strategy is invalid:", e.body)
    elif e.status == 429:
        print("Rate limited — retry after a moment")
    else:
        raise
```

## Common codes

See [Exceptions reference](../sdk-reference/exceptions.md) for the full list.

## Retrying on 429

```python
import time

for attempt in range(3):
    try:
        result = client.backtest(strategy, df)
        break
    except Backtest360Error as e:
        if e.status == 429 and attempt < 2:
            time.sleep(2 ** attempt)
        else:
            raise
```
