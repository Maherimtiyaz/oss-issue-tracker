# Open Source Issue Tracker — Mahek Imtiyaz

Tracker for unclaimed open-source issues matched to my background (backend Python / FastAPI / AI), prioritized for
fast merges, portfolio visibility, and intern / backend-engineer opportunities.

Repo: `D:\oss-tracker`  —  push to GitHub as `oss-issue-tracker`.

## Master list

| # | Repo / Issue | Title (short) | Priority | Difficulty | Effort |
|---|--------------|---------------|----------|-----------|--------|
| 01 | microsoft/autogen [#8337](https://github.com/microsoft/autogen/issues/8337) | `create_stream()` skips `normalize_name()` on tool-call names | P0 — **solution done, PR ready** | Easy | done |
| 02 | dottxt-ai/outlines [#2070](https://github.com/dottxt-ai/outlines/issues/2070) | Mistral adapter broken with `mistralai >= 3.0.0` | P1 — **claimed** | Easy | ~½ day |
| 03 | langchain-ai/langchain [#41149](https://github.com/langchain-ai/langchain/issues/41149) | `XMLOutputParser` drops valid elements with encoding attrs | P1 | Easy | ~½ day |
| 04 | langchain-ai/langgraph [#9241](https://github.com/langchain-ai/langgraph/issues/9241) | Sync SSE handshake leak exhausts HTTP connection pool | P1 | Medium | ~1 day |
| 05 | BerriAI/litellm [#45787](https://github.com/BerriAI/litellm/issues/45787) | URL containing "predict" misrouted to Vertex handler, kills logging | P2 | Medium | ~1 day |
| 06 | crewAIInc/crewAI [#8006](https://github.com/crewAIInc/crewAI/issues/8006) | Student project: LLM-provider behavioral compat tests (3 PRs) | P2 | Medium | 1–2 wks |
| 07 | BerriAI/litellm [#45541](https://github.com/BerriAI/litellm/issues/45541) | `post_call` keeps serialized copy of every response (~22 MB) | P3 | Medium+ | 1–2 days |
| 08 | simonw/llm [#1720](https://github.com/simonw/llm/issues/1720) | `batch_size=0` silently no-ops in `embed_multi` | P3 | Easy | 1–2 days |
| 09 | simonw/llm [#1697](https://github.com/simonw/llm/issues/1697) / [#1722](https://github.com/simonw/llm/issues/1722) | `--no-stream` AttributeError; `embed_multi` zip mismatch | P3 | Easy | ~1 day |
| 10 | huggingface/smolagents [#2951](https://github.com/huggingface/smolagents/issues/2951) / [#2924](https://github.com/huggingface/smolagents/issues/2924) | Unparseable tool-call args; `--max-steps` CLI flag | P3 | Med/Easy | 1–2 days |
| 11 | pydantic/pydantic-ai [#10005](https://github.com/pydantic/pydantic-ai/issues/10005) | `dict[Literal[...], float]` probability-mapping typing | P4 | Easy | ~1 day |
| 12 | Backups (marimo / openllmetry / llama_index) | Fallback pool | P4 | Easy | — |

## Order of attack (why)

1. **#01 autogen (DONE)** — named-repo first PR, reviewer relationship starts here.
2. **#02 outlines** then **#03 langchain** — two fast, easy merges in the same week build contributor credit.
3. **#04 langgraph** — first real backend fix with a resource-leak story (sync vs async).
4. **#06 crewAI** — the 3-PR student track; sustain a relationship with one reviewer.
5. **#05 litellm** as fallback/interleave; **#07 litellm (memory)** is the best "viral write-up" material — do it once comfortable.

Rule: comment "taking this" on the issue the same day you start. If someone claims before your first commit, move to the
next row instead of fighting.

## Keep the pool fresh (weekly scan)

Most *labeled* good-first-issues are already claimed. The unlabeled-but-detailed pool is where the wins are.
Search per repo with (unauthenticated GitHub API, max 10 search req/min):

```
is:issue is:open language:Python -linked:pr -label:"good first issue" created:>2026-10-01 repo:<owner>/<repo>
```

Verified-pool insight (Oct 2026): unlinked counts = litellm 503, vllm 1,126 (mostly GPU/kernel — skip),
langgraph+crewAI 124, langchain+autogen 93. Saturated examples: crewAI #8009/#8011, dify #43358, airflow #72265,
openllmetry #417.

Filter for: unassigned, ~0 comments, explicit expected behavior, a self-contained reproduction that can run with mocks,
and a backend/API/logging shape. Skip anything GPU/kernel/cuda for now.

## Rules of engagement

- First PR **tests-only** = preferred when possible, else minimal behavior fix + regression test.
- Verify every claim before starting (unassigned + `-linked:pr` + no one said "taking this").
- Run tests + formatter/linter of the repo before opening the PR (`uv run pytest`, `ruff`, etc.).
- Virality comes from the write-up: 300-word story (silently-broken → found → before/after → link), repeated in one
  repo so reviewers know your name.

## Per-issue details

Each issue has its own file in `issues/` with full approach + verification commands.