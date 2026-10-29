---
id: agent-developer/web_app/web-app/GAPS.md
type: gaps
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.8.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/GAPS.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:web-app/GAPS.md
---

<!-- SECTION: web-app.gaps | status: VERIFIED -->
## Gaps

### `GAP-index` — 13 `GAP-` item(s) were raised while writing `WEB-APP-SPEC.md`: GAP-WEB-003, GAP-WEB-011, GAP-WEB-001, GAP-WEB-002, GAP-WEB-004, GAP-WEB-005, GAP-WEB-006, GAP-WEB-007, GAP-WEB-008, GAP-WEB-009, GAP-WEB-010, GAP-WEB-012, GAP-WEB-013

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** WEB-APP-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `WEB-APP-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 3 `CON-` item(s) were raised while writing `WEB-APP-SPEC.md`: CON-WEB-001, CON-WEB-003, CON-WEB-002

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** WEB-APP-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `WEB-APP-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 16 `ASM-` item(s) were raised while writing `WEB-APP-SPEC.md`: ASM-WEB-005, ASM-WEB-015, ASM-WEB-016, ASM-WEB-003, ASM-WEB-004, ASM-WEB-010, ASM-WEB-009, ASM-WEB-001, ASM-WEB-002, ASM-WEB-006, ASM-WEB-007, ASM-WEB-008, ASM-WEB-011, ASM-WEB-012, ASM-WEB-013, ASM-WEB-014

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** WEB-APP-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `WEB-APP-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-WEB-014` — unasked degradable question: ASM-WEB-013: Confirm assumption ASM-WEB-013: Network failure, 5xx and 503 show a retryable error state and keep the user's inputs. US-006-01 requires an error state only for list errors. — An error-handling decision.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-015` — unasked degradable question: ASM-WEB-014: Confirm assumption ASM-WEB-014: Rows change on the success response. There are no optimistic updates. — An interaction decision.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-016` — unasked degradable question: ASM-WEB-015: Confirm assumption ASM-WEB-015: Input/Text` is used for the email and code fields, and for editor fields. `Button/Primary` is used for submit and resend. The screens' component lists in the inventory omit some of these. — Design frames.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-017` — unasked degradable question: ASM-WEB-016: Confirm assumption ASM-WEB-016: CodeEntryScreen` has a resend control, required by US-006-05 ("an option to request a new code"). — A design frame.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-018` — unasked blocking question: Q-WEB-001: After sign-in, return the user to the originally requested route? — No; land on `/notes` (`ASM-WEB-011`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-019` — unasked blocking question: Q-WEB-002: Should `InfrastructureProvisioningScreen`, `ResidencyReportScreen` and `ScopeDocumentationScreen` be routed in the web app? — Not routed.

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-020` — unasked blocking question: Q-WEB-003: Should `CodeEntryScreen` let the user go back and correct the email? — No control; the user restarts at `/login`.

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-021` — unasked blocking question: Q-WEB-004: Does lockout also disable the resend control? — No; US-006-05 disables code submission only, and `API-AUTH-015365` stays callable while locked.

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-022` — 30 user stor(ies) belong to no web-app module: US-001-01, US-001-02, US-001-03, US-001-04, US-001-05, US-001-06, US-002-01, US-002-02

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** user_stories, WEB-APP-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** a module in the project spec's module map that owns them, or a decision that this surface does not serve them
- **Status.** open

### `GAP-index` — 7 `GAP-` item(s) were raised while writing `modules/login-SPEC.md`: GAP-WEB-001, GAP-WEB-004, GAP-WEB-005, GAP-WEB-024, GAP-WEB-008, GAP-WEB-023, GAP-WEB-006

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/login-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/login-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 2 `CON-` item(s) were raised while writing `modules/login-SPEC.md`: CON-WEB-001, CON-WEB-002

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/login-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/login-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 18 `ASM-` item(s) were raised while writing `modules/login-SPEC.md`: ASM-WEB-001, ASM-WEB-016, ASM-WEB-006, ASM-WEB-005, ASM-WEB-019, ASM-WEB-018, ASM-WEB-026, ASM-WEB-007, ASM-WEB-020, ASM-WEB-011, ASM-WEB-022, ASM-WEB-017, ASM-WEB-021, ASM-WEB-008, ASM-WEB-025, ASM-WEB-023, ASM-WEB-024, ASM-WEB-015

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/login-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/login-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-WEB-025` — unasked blocking question: Q-WEB-005: Should `CodeEntryScreen` show the address the code was sent to, masked? —  — No.

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-026` — unasked blocking question: Q-WEB-006: Should browser autofill of one-time codes be enabled on the code field? —  — Yes (`ASM-WEB-025`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-027` — unasked degradable question: ASM-WEB-017: Confirm assumption ASM-WEB-017: Lockout (423) and resend-limit (429 on `/login/code`) disablement last while `CodeEntryScreen` stays mounted. It is not stored. A fresh visit recomputes it from a new response. — Backend remaining-lock-time and re-enable rules (`GAP-WEB-004`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-028` — unasked degradable question: ASM-WEB-018: Confirm assumption ASM-WEB-018: On `/login`, a 429 keeps the submit control enabled, because the server decides when the window ends. — A retry-after or re-enable rule.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-029` — unasked degradable question: ASM-WEB-019: Confirm assumption ASM-WEB-019: The email is trimmed and otherwise sent as typed. The field starts empty on each visit to `/login`. Format and case handling are the server's. — Backend decision on case handling (`ASM-BE-001`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-030` — unasked degradable question: ASM-WEB-020: Confirm assumption ASM-WEB-020: Whitespace in the code entry is removed before the 6-digit check. A non-conforming entry shows a field-level format message and makes no call. — A backend ruling on malformed codes.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-031` — unasked degradable question: ASM-WEB-021: Confirm assumption ASM-WEB-021: After a successful resend: code field and error cleared, focus on the field, plain-text "new code sent" notice. — Design or product.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-032` — unasked degradable question: ASM-WEB-022: Confirm assumption ASM-WEB-022: After 401, 400, 409 or 410 the code field is cleared and focused. — Design or product.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-033` — unasked degradable question: ASM-WEB-023: Confirm assumption ASM-WEB-023: Messages are plain text in a message region, because no message component exists. Field-level for 400 and format failures, form-level for the rest. — Design frames with a message component.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-034` — unasked degradable question: ASM-WEB-024: Confirm assumption ASM-WEB-024: Token bindings are proposals from token names. Values are unseen. — Figma verification.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-035` — unasked degradable question: ASM-WEB-025: Confirm assumption ASM-WEB-025: The accessibility behaviours and input hints are proposals, because no standard is stated. — A stated standard and level (`GAP-WEB-008`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-036` — unasked degradable question: ASM-WEB-026: Confirm assumption ASM-WEB-026: On 500 or a network failure, inputs are kept and the user retries by resubmitting. There is no automatic retry. — An error-handling decision.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 11 `GAP-` item(s) were raised while writing `modules/notes-SPEC.md`: GAP-WEB-001, GAP-WEB-003, GAP-WEB-005, GAP-WEB-004, GAP-WEB-010, GAP-WEB-073, GAP-WEB-013, GAP-WEB-075, GAP-WEB-074, GAP-WEB-008, GAP-WEB-076

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 2 `CON-` item(s) were raised while writing `modules/notes-SPEC.md`: CON-WEB-054, CON-WEB-003

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 18 `ASM-` item(s) were raised while writing `modules/notes-SPEC.md`: ASM-WEB-001, ASM-WEB-068, ASM-WEB-073, ASM-WEB-071, ASM-WEB-069, ASM-WEB-070, ASM-WEB-009, ASM-WEB-079, ASM-WEB-072, ASM-WEB-015, ASM-WEB-014, ASM-WEB-067, ASM-WEB-075, ASM-WEB-074, ASM-WEB-013, ASM-WEB-076, ASM-WEB-077, ASM-WEB-078

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/notes-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/notes-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-WEB-077` — unasked degradable question: ASM-WEB-067: Confirm assumption ASM-WEB-067: NoteEditorScreen` at `/notes/:id` is one editable screen with save, archive and restore controls. Empty is the create form and Filled is the loaded note. Variant meanings are unverified. — A design frame or mapping.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-078` — unasked degradable question: ASM-WEB-068: Confirm assumption ASM-WEB-068: The client does not pre-validate emptiness (US-006-02 wording). A 400 with no field identified is attributed to the empty field among title and body, else shown form-level. — GAP-WEB-004` closed.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-079` — unasked degradable question: ASM-WEB-069: Confirm assumption ASM-WEB-069: Save sends only changed title or body. The save control is disabled while nothing has changed. — An interaction decision.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-080` — unasked degradable question: ASM-WEB-070: Confirm assumption ASM-WEB-070: After save, archive or restore the user stays on `/notes/:id` and sees the new state from the response (`Q-WEB-056` default). — Q-WEB-056` answered.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-081` — unasked degradable question: ASM-WEB-071: Confirm assumption ASM-WEB-071: A 404 on load, save, archive or restore shows the same not-found state. On a save 404 the typed values stay in the form, and the cached note is unchanged. — Design frames.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-082` — unasked degradable question: ASM-WEB-072: Confirm assumption ASM-WEB-072: While a request is pending, the control shows Loading and the title and body are not editable. — Design annotation.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-083` — unasked degradable question: ASM-WEB-073: Confirm assumption ASM-WEB-073: Only `created_at` is displayed, formatted in the browser locale. — GAP-WEB-076` closed.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-084` — unasked degradable question: ASM-WEB-074: Confirm assumption ASM-WEB-074: Button/Primary` is reused for create, save, archive and restore. `Input/Text` is used for both title and body. — GAP-WEB-073` closed.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-085` — unasked degradable question: ASM-WEB-075: Confirm assumption ASM-WEB-075: Token bindings are proposals from token names, with values unseen. — Figma verification.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-086` — unasked degradable question: ASM-WEB-076: Confirm assumption ASM-WEB-076: The accessibility behaviours are proposals, because no standard is stated. — GAP-WEB-008` closed.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-087` — unasked degradable question: ASM-WEB-077: Confirm assumption ASM-WEB-077: Edits are last-write-wins with no conflict UI (`stories.md` US-001-02). The contract lists no 409 for edit. — A conflict-handling decision.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-088` — unasked degradable question: ASM-WEB-078: Confirm assumption ASM-WEB-078: Leaving `/notes/:id` with unsaved edits shows no prompt (`Q-WEB-055` default). — Q-WEB-055` answered.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-089` — unasked degradable question: ASM-WEB-079: Confirm assumption ASM-WEB-079: Archive or restore acts on the saved note only. Unsaved typed edits stay in the form, unsaved. — An interaction decision.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-090` — unasked blocking question: Q-WEB-055: Should leaving the editor with unsaved changes prompt the user? — No prompt (`ASM-WEB-078`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-091` — unasked blocking question: Q-WEB-056: After archive or restore, does the user stay on the note or return to a list? — Stay on `/notes/:id` (`ASM-WEB-070`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 14 `GAP-` item(s) were raised while writing `modules/search-SPEC.md`: GAP-WEB-001, GAP-WEB-002, GAP-WEB-003, GAP-WEB-004, GAP-WEB-012, GAP-WEB-123, GAP-WEB-124, GAP-WEB-015, GAP-WEB-013, GAP-WEB-125, GAP-WEB-008, GAP-WEB-010, GAP-WEB-005, GAP-WEB-126

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 1 `CON-` item(s) were raised while writing `modules/search-SPEC.md`: CON-WEB-001

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 13 `ASM-` item(s) were raised while writing `modules/search-SPEC.md`: ASM-WEB-119, ASM-WEB-125, ASM-WEB-013, ASM-WEB-117, ASM-WEB-014, ASM-WEB-124, ASM-WEB-121, ASM-WEB-120, ASM-WEB-127, ASM-WEB-118, ASM-WEB-126, ASM-WEB-123, ASM-WEB-122

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/search-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/search-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-WEB-127` — unasked degradable question: ASM-WEB-117: Confirm assumption ASM-WEB-117: Tags are chosen by typing free text in `Input/Text`, trimmed, lowercased (`BR-005`) and de-duplicated. Selected tags show as removable chips. — Q-WEB-105` answered; tag rules.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-128` — unasked degradable question: ASM-WEB-118: Confirm assumption ASM-WEB-118: The module never sends `include_archived`, so archived notes never appear here. The archived view belongs to `archive`. — A decision on archived notes in search.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-129` — unasked degradable question: ASM-WEB-119: Confirm assumption ASM-WEB-119: Until `GAP-WEB-002` closes, `/notes` opens in an idle state and builds no plain-list load. Submitting with no keyword and no tag stays idle with no request. — The list operation; `Q-WEB-107`.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-130` — unasked degradable question: ASM-WEB-120: Confirm assumption ASM-WEB-120: The module requests `page` 1 with `page_size` 100 (the maximum, `NFR-003`) and renders that page. If matches exceed it, a notice says not all are shown. — GAP-WEB-123` closed.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-131` — unasked degradable question: ASM-WEB-121: Confirm assumption ASM-WEB-121: A search runs only on submit, as the stories say. Editing fields does not fetch. The latest submission wins and responses from superseded requests are discarded. — A trigger decision.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-132` — unasked degradable question: ASM-WEB-122: Confirm assumption ASM-WEB-122: Sorting applies within the pinned group and within the unpinned group. Newest and Oldest first sort on `created_at`. Title A-Z compares titles case-insensitively. No input names the key. — A ruling on the sort key.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-133` — unasked degradable question: ASM-WEB-123: Confirm assumption ASM-WEB-123: The note row, sort control, pin icon, pin toggle, count, tag chips and message region have no design component and use plain elements. All token bindings are proposals from token names, with values unseen. `Button/Primary` serves as the submit control. — Design frames or Figma verification.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-134` — unasked degradable question: ASM-WEB-124: Confirm assumption ASM-WEB-124: A failed pin leaves the row unchanged and shows a message. The edit operation's 404 reads as "note not found". Other failures read as retryable. The toggle is disabled while the request is pending. — GAP-WEB-124` closed.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-135` — unasked degradable question: ASM-WEB-125: Confirm assumption ASM-WEB-125: Variant mapping: Default is the initial and populated page, Empty is zero results, Error is a failure, and Loading is shown through `Button/Primary` Loading. The count is shown only when populated or empty, so it never shows a false 0. — Design frames (`GAP-WEB-126`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-136` — unasked degradable question: ASM-WEB-126: Confirm assumption ASM-WEB-126: The accessibility behaviours in this module are proposals, because no standard is stated. — A stated standard and level (`GAP-WEB-008`).

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-137` — unasked degradable question: ASM-WEB-127: Confirm assumption ASM-WEB-127: Search text, tags and sort are client state and are not persisted in the URL or across reloads. — Q-WEB-106` answered.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-138` — unasked blocking question: Q-WEB-105: How does the user choose tags to filter by? — Typed free text with removable chips (`ASM-WEB-117`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-139` — unasked blocking question: Q-WEB-106: Should the search text, tags and sort persist in the URL or across reloads? — No (`ASM-WEB-127`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-140` — unasked blocking question: Q-WEB-107: When keyword and tags are cleared and the user submits, should the plain list show? — Idle state until a list operation exists (`ASM-WEB-119`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 9 `GAP-` item(s) were raised while writing `modules/archive-SPEC.md`: GAP-WEB-001, GAP-WEB-002, GAP-WEB-004, GAP-WEB-174, GAP-WEB-005, GAP-WEB-175, GAP-WEB-013, GAP-WEB-008, GAP-WEB-173

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/archive-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/archive-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 1 `CON-` item(s) were raised while writing `modules/archive-SPEC.md`: CON-WEB-003

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/archive-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/archive-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 12 `ASM-` item(s) were raised while writing `modules/archive-SPEC.md`: ASM-WEB-014, ASM-WEB-015, ASM-WEB-172, ASM-WEB-013, ASM-WEB-001, ASM-WEB-168, ASM-WEB-169, ASM-WEB-009, ASM-WEB-171, ASM-WEB-167, ASM-WEB-170, ASM-WEB-173

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/archive-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/archive-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-WEB-176` — unasked blocking question: Q-WEB-155: Should restore ask for confirmation? — No. Restore is non-destructive and reversible (`requirements.md` BR-009).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-177` — unasked blocking question: Q-WEB-156: Can a note be opened from the archived view? — Yes, at `/notes/:id` (`WEB-ARCHIVE-FR-005`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-178` — unasked degradable question: ASM-WEB-167: Confirm assumption ASM-WEB-167: The restore control is a `Button/Primary` on each row of the archived view. `stories.md` does not say where it sits. — A design frame.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-179` — unasked degradable question: ASM-WEB-168: Confirm assumption ASM-WEB-168: Variant meanings: Default is the initial state, Empty is no archived notes, Filled is a populated list, Error is a load failure. — Figma verification.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-180` — unasked degradable question: ASM-WEB-169: Confirm assumption ASM-WEB-169: After a restore the user stays on `/archive`, and the Empty state shows when the last note is restored. — A navigation decision.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-181` — unasked degradable question: ASM-WEB-170: Confirm assumption ASM-WEB-170: Token bindings are proposals from token names. Values are unseen. — Figma verification.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-182` — unasked degradable question: ASM-WEB-171: Confirm assumption ASM-WEB-171: The accessibility behaviours in this module are proposals. — An accessibility standard and level.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-183` — unasked degradable question: ASM-WEB-172: Confirm assumption ASM-WEB-172: On a 404 the row and cache are left unchanged, mirroring US-006-03. — A ruling.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-184` — unasked degradable question: ASM-WEB-173: Confirm assumption ASM-WEB-173: The archived view is reached through navigation that the design does not frame. Only the route is specified. — Design frames for navigation.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 9 `GAP-` item(s) were raised while writing `modules/tags-SPEC.md`: GAP-WEB-003, GAP-WEB-011, GAP-WEB-223, GAP-WEB-001, GAP-WEB-005, GAP-WEB-004, GAP-WEB-008, GAP-WEB-225, GAP-WEB-224

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/tags-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/tags-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 1 `CON-` item(s) were raised while writing `modules/tags-SPEC.md`: CON-WEB-204

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/tags-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/tags-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 2 `ASM-` item(s) were raised while writing `modules/tags-SPEC.md`: ASM-WEB-218, ASM-WEB-217

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/tags-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/tags-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-WEB-226` — unasked degradable question: ASM-WEB-217: Confirm assumption ASM-WEB-217: Token bindings for `Input/Text` would come from the border, surface and text tokens named in `tokens-index.md`. No binding is asserted here. — Figma verification.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-227` — unasked degradable question: ASM-WEB-218: Confirm assumption ASM-WEB-218: The module stays unrouted and unimplemented until `Q-WEB-205` is answered. — A routing decision.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-228` — unasked blocking question: Q-WEB-205: Should the web app offer tag add, remove and list for a note, and on which route? — No; the module is not routed (`ASM-WEB-218`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 7 `GAP-` item(s) were raised while writing `modules/residency-SPEC.md`: GAP-WEB-011, GAP-WEB-273, GAP-WEB-274, GAP-WEB-275, GAP-WEB-005, GAP-WEB-276, GAP-WEB-008

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/residency-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 2 `ASM-` item(s) were raised while writing `modules/residency-SPEC.md`: ASM-WEB-267, ASM-WEB-268

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/residency-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/residency-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-WEB-277` — unasked blocking question: Q-WEB-002: whether `InfrastructureProvisioningScreen`, `ResidencyReportScreen` and `ScopeDocumentationScreen` are routed. Default adopted: not routed.

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-278` — unasked blocking question: Q-WEB-255: Should any signed-in role, other than the Team member, see these screens? — None; module stays unrouted

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-279` — unasked degradable question: ASM-WEB-267: Confirm assumption ASM-WEB-267: The module is not routed, per the default for `Q-WEB-002`; it has no navigation, requirement or behaviour. — Q-WEB-002` answered

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-280` — unasked degradable question: ASM-WEB-268: Confirm assumption ASM-WEB-268: The candidate operation pairing in the API integration section follows the story mapping and the contract summaries. It is not a binding. — A design flow plus the routing decision

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-index` — 8 `GAP-` item(s) were raised while writing `modules/scope-docs-SPEC.md`: GAP-WEB-011, GAP-WEB-323, GAP-WEB-324, GAP-WEB-001, GAP-WEB-008, GAP-WEB-325, GAP-WEB-326, GAP-WEB-005

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/scope-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `CON-index` — 1 `CON-` item(s) were raised while writing `modules/scope-docs-SPEC.md`: CON-WEB-304

- **Type.** CONTRADICTION
- **Severity.** non-critical
- **Sources.** modules/scope-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `ASM-index` — 3 `ASM-` item(s) were raised while writing `modules/scope-docs-SPEC.md`: ASM-WEB-317, ASM-WEB-318, ASM-WEB-319

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** modules/scope-docs-SPEC.md
- **Degrades.** nothing yet — intake only
- **Closing artefact.** each item is defined in `modules/scope-docs-SPEC.md`, with its evidence and what would close it. They are referenced here, not copied — a second copy goes stale the first time the document is re-run.
- **Status.** open

### `GAP-WEB-327` — unasked degradable question: ASM-WEB-317: Confirm assumption ASM-WEB-317: If built, the screen would display the API's Scope section (what is covered, what is excluded) from `API-CONTRACTDOCS-206969`, read-only. — A screen description or story.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-328` — unasked degradable question: ASM-WEB-318: Confirm assumption ASM-WEB-318: The two design transitions to `SearchScreen` are triggered by `Button/Primary` instances. — Design frames.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-329` — unasked degradable question: ASM-WEB-319: Confirm assumption ASM-WEB-319: The module is not routed, matching the project-spec default for `Q-WEB-002`. — Q-WEB-002` answered.

- **Type.** MISSING-STORY
- **Severity.** non-critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open

### `GAP-WEB-330` — unasked blocking question: Q-WEB-305: Should the web app display the API's out-of-scope list at all, given no web story asks for it? — No; the module stays unbuilt (`ASM-WEB-319`).

- **Type.** MISSING-STORY
- **Severity.** critical
- **Sources.** question budget exhausted
- **Degrades.** nothing yet — intake only
- **Closing artefact.** an answer from the human, or a larger DEVAGENT_QUESTION_BUDGET
- **Status.** open
<!-- /SECTION: web-app.gaps -->
