# 04 — langchain-ai/langgraph #9241 — Sync SSE handshake leaks HTTP connection pool

| | |
|---|---|
| Link | https://github.com/langchain-ai/langgraph/issues/9241 |
| Status | Unclaimed (unassigned, no linked PR, labeled `external`) |
| Priority | P1 — first "real backend" fix |
| Difficulty | Medium |
| Effort | ~1 day |

## Problem

When a synchronous SSE handshake fails (`4xx/5xx`), the streaming HTTP response is leaked and never closed.
Repeated failures exhaust the shared `httpx` client's connection pool, so subsequent requests fail even after the
stream context exits — e.g. a `503` handshake.

Root cause (from issue): `SyncProtocolSseTransport.open_event_stream()` raises inside `raise_for_status()`
*before* registering the response for cleanup, so `transport.close()` never releases it.

Key advantage: the **async transport already closes failed responses** — your fix is "make the sync path mirror the
async path" + a regression test.

## Approach

1. Clone `langchain-ai/langgraph` (`libs/sdk-py` is where the SDK lives), `uv sync` + dev deps.
2. Read `langgraph_sdk/stream/transport/sync_http.py` (`SyncProtocolSseTransport.open_event_stream`) and its async
   sibling `async_http.py`.
3. Confirm the leak with the issue's self-contained repro (it uses an `httpx` MockTransport returning a 503 with a
   `ByteStream` and counts connections/fail-on-close).
4. Change the sync transport so the response is closed on any failure path (context-manager or
   `try/finally`/explicit `await response.aclose()`/`resp.close()` before re-raising).
5. Add a regression test that opens several failed handshakes in one `httpx.Client` and asserts the pool stays
   healthy afterward.

## Verification

- `pytest libs/sdk-py/langgraph_sdk -q -k sse` (or wherever the stream tests live).
- The issue's repro before/after: repeated 503 handshakes then a working request.

## Notes

- Backend + HTTP + connection-management = exactly the "backend engineer" story on a portfolio.
- Async fix already exists in-repo → low design risk, reviewer sees "mirrored async behavior".

## Next action

Clone, confirm unclaimed, reproduce, then fix + test.