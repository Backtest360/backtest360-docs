# backtest360-docs

Source for the Backtest360 public documentation site at [docs.backtest360.com](https://docs.backtest360.com).

## Local build

```bash
pip install -r requirements.txt
mkdocs serve        # → http://localhost:8000
mkdocs build --strict   # must exit 0 before any PR merge
```

## Keeping things in sync

| Trigger | Action |
|---------|--------|
| New engine release (vX.Y.Z) | Fetch `openapi.json` snapshot from the engine, add as `docs/openapi/vX.Y.Z.json`, update `api-reference/index.md` src path and `mkdocs.yml` if needed |
| New SDK release | Update `requirements.txt` pin for `backtest360-client` |
| New public SDK method | Rebuild — `mkdocstrings` picks it up automatically from the installed wheel |

## GitHub Pages setup (one-time)

1. Enable Pages: **Settings → Pages → Source: Deploy from a branch → gh-pages**
2. Set custom domain: `docs.backtest360.com`
3. Enforce HTTPS

DNS: `docs.backtest360.com` CNAME → `Backtest360.github.io`

Optional: Cloudflare Page Rule `backtest360.com/docs/*` → 301 → `https://docs.backtest360.com/$1` (no Replit code change needed).
