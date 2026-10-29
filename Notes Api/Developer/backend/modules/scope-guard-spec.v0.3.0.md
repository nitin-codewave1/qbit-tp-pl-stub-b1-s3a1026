---
id: agent-developer/notes_api/backend/modules/scope-guard-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/scope-guard-SPEC.md
depends_on: [notes_api:US-005-02, notes_api:US-005-03, notes_api:US-005-04, notes_api:US-005-05]
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
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
scope: []
```

`scope` is empty because DevAgent minted no operation records for this module. It is request middleware over the whole HTTP surface (project `backend.module-map`). It owns refusal behaviour, not endpoints.
<!-- /SECTION: backend.scope-guard.frontmatter -->
<!-- SECTION: backend.scope-guard.purpose | status: DERIVED -->
## Purpose

**Responsibility.** `scope-guard` keeps notes_api inside its documented scope. It enforces three things:

- **Absence.** No attachment or file-upload endpoint (BR-014), no real-time channel (BR-015), and no mobile-specific endpoint (BR-016) exists.
- **Refusal.** A call into an excluded category gets a clear, documented, structured refusal, not a silent failure or a bare 404 (BR-016; US-005-04; NFR-011).
- **Continuous check.** A contract test suite proves that absence on every CI build (NFR-010; US-005-05).

**Owned stories:** US-005-02, US-005-03, US-005-04, US-005-05.

**Not responsible for:**

- Publishing the Scope section of the API documentation, and the docs-build lint that fails when it is missing (US-005-01). `contract-docs` owns these.
- Documenting the refusal responses in the OpenAPI document. `contract-docs` owns this (US-004-03).
- Logging and classifying stakeholder requests (BR-017, US-005-06). This is a product-manager process (`GAP-BE-009`).
- Concurrent-edit semantics of PATCH. The `notes` module defines them. This module only requires that no live broadcast or merge logic exists (US-005-03).
- Authentication and authorisation (`backend.auth-model`, `BLOCKED` on `Q-BE-003`).
- Behaviour for paths outside the exclusion categories.
<!-- /SECTION: backend.scope-guard.purpose -->
<!-- SECTION: backend.scope-guard.data-model | status: DERIVED -->
## Data model

This module has **no persistent tables, columns, constraints or indexes**.

- Its decision is a pure function of the request: method, path, headers and content type.
- No input requires it to store anything. Logging and classifying stakeholder requests is a product-manager process (BR-017; `GAP-BE-009`).
- Refusing a file payload persists no file content (US-005-02).

The exclusion categories are configuration, not data: attachments, real-time (WebSocket, SSE, polling-push) and mobile-specific (BR-014 to BR-016). Their match patterns are open (`GAP-BE-217`).
<!-- /SECTION: backend.scope-guard.data-model -->
<!-- SECTION: backend.scope-guard.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

The module is stateless across requests. It has no entity with a status column. Each request has this per-request lifecycle:

| State | Meaning | Terminal |
|---|---|---|
| `RECEIVED` | Request arrived at the HTTP surface | no |
| `PASSED` | Request matches no excluded category and is handed to the owning module | yes, for this module |
| `REFUSED` | Request matches an excluded category and a structured refusal is returned | yes |

| Transition | Trigger | Guard | Result |
|---|---|---|---|
| `RECEIVED` → `REFUSED` | Path matches an attachment pattern, for example `POST /notes/{id}/attachments` | none | 404 with code `OUT_OF_SCOPE` (US-005-02; `ASM-BE-008`) |
| `RECEIVED` → `REFUSED` | Multipart or file payload sent to a note create/update endpoint | content type is multipart or carries a file | 415 with a structured error; no file content persisted (US-005-02; US-005-04; `CON-BE-205`) |
| `RECEIVED` → `REFUSED` | WebSocket upgrade or SSE connection attempt on any endpoint | none | Connection rejected with no protocol upgrade; status unstated (US-005-03; `GAP-BE-011`) |
| `RECEIVED` → `REFUSED` | Call to a mobile-specific path | pattern defined (`GAP-BE-217`) | Documented out-of-scope refusal (BR-016) |
| `RECEIVED` → `PASSED` | No category matches | none | Forwarded unchanged |

**Illegal transition.** None exists. Every request ends in exactly one terminal state. `REFUSED` never reverts to `PASSED`.
<!-- /SECTION: backend.scope-guard.state-machine -->
<!-- SECTION: backend.scope-guard.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

**BE-SCOPE-GUARD-FR-001**
- Given the deployed API, when a request is sent to any attachment path (for example `POST /notes/{id}/attachments`), then the API returns 404 and the body carries the structured code `OUT_OF_SCOPE` with a message referencing the scope exclusion, not a bare 404.
- Traces to. US-005-02 (AC1); US-005-04 (AC1); BR-014; BR-016; `CON-BE-002`; `ASM-BE-008`.
- Verification. `scope_guard_attachment_path_returns_404_out_of_scope` (planned).
- API. none.

**BE-SCOPE-GUARD-FR-002**
- Given a note create or update endpoint, when a multipart/form-data request with a file payload is submitted, then the API returns 415, persists no file content, and the body carries a structured error code and a human-readable reason.
- Traces to. US-005-02 (AC2); US-005-04 (AC2); `CON-BE-205`.
- Verification. `scope_guard_multipart_to_notes_returns_415_persists_nothing` (planned).
- API. none.

**BE-SCOPE-GUARD-FR-003**
- Given the running API server, when a client attempts a WebSocket upgrade or an SSE connection to any notes_api endpoint, then the connection is rejected, no protocol upgrade occurs, and a standard error status is returned.
- Traces to. US-005-03 (AC1); BR-015.
- Verification. `scope_guard_websocket_upgrade_rejected_no_upgrade`; `scope_guard_sse_connection_rejected` (planned).
- API. none.
- Open: the status and the detection signature for SSE are unstated (`GAP-BE-011`, `GAP-BE-218`).

**BE-SCOPE-GUARD-FR-004**
- Given two clients submit PATCH requests to the same note concurrently, when both are processed, then the API applies a standard last-write or conflict response, with no live broadcast and no merge logic.
- Traces to. US-005-03 (AC2); BR-015.
- Verification. `scope_guard_concurrent_patch_no_broadcast_no_merge` (planned).
- API. none. The PATCH operation and its conflict behaviour belong to the `notes` module.

**BE-SCOPE-GUARD-FR-005**
- Given an integrator calls an out-of-scope endpoint (mobile-specific, attachment or real-time), when the request is made, then the response is a documented 4xx whose body contains a structured error code and a message referencing the scope exclusion.
- Traces to. US-005-04 (AC1, AC2); BR-016; NFR-011.
- Verification. `scope_guard_all_excluded_categories_return_documented_4xx` (planned).
- API. none.

**BE-SCOPE-GUARD-FR-006**
- Given the CI pipeline runs, when the scope-guardrail test suite executes against the OpenAPI document, then it passes only when zero endpoints match an excluded category (mobile, attachments, real-time).
- Traces to. US-005-05 (AC1); BR-014; BR-015; NFR-010.
- Verification. `scope_guard_contract_zero_excluded_endpoints_in_openapi` (planned).
- API. none.

**BE-SCOPE-GUARD-FR-007**
- Given a developer adds an endpoint matching an excluded pattern (for example `/attachments` or `/ws`), when CI runs on the pull request, then the guardrail test fails and blocks the merge.
- Traces to. US-005-05 (AC2); NFR-010.
- Verification. `scope_guard_contract_fails_on_added_excluded_endpoint` (planned).
- API. none.

**BE-SCOPE-GUARD-FR-008**
- Given any refusal produced by this module, when the response body is built, then it contains no login code, no unmasked email address and no echo of the request payload.
- Traces to. NFR-018; `ASM-BE-215`.
- Verification. `scope_guard_refusal_body_has_no_payload_echo` (planned).
- API. none.
<!-- /SECTION: backend.scope-guard.requirements -->
<!-- SECTION: backend.scope-guard.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

No HTTP operation is defined.
<!-- /SECTION: backend.scope-guard.api-contract -->
<!-- SECTION: backend.scope-guard.invariants | status: DRAFT-UNVERIFIED -->
## Invariants

- **INV-BE-201.** After every build, the route registry and the published OpenAPI document contain no endpoint in an excluded category (attachments, real-time, mobile-specific). Source: BR-014 to BR-016; NFR-010.
- **INV-BE-202.** After any refused request, no file content has been persisted. Source: US-005-02.
- **INV-BE-203.** No request on any endpoint ever results in a protocol upgrade to WebSocket or SSE. Source: US-005-03; BR-015.
- **INV-BE-204.** Every refusal returned by this module carries a structured error code and a message that references the scope exclusion. Source: US-005-04; BR-016; NFR-011.
- **INV-BE-205.** No refusal body contains a login code, an unmasked email address or an echo of the request payload. Source: NFR-018; `ASM-BE-215`.
<!-- /SECTION: backend.scope-guard.invariants -->
<!-- SECTION: backend.scope-guard.authorization | status: DRAFT-UNVERIFIED -->
## Authorization

This module owns no operation, so there is no role × operation matrix.

- **Rule.** Refusals apply to every caller. No role may reach an excluded endpoint, because none exists (BR-014 to BR-016, all with "Exception: None").
- **Ordering.** Refusal is evaluated independently of authentication, so an unauthenticated call to an excluded path gets the same refusal as an authenticated one (`ASM-BE-214`). Whether other endpoints require authentication depends on `Q-BE-003`.
- **Ownership predicate.** None. This module touches no user-scoped resource.
<!-- /SECTION: backend.scope-guard.authorization -->
<!-- SECTION: backend.scope-guard.jobs | status: DERIVED -->
## Jobs

- **Scheduled work.** None at runtime. No input requires a sweep or retry in this module.
- **CI check.** The contract test suite runs on every CI build (NFR-010; BE-SCOPE-GUARD-FR-006, FR-007). It is a build step, not a scheduled job.
- **Idempotency.** Refusals have no side effects, so repeating a refused request returns the same refusal. No idempotency key is needed.
- **Retries.** None.
<!-- /SECTION: backend.scope-guard.jobs -->
<!-- SECTION: backend.scope-guard.test-plan | status: UNKNOWN -->
## Test plan

All tests below are planned. None exists yet.

**Unit**
- `scope_guard_matcher_classifies_attachment_paths`: `/notes/{id}/attachments` and `/attachments` match the attachment category.
- `scope_guard_matcher_classifies_realtime_paths`: `/ws` matches the real-time category.
- `scope_guard_matcher_passes_in_scope_paths`: `/notes`, `/notes/{id}`, `/notes/search`, `/auth/request-code` and `/auth/verify-code` are not matched.
- `scope_guard_refusal_body_shape`: the body has a code and a message, and no payload echo (INV-BE-204, INV-BE-205).

**Integration**
- `scope_guard_attachment_path_returns_404_out_of_scope` (FR-001)
- `scope_guard_multipart_to_notes_returns_415_persists_nothing` (FR-002)
- `scope_guard_websocket_upgrade_rejected_no_upgrade` and `scope_guard_sse_connection_rejected` (FR-003)
- `scope_guard_concurrent_patch_no_broadcast_no_merge` (FR-004)
- `scope_guard_all_excluded_categories_return_documented_4xx` (FR-005)

**Contract**
- `scope_guard_contract_zero_excluded_endpoints_in_openapi` (FR-006)
- `scope_guard_contract_fails_on_added_excluded_endpoint` (FR-007)
- `scope_guard_contract_zero_excluded_routes_in_registry`: runs the same check against the route registry (`ASM-BE-216`).
<!-- /SECTION: backend.scope-guard.test-plan -->
<!-- SECTION: backend.scope-guard.gaps | status: VERIFIED -->
## Gaps

**Assumptions**
- `ASM-BE-214`: Refusal of excluded paths is evaluated before and independent of authentication. Confirmed by: the outcome of `Q-BE-003`.
- `ASM-BE-215`: A refusal body uses the proposed envelope (`ASM-BE-007`: `code` and `message`) and never echoes the request payload. Confirmed by: an error-model decision (`GAP-BE-011`).
- `ASM-BE-216`: The contract suite checks both the OpenAPI document (US-005-05) and the route registry (BR-014, BR-015). Confirmed by: a statement of the suite's input.

**Contradictions**
- `CON-BE-205` (non-critical): The project error-model proposes `UNSUPPORTED_MEDIA_TYPE` for 415 (`ASM-BE-007`). US-005-04 AC2 gives `OUT_OF_SCOPE` as the example code for a multipart upload, and US-005-02 AC2 fixes the status at 415. Reading adopted: status 415 with code `OUT_OF_SCOPE`, because it satisfies both stories' text. If `OUT_OF_SCOPE` is only an example, `UNSUPPORTED_MEDIA_TYPE` remains valid. A decision on the 415 code name would close it. Related: `CON-BE-002`.

**Gaps**
- `GAP-BE-217` (non-critical): No input defines the match patterns for the excluded categories. Only examples exist: `/notes/{id}/attachments`, `/attachments` and `/ws`. There is no definition of "mobile-specific" or of a "polling-push" channel (BR-015). This degrades FR-003, FR-005, FR-006 and FR-007, which rely on a concrete exclusion list. Closing artefact: the documented exclusion list, which the Scope section (US-005-01) may supply.
- `GAP-BE-218` (non-critical): No input says how an SSE attempt is recognised, since it is an ordinary GET. This degrades FR-003. Closing artefact: a detection rule, such as the request signature that counts as an SSE attempt.

**Referenced, not re-minted**
- `GAP-BE-011`: the rejection status for WebSocket and SSE attempts, and the final error-code names.
- `GAP-BE-009`: no module owns BR-017 and US-005-06.
- `CON-BE-002`: the out-of-scope status (404 versus "not a bare 404").
- `Q-BE-003`: the session decision.
<!-- /SECTION: backend.scope-guard.gaps -->
