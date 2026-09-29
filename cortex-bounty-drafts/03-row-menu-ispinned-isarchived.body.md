## Summary
The chat Recents row overflow menu pins and archives by calling `patchConversation` with camelCase bodies `{isPinned: true}` / `{isArchived: true}` (and the toast Undo repeats `{isArchived: false}`). `PATCH /v1/conversations/{id}` only accepts `pinned`, `archived`, `title`, or `project_id`. Those camelCase fields return 422 validation_failed: "Pass project_id, title, pinned or archived to change a conversation." Server state does not change. A separate helper `setConversationPinned` correctly sends `{pinned: bool}`, which is why some other pin entry points still work.

## Expected
Row menu Pin / Unpin / Archive / Undo should persist via the same field names the API documents/accepts (`pinned`, `archived`), matching `setConversationPinned` / `archiveConversation`.

## Actual
1. Recents row menu code paths call `patchConversation(client, id, {isPinned: true|false})` or `{isArchived: true|false}` (apps/web shell row menu; minified: `D({isPinned:!0})`, `D({isArchived:!0})` with `D` → `patchConversation` that forwards the body unchanged).
2. `PATCH /v1/conversations/{id}` with `{"isPinned":true}` or `{"isArchived":true}` → 422, detail "Pass project_id, title, pinned or archived to change a conversation."
3. Same for snake_case wrong names `is_pinned` / `is_archived` (another client helper maps to those).
4. `PATCH` with `{"pinned":true}` or `{"archived":true}` → 200 and `is_pinned` / `is_archived` appear on the conversation resource.
5. `title` patches still work through the same row menu rename path.

## Impact
Pin and Archive from the Recents row menu do not persist. Undo on an archive toast that used this path cannot unarchive. Users see failure toasts or optimistic UI that disagrees with the server. Other pin entry points that call `setConversationPinned` (`body: {pinned}`) are unaffected.

## Environment
- https://app.cortex.foundation / https://api.cortex.foundation
- Guest or signed-in; conversation PATCH auth only needs a session
- Reproduced 2026-09-26
- Example request_ids: req_01a0de8256827293910afefffe4ce9ec (isArchived), req_01a0de82595c75f0a57c19639ebe4fe7 (isPinned)

Not a duplicate of the title-row Branch failure (Pin works there via setConversationPinned), nor only the archive-undo toast symptom report — this is the field-name mismatch root cause on the Recents row menu path.
