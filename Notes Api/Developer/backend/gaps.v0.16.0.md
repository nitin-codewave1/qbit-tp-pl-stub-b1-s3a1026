---
id: agent-developer/notes_api/backend/GAPS.md
type: gaps
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.16.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/GAPS.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: notes_api:backend/GAPS.md
---

<!-- SECTION: backend.gaps | status: VERIFIED -->
## Gaps

### `GAP-index` — 32 `GAP-` item(s) were raised while writing `BACKEND-SPEC.md`: GAP-BE-085, GAP-BE-038, GAP-BE-053, GAP-BE-306, GAP-BE-602, GAP-BE-001, GAP-BE-003, GAP-BE-017, GAP-BE-024, GAP-BE-031, GAP-BE-033, GAP-BE-035, GAP-BE-083, GAP-BE-089, GAP-BE-101, GAP-BE-104, GAP-BE-300, GAP-BE-301, GAP-BE-302, GAP-BE-303, GAP-BE-304, GAP-BE-305, GAP-BE-307, GAP-BE-308, GAP-BE-309, GAP-BE-310, GAP-BE-500, GAP-BE-501, GAP-BE-600, GAP-BE-601, GAP-BE-603, GAP-BE-604

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 7 `CON-` item(s) were raised while writing `BACKEND-SPEC.md`: CON-BE-300, CON-BE-007, CON-BE-151, CON-BE-003, CON-BE-006, CON-BE-011, CON-BE-019

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 16 `ASM-` item(s) were raised while writing `BACKEND-SPEC.md`: ASM-BE-026, ASM-BE-153, ASM-BE-305, ASM-BE-300, ASM-BE-015, ASM-BE-019, ASM-BE-025, ASM-BE-027, ASM-BE-029, ASM-BE-034, ASM-BE-044, ASM-BE-301, ASM-BE-302, ASM-BE-303, ASM-BE-304, ASM-BE-400

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-605` — unasked degradable question: ASM-BE-029: Confirm assumption ASM-BE-029: assumption — The resend and lock counters are keyed per normalised email address.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-606` — unasked degradable question: ASM-BE-034: Confirm assumption ASM-BE-034: assumption — pinned` is `NOT NULL`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-607` — unasked degradable question: ASM-BE-044: Confirm assumption ASM-BE-044: assumption — The resend window is sliding (trailing 10 minutes).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-608` — unasked degradable question: ASM-BE-153: Confirm assumption ASM-BE-153: assumption — The scan period is no longer than 15 minutes, because NFR-003 requires an alert within 15 minutes of drift detection.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-609` — unasked degradable question: ASM-BE-300: Confirm assumption ASM-BE-300: assumption — The error envelope is the proposal in `backend.error-model`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-610` — unasked degradable question: ASM-BE-301: Confirm assumption ASM-BE-301: assumption — Error code names other than `OUT_OF_SCOPE` are proposed names. [US-005-04]

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-611` — unasked degradable question: ASM-BE-302: Confirm assumption ASM-BE-302: assumption — A role has no permission on an operation unless an input grants it.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-612` — unasked degradable question: ASM-BE-303: Confirm assumption ASM-BE-303: assumption — NFR-019 lists, positionally: code challenges 1 day, send attempts 1 day, account locks 5 days. Notes are not deleted.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-613` — unasked degradable question: ASM-BE-304: Confirm assumption ASM-BE-304: assumption — No notes_api database exists yet, so migrations start from empty.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-614` — unasked degradable question: ASM-BE-305: Confirm assumption ASM-BE-305: assumption — The wrong-code count is per normalised email, increments on each wrong code, is cleared by a successful verification, and has no time window.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-615` — unasked degradable question: ASM-BE-400: Confirm assumption ASM-BE-400: assumption — No response distinguishes an email that has a User from one that has none.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-616` — 6 user stor(ies) belong to no backend module: US-005-06, US-006-01, US-006-02, US-006-03, US-006-04, US-006-05

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** user_stories, BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** a module in the project spec's module map that owns them, or a decision that this surface does not serve them
- **Status.** open

### `GAP-index` — 25 `GAP-` item(s) were raised while writing `modules/auth-SPEC.md`: GAP-BE-302, GAP-BE-085, GAP-BE-600, GAP-BE-602, GAP-BE-089, GAP-BE-031, GAP-BE-620, GAP-BE-033, GAP-BE-304, GAP-BE-306, GAP-BE-083, GAP-BE-601, GAP-BE-035, GAP-BE-617, GAP-BE-618, GAP-BE-604, GAP-BE-623, GAP-BE-104, GAP-BE-603, GAP-BE-101, GAP-BE-619, GAP-BE-024, GAP-BE-301, GAP-BE-621, GAP-BE-622

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/auth-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/auth-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 3 `CON-` item(s) were raised while writing `modules/auth-SPEC.md`: CON-BE-003, CON-BE-006, CON-BE-011

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/auth-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/auth-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 19 `ASM-` item(s) were raised while writing `modules/auth-SPEC.md`: ASM-BE-025, ASM-BE-027, ASM-BE-401, ASM-BE-015, ASM-BE-019, ASM-BE-403, ASM-BE-404, ASM-BE-407, ASM-BE-029, ASM-BE-402, ASM-BE-405, ASM-BE-406, ASM-BE-305, ASM-BE-408, ASM-BE-044, ASM-BE-409, ASM-BE-400, ASM-BE-303, ASM-BE-302

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/auth-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/auth-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-624` — unasked degradable question: ASM-BE-405: Confirm assumption ASM-BE-405: only the latest challenge for the email is checked.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 14 `GAP-` item(s) were raised while writing `modules/notes-SPEC.md`: GAP-BE-667, GAP-BE-089, GAP-BE-035, GAP-BE-304, GAP-BE-017, GAP-BE-309, GAP-BE-668, GAP-BE-669, GAP-BE-085, GAP-BE-003, GAP-BE-670, GAP-BE-671, GAP-BE-300, GAP-BE-310

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 1 `CON-` item(s) were raised while writing `modules/notes-SPEC.md`: CON-BE-003

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 13 `ASM-` item(s) were raised while writing `modules/notes-SPEC.md`: ASM-BE-451, ASM-BE-453, ASM-BE-454, ASM-BE-452, ASM-BE-034, ASM-BE-455, ASM-BE-456, ASM-BE-457, ASM-BE-458, ASM-BE-459, ASM-BE-302, ASM-BE-300, ASM-BE-301

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-672` — unasked degradable question: ASM-BE-451: Confirm assumption ASM-BE-451: Column types: uuid keys, text strings, boolean, timestamptz. — PostgreSQL 16 is the platform and no types are supplied. Confirm by a platform decision.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-673` — unasked degradable question: ASM-BE-452: Confirm assumption ASM-BE-452: A new note has `status` `active`. — US-002-04 archives an "active, existing note"; the create criteria carry no status. Confirm by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-674` — unasked degradable question: ASM-BE-453: Confirm assumption ASM-BE-453: The note id is a server-assigned opaque uuid, never client-supplied. — AC-002-01-01 says the API returns a unique ID. Confirm by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-675` — unasked degradable question: ASM-BE-454: Confirm assumption ASM-BE-454: A title or body of whitespace only counts as empty. — BR-005 rejects "empty/null" payloads. Confirm by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-676` — unasked degradable question: ASM-BE-455: Confirm assumption ASM-BE-455: updated_at` changes on any edit, pin change or tag change. — AC-002-02-01 returns "an updated timestamp"; tag changes are an edit of the note per US-002-02. Confirm by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-677` — unasked degradable question: ASM-BE-456: Confirm assumption ASM-BE-456: Edit, pin and tag changes on an archived note behave as on an active note. — No input forbids them. Confirm by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-678` — unasked degradable question: ASM-BE-457: Confirm assumption ASM-BE-457: PATCH accepts only `title`, `body` and `pinned`. An empty payload, an unknown field, or an empty or null title or body is 400. Tags change only through the tag operations. — The supplied operation records list those fields; BR-005's non-empty rule is extended to edits to avoid content loss, in the spirit of BR-006. Confirm by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-679` — unasked degradable question: ASM-BE-458: Confirm assumption ASM-BE-458: Archive and tag operations return 404 for a missing note, like the stated cases. — US-002-02, US-002-05 and US-002-06 state 404 for the other operations. Confirm by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-680` — unasked degradable question: ASM-BE-459: Confirm assumption ASM-BE-459: A concurrent PATCH is last-write-wins on the supplied fields. — US-005-03 allows "last-write/conflict". Last-write is the narrower, non- reading. Confirm by the product owner (`GAP-BE-309`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 17 `GAP-` item(s) were raised while writing `modules/search-SPEC.md`: GAP-BE-303, GAP-BE-035, GAP-BE-500, GAP-BE-085, GAP-BE-725, GAP-BE-724, GAP-BE-717, GAP-BE-718, GAP-BE-719, GAP-BE-720, GAP-BE-721, GAP-BE-722, GAP-BE-723, GAP-BE-001, GAP-BE-017, GAP-BE-304, GAP-BE-310

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 1 `CON-` item(s) were raised while writing `modules/search-SPEC.md`: CON-BE-003

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 6 `ASM-` item(s) were raised while writing `modules/search-SPEC.md`: ASM-BE-502, ASM-BE-503, ASM-BE-302, ASM-BE-504, ASM-BE-501, ASM-BE-505

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-726` — unasked degradable question: Q-BE-401: Is a search with `tags` and no `q` allowed? US-003-01 requires `q`; US-003-02 supplies `tags` alone.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-727` — unasked degradable question: ASM-BE-501: Confirm assumption ASM-BE-501: Search is implemented with PostgreSQL 16 full-text and btree indexes. PostgreSQL is the platform [platform.md]; the specific index types are proposals. Confirm with the platform decision.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-728` — unasked degradable question: ASM-BE-502: Confirm assumption ASM-BE-502: Results are de-duplicated per note when several tags match. Stories imply a list of notes, not note-tag rows.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-729` — unasked degradable question: ASM-BE-503: Confirm assumption ASM-BE-503: Validation precedes query execution. Standard behaviour implied by the 400 criteria.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-730` — unasked degradable question: ASM-BE-504: Confirm assumption ASM-BE-504: The total count honours the same visibility rules as the results. Depends on the access rule.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-731` — unasked degradable question: ASM-BE-505: Confirm assumption ASM-BE-505: Default for `Q-BE-401`: at least one of `q` or `tags` is required.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 20 `GAP-` item(s) were raised while writing `modules/residency-compliance-SPEC.md`: GAP-BE-308, GAP-BE-307, GAP-BE-767, GAP-BE-602, GAP-BE-305, GAP-BE-304, GAP-BE-774, GAP-BE-775, GAP-BE-771, GAP-BE-769, GAP-BE-776, GAP-BE-035, GAP-BE-500, GAP-BE-770, GAP-BE-773, GAP-BE-303, GAP-BE-085, GAP-BE-772, GAP-BE-768, GAP-BE-301

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/residency-compliance-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-compliance-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 3 `CON-` item(s) were raised while writing `modules/residency-compliance-SPEC.md`: CON-BE-151, CON-BE-006, CON-BE-451

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/residency-compliance-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-compliance-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 13 `ASM-` item(s) were raised while writing `modules/residency-compliance-SPEC.md`: ASM-BE-557, ASM-BE-551, ASM-BE-561, ASM-BE-554, ASM-BE-555, ASM-BE-552, ASM-BE-559, ASM-BE-560, ASM-BE-556, ASM-BE-553, ASM-BE-302, ASM-BE-153, ASM-BE-562

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/residency-compliance-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-compliance-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-index` — 16 `GAP-` item(s) were raised while writing `modules/api-docs-SPEC.md`: GAP-BE-822, GAP-BE-817, GAP-BE-035, GAP-BE-500, GAP-BE-304, GAP-BE-824, GAP-BE-819, GAP-BE-820, GAP-BE-309, GAP-BE-818, GAP-BE-821, GAP-BE-303, GAP-BE-823, GAP-BE-089, GAP-BE-306, GAP-BE-602

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/api-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/api-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 1 `CON-` item(s) were raised while writing `modules/api-docs-SPEC.md`: CON-BE-006

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/api-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/api-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 7 `ASM-` item(s) were raised while writing `modules/api-docs-SPEC.md`: ASM-BE-601, ASM-BE-605, ASM-BE-606, ASM-BE-604, ASM-BE-603, ASM-BE-602, ASM-BE-302

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/api-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/api-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-825` — unasked degradable question: ASM-BE-601: Confirm assumption ASM-BE-601: The document is a generated build artefact built from the route registry and operation records, not hand-authored and not stored in PostgreSQL. — BR-012 refers to a "route registry" and an automated coverage check. Confirm by `GAP-BE-818`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-826` — unasked degradable question: ASM-BE-602: Confirm assumption ASM-BE-602: Active and archived notes share one store, distinguished by a status flag. The documentation therefore states no storage-location difference between archive and active endpoints. — BR-007: "Soft-delete/archive flag, not hard delete". Confirm by the data-model decision for `notes`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-827` — unasked degradable question: ASM-BE-603: Confirm assumption ASM-BE-603: An unsupported Accept header returns 406 with the proposed error code `NOT_ACCEPTABLE`, not a fallback. — backend.error-model` lists 406 for the spec endpoint. `GAP-BE-309` is open.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-828` — unasked degradable question: ASM-BE-604: Confirm assumption ASM-BE-604: The endpoint serves JSON, because the path ends in `.json`. The content type is the JSON media type. — The path is itself proposed (`GAP-BE-303`). Confirm by `GAP-BE-819`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-829` — unasked degradable question: ASM-BE-605: Confirm assumption ASM-BE-605: The checks in `backend.api-docs.jobs` run on every CI build. — NFR-010 runs the scope-guardrail suite on every CI build. No input states this for the documentation checks (`GAP-BE-823`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-830` — unasked degradable question: ASM-BE-606: Confirm assumption ASM-BE-606: Coverage below 100% rejects the build (the build is not published). — NFR-008 targets 100% endpoint coverage and US-004-01 requires an endpoint to be documented no later than it is callable. US-004-04 only requires reporting.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 9 `GAP-` item(s) were raised while writing `modules/scope-guardrail-SPEC.md`: GAP-BE-309, GAP-BE-001, GAP-BE-085, GAP-BE-602, GAP-BE-867, GAP-BE-868, GAP-BE-869, GAP-BE-089, GAP-BE-870

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/scope-guardrail-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-guardrail-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 1 `CON-` item(s) were raised while writing `modules/scope-guardrail-SPEC.md`: CON-BE-007

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/scope-guardrail-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-guardrail-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 7 `ASM-` item(s) were raised while writing `modules/scope-guardrail-SPEC.md`: ASM-BE-651, ASM-BE-654, ASM-BE-653, ASM-BE-655, ASM-BE-301, ASM-BE-652, ASM-BE-302

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/scope-guardrail-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-guardrail-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-871` — unasked degradable question: ASM-BE-302: Confirm assumption ASM-BE-302: authentication and roles.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-872` — unasked degradable question: ASM-BE-301: Confirm assumption ASM-BE-301: error code names other than `OUT_OF_SCOPE` are proposals.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-873` — unasked degradable question: ASM-BE-651: Confirm assumption ASM-BE-651: the exclusion list is static configuration in the codebase, not a database table. No input says where it lives and none requires it to change at runtime. A different choice would only add a table.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-874` — unasked degradable question: ASM-BE-652: Confirm assumption ASM-BE-652: out-of-scope matching runs before authentication. BR-016 asks for a clear, documented error for integrators, and the Scope section is public documentation. Confirm with the owner of the authorisation model.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-875` — unasked degradable question: ASM-BE-653: Confirm assumption ASM-BE-653: an attachment-path request returns status 404 with an `OUT_OF_SCOPE` envelope. That satisfies US-005-02's status and US-005-04's message together. If the product owner prefers a different 4xx, `CON-BE-007` governs.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-876` — unasked degradable question: ASM-BE-654: Confirm assumption ASM-BE-654: an attachment pattern is any request path that has a segment equal to `attachments`. A real-time pattern is a path whose segment is `ws`, or a request carrying an `Upgrade: websocket` header or an `Accept: text/event-stream` header. US-005-05 names `/attachments` and `/ws` as examples and gives nothing further. Confirm with `GAP-BE-867`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-877` — unasked degradable question: ASM-BE-655: Confirm assumption ASM-BE-655: a file payload is detected from a `Content-Type` of `multipart/form-data`, not from the body contents. US-005-02 and US-005-04 name multipart uploads as the case.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open
<!-- /SECTION: backend.gaps -->
