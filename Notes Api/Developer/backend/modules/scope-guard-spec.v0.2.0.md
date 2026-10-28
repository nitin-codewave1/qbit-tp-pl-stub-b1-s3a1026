---
id: agent-developer/notes_api/backend/modules/scope-guard-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/scope-guard-SPEC.md
depends_on: [notes_api:US-005-02, notes_api:US-005-03, notes_api:US-005-04, notes_api:US-005-05]
commit_kind: fork
confidence: agent
run_id: e1e628bb-b234-5a13-b948-b4689dcc8eab
okf_id: notes_api:backend/modules/scope-guard-SPEC.md
---

<!-- SECTION: backend.scope-guard.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: scope-guard
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: []
scope: []   # no operation records were supplied for this module; it owns no API- id
```

`scope` is empty because the list of operation records supplied for this module is empty. The project spec's module map describes `scope-guard` as request middleware over the whole HTTP surface (`backend.module-map`). `contract_pin` is omitted because the backend is the source.
<!-- /SECTION: backend.scope-guard.frontmatter -->
<!-- SECTION: backend.scope-guard.purpose | status: DERIVED -->
## Purpose

**Responsibility.** `scope-guard` is the middleware that keeps `notes_api` inside its documented scope. It does this in four ways:

- It refuses calls to attachment, real-time and mobile-specific endpoints with a structured, documented error (BR-016, US-005-04, NFR-011).
- It makes sure no attachment or file-upload endpoint exists and no multipart file payload is accepted (BR-014, US-005-02).
- It makes sure no WebSocket, SSE or other real-time channel exists and that none is established (BR-015, US-005-03).
- It owns the automated guardrail contract test, run on every CI build, that fails when any endpoint in an excluded category appears (NFR-010, US-005-05).

**Stories owned:** US-005-02, US-005-03, US-005-04, US-005-05.

**Not responsible for:**

- The published OpenAPI document and its Scope section (US-005-01). `contract-docs` owns them. This module only supplies the exclusion list that the Scope section describes.
- Logging and classifying stakeholder requests against the exclusion list (US-005-06, BR-017). That is a product-manager process (`GAP-BE-009`). The run-time refusal event in FR-009 is a different thing from that log.
- The outcome of concurrent edits to one note. `notes` owns the PATCH behaviour. This module owns only the absence of live broadcast and merge logic (FR-005; `GAP-BE-221`).
- Authentication and authorisation (`backend.auth-model`, which is BLOCKED).
- The full error envelope and registry (`backend.error-model`). This module uses them and does not redefine them.
- Residency, which is `residency`'s concern.
<!-- /SECTION: backend.scope-guard.purpose -->
<!-- SECTION: backend.scope-guard.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**This module has no database tables, columns or indexes, and no migration.** `backend.migration-plan` lists no `scope-guard` migration, and `backend.data-lifecycle` lists no `scope-guard` data. No request is persisted. The module is stateless per request, and a refused request writes nothing (US-005-02, multipart criterion).

The module does need an **exclusion list**: the set of request patterns it refuses. No input says where it is stored. `ASM-BE-217` assumes it is a versioned configuration artefact in the repository. The runtime classifier and the CI guardrail test both read it, so the two cannot drift apart.

### Logical shape of one exclusion entry

| Field | Type | Nullable | Default | Constraint |
|---|---|---|---|---|
| `category` | enum `attachment` / `real-time` / `mobile` | no | none | Categories come from BR-014, BR-015 and BR-016 |
| `method` | HTTP method | yes | null, meaning any method (`ASM-BE-222`) | none |
| `path_pattern` | text | no | none | Segments in braces match exactly one path segment. The entry also matches any deeper path beneath it (`ASM-BE-223`). |
| `reason` | text | no | none | Human-readable explanation that names the scope exclusion (US-005-04) |

The pair (`method`, `path_pattern`) is unique.

### Seed entries

| category | method | path_pattern | Source |
|---|---|---|---|
| attachment | any | `/notes/{id}/attachments` | US-005-02, example path |
| attachment | any | `/attachments` | US-005-05, example |
| real-time | any | `/ws` | US-005-05, example |
| mobile | none | none | No input gives any mobile-specific pattern (`GAP-BE-220`) |

Protocol signals for real-time connections (an upgrade request, an event-stream request) are fixed in code and not configured (`ASM-BE-220`).

PII: the entries carry no personal data.
<!-- /SECTION: backend.scope-guard.data-model -->
<!-- SECTION: backend.scope-guard.state-machine | status: DERIVED -->
## State machine

**No entity owned by this module has a status column, so there is no entity state machine.** The module makes one decision per request. Nothing persists between requests.

| From | Trigger and guard | To |
|---|---|---|
| Received | The request path matches an exclusion entry (`ASM-BE-222`, `ASM-BE-223`) | Refused: path (attachment, real-time or mobile) |
| Received | No path match, and the request carries a real-time protocol signal (`ASM-BE-220`) | Refused: real-time |
| Received | No match so far, and the request carries a multipart file payload (`ASM-BE-219`) | Refused: media type |
| Received | None of the above | Passed to routing |

Within this module the guards are evaluated in the order shown: path, then protocol signal, then media type (`ASM-BE-224`).

**Terminal states.** All three Refused states are terminal. The request ends with the refusal response and no side effect. Passed to routing is terminal for this module, and the request then belongs to the owning module.

**Illegal transitions.** None exist, because there is no persisted state to transition. A refusal is never reversed within a request.
<!-- /SECTION: backend.scope-guard.state-machine -->
<!-- SECTION: backend.scope-guard.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

All `API.` entries below are "none" because this module owns no operation. Routes are referred to by method and path.

### BE-SCOPE-GUARD-FR-001: attachment paths are refused
- **Given** a request, with any HTTP method, to a path that matches an `attachment` exclusion entry, for example `POST /notes/{id}/attachments`,
- **When** the request is received,
- **Then** the response status is 404, the body is the structured error with the registry code `OUT_OF_SCOPE` and a message referencing the scope exclusion, and nothing is persisted.
- **Traces to.** US-005-02 (first criterion), US-005-04 (first criterion), BR-014, BR-016, NFR-011. The reading "404 status with a descriptive body" resolves `CON-BE-002` (`ASM-BE-016`).
- **Verification.** `scope_guard.attachment_path_returns_404_out_of_scope` (planned).
- **API.** none.

### BE-SCOPE-GUARD-FR-002: multipart file payloads are refused
- **Given** a request with a `multipart/form-data` file payload sent to `POST /notes` or to `PATCH /notes/{id}` (`ASM-BE-219` extends this to every endpoint that takes a body),
- **When** the request is received,
- **Then** the response status is 415, the body is a structured error with a code and a human-readable reason that references the scope exclusion, and no file content is persisted.
- **Traces to.** US-005-02 (second criterion), US-005-04 (second criterion), BR-014, NFR-011.
- **Verification.** `scope_guard.multipart_upload_returns_415_no_persist` (planned).
- **API.** none.
- **Open.** The code is `UNSUPPORTED_MEDIA_TYPE`, taken from the project error registry. US-005-04 gives `OUT_OF_SCOPE` only as an example (`ASM-BE-218`).

### BE-SCOPE-GUARD-FR-003: real-time connections are never established
- **Given** a client attempts a WebSocket upgrade or an SSE connection to any `notes_api` endpoint,
- **When** the request is received,
- **Then** the connection is rejected, no protocol upgrade occurs, and a standard error status is returned. The body follows the error envelope.
- **Traces to.** US-005-03 (first criterion), BR-015, US-005-04.
- **Verification.** `scope_guard.websocket_upgrade_rejected_no_101` and `scope_guard.sse_request_rejected` (both planned).
- **API.** none.
- **Open.** The exact status is not defined by any input (`GAP-BE-011`). Detection of the signals is `ASM-BE-220`.

### BE-SCOPE-GUARD-FR-004: mobile-specific endpoints are refused
- **Given** a request to a path that matches a `mobile` exclusion entry,
- **When** the request is received,
- **Then** the response is the same structured refusal as FR-001, referencing the scope exclusion.
- **Traces to.** BR-016, US-005-04 (first criterion), NFR-011.
- **Verification.** `scope_guard.mobile_pattern_refused` (planned). It cannot be written until `GAP-BE-220` is closed.
- **API.** none.
- **Open.** No input defines any mobile-specific pattern, so the seed list has no mobile entry.

### BE-SCOPE-GUARD-FR-005: no live broadcast or merge logic on concurrent edits
- **Given** two clients send `PATCH /notes/{id}` to the same note concurrently,
- **When** both requests are processed,
- **Then** each gets the standard response of the `notes` module, and no component in `notes_api` broadcasts the change to other clients or merges the two edits.
- **Traces to.** US-005-03 (second criterion), BR-015.
- **Verification.** `scope_guard.concurrent_patch_no_broadcast_no_merge` (planned).
- **API.** none.
- **Open.** The inputs say "last-write/conflict" without choosing one. That choice belongs to `notes` (`GAP-BE-221`).

### BE-SCOPE-GUARD-FR-006: every refusal is a documented 4xx with an explanatory message
- **Given** any refusal produced by this module,
- **When** the response is built,
- **Then** it uses the error envelope and registry of `backend.error-model`, carries a machine-readable code and a message naming the scope exclusion, and its 4xx status is documented in the published spec.
- **Traces to.** NFR-011 (100% of out-of-scope calls return a documented 4xx error with an explanatory message), BR-016, US-005-04.
- **Verification.** `scope_guard.refusals_use_error_envelope` (planned).
- **API.** none. The documentation of these responses is `contract-docs`'s section, not restated here.

### BE-SCOPE-GUARD-FR-007: the guardrail test passes only when no excluded endpoint exists
- **Given** the CI pipeline runs,
- **When** the scope-guardrail test suite executes against the OpenAPI document,
- **Then** it passes only if zero endpoints match the excluded categories (mobile, attachment, real-time). The detected count must be 0 on every build.
- **Traces to.** US-005-05 (first criterion), NFR-010, BR-014, BR-015.
- **Verification.** `scope_guard.contract_suite_zero_excluded_endpoints` (planned).
- **API.** none.
- **Dependency.** The published document must not list the refused paths as endpoints, or the suite would fail by construction. The refusal behaviour is documented through the shared error response and the Scope section, owned by `contract-docs`.

### BE-SCOPE-GUARD-FR-008: a new excluded endpoint fails CI and blocks the merge
- **Given** a developer adds an endpoint matching an excluded pattern, for example `/attachments` or `/ws`,
- **When** CI runs on the pull request,
- **Then** the guardrail test fails and the merge is blocked.
- **Traces to.** US-005-05 (second criterion), NFR-010.
- **Verification.** `scope_guard.injected_excluded_path_fails_suite` (planned).
- **API.** none.
- **Open.** How the merge is blocked depends on the CI host, which no input names (`GAP-BE-223`).

### BE-SCOPE-GUARD-FR-009: each refusal produces an event
- **Given** this module refuses a request,
- **When** the response is sent,
- **Then** an out-of-scope-refusal event is logged, as `backend.observability` already lists.
- **Traces to.** `backend.observability`. It supports the metric context of BR-017 but does not replace the product-manager log (`GAP-BE-009`).
- **Verification.** `scope_guard.refusal_emits_event` (planned).
- **API.** none.
- **Open.** The fields of the event are not specified by any input (`GAP-BE-008`). They must exclude any one-time code and any unmasked email address (`backend.observability`, `ASM-BE-014`).
<!-- /SECTION: backend.scope-guard.requirements -->
<!-- SECTION: backend.scope-guard.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

No HTTP operation is defined.
<!-- /SECTION: backend.scope-guard.api-contract -->
<!-- SECTION: backend.scope-guard.invariants | status: DERIVED -->
## Invariants

- **INV-BE-201.** After every build, the OpenAPI document and the route registry contain no endpoint in an excluded category (attachment, real-time, mobile). *Source: BR-014, BR-015, NFR-010, US-005-05.*
- **INV-BE-202.** After any request this module refuses, no file content, note, tag or other record was written because of that request. *Source: US-005-02, second criterion.*
- **INV-BE-203.** Every refusal this module sends carries a machine-readable code and a human-readable reason that references the scope exclusion. It is never a bare 404 and never a silent failure. *Source: BR-016, US-005-04, NFR-011.*
- **INV-BE-204.** No protocol upgrade and no event-stream connection is ever established on any `notes_api` endpoint. *Source: US-005-03, BR-015.*
- **INV-BE-205.** Refusing a request never changes any state, and the same request gets the same verdict from the same exclusion list. The decision is a pure function of the request and the list. *Derived from the stateless design; see `backend.scope-guard.state-machine`.*
<!-- /SECTION: backend.scope-guard.invariants -->
<!-- SECTION: backend.scope-guard.authorization | status: DRAFT-UNVERIFIED -->
## Authorization

**Operations and roles.** This module owns no operation, so there is no role × operation matrix. The refusal verdict is not tied to a role. `ASM-BE-221` assumes the module runs before authentication and validation, so every caller, authenticated or not, gets the same verdict for an excluded request.

**Ownership predicate.** None. The module handles no user-scoped resource.

**Relationship to `backend.auth-model`.** That section is BLOCKED (`Q-BE-001`, `Q-BE-002`, `Q-BE-003`). Nothing in this module waits on those decisions as long as `ASM-BE-221` holds. If the human decides that unauthenticated callers must get 401 before any other response, FR-001 to FR-004 change order, and that is a decision for the human.
<!-- /SECTION: backend.scope-guard.authorization -->
<!-- SECTION: backend.scope-guard.jobs | status: DERIVED -->
## Jobs

- **Scheduled work and sweeps.** None. The module stores nothing that needs a sweep, and `backend.data-lifecycle` lists no `scope-guard` data.
- **Retries.** None. A refusal is final and needs no retry.
- **Idempotency keys.** None. The classifier is a pure function of the request, so repeating a request gives the same refusal.
- **CI execution.** The guardrail contract test is the only recurring execution. It runs on every CI build and on every pull request (NFR-010, US-005-05). It is a pipeline step, not a scheduled job. The CI host is not named by any input (`GAP-BE-223`).
<!-- /SECTION: backend.scope-guard.jobs -->
<!-- SECTION: backend.scope-guard.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All test names are proposals, marked "(planned)" in the requirements.

### Unit
- The classifier matches `/notes/{id}/attachments`, `/attachments` and `/ws` for any method. *(FR-001, FR-003)*
- The classifier does not match in-scope paths such as `/notes`, `/notes/search`, `/notes/{id}/tags`, `/auth/request-code`, `/auth/verify-code` or `/openapi`. *(FR-001)*
- A deeper path beneath an excluded pattern matches (`ASM-BE-223`). *(FR-001)*
- Evaluation order is path, then protocol signal, then media type (`ASM-BE-224`). *(state machine)*
- Detection of an upgrade signal and an event-stream signal (`ASM-BE-220`). *(FR-003)*
- A refusal body has a code and a message that references the scope exclusion. *(FR-006)*

### Integration
- `POST /notes/{id}/attachments` returns 404 with the refusal body. *(FR-001)*
- A multipart file upload to `POST /notes` and to `PATCH /notes/{id}` returns 415, and the notes tables are unchanged afterwards. *(FR-002, INV-BE-202)*
- A WebSocket upgrade request is rejected and no upgrade is performed. An SSE request is rejected. *(FR-003)*
- Two concurrent `PATCH /notes/{id}` requests each get the `notes` module's standard response, with no broadcast. *(FR-005)*
- A refusal emits an out-of-scope-refusal event that contains no code and no unmasked email. *(FR-009)*

### Contract
- The suite runs against `contracts/openapi.yaml` and passes only when zero endpoints match an excluded category. *(FR-007, INV-BE-201)*
- Negative test: inject `/attachments` and `/ws` paths into a copy of the document and confirm the suite fails. *(FR-008)*
- Every refusal response in the document is documented with the shared error envelope. *(FR-006, NFR-011)*
- The detected out-of-scope endpoint count is exported per build, as NFR-010 requires.
<!-- /SECTION: backend.scope-guard.test-plan -->
<!-- SECTION: backend.scope-guard.gaps | status: VERIFIED -->
## Gaps

### Gaps raised by this module

- **`GAP-BE-220`** (non-critical) — No input defines any mobile-specific endpoint pattern, and the attachment and real-time patterns are only examples (`/notes/{id}/attachments`, `/attachments`, `/ws`). *Degrades:* FR-004 and the data-model seed list. *Done instead:* seeded with the cited examples and left the mobile category empty. *Closing artefact:* the exclusion list that the Scope section describes (US-005-01), or a product decision.
- **`GAP-BE-221`** (non-critical) — US-005-03 says concurrent PATCH requests get a "last-write/conflict" response without choosing one. *Degrades:* FR-005. *Done instead:* this module asserts only that no broadcast or merge logic exists. *Closing artefact:* a decision in the `notes` module on concurrent-edit behaviour.
- **`GAP-BE-222`** (non-critical) — No input defines the response to a request for a path that matches no exclusion entry and no route. BR-016 forbids a bare 404 only for out-of-scope calls, and the registry has no generic not-found code. *Degrades:* the "Passed to routing" boundary. *Done instead:* nothing is specified. *Closing artefact:* an error-handling requirement for unknown routes.
- **`GAP-BE-223`** (non-critical) — No input names the CI host or says how a failing check blocks a merge. `platform.md` names AWS only. *Degrades:* FR-008, the CI part of `backend.scope-guard.jobs`. *Done instead:* the requirement states the outcome (the guardrail fails and the merge is blocked) without a mechanism. *Closing artefact:* a platform input naming the CI system and its merge rules.

### Carried from the project spec (cited, not re-raised)

`CON-BE-002`, `GAP-BE-008`, `GAP-BE-009`, `GAP-BE-011`, `ASM-BE-001`, `ASM-BE-014`, `ASM-BE-016`. `Q-BE-001`, `Q-BE-002` and `Q-BE-003` are blocking items for `backend.auth-model`, not for this module (`ASM-BE-221`).

### Assumptions

- **`ASM-BE-217`** — The exclusion list is a versioned configuration artefact in the repository, not a database table. No input names its storage. It is read by both the runtime classifier and the CI test. *Confirm with:* the platform owner.
- **`ASM-BE-218`** — A multipart file upload returns 415 with the registry code `UNSUPPORTED_MEDIA_TYPE`, and the message references the scope exclusion. US-005-04 gives `OUT_OF_SCOPE` only as an example ("e.g."). *Confirm with:* the contract owner. If `OUT_OF_SCOPE` with 415 is wanted, the registry in `backend.error-model` changes.
- **`ASM-BE-219`** — The multipart refusal applies to every endpoint that takes a body, not only note create and update. No endpoint accepts files (BR-014), so a broader rule has no conflicting case. *Confirm with:* the contract owner.
- **`ASM-BE-220`** — Real-time attempts are detected by an `Upgrade: websocket` header and an `Accept: text/event-stream` header, on any path. No input says how detection works. *Confirm with:* the platform owner.
- **`ASM-BE-221`** — The module runs before authentication and validation, so the verdict is the same for every caller. This keeps the work independent of the BLOCKED auth model. *Confirm with:* the human, together with `Q-BE-003`.
- **`ASM-BE-222`** — An exclusion entry with no method applies to every HTTP method. US-005-02 shows a POST example, and BR-014 forbids all attachment endpoints.
- **`ASM-BE-223`** — A `path_pattern` also matches any deeper path beneath it, and `{…}` matches exactly one segment.
- **`ASM-BE-224`** — Evaluation order inside the module is path, then protocol signal, then media type. A multipart request to `/notes/{id}/attachments` therefore gets the 404 refusal.
<!-- /SECTION: backend.scope-guard.gaps -->
