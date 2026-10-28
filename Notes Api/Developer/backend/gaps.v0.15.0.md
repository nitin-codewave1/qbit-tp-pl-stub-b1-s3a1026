---
id: agent-developer/notes_api/backend/GAPS.md
type: gaps
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.15.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/GAPS.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 6a137249-ccab-5a0a-bd2d-8ea991e30fa2
okf_id: notes_api:backend/GAPS.md
---

<!-- SECTION: backend.gaps | status: VERIFIED -->
## Gaps

### `GAP-index` — 27 `GAP-` item(s) were raised while writing `BACKEND-SPEC.md`: GAP-BE-085, GAP-BE-024, GAP-BE-001, GAP-BE-003, GAP-BE-017, GAP-BE-031, GAP-BE-033, GAP-BE-035, GAP-BE-038, GAP-BE-053, GAP-BE-083, GAP-BE-089, GAP-BE-101, GAP-BE-104, GAP-BE-300, GAP-BE-301, GAP-BE-302, GAP-BE-303, GAP-BE-304, GAP-BE-305, GAP-BE-306, GAP-BE-307, GAP-BE-308, GAP-BE-309, GAP-BE-310, GAP-BE-500, GAP-BE-501

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 8 `CON-` item(s) were raised while writing `BACKEND-SPEC.md`: CON-BE-007, CON-BE-151, CON-BE-003, CON-BE-006, CON-BE-011, CON-BE-019, CON-BE-300, CON-BE-010

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 15 `ASM-` item(s) were raised while writing `BACKEND-SPEC.md`: ASM-BE-026, ASM-BE-305, ASM-BE-153, ASM-BE-300, ASM-BE-015, ASM-BE-019, ASM-BE-025, ASM-BE-027, ASM-BE-029, ASM-BE-034, ASM-BE-044, ASM-BE-301, ASM-BE-302, ASM-BE-303, ASM-BE-304

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-502` — unasked degradable question: ASM-BE-027: Confirm assumption ASM-BE-027: assumption — Email goes through AWS SES. NFR-017 names a transactional email service in an AWS region and the platform is AWS.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-503` — unasked degradable question: ASM-BE-029: Confirm assumption ASM-BE-029: assumption — The resend and lock counters are keyed per normalised email address.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-504` — unasked degradable question: ASM-BE-034: Confirm assumption ASM-BE-034: assumption — pinned` is `NOT NULL`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-505` — unasked degradable question: ASM-BE-044: Confirm assumption ASM-BE-044: assumption — The resend window is sliding (trailing 10 minutes).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-506` — unasked degradable question: ASM-BE-153: Confirm assumption ASM-BE-153: assumption — The scan period is no longer than 15 minutes, because NFR-003 requires an alert within 15 minutes of drift detection.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-507` — unasked degradable question: ASM-BE-300: Confirm assumption ASM-BE-300: assumption — The error envelope is the proposal in `backend.error-model`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-508` — unasked degradable question: ASM-BE-301: Confirm assumption ASM-BE-301: assumption — Error code names other than `OUT_OF_SCOPE` are proposed names. [US-005-04]

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-509` — unasked degradable question: ASM-BE-302: Confirm assumption ASM-BE-302: assumption — A role has no permission on an operation unless an input grants it.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-510` — unasked degradable question: ASM-BE-303: Confirm assumption ASM-BE-303: assumption — NFR-019 lists, positionally: code challenges 1 day, send attempts 1 day, account locks 5 days, notes 1 day.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-511` — unasked degradable question: ASM-BE-304: Confirm assumption ASM-BE-304: assumption — No notes_api database exists yet, so migrations start from empty.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-512` — unasked degradable question: ASM-BE-305: Confirm assumption ASM-BE-305: assumption — The wrong-code count is per normalised email, increments on each wrong code, is cleared by a successful verification, and has no time window.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-513` — 6 user stor(ies) belong to no backend module: US-005-06, US-006-01, US-006-02, US-006-03, US-006-04, US-006-05

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** user_stories, BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** a module in the project spec's module map that owns them, or a decision that this surface does not serve them
- **Status.** open

### `GAP-index` — 22 `GAP-` item(s) were raised while writing `modules/auth-SPEC.md`: GAP-BE-302, GAP-BE-085, GAP-BE-514, GAP-BE-031, GAP-BE-518, GAP-BE-033, GAP-BE-304, GAP-BE-306, GAP-BE-083, GAP-BE-038, GAP-BE-053, GAP-BE-515, GAP-BE-035, GAP-BE-516, GAP-BE-517, GAP-BE-519, GAP-BE-104, GAP-BE-101, GAP-BE-520, GAP-BE-024, GAP-BE-089, GAP-BE-301

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

### `ASM-index` — 18 `ASM-` item(s) were raised while writing `modules/auth-SPEC.md`: ASM-BE-025, ASM-BE-303, ASM-BE-027, ASM-BE-306, ASM-BE-015, ASM-BE-019, ASM-BE-308, ASM-BE-309, ASM-BE-312, ASM-BE-305, ASM-BE-029, ASM-BE-307, ASM-BE-310, ASM-BE-311, ASM-BE-313, ASM-BE-044, ASM-BE-314, ASM-BE-302

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/auth-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/auth-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-521` — unasked degradable question: ASM-BE-306: Confirm assumption ASM-BE-306: Column types: uuid keys, text email, timestamptz instants, integer counter, bigint identity. — PostgreSQL 16 is the platform and no types are supplied. — A platform decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-522` — unasked degradable question: ASM-BE-307: Confirm assumption ASM-BE-307: A code is expired only when the instant is strictly after issue + 10 minutes. — BR-024 and US-006-06 say "after". BR-020 and BR-026 say "expires", so `GAP-BE-306` stays open. — A decision on `GAP-BE-306

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-523` — unasked degradable question: ASM-BE-308: Confirm assumption ASM-BE-308: A refused send writes no `send_attempts` row. — Otherwise a caller that keeps retrying is never released from the window. — A rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-524` — unasked degradable question: ASM-BE-309: Confirm assumption ASM-BE-309: failed_count` is reset to 0 when a lock is entered, so it ranges 0 to 4. — Otherwise the first wrong code after the lock would re-lock immediately. — A rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-525` — unasked degradable question: ASM-BE-310: Confirm assumption ASM-BE-310: Only the latest unconsumed challenge for an email is checked at verification. — GAP-BE-083` is open. One live code is the narrower reading. — A rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-526` — unasked degradable question: ASM-BE-311: Confirm assumption ASM-BE-311: Only a wrong code against the latest unexpired, unconsumed challenge counts toward the lock. Expired and consumed codes do not. — BR-022 says "wrong codes". An expired or used code is refused, not wrong. — A rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-527` — unasked degradable question: ASM-BE-312: Confirm assumption ASM-BE-312: login_locks.updated_at` is the retention basis for locks. — NFR-019 gives a period but no basis for locks. — A rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-528` — unasked degradable question: ASM-BE-313: Confirm assumption ASM-BE-313: A verify refused because of a lock changes neither `failed_count` nor `locked_until`. — BR-022 refuses verification during the lock and states no extension. — A rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-529` — unasked degradable question: ASM-BE-314: Confirm assumption ASM-BE-314: A verify with no challenge for the email does not count toward the lock. — There is no code to be wrong against. — A rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 17 `GAP-` item(s) were raised while writing `modules/notes-SPEC.md`: GAP-BE-564, GAP-BE-085, GAP-BE-089, GAP-BE-035, GAP-BE-017, GAP-BE-304, GAP-BE-309, GAP-BE-565, GAP-BE-566, GAP-BE-567, GAP-BE-003, GAP-BE-101, GAP-BE-568, GAP-BE-300, GAP-BE-569, GAP-BE-570, GAP-BE-571

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 2 `CON-` item(s) were raised while writing `modules/notes-SPEC.md`: CON-BE-003, CON-BE-300

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 18 `ASM-` item(s) were raised while writing `modules/notes-SPEC.md`: ASM-BE-356, ASM-BE-358, ASM-BE-359, ASM-BE-357, ASM-BE-034, ASM-BE-366, ASM-BE-370, ASM-BE-368, ASM-BE-367, ASM-BE-362, ASM-BE-363, ASM-BE-369, ASM-BE-365, ASM-BE-371, ASM-BE-360, ASM-BE-361, ASM-BE-364, ASM-BE-302

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-572` — unasked blocking question: Q-BE-351: May an archived note be edited, pinned, or have tags changed? — Yes, as for an active note (`ASM-BE-362`). It adds no state and no transition, only a guard, so it does not meet the blocking test.

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-573` — unasked blocking question: Q-BE-352: What does unarchive of an already-active note return? — A conflict with `NOTE_NOT_ARCHIVED` (`ASM-BE-363`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-574` — unasked blocking question: Q-BE-353: Is a concurrent PATCH last-write-wins or a conflict? — Last-write-wins on supplied fields (`ASM-BE-365`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-575` — unasked degradable question: ASM-BE-356: Confirm assumption ASM-BE-356: Column types: uuid keys, text strings, boolean, timestamptz. — PostgreSQL 16 is the platform and no types are supplied. — a platform decision (`GAP-BE-089`)

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-576` — unasked degradable question: ASM-BE-357: Confirm assumption ASM-BE-357: A new note has `status` `active`. — US-002-04 archives an "active, existing note" and the create criteria carry no status. — product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-577` — unasked degradable question: ASM-BE-358: Confirm assumption ASM-BE-358: The note id is a server-assigned, opaque uuid, never client-supplied. — AC-002-01-01 says the API returns a unique id. — product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-578` — unasked degradable question: ASM-BE-359: Confirm assumption ASM-BE-359: A title or body of whitespace only counts as empty. — BR-005 rejects "empty/null" payloads. — product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-579` — unasked degradable question: ASM-BE-360: Confirm assumption ASM-BE-360: The non-empty rule applies to PATCH as well as create. — BR-005 says "at creation" only; an edit that blanks a field would lose content, against BR-006's intent. — product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-580` — unasked degradable question: ASM-BE-361: Confirm assumption ASM-BE-361: PATCH accepts only `title`, `body`, `pinned`. An empty payload or an unknown field is 400. Tags change only through the tag operations. — The supplied operation records list these fields; see `GAP-BE-566`. — GAP-BE-566

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-581` — unasked degradable question: ASM-BE-362: Confirm assumption ASM-BE-362: Edit, pin and tag changes on an archived note behave as on an active note. — No input forbids them. — Q-BE-351

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-582` — unasked degradable question: ASM-BE-363: Confirm assumption ASM-BE-363: Unarchive of an active note is a conflict with `NOTE_NOT_ARCHIVED`. — Symmetry with US-002-04. — Q-BE-352

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-583` — unasked degradable question: ASM-BE-364: Confirm assumption ASM-BE-364: Removing a tag not on the note returns 404 `NOT_FOUND`. — No input states it; consistent with the 404 policy. — a status decision (`GAP-BE-567`)

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-584` — unasked degradable question: ASM-BE-365: Confirm assumption ASM-BE-365: Concurrent PATCH requests are last-write-wins on the fields each supplies. — US-005-03 allows "a standard last-write/conflict response". — Q-BE-353

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-585` — unasked degradable question: ASM-BE-366: Confirm assumption ASM-BE-366: updated_at` advances on any real change to title, body, `pinned`, tags or `status`. A duplicate tag add changes nothing and does not advance it. — AC-002-02-01 requires an updated timestamp on edit; no input covers other changes. — product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-586` — unasked degradable question: ASM-BE-367: Confirm assumption ASM-BE-367: Foreign keys use `ON DELETE RESTRICT`. — The module never deletes notes; BR-007 and NFR-005 forbid data loss. — a platform decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-587` — unasked degradable question: ASM-BE-368: Confirm assumption ASM-BE-368: A tag row persists after its last note is untagged. — No input states the fate of an unused tag. — product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-588` — unasked degradable question: ASM-BE-369: Confirm assumption ASM-BE-369: Archive, add tag and remove tag on a missing note return 404 `NOT_FOUND`. — The 404 policy covers edit, unarchive and get; these follow it. — product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-589` — unasked degradable question: ASM-BE-370: Confirm assumption ASM-BE-370: Tag names are compared exactly as supplied (case-sensitive, no trimming or normalisation). — GAP-BE-035` states no rule. — GAP-BE-035

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-590` — unasked degradable question: ASM-BE-371: Confirm assumption ASM-BE-371: pinned` must be a JSON boolean. No coercion from strings or numbers. — BR-018 calls it boolean. — product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 16 `GAP-` item(s) were raised while writing `modules/search-SPEC.md`: GAP-BE-614, GAP-BE-502, GAP-BE-035, GAP-BE-500, GAP-BE-616, GAP-BE-501, GAP-BE-085, GAP-BE-615, GAP-BE-310, GAP-BE-617, GAP-BE-621, GAP-BE-618, GAP-BE-620, GAP-BE-619, GAP-BE-003, GAP-BE-089

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 3 `CON-` item(s) were raised while writing `modules/search-SPEC.md`: CON-BE-003, CON-BE-401, CON-BE-300

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 9 `ASM-` item(s) were raised while writing `modules/search-SPEC.md`: ASM-BE-406, ASM-BE-410, ASM-BE-409, ASM-BE-412, ASM-BE-301, ASM-BE-408, ASM-BE-411, ASM-BE-302, ASM-BE-407

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-622` — unasked degradable question: ASM-BE-406: Confirm assumption ASM-BE-406: Several tags combine as AND: a note must carry every listed tag. — US-003-02 says filters "narrow results" and US-003-04 returns notes "matching both criteria". Narrowing is the narrower reading. — answer to `GAP-BE-614

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-623` — unasked degradable question: ASM-BE-407: Confirm assumption ASM-BE-407: tags` is a single parameter holding a comma-separated list. — No input states an encoding. — answer to `GAP-BE-614

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-624` — unasked degradable question: ASM-BE-408: Confirm assumption ASM-BE-408: The keyword match is case-insensitive over title and body, and relevance is the rank produced by the database text-search facility. — US-003-01 says "across title and content" and "ordered by relevance"; nothing defines either. — answer to `GAP-BE-615

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-625` — unasked degradable question: ASM-BE-409: Confirm assumption ASM-BE-409: page` starts at 1. — No input states a base. `page=0` would then give 400. — answer to `GAP-BE-616

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-626` — unasked degradable question: ASM-BE-410: Confirm assumption ASM-BE-410: include_archived` accepts the literals `true` and `false` only; any other value gives 400. — US-003-06 requires 400 for an invalid value and names `true`. — answer to `GAP-BE-616

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-627` — unasked degradable question: ASM-BE-411: Confirm assumption ASM-BE-411: The final sort key after pinned and relevance is the note `id`, so results are deterministic. — Pagination (US-003-05) needs a total order, and `GAP-BE-310` leaves the within-group order undefined. — answer to `GAP-BE-310

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-628` — unasked degradable question: ASM-BE-412: Confirm assumption ASM-BE-412: The indexes proposed in the data model (text-search on title and body, btree on tag name and note id, btree on status and pinned) are used. — US-003-03 requires indexed lookup; the type is unstated (`backend.migration-plan`). — a platform decision (`GAP-BE-089`)

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 18 `GAP-` item(s) were raised while writing `modules/residency-compliance-SPEC.md`: GAP-BE-303, GAP-BE-305, GAP-BE-308, GAP-BE-664, GAP-BE-304, GAP-BE-672, GAP-BE-667, GAP-BE-668, GAP-BE-673, GAP-BE-500, GAP-BE-666, GAP-BE-085, GAP-BE-669, GAP-BE-665, GAP-BE-671, GAP-BE-307, GAP-BE-089, GAP-BE-670

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

### `ASM-index` — 12 `ASM-` item(s) were raised while writing `modules/residency-compliance-SPEC.md`: ASM-BE-456, ASM-BE-461, ASM-BE-462, ASM-BE-458, ASM-BE-463, ASM-BE-459, ASM-BE-466, ASM-BE-460, ASM-BE-457, ASM-BE-302, ASM-BE-153, ASM-BE-465

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/residency-compliance-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-compliance-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-674` — unasked degradable question: Q-BE-451: Should the launch gate require a scan newer than some age?

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-675` — unasked degradable question: Q-BE-452: Is a resource with a missing region in a scan `fail` or `unverified`?

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-676` — unasked degradable question: ASM-BE-456: Confirm assumption ASM-BE-456: Types are uuid keys, text, timestamptz, integer, jsonb, and the data lives in the notes_api database. Reason: no types or location are supplied (`GAP-BE-305`); PostgreSQL 16 is the platform.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-677` — unasked degradable question: ASM-BE-457: Confirm assumption ASM-BE-457: Percent compliant is `pass` over the total, with `unverified` counted as not verified; zero resources is not 100%. Reason: US-001-05 asks for "100% EU-verified" and AC-001-04-02 says unverified is not silently omitted.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-678` — unasked degradable question: ASM-BE-458: Confirm assumption ASM-BE-458: Recognised resource types are database, compute and backup storage; any other is unrecognised. Reason: BR-001 names database, compute and backups.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-679` — unasked degradable question: ASM-BE-459: Confirm assumption ASM-BE-459: A resource with a null or unreadable region is `fail` in a scan. Reason: AC-001-03-03 fails closed for the deployment check; the scan case is not stated (`Q-BE-452`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-680` — unasked degradable question: ASM-BE-460: Confirm assumption ASM-BE-460: A drift alert is raised when a resource enters `fail` on a non-approved region, or is found failing with no earlier alert for that region; later scans of an unchanged failing resource raise none. Reason: BR-004 alerts on detection; repeating the alert every scan is not stated.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-681` — unasked degradable question: ASM-BE-461: Confirm assumption ASM-BE-461: Region codes are compared exactly as supplied, case-sensitive, with no normalisation. Reason: no input states a rule; supplied values are carried verbatim.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-682` — unasked degradable question: ASM-BE-462: Confirm assumption ASM-BE-462: An empty `approved_regions` approves nothing. Reason: BR-001 allows only approved regions, so absence of a list must not approve any.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-683` — unasked degradable question: ASM-BE-463: Confirm assumption ASM-BE-463: The gate evaluates the latest completed scan, reports its identity and time, and is `FAILED` when no scan has completed. Reason: US-001-05 reads the compliance report; NFR-004 asks it to be refreshed at each check but states no mechanism (`GAP-BE-666`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-684` — unasked degradable question: ASM-BE-465: Confirm assumption ASM-BE-465: API-RESIDENCYCOMPLIANCE-405210` and `API-RESIDENCYCOMPLIANCE-585907` are repeatable. Reason: they are decisions over their input.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-685` — unasked degradable question: ASM-BE-466: Confirm assumption ASM-BE-466: A deployment check with no resource supplied fails. Reason: nothing could be verified, and AC-001-03-03 forbids passing by default.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 17 `GAP-` item(s) were raised while writing `modules/api-docs-SPEC.md`: GAP-BE-001, GAP-BE-714, GAP-BE-720, GAP-BE-304, GAP-BE-718, GAP-BE-716, GAP-BE-303, GAP-BE-309, GAP-BE-035, GAP-BE-300, GAP-BE-500, GAP-BE-715, GAP-BE-717, GAP-BE-719, GAP-BE-721, GAP-BE-722, GAP-BE-089

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/api-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/api-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 2 `CON-` item(s) were raised while writing `modules/api-docs-SPEC.md`: CON-BE-006, CON-BE-019

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/api-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/api-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 10 `ASM-` item(s) were raised while writing `modules/api-docs-SPEC.md`: ASM-BE-506, ASM-BE-507, ASM-BE-513, ASM-BE-509, ASM-BE-512, ASM-BE-510, ASM-BE-511, ASM-BE-508, ASM-BE-302, ASM-BE-300

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/api-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/api-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-723` — unasked degradable question: ASM-BE-506: Confirm assumption ASM-BE-506: This module owns no tables. The Scope section, data-handling note and examples are source-controlled content. — No input states persistence; US-005-01 AC2 says "before merge"; `backend.migration-plan` has no `api-docs` migration. — a decision on `GAP-BE-720

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-724` — unasked degradable question: ASM-BE-507: Confirm assumption ASM-BE-507: The document is generated from the route registry at build time and ships in the same artefact as the routes. The checks run on every build. — US-004-01 AC2 requires "before or at the same time it becomes callable". — the orchestrator or a platform decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-725` — unasked degradable question: ASM-BE-508: Confirm assumption ASM-BE-508: "Search and tagging endpoints" means the operations of the `search` module and the tag add and tag remove operations of the `notes` module. — NFR-009 names "search and tagging" with no paths (`GAP-BE-303`). — path decisions

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-726` — unasked degradable question: ASM-BE-509: Confirm assumption ASM-BE-509: Endpoint coverage below 100% fails publication. Example coverage below 100% is reported and does not. — BR-012 has no exception and NFR-008 is Must Have. NFR-009 is Should Have. — a decision on build gating

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-727` — unasked degradable question: ASM-BE-510: Confirm assumption ASM-BE-510: An unsatisfiable Accept header returns 200 with the JSON document and an explicit content type, not 406. — US-004-01 AC3 allows either. The status policy and registry list no 406 code. — GAP-BE-309

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-728` — unasked degradable question: ASM-BE-511: Confirm assumption ASM-BE-511: The coverage percentage is documented count over implemented count, and 100% is reported only on exact equality. — US-004-04 AC2 needs a visible shortfall. Rounding could hide one. — confirmation

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-729` — unasked degradable question: ASM-BE-512: Confirm assumption ASM-BE-512: An endpoint counts as documented when the document has an operation with the same method and path. Path templates are compared after normalisation. Schema completeness is checked separately under FR-005 and FR-007. — US-004-04 defines coverage as presence in the spec. — confirmation

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-730` — unasked degradable question: ASM-BE-513: Confirm assumption ASM-BE-513: The served document has the same content as `contracts/openapi.yaml`, differing only in representation. — Both derive from the same operation records and schemas. — the orchestrator

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 11 `GAP-` item(s) were raised while writing `modules/scope-guardrail-SPEC.md`: GAP-BE-001, GAP-BE-764, GAP-BE-765, GAP-BE-766, GAP-BE-767, GAP-BE-769, GAP-BE-768, GAP-BE-085, GAP-BE-089, GAP-BE-309, GAP-BE-300

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

### `ASM-index` — 7 `ASM-` item(s) were raised while writing `modules/scope-guardrail-SPEC.md`: ASM-BE-556, ASM-BE-557, ASM-BE-558, ASM-BE-559, ASM-BE-300, ASM-BE-560, ASM-BE-302

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/scope-guardrail-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-guardrail-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open
<!-- /SECTION: backend.gaps -->
