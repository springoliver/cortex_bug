## Summary
Settings → Data & privacy → Memory shows Use memory on (GET /v1/memory/settings returns enabled:true). GET /v1/memory and GET /v1/memory/export succeed. But any write path fails: POST /v1/memory/import returns 503 service_unavailable with detail "Memory is not available on this deployment.", and Chat turns that call the memory_remember tool get the same error, so the model cannot store preferences the UI and docs claim are supported.

## Expected
Per product docs (Memory is on by default; import/export and saving from Chat should work while Use memory is on), import and memory_remember should persist entries when enabled:true, or settings/export should not advertise a working memory subsystem.

## Actual
1. GET /v1/memory/settings → {"enabled":true,"include_sensitive_topics":false,"retention_days":null}
2. PATCH /v1/memory/settings {"enabled":true,...} → same enabled:true body (persists)
3. GET /v1/memory → {"items":[],"has_more":false}
4. GET /v1/memory/export → 200 plain text "# Cortex memory / No memories."
5. POST /v1/memory/import {"text":"- favorite color is blue\n"} → 503 service_unavailable, detail "Memory is not available on this deployment.", instance /memory/import
6. In Chat, ask to remember a preference → tool_start memory_remember → tool_end error service_unavailable: Memory is not available on this deployment. → assistant cannot store it; GET /v1/memory stays empty

## Impact
Memory appears available in Settings and docs, but users cannot import or save memories from Chat. Export of an empty store still succeeds, which makes the feature look healthy while writes are hard-failed.

## Environment
- https://app.cortex.foundation / https://api.cortex.foundation
- Guest session (POST /v1/auth/guest); also visible whenever memory_remember is invoked
- Reproduced 2026-09-26
- Example request_id: req_01a0dcd5ccb17be2b2a1411e1f89fa5a

Not a duplicate of the guest "Use memory" control visibility bug or the duplicated Memory sections UI bug — this is a write-path 503 while the control reports enabled.
