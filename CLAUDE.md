# backtest360-docs

Documentation site for Backtest360 at `docs.backtest360.com`.

> **Upstream:** [backtest360-engine](https://github.com/Backtest360/backtest360-engine) · [backtest360-client](https://github.com/Backtest360/backtest360-client)

## CARDINAL SESSION RULE — EVERY SESSION RUNS IN ITS OWN WORKTREE

Every session that will commit MUST open its own worktree on its own branch. Never commit from the plain clone root.

```bash
git worktree add ~/Code-docs-<topic> -b backtest360-docs/<topic>
cd ~/Code-docs-<topic>
git branch --show-current   # sanity-check
```

Start-of-session: run `git worktree list`, flag any open non-main worktrees.
End-of-session: alert if any worktree is still open (PR not merged).

Full discipline: see [backtest360-engine CLAUDE.md](https://github.com/Backtest360/backtest360-engine/blob/main/Projects/Backtest360/CLAUDE.md).

## Sync rules

| Source changes | Action in this repo |
|---|---|
| Engine release vX.Y.Z | Fetch new openapi snapshot → `docs/openapi/vX.Y.Z.json`, update `api-reference/index.md` src path |
| `backtest360-client` release | Update `requirements.txt` pin |
| New SDK public method | No action — mkdocstrings auto-picks from installed wheel |

## Verification gate

`mkdocs build --strict` must exit 0 before any PR is merged. Broken links or missing mkdocstrings targets fail the build.

## Out of scope

Admin API docs (`docs/internal/API_SPEC_ADMIN.md`) live in the engine repo and are never published here.
