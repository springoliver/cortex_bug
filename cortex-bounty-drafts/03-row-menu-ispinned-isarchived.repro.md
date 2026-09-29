1. POST /v1/auth/guest; create a chat with POST /v1/conversations/turns.
2. PATCH /v1/conversations/{id} with {"isPinned":true} → 422 "Pass project_id, title, pinned or archived…".
3. PATCH /v1/conversations/{id} with {"isArchived":true} → same 422; GET conversation shows neither is_pinned nor is_archived.
4. PATCH /v1/conversations/{id} with {"pinned":true} → 200, is_pinned:true.
5. PATCH /v1/conversations/{id} with {"archived":true} → 200, is_archived:true.
6. In the web app Recents list, open the row ⋯ menu and choose Pin or Archive — network tab shows PATCH body with isPinned/isArchived and a 422 (or failure toast). Rename in the same menu still works (sends title).
