---
id: agent-developer/notes_api/backend/GAPS.md
type: gaps
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.13.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/GAPS.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 825ae1d3-abc3-5c68-8294-c18eb9926d6e
okf_id: notes_api:backend/GAPS.md
---

<!-- SECTION: backend.gaps | status: VERIFIED -->
## Gaps

### `GAP-index` — 25 `GAP-` item(s) were raised while writing `BACKEND-SPEC.md`: GAP-BE-085, GAP-BE-024, GAP-BE-001, GAP-BE-003, GAP-BE-017, GAP-BE-031, GAP-BE-033, GAP-BE-035, GAP-BE-038, GAP-BE-053, GAP-BE-083, GAP-BE-089, GAP-BE-101, GAP-BE-104, GAP-BE-300, GAP-BE-301, GAP-BE-302, GAP-BE-303, GAP-BE-304, GAP-BE-305, GAP-BE-306, GAP-BE-307, GAP-BE-308, GAP-BE-309, GAP-BE-310

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 8 `CON-` item(s) were raised while writing `BACKEND-SPEC.md`: CON-BE-019, CON-BE-007, CON-BE-151, CON-BE-003, CON-BE-006, CON-BE-011, CON-BE-300, CON-BE-010

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 15 `ASM-` item(s) were raised while writing `BACKEND-SPEC.md`: ASM-BE-026, ASM-BE-303, ASM-BE-305, ASM-BE-153, ASM-BE-300, ASM-BE-015, ASM-BE-019, ASM-BE-025, ASM-BE-027, ASM-BE-029, ASM-BE-034, ASM-BE-044, ASM-BE-301, ASM-BE-302, ASM-BE-304

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-311` — unasked degradable question: ASM-BE-027: Confirm assumption ASM-BE-027: assumption — Email goes through AWS SES. Reason: NFR-017 names a transactional email service in an AWS region and the platform is AWS. Confirmed by `Q-BE-005`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-312` — unasked degradable question: ASM-BE-029: Confirm assumption ASM-BE-029: assumption — The resend and lock counters are keyed per normalised email address. Reason: BR-021 to BR-028 speak of requests and of an account, never of a client. Confirmed by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-313` — unasked degradable question: ASM-BE-034: Confirm assumption ASM-BE-034: assumption — pinned` is `NOT NULL`. Reason: BR-018 gives it a default and no null state.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-314` — unasked degradable question: ASM-BE-044: Confirm assumption ASM-BE-044: assumption — The resend window is sliding (trailing 10 minutes). Reason: BR-021 reads "within 10 minutes". Confirmed by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-315` — unasked degradable question: ASM-BE-153: Confirm assumption ASM-BE-153: assumption — The scan period is no longer than 15 minutes. Reason: NFR-003 requires an alert within 15 minutes of drift detection. Confirmed by `Q-BE-153`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-316` — unasked degradable question: ASM-BE-300: Confirm assumption ASM-BE-300: assumption — The error envelope is the proposal in `backend.error-model`. Reason: none is supplied. Confirmed by `GAP-BE-300`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-317` — unasked degradable question: ASM-BE-301: Confirm assumption ASM-BE-301: assumption — Error code names other than `OUT_OF_SCOPE` are proposed names. Reason: only `OUT_OF_SCOPE` is cited, as an example. [stories.md US-005-04]

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-318` — unasked degradable question: ASM-BE-302: Confirm assumption ASM-BE-302: assumption — A role has no permission on an operation unless an input grants it. Reason: no input defines a roles model. Confirmed by `GAP-BE-085`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-319` — unasked degradable question: ASM-BE-303: Confirm assumption ASM-BE-303: assumption — NFR-019 lists, positionally: code challenges 1 day, send attempts 1 day, account locks 5 days, notes 1 day. Reason: the sentence names four record types and four numbers. Confirmed by a corrected NFR-019.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-320` — unasked degradable question: ASM-BE-304: Confirm assumption ASM-BE-304: assumption — No notes_api database exists yet, so the migrations below start from empty. Reason: no input states otherwise. Confirmed by the orchestrator.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-321` — unasked degradable question: ASM-BE-305: Confirm assumption ASM-BE-305: assumption — The wrong-code count is per normalised email, increments on each wrong code, is cleared by a successful verification, and has no time window. Reason: no input states a window. Confirmed by `GAP-BE-024`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 20 `GAP-` item(s) were raised while writing `modules/auth-SPEC.md`: GAP-BE-302, GAP-BE-085, GAP-BE-322, GAP-BE-031, GAP-BE-327, GAP-BE-033, GAP-BE-304, GAP-BE-306, GAP-BE-038, GAP-BE-053, GAP-BE-328, GAP-BE-323, GAP-BE-035, GAP-BE-325, GAP-BE-104, GAP-BE-101, GAP-BE-326, GAP-BE-324, GAP-BE-024, GAP-BE-083

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/auth-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/auth-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 4 `CON-` item(s) were raised while writing `modules/auth-SPEC.md`: CON-BE-003, CON-BE-006, CON-BE-300, CON-BE-011

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/auth-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/auth-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 16 `ASM-` item(s) were raised while writing `modules/auth-SPEC.md`: ASM-BE-025, ASM-BE-027, ASM-BE-306, ASM-BE-311, ASM-BE-308, ASM-BE-015, ASM-BE-019, ASM-BE-312, ASM-BE-305, ASM-BE-307, ASM-BE-309, ASM-BE-313, ASM-BE-044, ASM-BE-310, ASM-BE-302, ASM-BE-303

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/auth-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/auth-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-329` — unasked degradable question: ASM-BE-306: Confirm assumption ASM-BE-306: A verify attempt refused because of a lock changes neither `failed_count` nor `locked_until`. — BR-022 refuses verification during the lock and states no extension. — rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-330` — unasked degradable question: ASM-BE-307: Confirm assumption ASM-BE-307: A code is expired only when the instant is strictly after issue + 10 minutes. — BR-024 and US-006-06 say "after". BR-020 and BR-026 say "expires", so `GAP-BE-306` stays open. — decision on `GAP-BE-306

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-331` — unasked degradable question: ASM-BE-308: Confirm assumption ASM-BE-308: The wrong-code counter is a column of `login_locks`, not of `otp_challenges`. — ASM-BE-305` keys the count per email, not per challenge. — GAP-BE-024` closing artefact

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-332` — unasked degradable question: ASM-BE-309: Confirm assumption ASM-BE-309: Only the latest unconsumed challenge for an email is checked at verification. — No input says whether earlier codes stay valid (`GAP-BE-083`). One live code is the narrower reading. — rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-333` — unasked degradable question: ASM-BE-310: Confirm assumption ASM-BE-310: Both operations return 200 on success; send returns an empty body. — No success status is stated for either. — contract decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-334` — unasked degradable question: ASM-BE-311: Confirm assumption ASM-BE-311: Column types: uuid keys, text email, timestamptz instants, integer counter. — PostgreSQL 16 is the platform and no types are supplied. — platform decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-335` — unasked degradable question: ASM-BE-312: Confirm assumption ASM-BE-312: A refused send writes no `send_attempts` row. — Otherwise a caller that keeps retrying is never released from the window. — GAP-BE-322

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-336` — unasked degradable question: ASM-BE-313: Confirm assumption ASM-BE-313: failed_count` is reset to 0 when a lock is entered. — Otherwise the first wrong code after the lock would re-lock immediately. — rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 21 `GAP-` item(s) were raised while writing `modules/notes-SPEC.md`: GAP-BE-303, GAP-BE-373, GAP-BE-089, GAP-BE-372, GAP-BE-003, GAP-BE-035, GAP-BE-378, GAP-BE-017, GAP-BE-033, GAP-BE-304, GAP-BE-309, GAP-BE-377, GAP-BE-376, GAP-BE-374, GAP-BE-379, GAP-BE-085, GAP-BE-101, GAP-BE-375, GAP-BE-380, GAP-BE-300, GAP-BE-310

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 3 `CON-` item(s) were raised while writing `modules/notes-SPEC.md`: CON-BE-003, CON-BE-300, CON-BE-019

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 17 `ASM-` item(s) were raised while writing `modules/notes-SPEC.md`: ASM-BE-361, ASM-BE-363, ASM-BE-359, ASM-BE-364, ASM-BE-034, ASM-BE-362, ASM-BE-360, ASM-BE-369, ASM-BE-366, ASM-BE-357, ASM-BE-358, ASM-BE-356, ASM-BE-368, ASM-BE-365, ASM-BE-367, ASM-BE-302, ASM-BE-303

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-381` — unasked degradable question: ASM-BE-356: Confirm assumption ASM-BE-356: Edit, pin and tag changes on an archived note behave as on an active note. — No input forbids them, and BR-008 says only that unarchive restores the prior state. — Q-BE-351

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-382` — unasked degradable question: ASM-BE-357: Confirm assumption ASM-BE-357: Unarchive of an active note returns the same conflict as archive of an archived note. — Mirrors US-002-04's second criterion. Nothing else is cited. — Q-BE-352

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-383` — unasked degradable question: ASM-BE-358: Confirm assumption ASM-BE-358: Removing a tag not on the note returns 404 (`NOT_FOUND`). — Add is explicitly idempotent while removal has no criteria. A 404 keeps the two distinguishable. — Q-BE-353

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-384` — unasked degradable question: ASM-BE-359: Confirm assumption ASM-BE-359: A title or body of whitespace only counts as empty. — BR-005 says "empty/null" rejected; a whitespace-only value carries no content. — Product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-385` — unasked degradable question: ASM-BE-360: Confirm assumption ASM-BE-360: Concurrent PATCH is last-write-wins; there is no version column. — US-005-03 allows last-write or conflict and neither is specified further. — Q-BE-354

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-386` — unasked degradable question: ASM-BE-361: Confirm assumption ASM-BE-361: Column types: `uuid` for `id`, `text` for title, body and tag name, `boolean`, `timestamptz`. — Platform is PostgreSQL 16; no input states types. — Platform decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-387` — unasked degradable question: ASM-BE-362: Confirm assumption ASM-BE-362: Timestamps are stored as UTC instants. `updated_at` is set on PATCH, archive, unarchive, and on any tag change that alters the tag set. A repeated add of an existing tag changes nothing. — AC-002-02-01 requires an updated timestamp on edit only. — Confirmation

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-388` — unasked degradable question: ASM-BE-363: Confirm assumption ASM-BE-363: The note id is server-assigned and opaque; clients cannot supply it. — AC-002-01-01 says the API returns a unique ID. — Confirmation

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-389` — unasked degradable question: ASM-BE-364: Confirm assumption ASM-BE-364: A new note has status `active`. — US-002-04 archives an "active, existing note" and the create criteria carry no status. — Confirmation

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-390` — unasked degradable question: ASM-BE-365: Confirm assumption ASM-BE-365: No delete operation for notes is in scope. "Deleted" in US-002-06 is treated as never-existing. — BR-007 keeps data; no input describes a delete. — Q-BE-300

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-391` — unasked degradable question: ASM-BE-366: Confirm assumption ASM-BE-366: note_tags` foreign keys use `ON DELETE RESTRICT`. — No delete is in scope (`ASM-BE-365`), so the choice has no visible effect. — When deletion is specified

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-392` — unasked degradable question: ASM-BE-367: Confirm assumption ASM-BE-367: POST `/notes` accepts no tags and creates no tag rows. — The create criteria carry title, body and `pinned` only. — Contract decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-393` — unasked degradable question: ASM-BE-368: Confirm assumption ASM-BE-368: A non-boolean `pinned` on create or PATCH is refused with 400. — Validation applies to every typed field, as in AC-002-01-03 and NFR-012. — Confirmation

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-394` — unasked degradable question: ASM-BE-369: Confirm assumption ASM-BE-369: Tag names are compared exactly, with no case folding. — No normalisation rule is supplied. Example tag names in the stories are lowercase. — GAP-BE-378

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-395` — unasked blocking question: Q-BE-351: Are edit, pin and tag changes allowed on an archived note? — Allowed (`ASM-BE-356`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-396` — unasked blocking question: Q-BE-352: What does unarchive of an active note return? — Conflict, as in `ASM-BE-357`.

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-397` — unasked blocking question: Q-BE-353: What does removing a tag that is not on the note return? — 404 (`ASM-BE-358`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-398` — unasked blocking question: Q-BE-354: Does concurrent PATCH return last-write or a conflict? — Last-write (`ASM-BE-360`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 17 `GAP-` item(s) were raised while writing `modules/search-SPEC.md`: GAP-BE-085, GAP-BE-422, GAP-BE-423, GAP-BE-310, GAP-BE-426, GAP-BE-433, GAP-BE-304, GAP-BE-429, GAP-BE-425, GAP-BE-035, GAP-BE-431, GAP-BE-424, GAP-BE-432, GAP-BE-427, GAP-BE-428, GAP-BE-104, GAP-BE-430

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 3 `CON-` item(s) were raised while writing `modules/search-SPEC.md`: CON-BE-003, CON-BE-300, CON-BE-401

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 13 `ASM-` item(s) were raised while writing `modules/search-SPEC.md`: ASM-BE-409, ASM-BE-412, ASM-BE-406, ASM-BE-407, ASM-BE-408, ASM-BE-417, ASM-BE-416, ASM-BE-415, ASM-BE-413, ASM-BE-411, ASM-BE-414, ASM-BE-410, ASM-BE-302

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-434` — unasked degradable question: ASM-BE-406: Confirm assumption ASM-BE-406: search owns no tables and reads the notes tables. Reason: the module map says read-only. Confirmed when `notes` and `search` boundaries are approved.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-435` — unasked degradable question: ASM-BE-407: Confirm assumption ASM-BE-407: "content" in US-003-01 means the note `body`. Reason: the Note entity has title and body only. Confirmed by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-436` — unasked degradable question: ASM-BE-408: Confirm assumption ASM-BE-408: the last ordering key is `id` ascending. Reason: stable pagination needs a total order. Confirmed by `Q-BE-404`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-437` — unasked degradable question: ASM-BE-409: Confirm assumption ASM-BE-409: the list operation uses `page`, `limit` and `include_archived`, and no `q` or `tags`. Reason: its operation record describes pagination and archived exclusion only. Confirmed by `GAP-BE-428`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-438` — unasked degradable question: ASM-BE-410: Confirm assumption ASM-BE-410: include_archived` accepts exactly `true` and `false`. Reason: US-003-06 cites `true` and an invalid-value case. Confirmed by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-439` — unasked degradable question: ASM-BE-411: Confirm assumption ASM-BE-411: page numbering starts at 1, and a `page` or `limit` of 0 is invalid. Reason: negative values are invalid (US-003-05 AC3) and zero has no meaning for either. Confirmed by `GAP-BE-424`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-440` — unasked degradable question: ASM-BE-412: Confirm assumption ASM-BE-412: the list operation applies archived exclusion by default, `include_archived`, pinned-first order and the page-size bounds as search does. Reason: its operation record summary, BR-019 ("search and list results"), BR-011, and US-006-05 ("default notes list view"). Confirmed by `GAP-BE-428`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-441` — unasked degradable question: ASM-BE-413: Confirm assumption ASM-BE-413: several tags combine as AND. Reason: "narrow results". Confirmed by `Q-BE-402`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-442` — unasked degradable question: ASM-BE-414: Confirm assumption ASM-BE-414: the maximum page size is 100, and a larger `limit` gets 400. Reason: NFR-007 cites page sizes up to 100, and invalid pagination already gives 400. Confirmed by `Q-BE-401`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-443` — unasked degradable question: ASM-BE-415: Confirm assumption ASM-BE-415: checks run in the order authenticate, authorise, validate, check index availability. Reason: no input states it. Confirmed by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-444` — unasked degradable question: ASM-BE-416: Confirm assumption ASM-BE-416: the list operation does not read the search index and never returns 503. Reason: the approved revision's operation records list only 401 and 403 errors for it. [feedback.md, approved BACKEND-SPEC.md api-surface] Confirmed by `GAP-BE-422`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-445` — unasked degradable question: ASM-BE-417: Confirm assumption ASM-BE-417: the indexes in the data model are proposals: NOTE on (`status`, `pinned`, `id`) and NOTE_TAG on (`tag_name`, `note_id`). Reason: they serve the queries stated. Confirmed by the final ordering rule (`GAP-BE-426`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-446` — unasked blocking question: Q-BE-401: What are the default and maximum page sizes, and is a `limit` above the maximum rejected or clamped? — maximum 100, above-maximum rejected with 400 (`ASM-BE-414`). Default size unasserted (`GAP-BE-424`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-447` — unasked blocking question: Q-BE-402: Do several tags combine as AND or OR? — AND (`ASM-BE-413`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-448` — unasked blocking question: Q-BE-403: Is a search with `tags` and no `q` valid? — yes (`CON-BE-401` reading)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-449` — unasked blocking question: Q-BE-404: What is the order within the pinned and unpinned groups, and how is relevance defined? — search by relevance, as cited, with relevance undefined; id tie-break only (`ASM-BE-408`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 19 `GAP-` item(s) were raised while writing `modules/residency-compliance-SPEC.md`: GAP-BE-305, GAP-BE-475, GAP-BE-307, GAP-BE-308, GAP-BE-304, GAP-BE-472, GAP-BE-484, GAP-BE-477, GAP-BE-480, GAP-BE-482, GAP-BE-473, GAP-BE-481, GAP-BE-479, GAP-BE-474, GAP-BE-085, GAP-BE-089, GAP-BE-478, GAP-BE-476, GAP-BE-483

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/residency-compliance-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-compliance-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 2 `CON-` item(s) were raised while writing `modules/residency-compliance-SPEC.md`: CON-BE-151, CON-BE-451

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/residency-compliance-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-compliance-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 18 `ASM-` item(s) were raised while writing `modules/residency-compliance-SPEC.md`: ASM-BE-456, ASM-BE-470, ASM-BE-466, ASM-BE-468, ASM-BE-457, ASM-BE-461, ASM-BE-464, ASM-BE-458, ASM-BE-467, ASM-BE-462, ASM-BE-460, ASM-BE-463, ASM-BE-469, ASM-BE-301, ASM-BE-459, ASM-BE-302, ASM-BE-153, ASM-BE-465

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/residency-compliance-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-compliance-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-485` — unasked degradable question: Q-BE-451: Should a post-launch scan finding an unreadable region raise a drift alert?

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-486` — unasked degradable question: Q-BE-452: Should a failed scan alert the DevOps team?

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-487` — unasked degradable question: ASM-BE-468: Confirm assumption ASM-BE-468: the report shows only the latest scan's resources. | A rule for decommissioned resources. |

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-488` — unasked degradable question: ASM-BE-456: Confirm assumption ASM-BE-456: Residency data is stored in the application PostgreSQL. The tables and column types in section 2 are proposals. — No input states them. Confirmed by closing `GAP-BE-305`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-489` — unasked degradable question: ASM-BE-457: Confirm assumption ASM-BE-457: Recognised types are `database`, `compute` and `backup-storage`. Any other value is unrecognised. — BR-001 names database, compute and backups. The value names are proposals. Confirmed by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-490` — unasked degradable question: ASM-BE-458: Confirm assumption ASM-BE-458: The evaluation precedence in section 3.2. — No input states one. Confirmed by a rule from the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-491` — unasked degradable question: ASM-BE-459: Confirm assumption ASM-BE-459: The percentage counts `fail` and `unverified` rows in the denominator. Zero rows is not 100%. — NFR-001 requires 100% of components EU-verified. Confirmed by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-492` — unasked degradable question: ASM-BE-460: Confirm assumption ASM-BE-460: The report is built from the latest completed scan and states its time. The launch-gate operation runs a fresh scan in the same request. A refused or failed scan gives `FAILED`. — NFR-004 requires an accurate report at each gate check. Confirmed by `GAP-BE-481`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-493` — unasked degradable question: ASM-BE-461: Confirm assumption ASM-BE-461: The validation and gate-check operations are stateless verdicts. Callers must treat a refusal, a timeout or no response as failure. — BR-001 Enforcement places enforcement in infrastructure code. Confirmed by `GAP-BE-475`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-494` — unasked degradable question: ASM-BE-462: Confirm assumption ASM-BE-462: An empty component list or an empty `approved_region` is not compliant. — Fail-closed, per AC-001-03-03.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-495` — unasked degradable question: ASM-BE-463: Confirm assumption ASM-BE-463: One alert per resource per detecting scan. It is dispatched at the end of that scan, with details: resource id, type, detected region and raise time. — AC-001-06-02 and NFR-003. "Resource details" is not defined. Confirmed by `GAP-BE-479`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-496` — unasked degradable question: ASM-BE-464: Confirm assumption ASM-BE-464: Scans are serialised. A second start while one runs is refused. — Prevents duplicate alerts and a contradictory report. Confirmed by `GAP-BE-481`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-497` — unasked degradable question: ASM-BE-465: Confirm assumption ASM-BE-465: The periodic job is enabled when deployed. — No launch signal exists. Confirmed by `GAP-BE-478`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-498` — unasked degradable question: ASM-BE-466: Confirm assumption ASM-BE-466: Module-created rows use application-assigned uuids. All instants are `timestamptz`, UTC. — No input states either. Confirmed by the product owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-499` — unasked degradable question: ASM-BE-467: Confirm assumption ASM-BE-467: The gate check ignores resource type and evaluates region only. — AC-001-03-01 to AC-001-03-03 speak of regions only.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-500` — unasked degradable question: ASM-BE-469: Confirm assumption ASM-BE-469: The 30-second bound applies to the whole backup validation operation. — AC-001-02-02 does not say how the budget is shared with the pipeline. Confirmed by `GAP-BE-481`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-501` — unasked degradable question: ASM-BE-470: Confirm assumption ASM-BE-470: delivered_at` is set when the notifier acknowledges, and pending alerts are retried until then. — AC-001-06-02 says "sent". Delivery proof and channel are unstated (`GAP-BE-308`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 19 `GAP-` item(s) were raised while writing `modules/api-docs-SPEC.md`: GAP-BE-303, GAP-BE-089, GAP-BE-522, GAP-BE-304, GAP-BE-528, GAP-BE-532, GAP-BE-523, GAP-BE-309, GAP-BE-524, GAP-BE-035, GAP-BE-300, GAP-BE-038, GAP-BE-053, GAP-BE-530, GAP-BE-531, GAP-BE-529, GAP-BE-525, GAP-BE-526, GAP-BE-527

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/api-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/api-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 2 `CON-` item(s) were raised while writing `modules/api-docs-SPEC.md`: CON-BE-501, CON-BE-006

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/api-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/api-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 7 `ASM-` item(s) were raised while writing `modules/api-docs-SPEC.md`: ASM-BE-300, ASM-BE-506, ASM-BE-509, ASM-BE-507, ASM-BE-508, ASM-BE-510, ASM-BE-302

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/api-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/api-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-533` — unasked blocking question: Q-BE-501: Who may call the OpenAPI document operation and the coverage operation: anyone, any authenticated caller, or specific roles? — none. The authorization section is `BLOCKED`.

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-534` — unasked degradable question: ASM-BE-506: Confirm assumption ASM-BE-506: The module owns no tables. The document is generated at build from the route registry and module contracts. Coverage is computed on request. — No input names storage for either. BR-012 describes an automated comparison. — GAP-BE-522

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-535` — unasked degradable question: ASM-BE-507: Confirm assumption ASM-BE-507: An unsupported Accept header yields the default-format fallback with a clear content type, not 406. — US-004-01 permits either. — GAP-BE-309

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-536` — unasked degradable question: ASM-BE-508: Confirm assumption ASM-BE-508: Success status for both operations is 200. — No status is stated. Reads elsewhere use 200. — a status decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-537` — unasked degradable question: ASM-BE-509: Confirm assumption ASM-BE-509: The Scope section and the data-handling note are carried in the OpenAPI document's descriptive text. — BR-013 says the OpenAPI spec must document residency. US-005-01 says the published API documentation. — GAP-BE-529

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-538` — unasked degradable question: ASM-BE-510: Confirm assumption ASM-BE-510: An endpoint is one method and path pair. — BR-012 says "endpoint". The project spec identifies operations by method and path. — GAP-BE-531

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 7 `GAP-` item(s) were raised while writing `modules/scope-guardrail-SPEC.md`: GAP-BE-572, GAP-BE-001, GAP-BE-575, GAP-BE-573, GAP-BE-574, GAP-BE-309, GAP-BE-576

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/scope-guardrail-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-guardrail-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 2 `CON-` item(s) were raised while writing `modules/scope-guardrail-SPEC.md`: CON-BE-007, CON-BE-019

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/scope-guardrail-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-guardrail-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 7 `ASM-` item(s) were raised while writing `modules/scope-guardrail-SPEC.md`: ASM-BE-556, ASM-BE-557, ASM-BE-558, ASM-BE-559, ASM-BE-560, ASM-BE-300, ASM-BE-301

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/scope-guardrail-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-guardrail-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-577` — unasked blocking question: Q-BE-551: Which error code does a multipart rejection use: `OUT_OF_SCOPE` or `UNSUPPORTED_MEDIA_TYPE`? — UNSUPPORTED_MEDIA_TYPE`, as in the project registry (`GAP-BE-573`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-578` — unasked degradable question: ASM-BE-556: Confirm assumption ASM-BE-556: The exclusion registry is static configuration and has no database tables. — Nothing in the inputs requires persisted guardrail data. — an architecture decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-579` — unasked degradable question: ASM-BE-557: Confirm assumption ASM-BE-557: A WebSocket upgrade is detected from the HTTP `Upgrade` header, and an SSE connection from an `Accept` of `text/event-stream`. — US-005-03 names the mechanisms but no input states how they are recognised. — an implementation decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-580` — unasked degradable question: ASM-BE-558: Confirm assumption ASM-BE-558: Classification order is path and protocol checks first, then content-type checks. — A single request can match more than one rule, for example an attachment path carrying multipart, and the response must be predictable. — a decision from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-581` — unasked degradable question: ASM-BE-559: Confirm assumption ASM-BE-559: An unreadable OpenAPI document fails the contract test. — It follows the fail-closed rule of US-001-03 AC3 and US-004-04 AC3 (a malformed spec fails rather than reporting false coverage). — confirmation

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-582` — unasked degradable question: ASM-BE-560: Confirm assumption ASM-BE-560: The multipart rule applies to POST `/notes` and PATCH `/notes/{id}`, the note create and update operations in the project api-surface. — US-005-02 says "a note create/update endpoint" and no input lists them by path. — the final operation list from `notes

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open
<!-- /SECTION: backend.gaps -->
