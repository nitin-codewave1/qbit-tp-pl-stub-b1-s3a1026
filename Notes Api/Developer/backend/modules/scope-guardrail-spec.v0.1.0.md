---
id: agent-developer/notes_api/backend/modules/scope-guardrail-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/scope-guardrail-SPEC.md
depends_on: [notes_api:US-005-02, notes_api:US-005-03, notes_api:US-005-04, notes_api:US-005-05]
commit_kind: append
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: notes_api:backend/modules/scope-guardrail-SPEC.md
---

<!-- SECTION: backend.scope-guardrail.frontmatter | status: DERIVED -->
```yaml
id: scope-guardrail
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [CON-BE-007, GAP-BE-309]
scope: []
```
`scope` is empty because this run supplied no operation records for this module. No input defines an endpoint that `scope-guardrail` owns, and no `API-` id is written here. The module acts through a request-rejection layer and a CI test suite (see `backend.scope-guardrail.requirements`). [US-005-02 to US-005-05; backend.module-map]
<!-- /SECTION: backend.scope-guardrail.frontmatter -->
<!-- SECTION: backend.scope-guardrail.purpose | status: DERIVED -->
## 1. Purpose

**Responsibility.** The `scope-guardrail` module keeps the API surface inside its documented boundary. Mobile-specific, attachment and real-time endpoints are excluded. [BR-014, BR-015, BR-016; backend.module-map] It owns four things:

1. **Attachment rejection.** No attachment or file-upload route exists. A request to an attachment path, for example `POST /notes/{id}/attachments`, is rejected. [US-005-02; BR-014]
2. **File-payload rejection.** A multipart/form-data request with a file payload, sent to a note create or update endpoint, is rejected with 415 and persists no file content. [US-005-02]
3. **Real-time rejection.** No WebSocket or SSE channel exists. A WebSocket upgrade or SSE connection to any notes_api endpoint is rejected with no protocol upgrade. Collaboration has no live broadcast or merge logic. [US-005-03; BR-015]
4. **Clear out-of-scope error.** A call to an unsupported mobile-specific, attachment or real-time endpoint returns a structured error. The error uses code `OUT_OF_SCOPE` and a human-readable message that references the scope exclusion. It is never a silent failure or a bare 404. [US-005-04; BR-016; NFR-011]
5. **Contract test suite.** An automated suite runs on every CI build. It checks the published OpenAPI document and passes only when zero endpoints match an excluded category. A new endpoint matching an excluded pattern, for example `/attachments` or `/ws`, fails the suite and blocks the merge. [US-005-05; BR-014, BR-015; NFR-010]

**Not responsible for.**

- The error envelope, status-code policy and error-code registry. They are owned by `backend.error-model`, and this module applies them.
- The published OpenAPI document, its coverage metric and its Scope section, including the lint that fails a docs build when the Scope section is missing. These belong to `api-docs`. [US-005-01; backend.module-map]
- Concurrent-PATCH semantics for a note. They belong to `notes`. This module only requires that no broadcast or merge logic exists (`BE-SCOPE-GUARDRAIL-FR-004`; `GAP-BE-309`).
- Logging and classifying stakeholder requests for mobile, attachments or real-time collaboration. That is a product-management process outside backend. [BR-017; US-005-06; `GAP-BE-001`]
- Authentication, roles and the permission matrix (`backend.auth-model`, `GAP-BE-085`).
- Any UI.

**Inbound handoffs.** HANDOFF-WEBAPP-163212, HANDOFF-WEBAPP-200601 and HANDOFF-WEBAPP-743710 are listed for this run. Their content was not supplied, so none could be read or answered here (`GAP-BE-602`).

**Identifier note.** Ids minted by this module come from its reserved block: `ASM-BE-651` onward, `GAP-BE-867` onward, `INV-BE-260` onward. It mints no `CON-` and no `Q-`. All other ids are cited from the project spec.
<!-- /SECTION: backend.scope-guardrail.purpose -->
<!-- SECTION: backend.scope-guardrail.data-model | status: DERIVED -->
## 2. Data model

**No persisted entities.** This module owns no table, column, index or foreign key. No input asks it to store anything, and none of its stories carries a persistence requirement beyond "no file content is persisted" and "no note is persisted" on a rejected request. [US-005-02; US-005-04]

- **Exclusion list.** The excluded categories are mobile-specific, attachment and real-time. [BR-014, BR-015, BR-016; US-005-01] They are held as static configuration in the codebase, read by both the request-rejection layer and the contract test suite, and are not stored in the database (`ASM-BE-651`). The path patterns inside each category are `ASM-BE-654` and `GAP-BE-867`.
- **Rejected requests.** A rejected request writes nothing to PostgreSQL. This is required by `INV-BE-260`.
- **PII.** The module stores no PII. Its error responses must not echo the request body or file content (`INV-BE-262`).
- **Encryption and retention.** Not applicable, since nothing is stored.

**Migrations.** None. This module is absent from `backend.migration-plan`.
<!-- /SECTION: backend.scope-guardrail.data-model -->
<!-- SECTION: backend.scope-guardrail.state-machine | status: DERIVED -->
## 3. State machine

**No entity with a status.** The module holds no persisted state, so there are no states, no transitions and no terminal states to define. Each rejection decision is made per request and is stateless.

**Request decision table.** This is not a state machine. The rows are evaluated in order, and the first match decides the response.

| # | Condition | Result | Source |
|---|---|---|---|
| 1 | WebSocket upgrade or SSE connection attempted on any notes_api endpoint | rejected; no protocol upgrade; status open (`GAP-BE-309`) | US-005-03; BR-015 |
| 2 | path matches an attachment pattern (`ASM-BE-654`) | 404 with an `OUT_OF_SCOPE` body (`ASM-BE-653`, `CON-BE-007`) | US-005-02; US-005-04 |
| 3 | path matches a mobile-specific pattern | rejected with `OUT_OF_SCOPE`; patterns undefined (`GAP-BE-867`) | BR-016 |
| 4 | note create or update request with multipart/form-data content type (`ASM-BE-655`) | 415 with `OUT_OF_SCOPE`; no file content persisted | US-005-02; US-005-04 |
| 5 | anything else | not handled by this module; falls through to normal routing (`GAP-BE-868`) | |

**Contract-test outcome.** The suite result is `PASS` or `FAIL` per CI build and is not persisted by this module. `FAIL` blocks the merge. [US-005-05]
<!-- /SECTION: backend.scope-guardrail.state-machine -->
<!-- SECTION: backend.scope-guardrail.requirements | status: DRAFT-UNVERIFIED -->
## 4. Requirements

### BE-SCOPE-GUARDRAIL-FR-001 Reject attachment paths
**Given** the deployed API,
**When** a request is sent to any attachment path, for example `POST /notes/{id}/attachments`,
**Then** the response status is 404, the body is the error envelope from `backend.error-model` with code `OUT_OF_SCOPE`, and the message references the scope exclusion.
- **Traces to.** US-005-02, US-005-04; BR-014, BR-016; NFR-011.
- **Verification.** `test-scope-attachment-path-404-out-of-scope` (planned).
- **API.** none; this is a routing-layer response.
- **Status.** `DRAFT-UNVERIFIED`. US-005-02 asks for 404, while US-005-04 and BR-016 ask for a message and not a bare 404. The reading used is `ASM-BE-653`. The contradiction is `CON-BE-007`.

### BE-SCOPE-GUARDRAIL-FR-002 Reject file payloads on note endpoints
**Given** a note create or update endpoint,
**When** a request with a multipart/form-data content type and a file payload is submitted,
**Then** the response is 415 with code `OUT_OF_SCOPE` and a human-readable reason, no file content is persisted, and no note is created or changed.
- **Traces to.** US-005-02, US-005-04; BR-014.
- **Verification.** `test-scope-multipart-415-no-persist` (planned).
- **API.** none (the endpoints are owned by `notes`).
- **Status.** Detection rule: `ASM-BE-655`. Which routes count as note endpoints: `GAP-BE-869`. Status 415 is shared with the login refusals, so consumers must switch on the error code (`backend.error-model`).

### BE-SCOPE-GUARDRAIL-FR-003 Reject real-time channels
**Given** the running API server,
**When** a client attempts a WebSocket upgrade or an SSE connection to any notes_api endpoint,
**Then** the connection is rejected, no protocol upgrade occurs, and a standard error status is returned.
- **Traces to.** US-005-03; BR-015; BR-016.
- **Verification.** `test-scope-websocket-upgrade-rejected`, `test-scope-sse-rejected` (planned).
- **API.** none.
- **Status.** The exact status, and the body shape for a rejected upgrade, are `GAP-BE-309`. How an SSE attempt is recognised is `ASM-BE-654`.

### BE-SCOPE-GUARDRAIL-FR-004 No live broadcast or merge logic
**Given** two clients submit PATCH requests to the same note concurrently,
**When** both requests are processed,
**Then** each is handled as an ordinary request, the API applies a standard last-write or conflict response, and no live broadcast or merge logic runs.
- **Traces to.** US-005-03; BR-015.
- **Verification.** `test-scope-concurrent-patch-no-broadcast` (planned).
- **API.** none (the PATCH endpoint is owned by `notes`).
- **Status.** Last-write versus conflict is `GAP-BE-309` and is decided in `notes`. This requirement fixes only the absence of broadcast and merge.

### BE-SCOPE-GUARDRAIL-FR-005 Reject mobile-specific endpoints
**Given** a call to an unsupported mobile-specific endpoint,
**When** the request is made,
**Then** the response carries code `OUT_OF_SCOPE` with a message referencing the scope exclusion, and not a silent failure or a bare 404.
- **Traces to.** BR-016; US-005-01; US-005-04.
- **Verification.** `test-scope-mobile-path-out-of-scope` (planned).
- **API.** none.
- **Status.** `BLOCKED` on `GAP-BE-867`: no input says what a mobile-specific path looks like.

### BE-SCOPE-GUARDRAIL-FR-006 Documented, explanatory 4xx
**Given** any call that this module rejects as out of scope,
**When** the response is produced,
**Then** it is a documented 4xx response carrying an explanatory message, in the shared error envelope.
- **Traces to.** NFR-011; BR-016; US-005-04.
- **Verification.** `test-scope-all-rejections-use-envelope` (planned).
- **API.** none. The response shape is defined once in `backend.error-model`, and `api-docs` documents it.
- **Status.** The target is 100% of out-of-scope calls, per NFR-011. Error code names other than `OUT_OF_SCOPE` are proposals (`ASM-BE-301`).

### BE-SCOPE-GUARDRAIL-FR-007 Contract suite passes on a clean surface
**Given** the CI pipeline runs,
**When** the scope-guardrail suite executes against the OpenAPI document,
**Then** it passes only if zero endpoints match the excluded categories (mobile, attachments, real-time).
- **Traces to.** US-005-05; BR-014, BR-015; NFR-010.
- **Verification.** `test-scope-contract-clean-spec-passes` (planned).
- **API.** none. The suite reads the document owned by `api-docs`.

### BE-SCOPE-GUARDRAIL-FR-008 Contract suite blocks an excluded endpoint
**Given** a developer adds a new endpoint matching an excluded pattern, for example `/attachments` or `/ws`,
**When** CI runs on the pull request,
**Then** the guardrail test fails and the merge is blocked.
- **Traces to.** US-005-05; NFR-010.
- **Verification.** `test-scope-contract-excluded-endpoint-fails` (planned, using a fixture spec containing `/attachments` and one containing `/ws`).
- **API.** none.
- **Status.** The patterns beyond the two examples are `ASM-BE-654` and `GAP-BE-867`.

### BE-SCOPE-GUARDRAIL-FR-009 Suite runs on every build
**Given** any CI build,
**When** the pipeline executes,
**Then** the scope-guardrail suite runs, and the metric is 0 out-of-scope endpoints detected per build.
- **Traces to.** NFR-010; US-005-05.
- **Verification.** `test-scope-ci-runs-every-build` (planned, as a pipeline configuration check).
- **API.** none.
<!-- /SECTION: backend.scope-guardrail.requirements -->
<!-- SECTION: backend.scope-guardrail.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

No HTTP operation is defined.
<!-- /SECTION: backend.scope-guardrail.api-contract -->
<!-- SECTION: backend.scope-guardrail.invariants | status: BLOCKED -->
## Invariants

**BLOCKED.** the run did not produce this section for module `scope-guardrail`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: backend.scope-guardrail.invariants -->
<!-- SECTION: backend.scope-guardrail.authorization | status: DRAFT-UNVERIFIED -->
## 8. Authorization

**No role invokes an operation of this module**, because the module owns no operation. It acts on requests addressed to other routes.

- **Order of checks.** Out-of-scope matching runs before authentication, so that an integrator gets the documented error without needing a credential (`ASM-BE-652`). The `OUT_OF_SCOPE` response reveals only what the published Scope section already states. [US-005-01]
- **Ownership predicate.** None. The module touches no user-scoped resource.
- **Permissions.** Roles are undefined (`GAP-BE-085`, `ASM-BE-302`). The module needs no permission grant, since a rejection performs no privileged action.
- **Disclosure.** A rejection says nothing about whether a note, user or session exists.
<!-- /SECTION: backend.scope-guardrail.authorization -->
<!-- SECTION: backend.scope-guardrail.jobs | status: DERIVED -->
## 9. Jobs

- **Runtime jobs.** None. There is no scheduled sweep, retry or background task in this module.
- **CI job.** The contract suite runs on every CI build, and this is the only recurring work. [NFR-010; US-005-05] It runs on pull requests as well as on merged builds, because a failure must block the merge.
- **Idempotency.** Not applicable. Rejections are stateless, and repeating a rejected request yields the same rejection.
- **Retries.** None. A rejected request is not retried by the server.
- **Tooling.** The CI system and the test framework are not named (`GAP-BE-089`).
<!-- /SECTION: backend.scope-guardrail.jobs -->
<!-- SECTION: backend.scope-guardrail.test-plan | status: DRAFT-UNVERIFIED -->
## 10. Test plan

**Unit.**
- `test-scope-matcher-attachment-patterns`: the attachment pattern matcher, with positive and negative paths (`ASM-BE-654`).
- `test-scope-multipart-detection`: content-type detection (`ASM-BE-655`), including case variants and a boundary parameter.
- `test-scope-response-builder-fixed-text`: the response uses fixed message text and echoes nothing (`INV-BE-262`).

**Integration.** Each runs against the application with a test PostgreSQL 16 database.
- `test-scope-attachment-path-404-out-of-scope` (FR-001).
- `test-scope-multipart-415-no-persist`: asserts row counts are unchanged after the request (FR-002, `INV-BE-260`).
- `test-scope-websocket-upgrade-rejected` and `test-scope-sse-rejected` (FR-003).
- `test-scope-concurrent-patch-no-broadcast` (FR-004).
- `test-scope-all-rejections-use-envelope` (FR-006).
- `test-scope-reject-before-auth`: an unauthenticated request to an excluded path returns `OUT_OF_SCOPE` and not 401 (`ASM-BE-652`).

**Contract.** The suite itself, run against fixture specs.
- `test-scope-contract-clean-spec-passes` (FR-007).
- `test-scope-contract-excluded-endpoint-fails`, with fixtures containing `/attachments` and `/ws` (FR-008).
- `test-scope-ci-runs-every-build` (FR-009).
- `test-scope-contract-detects-real-time-in-spec`: a spec entry that declares a WebSocket or SSE channel fails the suite.

**Blocked tests.** The mobile-specific case (FR-005) and the exact real-time status (FR-003) cannot be asserted until `GAP-BE-867` and `GAP-BE-309` are closed.
<!-- /SECTION: backend.scope-guardrail.test-plan -->
<!-- SECTION: backend.scope-guardrail.gaps | status: DERIVED -->
## 11. Gaps, contradictions and assumptions

Items defined in the project spec are cited here, not copied.

**Cited from the project spec.**
- `CON-BE-007` (non-critical): US-005-02 requires 404 for attachment paths, while US-005-04 and BR-016 require a message and not a bare 404. This module follows `ASM-BE-653` and does not resolve it.
- `GAP-BE-309`: the status for WebSocket and SSE rejection, and the concurrent-PATCH outcome, are open.
- `GAP-BE-001`: US-005-06 belongs to no backend module.
- `GAP-BE-602`: the three inbound handoffs were not supplied.
- `GAP-BE-089`: no CI system, test framework or library is named.
- `GAP-BE-085`, `ASM-BE-302`: authentication and roles.
- `ASM-BE-301`: error code names other than `OUT_OF_SCOPE` are proposals.

**Gaps raised by this module.**

| ID | Gap | Degrades | Fallback | Closing artefact |
|---|---|---|---|---|
| `GAP-BE-867` | No input defines what a mobile-specific endpoint or path is. | FR-005, FR-008 (partly), `INV-BE-261` | None. FR-005 is `BLOCKED`. | A list of excluded mobile path patterns |
| `GAP-BE-868` | Behaviour for an unknown path outside every excluded category is not stated. | decision-table row 5 | Falls through to normal routing and the framework's default for unknown routes. | A statement of the unknown-route response |
| `GAP-BE-869` | Which routes count as "a note create/update endpoint" for the multipart rule is not stated. US-005-02 names create/update only. | FR-002 | Applies to every note route that accepts a body, as named in `notes`. | The notes endpoint list, or a rule that covers all routes |
| `GAP-BE-870` | No input says whether rejected out-of-scope calls are logged or counted in the API for the rejected-requests KPI. BR-017 assigns that count to a product-management log. | observability | Nothing is logged here beyond the standard request logging in `backend.observability`. | A decision on whether the API emits a metric for these rejections |

**Assumptions made by this module.**
- `ASM-BE-651`: the exclusion list is static configuration in the codebase, not a database table. No input says where it lives and none requires it to change at runtime. A different choice would only add a table.
- `ASM-BE-652`: out-of-scope matching runs before authentication. BR-016 asks for a clear, documented error for integrators, and the Scope section is public documentation. Confirm with the owner of the authorisation model.
- `ASM-BE-653`: an attachment-path request returns status 404 with an `OUT_OF_SCOPE` envelope. That satisfies US-005-02's status and US-005-04's message together. If the product owner prefers a different 4xx, `CON-BE-007` governs.
- `ASM-BE-654`: an attachment pattern is any request path that has a segment equal to `attachments`. A real-time pattern is a path whose segment is `ws`, or a request carrying an `Upgrade: websocket` header or an `Accept: text/event-stream` header. US-005-05 names `/attachments` and `/ws` as examples and gives nothing further. Confirm with `GAP-BE-867`.
- `ASM-BE-655`: a file payload is detected from a `Content-Type` of `multipart/form-data`, not from the body contents. US-005-02 and US-005-04 name multipart uploads as the case.
<!-- /SECTION: backend.scope-guardrail.gaps -->
