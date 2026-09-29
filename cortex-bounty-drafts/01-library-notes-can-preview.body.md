## Summary
A Library note created through POST /v1/library/notes (Chat "Save to Library") is stored as artifact_kind=document with content_type=text/markdown. GET /v1/library/{id}/content returns the markdown body (HTTP 200). The API still sets can_preview=false, and GET /v1/library/{id}/export returns 404 "No library export with id …". The web Library detail view gates on canPreview and therefore never fetches or renders the note.

## Expected
- Notes from /v1/library/notes should be previewable (can_preview=true), or the UI should load /content for markdown notes anyway.
- /export should return the note bytes or a clear unsupported/entitlement error — not not_found for an id that GET /v1/library/{id} returns.

## Actual
1. POST /v1/library/notes succeeds and returns an lbf_… id.
2. GET /v1/library/{id} → can_preview:false, kind:other, artifact_kind:document, content_type:text/markdown.
3. GET /v1/library/{id}/content → 200 text/markdown with the saved body.
4. GET /v1/library/{id}/export → 404 not_found: "No library export with id <same id>".
5. Web library detail short-circuits when !canPreview and never calls libraryFileUrl(/content).

## Impact
Users who save a note into Library cannot read it again in the Library UI even though the bytes exist; export also fails. The Documents tab still lists the row.

## Environment / evidence
- https://app.cortex.foundation / https://api.cortex.foundation
- Guest session via POST /v1/auth/guest (same note path as signed-in Save to Library)
- Reproduced 2026-09-26
- Fresh example id: lbf_01M3E03P96EZ08CE8V6XXB5R6D
