## Summary
In Chat, the `python` tool dies in a few milliseconds with an internal error, before any code runs. The turn emits `generated_file` status `generating` and then `failed`, and the tool row ends with outcome `error`. The visible detail is: "The `python` tool failed: the tool failed for an internal reason." The label on the row is "Ran Python".

## Expected
A request that needs Python (a calculation, or writing a file) should run the tool and return stdout or a finished file. A guest or plan limit should be a clear entitlement error, not an instant internal failure.

## Actual
1. `POST /v1/conversations/turns` on `cortex-1-mini`, message "Use the python tool to compute 2+2 and reply with only the number." Conversation `cnv_01M3GFCRJPE18RWJ2G292BHEWY`, request_id `req_01a0e0f6624471f08f9ae0a169ca8493`.
2. The stream starts `python` ("Ran Python"), emits `generated_file` `generating` then `failed` (id `py_msg_01M3GFCRMVF18AXEATZ1XK086J`), then `tool_end` with `outcome: error`, `duration_ms: 4`, `error_detail: "The python tool failed: the tool failed for an internal reason"`. It retries once and fails again in 3ms with the same detail.
3. The same instant failure happened on an earlier turn that asked for a markdown file (`cnv_01M3GFB5YPFMHVVZ90WY53VD9Z`): two `python` calls, 4ms and 3ms, both the same internal error. The model then wrote the text with `canvas_write` instead.
4. The math turn still finished with the text "4", which did not come from the tool.

## Impact
Code execution in Chat does not run. Generated-file cards flip to failed. Users only see a generic internal error on the "Ran Python" row. The model may answer anyway without having run the code.

## Environment
- https://app.cortex.foundation / https://api.cortex.foundation
- Guest session, model `cortex-1-mini`
- Reproduced twice on 2026-09-27

This is the web Chat tool, not the CLI sandbox or execpolicy.
