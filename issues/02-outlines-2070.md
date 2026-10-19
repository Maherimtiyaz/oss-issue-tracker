# 02 — dottxt-ai/outlines #2070 — Mistral adapter dead with `mistralai >= 3.0.0`

| | |
|---|---|
| Link | https://github.com/dottxt-ai/outlines/issues/2070 |
| Status | **claimed** (2026-10-19) — unassigned, no linked PR |
| Priority | P1 — recommended #2 after autogen PR |
| Difficulty | Easy |
| Effort | ~½ day |

## Problem

The Mistral integration in outlines 1.x imports symbols that no longer exist in `mistralai>=3.0.0`
(the import path moved to `mistralai.client.Mistral` / `mistralai.client.models`), so the adapter is dead on
arrival with the current unpinned extra.

Key facts from research:
- `pyproject.toml:48` — extra `mistral = ["mistralai"]` is **unversioned** (let `>=3.0.0` slip in; this is what broke
  the adapter).
- `pyproject.toml:75` — the test suite's mistral dependency entry.

## Approach

1. Clone + setup (outlines is Poetry/uv-managed; use `uv sync --extra mistral`, or `pip install -e .[mistral]`).
2. Confirm the break: `from outlines.integrations.mistralai import ...` (or wherever the adapter lives) against
   `mistralai==3.*` → ImportError.
3. Bump the extra to the newest known-good `mistralai` (`>=3.0.0,<4` after verifying) and update the test dep.
4. Update the adapter's imports to the new `mistralai.client` paths; run the mistral test file.
5. Add a `mistralai` version-pinning regression note/test if the suite has a CI matrix (some repos run the adapters
   against installed SDK versions).

## Verification

- `pytest tests/integrations/mistralai/ -q` (path per repo layout).
- `uv pip install "mistralai>=3.0.0"` in a fresh env + import the adapter directly.

## Notes

- Outlines = typed structured-generation library used widely; a small well-scoped dependency fix merges fast.
- Double-check the exact import move first — the issue text names `mistralai.client.Mistral` / `mistralai.client.models`.