# 01 — microsoft/autogen #8337 — Streamed tool-call names not normalized

| | |
|---|---|
| Link | https://github.com/microsoft/autogen/issues/8337 |
| Status | **Solution complete locally, PR not yet pushed** |
| Priority | P0 — first PR |
| Difficulty | Easy |
| Effort | Done (was ~½ day) |

## Problem

`create()` applies `normalize_name()` to tool-call names, but `create_stream()` returns the raw provider name.
Same API response → `"get weather!"` (streamed) vs `"get_weather_"` (non-streamed). Raw names can violate
`^[a-zA-Z0-9_-]{1,64}$`, so replaying the turn on the next request can be rejected with 400.

## Fix (already applied, verified)

File: `python/packages/autogen-ext/src/autogen_ext/models/openai/_openai_client.py` (in `create_stream`)

```python
if full_tool_calls:
    # This is a tool call response
    content = [
        FunctionCall(id=tc.id, arguments=tc.arguments, name=normalize_name(tc.name))
        for tc in full_tool_calls.values()
    ]
```

Regression test added: `test_create_stream_normalizes_tool_call_name`
in `python/packages/autogen-ext/tests/models/test_openai_model_client.py`
(mirrors `test_r1_reasoning_content_streaming` mock pattern; fails before / passes after).

## Verification already done

- Repro script (mocks the OpenAI API, no key): before `match = False`, after `match = True`.
- `pytest packages/autogen-ext/tests/models/test_openai_model_client.py::test_create_stream_normalizes_tool_call_name` → 1 passed.
- Full module: 41 passed + 1 new, identical 13 pre-existing failures also present on clean checkout
  (they require a real `OPENAI_API_KEY` integration env — unrelated).
- `ruff format` + `ruff check` on both files → clean (repo rules: line-length 120).

## Push / PR instructions

Clone exists at `C:\Users\Lenovo\AppData\Local\Temp\opencode\oss\autogen\autogen` — move it somewhere stable first.

```powershell
Move-Item "C:\Users\Lenovo\AppData\Local\Temp\opencode\oss\autogen\autogen" "D:\autogen"
cd D:\autogen
git remote add fork https://github.com/<YOUR_USERNAME>/autogen.git   # after forking microsoft/autogen on GitHub
git checkout -b fix-stream-toolcall-name-normalization
git add python/packages/autogen-ext/src/autogen_ext/models/openai/_openai_client.py
git add python/packages/autogen-ext/tests/models/test_openai_model_client.py
git commit -m "fix(autogen-ext): normalize streamed tool-call names in OpenAI create_stream"
git push -u fork fix-stream-toolcall-name-normalization
```

PR body — first line must be `Fixes #8337`, then:

```
create() applied normalize_name() to tool-call names but create_stream() accumulated
them raw and never normalized, so an identical API response produced
FunctionCall.name = "get weather!" (streamed) vs "get_weather_" (non-streamed).

Fix: build the final tool-call content in create_stream() through normalize_name(),
mirroring create().

Test test_create_stream_normalizes_tool_call_name fails before / passes after
("get weather!" -> "get_weather_").
```

Then comment "Taking this" on https://github.com/microsoft/autogen/issues/8337.

## Clincheck

- Autogen repo is a uv workspace (`python/packages/*`). Local env already installed:
  `uv venv .venv --python 3.12` + editable `-e packages/autogen-core -e packages/autogen-agentchat
  -e "packages/autogen-ext[openai]" -e packages/autogen-test-utils` + `pytest pytest-asyncio httpx ruff==0.4.8`.
- Tests that pass `api_key=""` need `OPENAI_API_KEY` set (same as CI). My new test uses `api_key="test-key"` and
  runs anywhere.