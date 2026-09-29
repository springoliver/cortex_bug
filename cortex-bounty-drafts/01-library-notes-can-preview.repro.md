1. POST https://api.cortex.foundation/v1/auth/guest (keep cortex_gt cookie). Origin https://app.cortex.foundation.
2. POST /v1/library/notes with JSON:
   {"title":"bounty-repro","text":"note body for can_preview repro test"}
3. Note the returned id (example: lbf_01M3E03P96EZ08CE8V6XXB5R6D).
4. GET /v1/library/{id} → can_preview:false, artifact_kind:"document", content_type:"text/markdown".
5. GET /v1/library/{id}/content → HTTP 200, body = "note body for can_preview repro test".
6. GET /v1/library/{id}/export → HTTP 404, detail "No library export with id …".
7. In the web app: Library → Documents → open the note → detail pane shows no body (client skips preview when canPreview is false).

Not a duplicate of guest Save-to-Library vs Attach entitlement mismatch, nor PDF artifact_kind tab bugs.
