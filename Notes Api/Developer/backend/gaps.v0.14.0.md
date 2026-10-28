---
id: agent-developer/notes_api/backend/GAPS.md
type: gaps
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.14.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/GAPS.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: cbf4f6bb-f33c-5c3c-8b4c-5b95b8794990
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

### `CON-index` — 8 `CON-` item(s) were raised while writing `BACKEND-SPEC.md`: CON-BE-019, CON-BE-007, CON-BE-151, CON-BE-003, CON-BE-006, CON-BE-011, CON-BE-300, CON-BE-010

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 18 `ASM-` item(s) were raised while writing `BACKEND-SPEC.md`: ASM-BE-026, ASM-BE-303, ASM-BE-305, ASM-BE-153, ASM-BE-300, ASM-BE-015, ASM-BE-019, ASM-BE-025, ASM-BE-027, ASM-BE-029, ASM-BE-034, ASM-BE-044, ASM-BE-301, ASM-BE-302, ASM-BE-304, ASM-BE-306, ASM-BE-502, ASM-BE-310

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-502` — unasked degradable question: ASM-BE-027: Confirm assumption ASM-BE-027: assumption — Email goes through AWS SES. Reason: NFR-017 names a transactional email service in an AWS region and the platform is AWS. Confirmed by `Q-BE-005`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-503` — unasked degradable question: ASM-BE-029: Confirm assumption ASM-BE-029: assumption — The resend and lock counters are keyed per normalised email address. Reason: BR-021 to BR-028 speak of requests and of an account, never of a client.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-504` — unasked degradable question: ASM-BE-034: Confirm assumption ASM-BE-034: assumption — pinned` is `NOT NULL`. Reason: BR-018 gives it a default and no null state.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-505` — unasked degradable question: ASM-BE-044: Confirm assumption ASM-BE-044: assumption — The resend window is sliding (trailing 10 minutes). Reason: BR-021 reads "within 10 minutes".

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-506` — unasked degradable question: ASM-BE-153: Confirm assumption ASM-BE-153: assumption — The scan period is no longer than 15 minutes. Reason: NFR-003 requires an alert within 15 minutes of drift detection. Confirmed by `Q-BE-153`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-507` — unasked degradable question: ASM-BE-300: Confirm assumption ASM-BE-300: assumption — The error envelope is the proposal in `backend.error-model`. Reason: none is supplied. Confirmed by `GAP-BE-300`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-508` — unasked degradable question: ASM-BE-301: Confirm assumption ASM-BE-301: assumption — Error code names other than `OUT_OF_SCOPE` are proposed names. Reason: only `OUT_OF_SCOPE` is cited, as an example. [stories.md US-005-04]

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-509` — unasked degradable question: ASM-BE-302: Confirm assumption ASM-BE-302: assumption — A role has no permission on an operation unless an input grants it. Reason: no input defines a roles model. Confirmed by `GAP-BE-085`'s closing artefact.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-510` — unasked degradable question: ASM-BE-303: Confirm assumption ASM-BE-303: assumption — NFR-019 lists, positionally: code challenges 1 day, send attempts 1 day, account locks 5 days, notes 1 day. Reason: the sentence names four record types and four numbers. Confirmed by a corrected NFR-019.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-511` — unasked degradable question: ASM-BE-304: Confirm assumption ASM-BE-304: assumption — No notes_api database exists yet, so the migrations start from empty. Reason: no input states otherwise. Confirmed by the orchestrator.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-512` — unasked degradable question: ASM-BE-305: Confirm assumption ASM-BE-305: assumption — The wrong-code count is per normalised email, increments on each wrong code, is cleared by a successful verification, and has no time window. Reason: no input states a window. Confirmed by `GAP-BE-024`'s closing artefact.

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

### `GAP-index` — 23 `GAP-` item(s) were raised while writing `modules/auth-SPEC.md`: GAP-BE-302, GAP-BE-085, GAP-BE-514, GAP-BE-031, GAP-BE-521, GAP-BE-033, GAP-BE-304, GAP-BE-306, GAP-BE-083, GAP-BE-038, GAP-BE-053, GAP-BE-515, GAP-BE-035, GAP-BE-516, GAP-BE-517, GAP-BE-104, GAP-BE-101, GAP-BE-518, GAP-BE-089, GAP-BE-024, GAP-BE-301, GAP-BE-519, GAP-BE-520

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

### `ASM-index` — 17 `ASM-` item(s) were raised while writing `modules/auth-SPEC.md`: ASM-BE-025, ASM-BE-303, ASM-BE-027, ASM-BE-503, ASM-BE-505, ASM-BE-015, ASM-BE-019, ASM-BE-507, ASM-BE-508, ASM-BE-510, ASM-BE-305, ASM-BE-504, ASM-BE-506, ASM-BE-509, ASM-BE-044, ASM-BE-302, ASM-BE-029

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/auth-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/auth-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-522` — unasked degradable question: ASM-BE-503: Confirm assumption ASM-BE-503: A verify refused because of a lock changes neither `failed_count` nor `locked_until`. — BR-022 refuses verification during the lock and states no extension. — A rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-523` — unasked degradable question: ASM-BE-504: Confirm assumption ASM-BE-504: A code is expired only when the instant is strictly after issue + 10 minutes. — BR-024 and US-006-06 say "after". BR-020 and BR-026 say "expires", so `GAP-BE-306` stays open. — A decision on `GAP-BE-306

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-524` — unasked degradable question: ASM-BE-505: Confirm assumption ASM-BE-505: Column types: uuid keys, text email, timestamptz instants, integer counter, bigint identity. — PostgreSQL 16 is the platform and no types are supplied. — A platform decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-525` — unasked degradable question: ASM-BE-506: Confirm assumption ASM-BE-506: Only the latest unconsumed challenge for an email is checked at verification. — No input says whether earlier codes stay valid (`GAP-BE-083`). One live code is the narrower reading. — A rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-526` — unasked degradable question: ASM-BE-507: Confirm assumption ASM-BE-507: A refused send writes no `send_attempts` row. — Otherwise a caller that keeps retrying is never released from the window. — A rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-527` — unasked degradable question: ASM-BE-508: Confirm assumption ASM-BE-508: failed_count` is reset to 0 when a lock is entered, so it ranges 0 to 4. — Otherwise the first wrong code after the lock would re-lock immediately. — A rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-528` — unasked degradable question: ASM-BE-509: Confirm assumption ASM-BE-509: Only a wrong code against the latest unexpired, unconsumed challenge counts toward the lock. Expired and consumed codes do not. — BR-022 says "wrong codes". An expired or used code is not wrong, only refused. — A rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-529` — unasked degradable question: ASM-BE-510: Confirm assumption ASM-BE-510: login_locks` has an `updated_at` column used as the retention basis. — NFR-019 gives a period but no basis for locks. — A rule from the product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 20 `GAP-` item(s) were raised while writing `modules/notes-SPEC.md`: GAP-BE-564, GAP-BE-003, GAP-BE-035, GAP-BE-571, GAP-BE-304, GAP-BE-033, GAP-BE-017, GAP-BE-309, GAP-BE-572, GAP-BE-565, GAP-BE-573, GAP-BE-101, GAP-BE-569, GAP-BE-089, GAP-BE-300, GAP-BE-303, GAP-BE-566, GAP-BE-567, GAP-BE-568, GAP-BE-570

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

### `ASM-index` — 18 `ASM-` item(s) were raised while writing `modules/notes-SPEC.md`: ASM-BE-565, ASM-BE-553, ASM-BE-555, ASM-BE-556, ASM-BE-554, ASM-BE-034, ASM-BE-567, ASM-BE-566, ASM-BE-559, ASM-BE-558, ASM-BE-562, ASM-BE-557, ASM-BE-560, ASM-BE-563, ASM-BE-561, ASM-BE-303, ASM-BE-302, ASM-BE-564

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-574` — unasked blocking question: Q-BE-351: May an archived note be edited, pinned or tagged? — Yes, as an active note (`ASM-BE-558`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-575` — unasked blocking question: Q-BE-352: What does unarchive of an active note return? — The archive conflict (`ASM-BE-559`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-576` — unasked blocking question: Q-BE-353: What does removing a tag that is not on the note return? — 404 `NOT_FOUND` (`ASM-BE-560`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-577` — unasked blocking question: Q-BE-354: Is concurrent PATCH last-write-wins or a conflict? — Last-write-wins (`ASM-BE-561`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-578` — unasked degradable question: ASM-BE-553: Confirm assumption ASM-BE-553: Column types: uuid keys, text for strings, boolean, timestamptz. — PostgreSQL 16 is the platform and no types are supplied. — platform decision (`GAP-BE-089`)

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-579` — unasked degradable question: ASM-BE-554: Confirm assumption ASM-BE-554: A new note has `status` `active`. — US-002-04 archives an "active, existing note" and the create criteria carry no status. — confirmation

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-580` — unasked degradable question: ASM-BE-555: Confirm assumption ASM-BE-555: The note id is server-assigned, opaque and never client-supplied. — AC-002-01-01 says the API returns a unique id. — confirmation

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-581` — unasked degradable question: ASM-BE-556: Confirm assumption ASM-BE-556: A title or body of whitespace only counts as empty. — BR-005 rejects "empty/null" payloads; whitespace carries no content. — product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-582` — unasked degradable question: ASM-BE-557: Confirm assumption ASM-BE-557: The non-empty rule for title and body also applies to PATCH. — BR-005 says "at creation" only; an edit that empties a field would lose content, against BR-006's intent. — product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-583` — unasked degradable question: ASM-BE-558: Confirm assumption ASM-BE-558: Edit, pin and tag changes on an archived note behave as on an active note. — No input forbids them; BR-008 says only that unarchive restores the prior state. — Q-BE-351

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-584` — unasked degradable question: ASM-BE-559: Confirm assumption ASM-BE-559: Unarchive of an active note returns the archive conflict. — Mirrors US-002-04's second criterion; nothing else is cited. — Q-BE-352

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-585` — unasked degradable question: ASM-BE-560: Confirm assumption ASM-BE-560: Removing a tag not on the note returns 404 `NOT_FOUND`. — Add is explicitly idempotent while removal has no criteria; 404 keeps the two distinguishable. — Q-BE-353

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-586` — unasked degradable question: ASM-BE-561: Confirm assumption ASM-BE-561: Concurrent PATCH is last-write-wins, atomic per request, with no version column. — US-005-03 allows last-write or conflict and specifies neither further. — Q-BE-354

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-587` — unasked degradable question: ASM-BE-562: Confirm assumption ASM-BE-562: updated_at` is set on PATCH, archive, unarchive and any tag change that alters the tag set. A repeated add of an existing tag, and a refused request, change nothing. — AC-002-02-01 requires an updated timestamp on edit only. — product owner (`GAP-BE-568`)

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-588` — unasked degradable question: ASM-BE-563: Confirm assumption ASM-BE-563: Tag and archive operations on an unknown note id return 404 `NOT_FOUND`. — US-002-02, US-002-05 and US-002-06 return 404 for unknown ids. — confirmation

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-589` — unasked degradable question: ASM-BE-564: Confirm assumption ASM-BE-564: A `tags` row is created on first add and kept when no note carries it. — No input says tags are removed. — GAP-BE-571

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-590` — unasked degradable question: ASM-BE-565: Confirm assumption ASM-BE-565: No delete operation for notes is in scope. "Deleted" in US-002-06 is treated as never-existing. — BR-007 keeps data; no input describes a delete operation. — CON-BE-300` resolution

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-591` — unasked degradable question: ASM-BE-566: Confirm assumption ASM-BE-566: note_tags.note_id` is `ON DELETE CASCADE`. — If a retention deletion is ever approved, it must leave no dangling links. Dormant until `CON-BE-300` is resolved. — CON-BE-300` resolution

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-592` — unasked degradable question: ASM-BE-567: Confirm assumption ASM-BE-567: Tag names are compared exactly, character for character. — No case or normalisation rule is stated. — GAP-BE-571

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 18 `GAP-` item(s) were raised while writing `modules/search-SPEC.md`: GAP-BE-614, GAP-BE-017, GAP-BE-624, GAP-BE-619, GAP-BE-616, GAP-BE-310, GAP-BE-617, GAP-BE-035, GAP-BE-618, GAP-BE-500, GAP-BE-620, GAP-BE-003, GAP-BE-085, GAP-BE-625, GAP-BE-622, GAP-BE-623, GAP-BE-615, GAP-BE-621

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

### `ASM-index` — 12 `ASM-` item(s) were raised while writing `modules/search-SPEC.md`: ASM-BE-603, ASM-BE-609, ASM-BE-607, ASM-BE-605, ASM-BE-611, ASM-BE-610, ASM-BE-608, ASM-BE-613, ASM-BE-612, ASM-BE-604, ASM-BE-606, ASM-BE-302

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-626` — unasked degradable question: Q-BE-401: Is a tag-only search (no `q`) allowed?

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-627` — unasked degradable question: Q-BE-402: With several tags, must a note carry all of them or any of them?

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-628` — unasked degradable question: Q-BE-403: Is an archived-only view needed?

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-629` — unasked degradable question: ASM-BE-603: Confirm assumption ASM-BE-603: The maximum `limit` is 100. NFR-007 states latency "up to page sizes of 100" but not a maximum. — Pagination decision (`GAP-BE-500`, `GAP-BE-614`)

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-630` — unasked degradable question: ASM-BE-604: Confirm assumption ASM-BE-604: page` starts at 1. No input states the base. — Contract decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-631` — unasked degradable question: ASM-BE-605: Confirm assumption ASM-BE-605: The final sort key is the note id, which makes paging deterministic. Required because the within-group order is undefined (`GAP-BE-310`). — Ordering decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-632` — unasked degradable question: ASM-BE-606: Confirm assumption ASM-BE-606: include_archived` accepts only `true` or `false`. Only `true` is cited. — GAP-BE-620

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-633` — unasked degradable question: ASM-BE-607: Confirm assumption ASM-BE-607: Several tags mean "all must be present". US-003-02 says filtering "narrows" results. — Q-BE-402

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-634` — unasked degradable question: ASM-BE-608: Confirm assumption ASM-BE-608: A keyword matches a note when its terms appear, case-insensitively, in title or body. Stories say "across title and content" only. — GAP-BE-616

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-635` — unasked degradable question: ASM-BE-609: Confirm assumption ASM-BE-609: The search index is PostgreSQL indexes (full-text and btree), since the platform names no other store. [platform.md] — Platform decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-636` — unasked degradable question: ASM-BE-610: Confirm assumption ASM-BE-610: 503 is returned when the database or its search indexes cannot serve the query. This is distinct from an unhandled failure (500). — GAP-BE-619

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-637` — unasked degradable question: ASM-BE-611: Confirm assumption ASM-BE-611: Authentication is checked before parameter validation. This is not cited and keeps unauthenticated callers from probing the parameters. — Contract decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-638` — unasked degradable question: ASM-BE-612: Confirm assumption ASM-BE-612: tags` is one parameter with comma-separated values, matching the `tags=project-x` form. — GAP-BE-617

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-639` — unasked degradable question: ASM-BE-613: Confirm assumption ASM-BE-613: Relevance ordering applies only when `q` is supplied. A tag-only search has no relevance score. — GAP-BE-310` (cited)

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 12 `GAP-` item(s) were raised while writing `modules/residency-compliance-SPEC.md`: GAP-BE-305, GAP-BE-308, GAP-BE-307, GAP-BE-085, GAP-BE-664, GAP-BE-666, GAP-BE-304, GAP-BE-665, GAP-BE-669, GAP-BE-667, GAP-BE-500, GAP-BE-668

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/residency-compliance-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-compliance-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 2 `CON-` item(s) were raised while writing `modules/residency-compliance-SPEC.md`: CON-BE-151, CON-BE-006

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/residency-compliance-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-compliance-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 17 `ASM-` item(s) were raised while writing `modules/residency-compliance-SPEC.md`: ASM-BE-653, ASM-BE-654, ASM-BE-655, ASM-BE-657, ASM-BE-656, ASM-BE-663, ASM-BE-662, ASM-BE-019, ASM-BE-301, ASM-BE-661, ASM-BE-665, ASM-BE-659, ASM-BE-658, ASM-BE-660, ASM-BE-664, ASM-BE-302, ASM-BE-153

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/residency-compliance-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-compliance-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-670` — unasked degradable question: ASM-BE-653: Confirm assumption ASM-BE-653: Residency records live in the notes_api PostgreSQL database in an EU region. — No other store is named, and BR-001 requires EU placement. — GAP-BE-305

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-671` — unasked degradable question: ASM-BE-654: Confirm assumption ASM-BE-654: Types are `uuid` ids generated by the service, `text`, `timestamptz`, `integer` and `numeric(5,2)`. — PostgreSQL 16 is the platform and no types are supplied. — Platform decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-672` — unasked degradable question: ASM-BE-655: Confirm assumption ASM-BE-655: An approved region is exactly a row of `approved_regions`. The table is empty until the list is supplied, and an empty list makes every resource `fail`. No region name is assumed. — AC-001-01-01 refers to an approved list that no input contains. Failing closed matches AC-001-03-03. — Q-BE-151

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-673` — unasked degradable question: ASM-BE-656: Confirm assumption ASM-BE-656: The status vocabulary is `pass`, `fail` and `unverified`. — AC-001-04-01 and AC-001-04-02 name pass/fail and `unverified`. — Product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-674` — unasked degradable question: ASM-BE-657: Confirm assumption ASM-BE-657: Recognised resource types are `database`, `compute` and `backup-storage`. Any other type is `unverified`. — BR-001 names database, compute and backups. The names are labels. — Product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-675` — unasked degradable question: ASM-BE-658: Confirm assumption ASM-BE-658: Percent compliant counts `pass` over all resources, with `fail` and `unverified` in the denominator, to two decimals. Zero resources gives 0 and a `FAILED` gate. — BR-003 requires 100% EU-verified, and AC-001-04-02 forbids omitting unverified resources. No evidence is not 100%. — Product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-676` — unasked degradable question: ASM-BE-659: Confirm assumption ASM-BE-659: The gate is computed at request time from persisted results of the latest scan run, which must be `COMPLETED`. A gate request triggers no scan. — NFR-004 requires an accurate report at each gate check; no input says a gate request triggers a scan. — GAP-BE-667

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-677` — unasked degradable question: ASM-BE-660: Confirm assumption ASM-BE-660: The region check and the backup validation are stateless and persist nothing except a log entry. — US-001-04 ties report data to scans, and no input says a pipeline check updates the records. — Product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-678` — unasked degradable question: ASM-BE-661: Confirm assumption ASM-BE-661: A resource whose region cannot be read in a scan is recorded `fail` and raises no alert. — It extends AC-001-03-03 to scans. BR-004 speaks only of resources detected outside the EU. — GAP-BE-669

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-679` — unasked degradable question: ASM-BE-662: Confirm assumption ASM-BE-662: At most one drift alert is `OPEN` per resource. An alert is `CLOSED` when a later scan reads the resource in an approved region or finds it gone, and a recurrence creates a new alert. — BR-004 requires an alert per detection and no input defines . — Product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-680` — unasked degradable question: ASM-BE-663: Confirm assumption ASM-BE-663: At most one scan run is `RUNNING`. A trigger or tick that finds one is refused or skipped. A `FAILED` run is not retried inline, and the next scheduled run is the retry. — No input states concurrency or retry for scans. — Operations decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-681` — unasked degradable question: ASM-BE-664: Confirm assumption ASM-BE-664: The 30 seconds in AC-001-02-02 run from receipt of the validation request to the response, and the validation inspects the configuration only. — The story speaks of the backup pipeline validating the config before running. — Contract decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-682` — unasked degradable question: ASM-BE-665: Confirm assumption ASM-BE-665: None of the seven operations takes an idempotency key. — No input states one. — GAP-BE-668

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 16 `GAP-` item(s) were raised while writing `modules/api-docs-SPEC.md`: GAP-BE-303, GAP-BE-309, GAP-BE-714, GAP-BE-715, GAP-BE-716, GAP-BE-717, GAP-BE-719, GAP-BE-035, GAP-BE-500, GAP-BE-089, GAP-BE-038, GAP-BE-053, GAP-BE-721, GAP-BE-720, GAP-BE-718, GAP-BE-722

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/api-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/api-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 3 `CON-` item(s) were raised while writing `modules/api-docs-SPEC.md`: CON-BE-007, CON-BE-003, CON-BE-006

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/api-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/api-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 11 `ASM-` item(s) were raised while writing `modules/api-docs-SPEC.md`: ASM-BE-704, ASM-BE-710, ASM-BE-703, ASM-BE-706, ASM-BE-711, ASM-BE-705, ASM-BE-300, ASM-BE-708, ASM-BE-709, ASM-BE-302, ASM-BE-707

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/api-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/api-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-723` — unasked blocking question: Q-BE-501: Who may call the four operations? Must an integrator authenticate? — Authenticated caller required (`ASM-BE-705`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-724` — unasked blocking question: Q-BE-502: Does coverage below 100% fail the CI build, or only report? — Fails the build (`ASM-BE-711`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-725` — unasked degradable question: ASM-BE-703: Confirm assumption ASM-BE-703: An unsupported `Accept` header returns the default-format (JSON) document with an explicit content type, not 406. — US-004-01 permits either; a fallback still serves the integrator. — decision on `GAP-BE-309

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-726` — unasked degradable question: ASM-BE-704: Confirm assumption ASM-BE-704: "All endpoints" in US-004-01 means every implemented endpoint, including these four and any later-pathed endpoints. — BR-012 says "every implemented endpoint". — product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-727` — unasked degradable question: ASM-BE-705: Confirm assumption ASM-BE-705: The four operations require an authenticated caller. — ASM-BE-302`: no grant exists in any input. — Q-BE-501

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-728` — unasked degradable question: ASM-BE-302: Confirm assumption ASM-BE-302: no grant exists in any input. | `Q-BE-501` |

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-729` — unasked degradable question: ASM-BE-706: Confirm assumption ASM-BE-706: The counting unit is an operation, a (method, path) pair. Coverage is documented divided by implemented, against the route registry. — BR-012: "entry for every endpoint", checked against the route registry. — GAP-BE-716

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-730` — unasked degradable question: ASM-BE-707: Confirm assumption ASM-BE-707: Each example is validated against its schema in CI. — Otherwise examples go stale (core rule 6 rationale). — product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-731` — unasked degradable question: ASM-BE-708: Confirm assumption ASM-BE-708: The "Given" of US-004-03's third criterion is a misplaced copy of US-004-02's. The intended criterion is that the documented 404 matches actual behaviour. — Its "When" checks the 404 against the spec. — product owner

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-732` — unasked degradable question: ASM-BE-709: Confirm assumption ASM-BE-709: Archived and active notes share one store, so the storage note has no difference to call out. — Archiving is a status flag, not a hard delete [BR-007; backend.domain-model]. — residency-compliance` and operations

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-733` — unasked degradable question: ASM-BE-710: Confirm assumption ASM-BE-710: The served document is built from `contracts/openapi.yaml`. — It is the machine-readable contract named in the output tree. — GAP-BE-715

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-734` — unasked degradable question: ASM-BE-711: Confirm assumption ASM-BE-711: Coverage below 100% fails the CI build. — NFR-008 targets 100% endpoint coverage and BR-012 requires an automated check. — Q-BE-502

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 13 `GAP-` item(s) were raised while writing `modules/scope-guardrail-SPEC.md`: GAP-BE-001, GAP-BE-300, GAP-BE-764, GAP-BE-501, GAP-BE-309, GAP-BE-765, GAP-BE-767, GAP-BE-766, GAP-BE-089, GAP-BE-770, GAP-BE-769, GAP-BE-768, GAP-BE-303

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/scope-guardrail-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-guardrail-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 2 `CON-` item(s) were raised while writing `modules/scope-guardrail-SPEC.md`: CON-BE-007, CON-BE-003

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/scope-guardrail-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-guardrail-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 7 `ASM-` item(s) were raised while writing `modules/scope-guardrail-SPEC.md`: ASM-BE-300, ASM-BE-753, ASM-BE-755, ASM-BE-754, ASM-BE-360, ASM-BE-302, ASM-BE-301

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/scope-guardrail-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-guardrail-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-771` — unasked degradable question: ASM-BE-753: Confirm assumption ASM-BE-753: An attachment path returns status 404 with the standard error envelope, code `OUT_OF_SCOPE` and a scope-exclusion message. — US-005-02 fixes the status at 404. US-005-04 asks for a message "rather than a bare 404". A 404 carrying a body satisfies both. BR-016's wording on "404 ambiguity" is not fully met, so `CON-BE-007` stays open. — A decision on `CON-BE-007

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-772` — unasked degradable question: ASM-BE-754: Confirm assumption ASM-BE-754: The multipart upload refusal uses code `OUT_OF_SCOPE`, not the proposed `UNSUPPORTED_MEDIA_TYPE`. — OUT_OF_SCOPE` is the only code cited in an input for this case. [US-005-04] `UNSUPPORTED_MEDIA_TYPE` is a proposed name (`ASM-BE-301`). — GAP-BE-769` closing artefact

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-773` — unasked degradable question: ASM-BE-755: Confirm assumption ASM-BE-755: The Scope section is part of the published API documentation produced by `api-docs`. — US-005-01 says "published API documentation" and "docs build pipeline". The module map gives the Scope section to this module and the OpenAPI document to `api-docs`. — GAP-BE-766` closing artefact

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open
<!-- /SECTION: backend.gaps -->
