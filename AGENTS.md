# AGENTS.md — oss-issue-tracker

Personal tracker for Mahek's open-source contributions (backend Python / FastAPI / AI). Mirrors the GitHub repo
`oss-issue-tracker` but is edited locally at `D:\oss-tracker` and pushed by me — **this environment has no GitHub
credentials**, so the remote/repo and PR pushes are done by the user from commands I provide.

## Structure and source of truth

- `README.md` = master list. The `| # | Repo / Issue | ... | Priority |` row is the canonical status view.
- `issues/NN-slug.md` = one file per issue (or issue cluster). Contains a `Status` line in its metadata table.
- **Both must stay in sync.** When an issue's status changes, edit BOTH the README row's "Priority/notes" (status lives
  in the issue file; README holds priority + a short status hint) and the issue file's `Status` line. Update README
  title line + Priority box for anything that moves to a different tier.

## Status lifecycle (user-defined workflow)

```
listed → claimed (user said "taking this") → fix-local (local fix done) → pr-open (PR pushed) → merged
```

- `listed`: default in `issues/NN.md` Status.
- `claimed`: user commits to the issue (comment "taking this"). Mark on the day they act.
- `fix-local`: local reproduction + fix verified, PR not pushed.
- `pr-open`: user pushed the PR. Record PR URL in the issue file.
- `merged`: PR merged. Record the merge/link.
- `dead`/`blocked`: use for issues the user should skip (e.g. preempted by another PR, maintainer said no).

Update the tracker (edit → `git commit` → user pushes) whenever the user reports they have acted, solved, or merged.

## Repo workflow

- Branch: `main`. Global git identity (`user.name`/`user.email`) is already configured.
- Commit style: short imperative subject matching the change, e.g. `Mark autogen #8337 as claimed`.
- On Windows, harmless `LF will be replaced by CRLF` warnings appear on every add/commit — ignore.
- No remote configured. First push:
  ```powershell
  cd D:\oss-tracker
  git remote add origin https://github.com/<USERNAME>/oss-issue-tracker.git   # repo must be created on github.com/new first
  git push -u origin main
  ```

## Issue-file conventions (keep uniform)

Metadata table at top: `Link | Status | Priority | Difficulty | Effort`. Sections: `## Problem`, `## Approach`,
`## Verification`, `## Notes`, `## Next action`. List exact test commands per repo (from `## Verification` of each
file) — these were verified during source research.

## Facts an agent must know before acting

- **Re-verify unclaimed before starting any issue.** Search:
  `is:issue is:open language:Python -linked:pr -label:"good first issue" created:>2026-10-01 repo:<owner>/<repo>`
  (unauthenticated API ≈10 searches/min — space them). Filter: unassigned, ~0 comments, mockable self-contained repro.
- The autogen #8337 fix + test are DONE and live in a **temp clone** `C:\Users\Lenovo\AppData\Local\Temp\opencode\oss\autogen\autogen`
  (env already installed there: `python/\.venv`, editable autogen-core/agentchat/ext[openai]/test-utils, pytest,
  pytest-asyncio, httpx, ruff==0.4.8). **Copy to `D:\autogen` before that temp dir is cleaned**, then branch/commit/push
  per `issues/01-autogen-8337.md`.
- simonw/llm problems are solved tests-first with `pytest` + formatting via `black`; run `uv sync` first.
- `vLLM` pool ≈90% GPU/kernel work — skip; only API/schema/docs-shaped issues.
- Weekly pool refresh: update `issues/12-backups.md`, that file documents the scan query and known-saturated issues.