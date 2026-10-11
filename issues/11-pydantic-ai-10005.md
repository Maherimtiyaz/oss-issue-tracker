# 11 — pydantic/pydantic-ai #10005 — `dict[Literal[...], float]` probability mappings

| | |
|---|---|
| Link | https://github.com/pydantic/pydantic-ai/issues/10005 |
| Status | Unclaimed (unassigned, no linked PR) |
| Priority | P4 — good "typing/robustness" contribution |
| Difficulty | Easy |
| Effort | ~1 day |

## Problem

Code that models probability mappings as `dict[Literal["a","b"], float]` (e.g. classification probabilities keyed by
a fixed set of labels) doesn't validate/behave as expected with Pydantic AI's model schemas — the issue details where
the literal-keyed dict typing is not enforced like users expect (schema generation / validation edge case).

## Approach

1. Clone `pydantic/pydantic-ai`; `uv sync`; find the schema/validation code for `dict[Literal[...], float]`
   (model-schema generation in `pydantic_ai`'s schema module or `pydantic` core).
2. Reproduce exactly with the issue's snippet (it should include the minimal failing case).
3. Add a regression test in the appropriate `tests/` (protocols/schema tests) capturing the expected behavior.
4. Fix at the schema-generation layer (likely default-key handling for literal-constrained dicts).

## Verification

- New test green + existing `pydantic-ai` schema/validation suite green.
- Run `ruff` on changed files.

## Notes

- pydantic = the backend-validation standard in every FastAPI/AI project you'll apply to — a visible contribution
  here is great signal. Verify the issue is unclaimed before starting.

## Next action

Clone + reproduce; if it's a genuine corner-case bug, claim and fix.