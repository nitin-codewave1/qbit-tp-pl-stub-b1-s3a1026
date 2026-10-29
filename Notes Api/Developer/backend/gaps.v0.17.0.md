---
id: agent-developer/notes_api/backend/GAPS.md
type: gaps
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.17.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/GAPS.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: e1e628bb-b234-5a13-b948-b4689dcc8eab
okf_id: notes_api:backend/GAPS.md
---

<!-- SECTION: backend.gaps | status: VERIFIED -->
## Gaps

### `GAP-index` — 12 `GAP-` item(s) were raised while writing `BACKEND-SPEC.md`: GAP-BE-003, GAP-BE-009, GAP-BE-007, GAP-BE-001, GAP-BE-010, GAP-BE-006, GAP-BE-004, GAP-BE-008, GAP-BE-011, GAP-BE-005, GAP-BE-002, GAP-BE-012

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 3 `CON-` item(s) were raised while writing `BACKEND-SPEC.md`: CON-BE-003, CON-BE-002, CON-BE-001

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 16 `ASM-` item(s) were raised while writing `BACKEND-SPEC.md`: ASM-BE-002, ASM-BE-013, ASM-BE-011, ASM-BE-001, ASM-BE-014, ASM-BE-004, ASM-BE-016, ASM-BE-007, ASM-BE-005, ASM-BE-010, ASM-BE-009, ASM-BE-003, ASM-BE-015, ASM-BE-008, ASM-BE-012, ASM-BE-006

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `BACKEND-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-013` — unasked degradable question: ASM-BE-011: Confirm assumption ASM-BE-011: assumption — Retention is 1 day for challenges, 1 day for send attempts and 5 days for locks (`GAP-BE-004`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-014` — unasked degradable question: ASM-BE-012: Confirm assumption ASM-BE-012: assumption — The method and paths for request-code (POST `/auth/request-code`) and verify-code (POST `/auth/verify-code`) are taken from the approved prior BACKEND-SPEC. No supplied input gives them.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-015` — unasked degradable question: ASM-BE-013: Confirm assumption ASM-BE-013: assumption — residency` is operational tooling and infrastructure-as-code, not request-path code.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-016` — unasked degradable question: ASM-BE-014: Confirm assumption ASM-BE-014: assumption — Codes and unmasked emails are never logged. Per-outcome login metrics are proposed.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-017` — unasked degradable question: ASM-BE-015: Confirm assumption ASM-BE-015: assumption — A search with only `tags` is allowed (`CON-BE-003`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-018` — unasked degradable question: ASM-BE-016: Confirm assumption ASM-BE-016: assumption — Out-of-scope calls return 404 with `OUT_OF_SCOPE` (`CON-BE-002`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-019` — 2 user stor(ies) belong to no backend module: US-005-06, US-006-01

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** user_stories, BACKEND-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** a module in the project spec's module map that owns them, or a decision that this surface does not serve them
- **Status.** open

### `GAP-index` — 9 `GAP-` item(s) were raised while writing `modules/notes-SPEC.md`: GAP-BE-005, GAP-BE-023, GAP-BE-020, GAP-BE-010, GAP-BE-006, GAP-BE-024, GAP-BE-021, GAP-BE-022, GAP-BE-025

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 2 `CON-` item(s) were raised while writing `modules/notes-SPEC.md`: CON-BE-005, CON-BE-004

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 15 `ASM-` item(s) were raised while writing `modules/notes-SPEC.md`: ASM-BE-017, ASM-BE-024, ASM-BE-018, ASM-BE-019, ASM-BE-020, ASM-BE-021, ASM-BE-030, ASM-BE-022, ASM-BE-025, ASM-BE-023, ASM-BE-027, ASM-BE-028, ASM-BE-029, ASM-BE-071, ASM-BE-026

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-026` — unasked blocking question: Q-BE-004: May archived notes be edited or have their tags changed? — Yes, allowed (`ASM-BE-023`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-027` — unasked blocking question: Q-BE-005: What does unarchiving an already-active note return? — 200 with the note unchanged (`ASM-BE-022`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-028` — unasked blocking question: Q-BE-006: Is there a maximum number of tags per note? — No maximum (`ASM-BE-030`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-029` — unasked degradable question: ASM-BE-017: Confirm assumption ASM-BE-017: The note id is a uuid. The stories say only "unique ID" (US-002-01).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-030` — unasked degradable question: ASM-BE-018: Confirm assumption ASM-BE-018: status` is a text column constrained to `active` or `archived`, defaulting to `active`. The inputs say only "archive flag" (BR-007) and "status changes to active" (US-002-05).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-031` — unasked degradable question: ASM-BE-019: Confirm assumption ASM-BE-019: Timestamps are `timestamptz` in UTC. `updated_at` is refreshed on any change to title, body, `pinned`, the tag set or `status` (`GAP-BE-021`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-032` — unasked degradable question: ASM-BE-020: Confirm assumption ASM-BE-020: note_tags.note_id` references `notes.id` with `ON DELETE RESTRICT`, because no input defines hard delete (`GAP-BE-023`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-033` — unasked degradable question: ASM-BE-021: Confirm assumption ASM-BE-021: Tag names are non-empty and compared exactly as supplied: case-sensitive, no trimming or normalisation. A tag in a DELETE path is URL-decoded before comparison.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-034` — unasked degradable question: ASM-BE-022: Confirm assumption ASM-BE-022: Unarchiving an already-active note returns 200 with the note unchanged. The operation record lists no 409 for it (`Q-BE-005`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-035` — unasked degradable question: ASM-BE-023: Confirm assumption ASM-BE-023: Edits and tag changes are allowed on archived notes. No input forbids them (`Q-BE-004`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-036` — unasked degradable question: ASM-BE-024: Confirm assumption ASM-BE-024: Create and edit validation treats an empty or whitespace-only title or body as invalid. An unknown field, a non-boolean `pinned`, or an empty PATCH body returns 400 `VALIDATION_ERROR`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-037` — unasked degradable question: ASM-BE-025: Confirm assumption ASM-BE-025: Tag add returns 200 whether the tag is new or repeated. Tag removal returns 200 for a present or absent tag. Both return 404 for an unknown note, and add returns 400 for an empty tag name. Archive of an unknown note returns 404 by analogy with US-002-05.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-038` — unasked degradable question: ASM-BE-026: Confirm assumption ASM-BE-026: A note's tags are returned in ascending order of name, so responses are deterministic. No input states an order.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-039` — unasked degradable question: ASM-BE-027: Confirm assumption ASM-BE-027: Create with tags is atomic, and duplicate tag names within one request are stored once.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-040` — unasked degradable question: ASM-BE-028: Confirm assumption ASM-BE-028: A path id that is not a valid uuid returns 400 `VALIDATION_ERROR` (depends on `ASM-BE-017`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-041` — unasked degradable question: ASM-BE-029: Confirm assumption ASM-BE-029: Concurrent edits are last-write-wins, with no version column (US-005-03 AC2; `GAP-BE-022`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-042` — unasked degradable question: ASM-BE-030: Confirm assumption ASM-BE-030: There is no maximum number of tags per note (`Q-BE-006`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 9 `GAP-` item(s) were raised while writing `modules/search-SPEC.md`: GAP-BE-005, GAP-BE-006, GAP-BE-070, GAP-BE-072, GAP-BE-010, GAP-BE-073, GAP-BE-011, GAP-BE-071, GAP-BE-001

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 2 `CON-` item(s) were raised while writing `modules/search-SPEC.md`: CON-BE-003, CON-BE-054

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 17 `ASM-` item(s) were raised while writing `modules/search-SPEC.md`: ASM-BE-010, ASM-BE-067, ASM-BE-072, ASM-BE-078, ASM-BE-074, ASM-BE-073, ASM-BE-001, ASM-BE-015, ASM-BE-076, ASM-BE-070, ASM-BE-071, ASM-BE-077, ASM-BE-079, ASM-BE-068, ASM-BE-009, ASM-BE-069, ASM-BE-075

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-074` — unasked blocking question: Q-BE-054: Is a `limit` above the maximum rejected with 400 or clamped to the maximum? —  — rejected with 400 (`ASM-BE-068`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-075` — unasked blocking question: Q-BE-055: With several tags, must a note carry all of them or any of them? —  — all of them (`ASM-BE-071`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-076` — unasked blocking question: Q-BE-056: Is a whitespace-only `q` treated as empty? —  — treated as empty, so 400 when no tags (`ASM-BE-076`)

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 12 `GAP-` item(s) were raised while writing `modules/auth-SPEC.md`: GAP-BE-007, GAP-BE-001, GAP-BE-006, GAP-BE-123, GAP-BE-124, GAP-BE-004, GAP-BE-125, GAP-BE-127, GAP-BE-121, GAP-BE-120, GAP-BE-122, GAP-BE-126

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/auth-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/auth-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 2 `CON-` item(s) were raised while writing `modules/auth-SPEC.md`: CON-BE-001, CON-BE-104

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/auth-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/auth-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 16 `ASM-` item(s) were raised while writing `modules/auth-SPEC.md`: ASM-BE-117, ASM-BE-002, ASM-BE-004, ASM-BE-120, ASM-BE-005, ASM-BE-119, ASM-BE-118, ASM-BE-007, ASM-BE-122, ASM-BE-121, ASM-BE-011, ASM-BE-008, ASM-BE-003, ASM-BE-125, ASM-BE-123, ASM-BE-124

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/auth-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/auth-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-128` — unasked degradable question: ASM-BE-122: Confirm assumption ASM-BE-122: 500 and nothing persisted. | An email-delivery requirement. |

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-129` — unasked degradable question: ASM-BE-117: Confirm assumption ASM-BE-117: Storage conventions: `email` is trimmed and lower-cased before storage and comparison; surrogate keys are `bigint` identity; timestamps are `timestamptz` in UTC. No input states them. — The platform owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-130` — unasked degradable question: ASM-BE-118: Confirm assumption ASM-BE-118: All time comparisons use the database clock. A send attempt counts while `requested_at > now - 10 minutes`. A lock is active while `now < locked_until`. — The requirements owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-131` — unasked degradable question: ASM-BE-119: Confirm assumption ASM-BE-119: account_locks` holds the running `failed_count`, `locked_until` and `updated_at`, one row per email. NFR-019 names locks but no counter store. — The requirements owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-132` — unasked degradable question: ASM-BE-120: Confirm assumption ASM-BE-120: Verify matches the most recently issued challenge whose code equals the submitted code, then applies used, expired, success. — The requirements owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-133` — unasked degradable question: ASM-BE-121: Confirm assumption ASM-BE-121: The 5th wrong code returns 401 itself and starts the lock; the lock applies from the next request. — BR-022 and BR-033 wording.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-134` — unasked degradable question: ASM-BE-123: Confirm assumption ASM-BE-123: Codes come from a cryptographically secure random source, uniform over 000000 to 999999. BR-029 states only the format. — The security owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-135` — unasked degradable question: ASM-BE-124: Confirm assumption ASM-BE-124: A missing field or a code that is not six digits returns 400 and is not counted toward the lock. — The requirements owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-136` — unasked degradable question: ASM-BE-125: Confirm assumption ASM-BE-125: A successful verify consumes only the matched challenge (`GAP-BE-122`). — A decision on outstanding codes.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 12 `GAP-` item(s) were raised while writing `modules/contract-docs-SPEC.md`: GAP-BE-009, GAP-BE-006, GAP-BE-174, GAP-BE-171, GAP-BE-170, GAP-BE-003, GAP-BE-173, GAP-BE-175, GAP-BE-010, GAP-BE-172, GAP-BE-001, GAP-BE-005

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/contract-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/contract-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 1 `CON-` item(s) were raised while writing `modules/contract-docs-SPEC.md`: CON-BE-154

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/contract-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/contract-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 10 `ASM-` item(s) were raised while writing `modules/contract-docs-SPEC.md`: ASM-BE-167, ASM-BE-170, ASM-BE-171, ASM-BE-174, ASM-BE-169, ASM-BE-173, ASM-BE-172, ASM-BE-168, ASM-BE-001, ASM-BE-175

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/contract-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/contract-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-176` — unasked blocking question: Q-BE-154: ). Should `API-CONTRACTDOCS-827943` be access-restricted, given it exposes the route inventory and any undocumented routes? Default taken, unattended: it follows the same access rule as `API-CONTRACTDOCS-304307` (`ASM-BE-168`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-177` — unasked blocking question: Q-BE-155: ). For an unsupported Accept header, should the response be 406 or a default-format fallback? Default taken, unattended: 406 (`ASM-BE-169`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 7 `GAP-` item(s) were raised while writing `modules/scope-guard-SPEC.md`: GAP-BE-009, GAP-BE-221, GAP-BE-220, GAP-BE-011, GAP-BE-223, GAP-BE-008, GAP-BE-222

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/scope-guard-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-guard-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 1 `CON-` item(s) were raised while writing `modules/scope-guard-SPEC.md`: CON-BE-002

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/scope-guard-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-guard-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 11 `ASM-` item(s) were raised while writing `modules/scope-guard-SPEC.md`: ASM-BE-217, ASM-BE-222, ASM-BE-223, ASM-BE-220, ASM-BE-219, ASM-BE-224, ASM-BE-016, ASM-BE-218, ASM-BE-014, ASM-BE-221, ASM-BE-001

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/scope-guard-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-guard-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-224` — unasked degradable question: ASM-BE-217: Confirm assumption ASM-BE-217: The exclusion list is a versioned configuration artefact in the repository, not a database table. No input names its storage. It is read by both the runtime classifier and the CI test. *Confirm with:* the platform owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-225` — unasked degradable question: ASM-BE-218: Confirm assumption ASM-BE-218: A multipart file upload returns 415 with the registry code `UNSUPPORTED_MEDIA_TYPE`, and the message references the scope exclusion. US-005-04 gives `OUT_OF_SCOPE` only as an example ("e.g."). *Confirm with:* the contract owner. If `OUT_OF_SCOPE` with 415 is wanted, the registry in `backend.error-model` changes.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-226` — unasked degradable question: ASM-BE-219: Confirm assumption ASM-BE-219: The multipart refusal applies to every endpoint that takes a body, not only note create and update. No endpoint accepts files (BR-014), so a broader rule has no conflicting case. *Confirm with:* the contract owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-227` — unasked degradable question: ASM-BE-220: Confirm assumption ASM-BE-220: Real-time attempts are detected by an `Upgrade: websocket` header and an `Accept: text/event-stream` header, on any path. No input says how detection works. *Confirm with:* the platform owner.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-228` — unasked degradable question: ASM-BE-221: Confirm assumption ASM-BE-221: The module runs before authentication and validation, so the verdict is the same for every caller. This keeps the work independent of the BLOCKED auth model. *Confirm with:* the human, together with `Q-BE-003`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-229` — unasked degradable question: ASM-BE-222: Confirm assumption ASM-BE-222: An exclusion entry with no method applies to every HTTP method. US-005-02 shows a POST example, and BR-014 forbids all attachment endpoints.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-230` — unasked degradable question: ASM-BE-223: Confirm assumption ASM-BE-223: A `path_pattern` also matches any deeper path beneath it, and `{…}` matches exactly one segment.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-231` — unasked degradable question: ASM-BE-224: Confirm assumption ASM-BE-224: Evaluation order inside the module is path, then protocol signal, then media type. A multipart request to `/notes/{id}/attachments` therefore gets the 404 refusal.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 10 `GAP-` item(s) were raised while writing `modules/residency-SPEC.md`: GAP-BE-270, GAP-BE-271, GAP-BE-277, GAP-BE-275, GAP-BE-272, GAP-BE-276, GAP-BE-006, GAP-BE-278, GAP-BE-273, GAP-BE-274

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/residency-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 1 `CON-` item(s) were raised while writing `modules/residency-SPEC.md`: CON-BE-254

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/residency-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 13 `ASM-` item(s) were raised while writing `modules/residency-SPEC.md`: ASM-BE-267, ASM-BE-275, ASM-BE-013, ASM-BE-268, ASM-BE-269, ASM-BE-277, ASM-BE-271, ASM-BE-270, ASM-BE-273, ASM-BE-272, ASM-BE-274, ASM-BE-278, ASM-BE-276

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/residency-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-BE-279` — unasked degradable question: Q-BE-254: . What are the approved EU regions?

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-280` — unasked degradable question: Q-BE-255: . What is the drift scan interval?

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-281` — unasked degradable question: Q-BE-256: . How is the DevOps team alerted?

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-282` — unasked degradable question: ASM-BE-267: Confirm assumption ASM-BE-267: The seven operations are those listed in the prior approved BACKEND-SPEC attached to `feedback.md` (CR-131). Their ids are not reused because this run did not mint them.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-283` — unasked degradable question: ASM-BE-268: Confirm assumption ASM-BE-268: Residency tables live in a `residency` schema of the same PostgreSQL 16 database, in an EU region. `platform.md` names PostgreSQL 16 only.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-284` — unasked degradable question: ASM-BE-269: Confirm assumption ASM-BE-269: Ids are UUIDs and timestamps are `timestamptz` in UTC. No input fixes either.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-285` — unasked degradable question: ASM-BE-270: Confirm assumption ASM-BE-270: A region is EU iff it exactly matches an active row of `approved_regions`. An empty list fails closed.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-286` — unasked degradable question: ASM-BE-271: Confirm assumption ASM-BE-271: unverified` resources count in the denominator and not as compliant. `percent_compliant` is `numeric(5,2)`. AC-001-04-02 only requires them to be flagged.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-287` — unasked degradable question: ASM-BE-272: Confirm assumption ASM-BE-272: The launch gate is derived from a report generated at evaluation time, because NFR-004 requires the report to be refreshed at each gate check.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-288` — unasked degradable question: ASM-BE-273: Confirm assumption ASM-BE-273: The 30-second backup deadline runs from receipt of the validation request to the failure result. A timeout is treated as invalid, not as a pass.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-289` — unasked degradable question: ASM-BE-274: Confirm assumption ASM-BE-274: Each completed scan with one or more findings raises exactly one alert, with no deduplication across scans.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-290` — unasked degradable question: ASM-BE-275: Confirm assumption ASM-BE-275: This module records provisioning decisions only. The real "no resource is created" is enforced by infrastructure-as-code region constraints (BR-001).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-291` — unasked degradable question: ASM-BE-276: Confirm assumption ASM-BE-276: "Immediately" (AC-001-06-02) means at scan completion. NFR-003's 15 minutes bounds the time from detection to the alert.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-292` — unasked degradable question: ASM-BE-277: Confirm assumption ASM-BE-277: A repeated CI check on the same deployment identifier creates a new row.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-BE-293` — unasked degradable question: ASM-BE-278: Confirm assumption ASM-BE-278: The proposed default scan interval is 15 minutes. It is unsourced and is the value `Q-BE-255` asks about.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open
<!-- /SECTION: backend.gaps -->
