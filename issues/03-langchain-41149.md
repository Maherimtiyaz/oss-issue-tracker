# 03 — langchain-ai/langchain #41149 — `XMLOutputParser` drops valid elements

| | |
|---|---|
| Link | https://github.com/langchain-ai/langchain/issues/41149 |
| Status | Unclaimed (unassigned, no linked PR, created Oct 2026) |
| Priority | P1 — recommended #3 |
| Difficulty | Easy |
| Effort | ~½ day |

## Problem

`XMLOutputParser.parse()` drops valid elements from the output when an element has an
XML `encoding` attribute in its declaration followed by a newline (i.e. a valid document
like `<?xml version="1.0" encoding="utf-8"?>\n<foo>...</foo>` is mishandled and the parse loses content).

Package: `langchain-core` (checkbox in the issue). This is a pure parser + unit-test fix —
ideal quick merge into LangChain core.

## Approach

1. Clone langchain (monorepo: `libs/core`), `cd libs/core && pip install -e ".[test]"` (`uv` works too).
2. Reproduce with the issue's self-contained example (it ships one — copy/paste straight from the issue).
3. Find `XMLOutputParser` (under `libs/core/langchain_core/output_parsers/`) and the existing
   `tests/unit_tests/output_parsers/test_xml.py` file.
4. Fix the regex/parse step that strips or mishandles the declaration line; make sure you only remove the
   declaration node, never valid body elements.
5. Add the exact issue case to `test_xml.py`.

## Verification

- `pytest tests/unit_tests/output_parsers/test_xml.py -q`
- Run the wider `libs/core` output-parser tests to catch regressions.

## Notes

- Regression-proof: the element in question must survive end-to-end (`parse` output fully round-trips from
  `to_string` → `parse`).

## Next action

Clone, reproduce, confirm unclaimed, then `git checkout -b fix-xml-outputparser-encoding`.