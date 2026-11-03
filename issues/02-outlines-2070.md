# 02 — dottxt-ai/outlines #2070 — Mistral adapter dead with `mistralai >= 3.0.0`

| | |
|---|---|
| Link | https://github.com/dottxt-ai/outlines/issues/2070 |
| Status | **fix-local** (2026-11-03) — fix + regression test verified on `mistralai` 1.9.11 and 3.6.0; PR not pushed |
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

Chosen: **Option A — dual-surface import shim, no dependency/lockfile change** (decided 2026-11-03).

Why not Option B (bound the extra + rewrite imports): CI runs `uv sync` and installs the locked `mistralai==1.9.11`
(`uv.lock:1576`), where the *new* paths do not exist. Rewriting imports would break CI unless the lockfile is
regenerated (large, risky diff) and would drop 1.x users — a maintainer call, not ours.

1. Added `_mistral_sdk()` in `src/outlines/models/mistral.py`: tries the 3.x layout
   (`mistralai.client.Mistral`, `mistralai.client.models.*`) and falls back to the 1.x top-level names.
2. Replaced the four import sites (`format_input(str/list/Chat)`, `from_mistral`) with calls to it, keeping the
   imports lazy (mistralai must not be imported unless the backend is used).
3. Applied the same shim to the two test modules' top-level SDK imports, so they collect on 1.x **and** 3.x.
4. Added regression test `test_mistral_sdk_imports_support_installed_layout`.

## Verification

- Targeted (no network):
  `pytest tests/models/test_mistral.py tests/models/test_mistral_type_adapter.py -m "not api_call" -q`
  → **23 passed** on both `mistralai==1.9.11` (CI parity) and `mistralai==3.6.0`.
- End-to-end on 3.x (was `ImportError` before the fix): `MistralTypeAdapter().format_input(...)`,
  `from_mistral(Mistral(api_key="x"))` in a venv with latest `mistralai` → all OK.
- Style gate: `ruff check --config=pyproject.toml <files>` (ruff 0.9.1) → clean;
  `mypy --allow-redefinition src/outlines/models/mistral.py` (mypy 1.14.1, **no** mistralai installed — mirrors the
  pre-commit hook env) → clean for `mistral.py`; no trailing whitespace, files end with newline.
- Full CI: `uv sync --no-group test-gpu --extra test` then `pytest -m "not api_call"` (needs the heavy test extra —
  not run locally).

## Notes

- Outlines = typed structured-generation library used widely; a small well-scoped dependency fix merges fast.
- Double-check the exact import move first — the issue text names `mistralai.client.Mistral` / `mistralai.client.models`.