1. Sign in or use a guest session at https://app.cortex.foundation.
2. In a chat, select text and choose Save to Library. Set the name to Budget/Q3-ZEPHYR and save.
3. Open Library. The new file is named Q3-ZEPHYR.md, not Budget/Q3-ZEPHYR.md.
4. API check: POST /v1/library/notes {"title":"Budget/Q3-ZEPHYR","text":"quarterly note body"} → 201 filename "Q3-ZEPHYR.md".
5. Rename that file to Other/Renamed-ZEPHYR.md. PATCH /v1/library/{id} {"filename":"Other/Renamed-ZEPHYR.md"} → 200 filename "Renamed-ZEPHYR.md".
