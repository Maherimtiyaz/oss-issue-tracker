# 12 — Backup pool (marimo / openllmetry / llama_index) + how to keep it full

| | |
|---|---|
| Status | Fallback targets — use when a listed issue gets claimed or you want a change of pace |
| Priority | P4 |

## marimo-labs/marimo — fresh small bugs (pick ONE, verify unclaimed)

- [#11174](https://github.com/marimo-team/marimo/issues/11174) · [#11173](https://github.com/marimo-team/marimo/issues/11173) ·
  [#11172](https://github.com/marimo-team/marimo/issues/11172) — small, clearly-scoped issues created Oct 2026 (a batch;
  pick whichever still has 0 comments and no linked PR).

Marimo is a hot project (Jupyter-alternative) with huge momentum — any merge there is visible.

## openllmetry/traceloop-sdk #137 — `trace_content` flag

- Link: https://github.com/openllmetry/traceloop-sdk/issues/137
- Older; treat as lower priority — confirm still open + unclaimed before starting. It's a tracing-content toggle
  (opt-in/opt-out of recording prompts/responses).

## run-llama/llama_index #23246 — the only remaining *labeled* good-first-issue

- Link: https://github.com/run-llama/llama_index/issues/23246
- Note: `llama_index #23339` (rank_llm) is DEAD — maintainer said it needs an upstream fix in castorini/rank_llm —
  **do not** pick it.

## How to refill this file (weekly, ~5 min)

Most labeled good-first-issues are claimed within days. The unlabeled pool is where the wins are. Per repo:

```
is:issue is:open language:Python -linked:pr -label:"good first issue" created:>2026-10-01 repo:<owner>/<repo>
```

Example verified totals (Oct 2026): litellm 503 · vLLM 1,126 (≈90% GPU/kernel — skip those) ·
langgraph+crewAI 124 · langchain+autogen 93.

Saturated proof (don't bother): crewAI #8009/#8011, dify #43358, airflow #72265, openllmetry #417.

**Wanted shape**: unassigned, ~0 comments, explicit expected behavior, self-contained mockable repro,
backend/API/logging/parsing (not GPU). Also check the sibling crewAI "student project" issues #8005/#8007/#8008 —
only take one track at a time.