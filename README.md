# backtest360-docs

Source for the Backtest360 public documentation site at [docs.backtest360.com](https://docs.backtest360.com).

## Local build

```bash
pip install -r requirements.txt
mkdocs serve        # → http://localhost:8000
mkdocs build --strict   # must exit 0 before any PR merge
```

## Keeping things in sync

The API-reference OpenAPI snapshot (`docs/openapi/`, `docs/api-reference/`) and the
`backtest360` pin in `requirements.txt` are kept current automatically — these are
generated artifacts and should not be hand-edited; a sync proposes the updates for review on
each engine/client release. The client reference pages are rendered by `mkdocstrings` from the
installed `backtest360` wheel, so a fresh build picks up new public methods on its own.

## GitHub Pages setup (one-time)

1. Enable Pages: **Settings → Pages → Source: Deploy from a branch → gh-pages**
2. Set custom domain: `docs.backtest360.com`
3. Enforce HTTPS

DNS: `docs.backtest360.com` CNAME → `Backtest360.github.io`

Optional: Cloudflare Page Rule `backtest360.com/docs/*` → 301 → `https://docs.backtest360.com/$1`.

## Questions / feedback

Questions or feedback? hello@backtest360.com — we read everything.

Bug reports and feature requests: [open an issue on GitHub](https://github.com/Backtest360/backtest360-docs/issues).
