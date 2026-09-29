## Summary
Saving a Library note, or renaming a Library file, with a slash in the name succeeds but drops everything before the last slash. The Save to Library dialog sends the typed title through unchanged (`title: v.trim()` into `POST /v1/library/notes`). The API answers 201 and stores only the final segment, plus `.md` when the title did not already end in it. `PATCH /v1/library/{id}` with `filename` does the same on rename. The Library then shows a different name from the one the user entered, with no error.

## Expected
A title such as `Budget/Q3-ZEPHYR` should be kept as the visible filename (slash allowed, or replaced with a safe character), or the request should fail with 422 and the previous name left unchanged. The name in the Library should match what was typed.

## Actual
1. Save to Library posts `{text, title}` with the trimmed title and does not strip slashes.
2. `POST /v1/library/notes` `{"title":"Budget/Q3-ZEPHYR","text":"quarterly note body"}` → 201, `filename` is `Q3-ZEPHYR.md` (id `lbf_01M3G6M9EQFQ8VVHM7CY1NRVVE`, request_id `req_01a0e06a257f7f7385dd92c89ddd136d`).
3. Same call with title `Q3/report` stores `report.md`. Title `a/b/c` stores `c.md`. A backslash is treated the same way (`Q3\report` → `report.md`).
4. `PATCH /v1/library/lbf_01M3G6M9EQFQ8VVHM7CY1NRVVE` `{"filename":"Other/Renamed-ZEPHYR.md"}` → 200, `filename` is `Renamed-ZEPHYR.md`.

## Impact
Notes and renames whose names include a slash (quarters, dates, fractions, folder-like labels) show up in the Library under the wrong name. Search by the prefix the user typed does not find them. Two different titles that share a last segment (`Budget/report` and `Tax/report`) both become `report.md`.

## Environment
- https://app.cortex.foundation / https://api.cortex.foundation
- Guest session is enough
- Reproduced 2026-09-27

This is not the chat-turn failure for messages that contain `../` (that route errors). Ordinary titles with a single slash are accepted and saved under a shortened name.
