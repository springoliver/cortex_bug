1. POST https://api.cortex.foundation/v1/auth/guest; keep cortex_gt. Origin https://app.cortex.foundation.
2. GET /v1/memory/settings → enabled:true.
3. GET /v1/memory → 200 empty items; GET /v1/memory/export → 200 "# Cortex memory" / "No memories."
4. POST /v1/memory/import with {"text":"- favorite color is blue\n"} → 503 "Memory is not available on this deployment."
5. POST /v1/conversations/turns (or continue a chat) with message: "Please remember that my favorite color is blue."
6. Observe SSE: tool memory_remember fails with the same service_unavailable detail; GET /v1/memory still empty.
7. Optionally open Settings → Data & privacy → Memory: Use memory is on, list empty, Import fails the same way.
