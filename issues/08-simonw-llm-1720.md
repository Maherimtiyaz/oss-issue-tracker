# 08 — simonw/llm #1720 — `batch_size=0` silently no-ops in `embed_multi`

| | |
|---|---|
| Link | https://github.com/simonw/llm/issues/1720 |
| Status | Unclaimed (0 comments, unassigned) |
| Priority | P3 — strong "tests-first Simón Wil­liams" route |
| Difficulty | Easy |
| Effort | 1–2 days |

## Problem

`batch_size=0` (or another invalid batch size) makes `EmbeddingModel.embed_multi` / `Collection.embed_multi`
silently skip embeddings instead of erroring — a silent no-op that can quietly produce an empty collection.
Expected: raise `ValueError` (the same class the codebase already raises for `batch_size < 0`).

## Why this repo is good for you

- Maintainer (Simon Willison) is extremely active; 0-comment precise issues get fixed fast; PRs with a failing test
  first are well received.
- TDD-friendly: `llm/embeddings.py` and `tests/test_embed.py` already exist — `test_embed_huge_list` /
  `test_embed_multi` are already parametrized with `batch_size, expected_batches`.

## Approach

1. Clone `simonw/llm`; setup = `uv sync` (dev deps are in `[dependency-groups] dev`: pytest, httpx2-pytest,
   pytest-asyncio, pytest-recording; `black` targets py310).
2. Write failing tests first in `tests/test_embed.py`: `with pytest.raises(ValueError)` for `batch_size=0`
   (and confirm negative already raises). Run `uv run pytest tests/test_embed.py -q` → new tests fail.
3. Add the guard `if batch_size < 1: raise ValueError(...)` in `llm/embeddings.py`
   (`EmbeddingModel.embed_multi` and `Collection.embed_multi`).
4. Run full suite + `uv run black .`.

## Verification

- `uv run pytest -q` (all green, new tests green).
- Manual: `llm embed_multi --batch-size 0` errors cleanly.

## PR

Title: `Raise ValueError for batch_size=0 in embed_multi`; body first line `Fixes #1720`.

## Notes

- Later/related (same repo, same tests-first style):
  - **#1697** — `llm --no-stream` raises `AttributeError` when the endpoint omits `usage` (cause near
    `openai_models.py:1491` / `:1608`). Fix + test for the streaming-completion path.
  - **#1722** — `Collection.embed_multi` zip mismatch (async batch embedding). Add failing test, fix tuple unpacking.

## Next action

Requires Python + `uv` only. Easiest self-contained first fix after the autogen/langchain PRs land.