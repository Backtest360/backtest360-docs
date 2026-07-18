# Connect your AI assistant

Backtest360 exposes a [Model Context Protocol](https://modelcontextprotocol.io) (MCP)
server, so an AI assistant or agent can use the engine's tools directly — discover
indicators, build and validate strategies, run backtests, and read the results — in a
single conversation.

It's the same engine and the **same API key** as the REST API and Python client. The MCP server
contains no AI of its own; it's a thin adapter that forwards your requests to the engine
and shapes the responses for the assistant.

## Two ways to connect

### Hosted (recommended)

Point your MCP client at the hosted endpoint over HTTP and send your key in an
`X-API-Key` header:

- **URL:** `https://mcp.backtest360.com/mcp`
- **Header:** `X-API-Key: b360_...`

Most MCP clients accept a configuration like this:

```json
{
  "mcpServers": {
    "backtest360": {
      "type": "streamable-http",
      "url": "https://mcp.backtest360.com/mcp",
      "headers": {
        "X-API-Key": "b360_..."
      }
    }
  }
}
```

Add that to your client's MCP configuration, restart the client, and the Backtest360
tools become available. Some MCP clients also offer a CLI to register an HTTP server in
a single step — check your client's documentation for the exact command.

### Local

Prefer to run the server yourself? Install it and let your MCP client launch it over
stdio — most desktop MCP clients support this through their JSON configuration file:

```bash
pip install backtest360-mcp
```

```json
{
  "mcpServers": {
    "backtest360": {
      "command": "backtest360-mcp",
      "env": {
        "BACKTEST360_API_KEY": "b360_..."
      }
    }
  }
}
```

`BACKTEST360_API_KEY` is the only required variable. The rest are optional:

| Variable | Default | Purpose |
|----------|---------|---------|
| `BACKTEST360_ENGINE_URL` | `https://api.backtest360.com` | The engine the server forwards to. |
| `BACKTEST360_MCP_TIMEOUT` | `300` | Per-request timeout, in seconds. |
| `BACKTEST360_MCP_MAX_OUTPUT_BYTES` | `100000` | Cap on the size of a tool response. |

The local server talks to the same engine; your key governs access either way.

## Authentication

The MCP server uses the same `b360_` key as everything else. It never stores the key —
it forwards it per request, and the engine enforces your plan's permissions, rate limits,
and quotas on every call. See [Authentication](../getting-started/authentication.md) to
get a key.

## What the assistant can do

The server exposes the engine as a set of tools — see [Tools](tools.md) for the full
list. A typical session: discover what exists, build a strategy, validate it, run a
backtest, read the numbers — the assistant chains the tools for you.

> All numbers come from the engine. The server never computes or estimates results; it
> only forwards your requests and shapes what the engine returns.

## Next

[Browse the tools →](tools.md)
