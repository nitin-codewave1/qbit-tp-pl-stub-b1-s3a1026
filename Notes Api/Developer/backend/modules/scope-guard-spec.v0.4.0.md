---
id: agent-developer/notes_api/backend/modules/scope-guard-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.4.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/scope-guard-SPEC.md
depends_on: [notes_api:US-005-02, notes_api:US-005-03, notes_api:US-005-04, notes_api:US-005-05]
commit_kind: fork
confidence: agent
run_id: df4d63c0-0252-56b1-8292-59d2f9d8aa48
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

`scope` is empty because this run supplied no operation records for `scope-guard`. The module owns no `API-` id. It is request middleware plus a CI contract test, as `backend.module-map` states.
<!-- /SECTION: backend.scope-guard.frontmatter -->
<!-- SECTION: backend.scope-guard.purpose | status: DERIVED -->
## Purpose

**Responsible for** (stories.md US-005-02 to US-005-05; requirements.md BR-014, BR-015, BR-016, NFR-010, NFR-011):
- Ensuring no attachment or file-upload endpoint exists, and refusing file payloads sent to note endpoints.
- Ensuring no real-time channel (WebSocket, SSE, polling-push) exists, and refusing upgrade or stream attempts.
- Returning a standardised, documented, structured error to calls aimed at excluded capabilities (attachment, real-time, mobile-specific), instead of a bare 404 or a silent failure.
- Providing the automated contract test suite that verifies the absence of excluded endpoints in the API surface, run on every CI build.

**Not responsible for:**
- The published Scope section of the documentation and the docs-build lint (US-005-01). `contract-docs` owns them (`backend.module-map`).
- Last-write or conflict handling of concurrent PATCH requests. `notes` owns it (`ASM-BE-024`, defined in `modules/notes-SPEC.md`). This module owns only the absence of live broadcast or merge logic.
- Logging and classifying stakeholder feature requests (US-005-06, BR-017). That is a product-manager process (`GAP-BE-009`).
- Generic 404 handling for paths that are not in any excluded category.
- Authentication and authorisation (`backend.auth-model`, blocked by `Q-BE-003`).
- Infrastructure and residency checks (`residency`).
<!-- /SECTION: backend.scope-guard.purpose -->
<!-- SECTION: backend.scope-guard.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**Tables.** None. The module stores nothing in PostgreSQL. No input requires persistence of refused requests; BR-017 places request logging with the product manager (`GAP-BE-009`). A refused upload persists no file content (US-005-02, AC2).

**Configuration artefact (not a table).** The middleware and the contract test share one exclusion list (`ASM-BE-204`). Each entry has:

| Field | Type | Nullable | Meaning | Source |
|---|---|---|---|---|
| `category` | enum: `attachment`, `real-time`, `mobile` | no | Excluded capability | BR-014, BR-015, BR-016 |
| `method` | HTTP method or any | no | Method matched | assumption |
| `path_pattern` | string pattern | no | Path matched | US-005-05 examples `/attachments`, `/ws` |
| `reason` | string | no | Human-readable text for the refusal body | US-005-04 |

Seed entries are limited to what the stories name: an attachment path such as `POST /notes/{id}/attachments` (US-005-02) and the `/attachments` and `/ws` patterns (US-005-05). No `mobile` pattern is supplied by any input (`GAP-BE-202`).

**Indexes and constraints.** None apply, since there is no table.
<!-- /SECTION: backend.scope-guard.data-model -->
<!-- SECTION: backend.scope-guard.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

The module has no entity with a status column, so there is no persistent state machine. The only lifecycle is per request:

| From | Trigger | Guard | To |
|---|---|---|---|
| `received` | request arrives | matches an exclusion entry, or carries a multipart/form-data file payload to a note endpoint, or is a WebSocket upgrade or SSE connection attempt | `refused` |
| `received` | request arrives | no match | `passed-through` (handled by the owning module) |

- Terminal states: `refused` and `passed-through`. A refused request never reaches a module handler, and no protocol upgrade completes (US-005-03, AC1).
- Illegal transition: not applicable. Nothing is mutated.
- Statuses per refusal category are in `backend.scope-guard.requirements`. Statuses for real-time and mobile refusals are open (`GAP-BE-203`).
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
<!-- SECTION: backend.scope-guard.invariants | status: DERIVED -->
## Invariants

- **INV-BE-201.** No route is registered under any attachment or real-time pattern in the exclusion list, in any build that passes CI. Derived from BR-014, BR-015 and FR-006.
- **INV-BE-202.** A refused file upload leaves no file content stored. Derived from US-005-02 AC2.
- **INV-BE-203.** Every refusal produced by this module carries a structured error code and a human-readable reason that references the scope exclusion. Derived from US-005-04 and NFR-011.
- **INV-BE-204.** No WebSocket upgrade or SSE stream is ever established by notes_api. Derived from US-005-03 AC1 and BR-015.
- **INV-BE-205.** The published OpenAPI document lists zero endpoints matching the exclusion list. Derived from NFR-010 and US-005-05.
<!-- /SECTION: backend.scope-guard.invariants -->
<!-- SECTION: backend.scope-guard.authorization | status: DRAFT-UNVERIFIED -->
## Authorization

No role is defined by any input, and `backend.auth-model` is blocked (`Q-BE-001`, `Q-BE-002`, `Q-BE-003`).

- This module exposes no operation, so there is no role-by-operation matrix to write.
- **Ownership predicate.** None. The module touches no user-scoped resource.
- `ASM-BE-203` assumes refusals apply regardless of caller identity, so an excluded call is refused whether or not the caller is authenticated. The order of this check relative to authentication is open (`GAP-BE-204`).
<!-- /SECTION: backend.scope-guard.authorization -->
<!-- SECTION: backend.scope-guard.jobs | status: DERIVED -->
## Jobs

- **Scheduled sweeps.** None.
- **Retries.** None. A refusal is a final answer to the request.
- **Idempotency.** Refusals are stateless and repeatable. The same request always produces the same refusal, so no idempotency key applies.
- **CI step.** The contract suite runs on every CI build (NFR-010; US-005-05). The CI platform is unnamed (`GAP-BE-206`).
<!-- /SECTION: backend.scope-guard.jobs -->
<!-- SECTION: backend.scope-guard.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

**Unit**
- Exclusion matcher: attachment and real-time patterns match, and ordinary note paths do not.
- Multipart detection on `Content-Type` for note create and update.
- Refusal body builder: `OUT_OF_SCOPE` code and reason are always present (INV-BE-203).

**Integration**
- `scope_guard.attachment_path_returns_404_out_of_scope` (FR-001).
- `scope_guard.multipart_upload_returns_415_and_persists_nothing`, checking the database for no stored file content (FR-002).
- `scope_guard.websocket_upgrade_and_sse_rejected` (FR-003).
- `scope_guard.concurrent_patch_has_no_broadcast_or_merge` (FR-004).
- `scope_guard.out_of_scope_response_has_code_and_reason` (FR-005).

**Contract**
- `scope_guard.contract_suite_zero_excluded_endpoints` against the OpenAPI document (FR-006).
- `scope_guard.contract_suite_fails_on_new_excluded_route`, using a deliberately added `/attachments` or `/ws` route to prove the suite fails (FR-007).
- The suite checks the published OpenAPI document and the live route registry, so a route absent from the spec is still caught (cf. BR-012).

The test framework and runner are unnamed by any input (`GAP-BE-206`).
<!-- /SECTION: backend.scope-guard.test-plan -->
<!-- SECTION: backend.scope-guard.gaps | status: VERIFIED -->
## Gaps

**Questions** (both `degradable`; the run is unattended, so the stated default is taken)
- `Q-BE-201` (degradable): which statuses do real-time and mobile refusals return? Default: `ASM-BE-202`.
- `Q-BE-202` (degradable): what path patterns define "mobile-specific"? Default: none defined; the exclusion list holds only the patterns the stories name.

**Gaps**
- `GAP-BE-202`: no input defines mobile-specific endpoint patterns, so the mobile category cannot be tested beyond the configured list. Degrades FR-005 and FR-006. Closing artefact: a list of mobile path or header patterns.
- `GAP-BE-203`: statuses for WebSocket/SSE and mobile refusals are not stated (US-005-03 says "standard error status"). Degrades FR-003 and FR-005. Closing artefact: an answer to `Q-BE-201`. The final error envelope and code names also remain open under `GAP-BE-011`.
- `GAP-BE-204`: whether the refusal runs before or after authentication is unknown, because the session model is open (`Q-BE-003`). Closing artefact: a session-model decision.
- `GAP-BE-205`: no input says whether refused requests are logged or metered. BR-017 places request classification with the product manager (`GAP-BE-009`). Closing artefact: an observability decision for refusals.
- `GAP-BE-206`: the CI platform, the test framework and the mechanism that blocks a merge are unnamed. `platform.md` names only Node.js 20, Express, PostgreSQL 16 and AWS. Degrades FR-007 and the test plan. Closing artefact: a CI platform input.

**Assumptions**
- `ASM-BE-201`: attachment-path refusals keep status 404 as US-005-02 states, with an `OUT_OF_SCOPE` body so the response is not a bare 404 (US-005-04, BR-016). To be confirmed by the product owner. No `CON-` is raised: the status and the body requirements are compatible.
- `ASM-BE-202`: WebSocket/SSE and mobile refusals use the same envelope as FR-005 with status 404, until `Q-BE-201` is answered.
- `ASM-BE-203`: refusals apply regardless of caller identity.
- `ASM-BE-204`: one exclusion list, held as configuration, is shared by the middleware and the contract test, so they cannot drift.

**Contradictions.** None raised. Checked: story to requirement (US-005-02 404 against BR-016 "404 ambiguity"), requirement to requirement (BR-014 to BR-016, NFR-010, NFR-011), story to design (the design brief carries no scope-guard content), and design to design (not applicable).
<!-- /SECTION: backend.scope-guard.gaps -->
