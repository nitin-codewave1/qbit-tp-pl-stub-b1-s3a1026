---
id: agent-developer/notes_api/backend/modules/scope-guard-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/scope-guard-SPEC.md
depends_on: [notes_api:US-005-02, notes_api:US-005-03, notes_api:US-005-04, notes_api:US-005-05]
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: notes_api:backend/modules/scope-guard-SPEC.md
---

<!-- SECTION: backend.scope-guard.frontmatter | status: VERIFIED -->
## Frontmatter

```yaml
id: scope-guard
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: []
scope: []
```
<!-- /SECTION: backend.scope-guard.frontmatter -->
<!-- SECTION: backend.scope-guard.purpose | status: DERIVED -->
## Purpose

**Responsibility.** `scope-guard` is request middleware over the whole HTTP surface of `notes_api` (project spec `backend.module-map`; Node.js 20 + Express per `platform.md`). It does three things:
1. It refuses calls that fall into an excluded category (attachment, real-time, mobile-specific) with a standardised, documented error (US-005-02, US-005-03, US-005-04; BR-014, BR-015, BR-016; NFR-011).
2. It refuses multipart file payloads on note create and update, and persists no file content (US-005-02, US-005-04).
3. It supplies the exclusion list and the contract test suite that fail a CI build when an excluded endpoint appears (US-005-05; NFR-010; BR-014, BR-015).

**Derivation.** The module map assigns US-005-02 to US-005-05 to this module. It has no dependency on any other module. `contract-docs` reads the route registry but does not call it at runtime.

**This module defines no operations.** DevAgent minted no `API-` ids for it, so `scope` in the frontmatter is empty. The refusals are responses to calls that must not exist, not endpoints.

**Not responsible for:**
- The published Scope section of the API documentation and the build lint that fails when it is missing (US-005-01, US-005-01 criteria 2 and 3). `contract-docs` owns them.
- The OpenAPI document and the coverage check (`contract-docs`).
- Logging and classifying stakeholder requests against the exclusion list (US-005-06, BR-017). BR-017 makes this a product-manager process (`GAP-BE-009` in the project spec).
- The shape of the global error envelope and the error-code registry (`backend.error-model`). This module uses `OUT_OF_SCOPE` and does not redefine the envelope.
- Last-write or conflict handling for concurrent `PATCH /notes/{id}` (`notes`). This module only guarantees that no broadcast or merge logic exists (`GAP-BE-225`).
- Authentication and authorisation. The auth model is `BLOCKED` in the project spec (`Q-BE-001`, `Q-BE-002`, `Q-BE-003`).
- Any CI platform or merge-protection configuration (`GAP-BE-226`).
<!-- /SECTION: backend.scope-guard.purpose -->
<!-- SECTION: backend.scope-guard.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**No tables, columns, constraints or indexes.** No input gives this module persisted data (`ASM-BE-219`). Refusals have no side effects other than logging, and logging is stated in the project spec (`ASM-BE-009`, `GAP-BE-006`).

**Exclusion list.** This is configuration, not stored data (`ASM-BE-216`). Where it is held is unstated. Its logical content:

| Field | Type | Nullable | Content | Source |
|---|---|---|---|---|
| `category` | enum: `attachment`, `real-time`, `mobile` | no | The three excluded categories | BR-014, BR-015, BR-016; US-005-05 |
| `path_patterns` | list of strings | yes for `mobile` until supplied | `attachment`: `/attachments`, `/notes/{id}/attachments`. `real-time`: `/ws`. `mobile`: none supplied. | US-005-02, US-005-05; `GAP-BE-224` |
| `protocol_signals` | list of header rules | no for `real-time` | `real-time`: WebSocket upgrade, SSE connection (`ASM-BE-214`) | US-005-03 |
| `content_type_rule` | string | yes | `attachment`: `multipart/form-data` on note create and update (`ASM-BE-217`) | US-005-02 |

The path examples are reproduced exactly as written in the stories. They are examples ("e.g."), not a complete list (`GAP-BE-224`).
<!-- /SECTION: backend.scope-guard.data-model -->
<!-- SECTION: backend.scope-guard.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

No entity of this module has a status column, so no persisted state machine exists. The only states are transient, per request, and are not stored.

| State | Meaning | Terminal |
|---|---|---|
| `RECEIVED` | The request has arrived and nothing has been read from its body | no |
| `CLASSIFIED_EXCLUDED` | The request matches an exclusion-list entry | no |
| `CLASSIFIED_IN_SCOPE` | The request matches no entry | no |
| `REFUSED` | An `OUT_OF_SCOPE` response has been sent (FR-001 to FR-005) | **yes** |
| `PASSED` | The request has been handed to the routed module | **yes** |

| Transition | Trigger | Guard |
|---|---|---|
| `RECEIVED → CLASSIFIED_EXCLUDED` | Path, protocol signal or content type matches an entry | Evaluated from the request line and headers only, before any body is parsed (`ASM-BE-217`) |
| `RECEIVED → CLASSIFIED_IN_SCOPE` | No entry matches | none |
| `CLASSIFIED_EXCLUDED → REFUSED` | Always | Status per FR-001 to FR-004 |
| `CLASSIFIED_IN_SCOPE → PASSED` | Always | none |

An illegal transition cannot occur, because every classified request has exactly one outcome. A `REFUSED` request never becomes `PASSED`.
<!-- /SECTION: backend.scope-guard.state-machine -->
<!-- SECTION: backend.scope-guard.requirements | status: BLOCKED -->
## Requirements

**BLOCKED.** the run did not produce this section for module `scope-guard`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: backend.scope-guard.requirements -->
<!-- SECTION: backend.scope-guard.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

No HTTP operation is defined.
<!-- /SECTION: backend.scope-guard.api-contract -->
<!-- SECTION: backend.scope-guard.invariants | status: DRAFT-UNVERIFIED -->
## Invariants

| ID | Invariant | Derived from |
|---|---|---|
| `INV-BE-201` | A refused request never reaches a handler of another module, never causes a protocol upgrade, and has no side effect other than logging. | FR-001 to FR-004; US-005-03 |
| `INV-BE-202` | No file content received in any request is persisted by `notes_api`. | FR-002; US-005-02 |
| `INV-BE-203` | Every response produced by this module carries the code `OUT_OF_SCOPE` and a message that references the scope exclusion. | FR-005; US-005-04; NFR-011 |
| `INV-BE-204` | On any build that passes CI, zero routes in the published OpenAPI document and the route registry match an excluded category. | FR-007, FR-008; NFR-010 |
| `INV-BE-205` | The middleware and the contract suite read the same exclusion list, so they cannot disagree about what is excluded. | `ASM-BE-216` |

`INV-BE-205` rests on an assumption. If the list is held in two places, it fails.
<!-- /SECTION: backend.scope-guard.invariants -->
<!-- SECTION: backend.scope-guard.authorization | status: DRAFT-UNVERIFIED -->
## Authorization

| Operation | Roles permitted | Ownership predicate |
|---|---|---|
| none owned | no role distinction | none; nothing this module guards is user-scoped |

- The refusals apply to every caller, authenticated or not, and are evaluated before authentication (`ASM-BE-215`).
- No input says whether an excluded call from an unauthenticated caller should be answered 401 instead. The `OUT_OF_SCOPE` refusal is used.
- The project auth model is `BLOCKED` (`Q-BE-001`, `Q-BE-002`, `Q-BE-003`). When it is decided, this section needs revisiting only if it introduces a rule that applies to excluded calls.
<!-- /SECTION: backend.scope-guard.authorization -->
<!-- SECTION: backend.scope-guard.jobs | status: DERIVED -->
## Jobs

- **Scheduled work.** None. No input asks for a sweep, a poll or a retry in this module.
- **CI contract suite.** It runs on every CI build and every pull request (NFR-010; US-005-05). It is triggered by the build, not scheduled.
- **Retries.** None. A refusal is final, and a retried excluded call is refused again.
- **Idempotency keys.** None needed. Refusals have no side effects (`INV-BE-201`), so repeating a refused call changes nothing.
<!-- /SECTION: backend.scope-guard.jobs -->
<!-- SECTION: backend.scope-guard.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

No input names a test framework or CI platform (`GAP-BE-226`), so none is named here. Test names are proposals.

**Unit**
- `scope_guard_classifier_matches_attachment_patterns`: the `/attachments` and `/notes/{id}/attachments` examples classify as `attachment`.
- `scope_guard_classifier_matches_ws_pattern`: `/ws` classifies as `real-time`.
- `scope_guard_classifier_detects_upgrade_and_event_stream_headers` (`ASM-BE-214`).
- `scope_guard_classifier_decides_before_body_parse` (`ASM-BE-217`).
- `scope_guard_classifier_passes_in_scope_paths`: `/notes`, `/notes/{id}`, `/search` and the login paths are not matched.
- `scope_guard_empty_mobile_category_matches_nothing` (until `GAP-BE-224` closes).

**Integration**
- FR-001: attachment path, 404 with `OUT_OF_SCOPE`.
- FR-002: multipart `POST /notes`, 415 with `OUT_OF_SCOPE`, and the note count in PostgreSQL 16 unchanged.
- FR-003: WebSocket upgrade and SSE connection both rejected, no upgrade response.
- FR-005: every refusal's body parses against the envelope in `backend.error-model`.
- FR-006: two simultaneous `PATCH` calls to one note, each answered, no broadcast, no merge.
- `INV-BE-201`: a refused request leaves no row or file behind.

**Contract**
- FR-007: the suite passes against the published OpenAPI document and the route registry when no excluded route exists.
- FR-008: the suite fails when an `/attachments` route or a `/ws` route is added.
- `INV-BE-205`: a test asserts the middleware and the suite load the same list.
- The suite runs on every CI build (NFR-010).
<!-- /SECTION: backend.scope-guard.test-plan -->
<!-- SECTION: backend.scope-guard.gaps | status: VERIFIED -->
## Gaps

**Direction checks run:**
- Story ↔ design: `design-brief.md` carries acceptance criteria for EP-001 and `US-002-01` only, and has no overlap with EP-005. Nothing to compare.
- Story ↔ requirement: one tension found (`CON-BE-204`).
- Requirement ↔ requirement: no further contradiction in BR-014 to BR-016 and NFR-010, NFR-011.
- Design ↔ design: no design content.

**No `Q-` is minted.** Every open point is degradable and takes the default stated below (core rule 10, unattended run).

### Gaps

| ID | What is missing | Degrades | What was done instead | Closing artefact |
|---|---|---|---|---|
| `GAP-BE-223` | The status for WebSocket, SSE and mobile refusals. US-005-03 says only "standard error status". | FR-003, FR-004 | 404 with the `OUT_OF_SCOPE` envelope (`ASM-BE-212`) | A product or API decision on those statuses |
| `GAP-BE-224` | The documented exclusion list. No mobile-specific path patterns, no polling-push patterns, and the `attachment` and `real-time` patterns are examples only. | FR-004, FR-007, data-model | `mobile` category left empty; examples reproduced as given | The documented exclusion list referred to in BR-017 |
| `GAP-BE-225` | Which of "last-write" or "conflict" applies to concurrent `PATCH`. No version field or precondition header is defined by any input. | FR-006 | Only the absence of broadcast and merge is asserted | A decision recorded in the `notes` module |
| `GAP-BE-226` | The CI platform, the test framework and the merge-blocking mechanism. `platform.md` says only "hosted on AWS". | FR-008, test-plan | The suite is specified to fail with a failing result; the block is left to the pipeline | A platform input naming the CI and branch protection |
| `GAP-BE-227` | Whether the multipart refusal applies to endpoints other than note create and update. | FR-002 | Applied to note create and update only (`ASM-BE-217`) | A decision on the set of endpoints |

### Contradiction

| ID | Claims | Severity | Used reading |
|---|---|---|---|
| `CON-BE-204` | BR-016 requires a clear, documented error "not a silent failure or 404 ambiguity". US-005-02 requires "404 Not Found" for attachment paths. | non-critical (no data shape, state machine, auth rule or money/PII path) | 404 status with an `OUT_OF_SCOPE` body (`ASM-BE-211`). Choosing BR-016 alone would change the status and break the US-005-02 criterion. Choosing US-005-02 alone with a bare body would break BR-016 and US-005-04. |

### Assumptions

| ID | Assumption | What would confirm it |
|---|---|---|
| `ASM-BE-211` | Attachment-path refusals keep status 404 and carry an `OUT_OF_SCOPE` body, resolving `CON-BE-204`. | Product owner confirmation |
| `ASM-BE-212` | WebSocket, SSE and mobile refusals use the same envelope with status 404 until `GAP-BE-223` is closed. | A status decision |
| `ASM-BE-213` | The refusal `details` carries a `category` value naming the matched category. | Confirmation of the error envelope (`ASM-BE-006`) |
| `ASM-BE-214` | A WebSocket attempt is detected by an `Upgrade: websocket` header and an SSE attempt by `Accept: text/event-stream`. The guard runs before route handlers, so even `GET /openapi.json` with that `Accept` header is refused rather than answered 406. | API owner confirmation |
| `ASM-BE-215` | Refusals apply to every caller, authenticated or not, and run before authentication. | The decided auth model |
| `ASM-BE-216` | One exclusion list, held as configuration, is shared by the middleware and the contract suite. | A design decision on where the list lives |
| `ASM-BE-217` | The multipart check applies to note create and update, and is made on the `Content-Type` header before the body is parsed. | Closure of `GAP-BE-227` |
| `ASM-BE-218` | The contract suite checks the route registry as well as the OpenAPI document. | Confirmation from the QA owner |
| `ASM-BE-219` | This module has no persistent storage of its own. | Confirmation that no refusal record is required beyond logs |

**Referenced, not redefined:** `GAP-BE-006` (log retention) and `GAP-BE-009` (BR-017 and US-005-06 have no backend owner) are defined in the project spec.
<!-- /SECTION: backend.scope-guard.gaps -->
