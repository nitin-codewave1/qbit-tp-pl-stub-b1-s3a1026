---
id: agent-developer/notes_api/backend/modules/scope-guard-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/scope-guard-SPEC.md
depends_on: [notes_api:US-005-02, notes_api:US-005-03, notes_api:US-005-04, notes_api:US-005-05]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
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

`scope` is empty because the operation list supplied for this module contained no operation records, so DevAgent minted no `API-` ids for it (`GAP-BE-229`). The module map defines `scope-guard` as request middleware applied to the whole HTTP surface (`backend.module-map`). It may therefore own no endpoint of its own. No `API-` id is written in this document.
<!-- /SECTION: backend.scope-guard.frontmatter -->
<!-- SECTION: backend.scope-guard.purpose | status: DERIVED -->
## Purpose

**Responsibility.** `scope-guard` enforces the documented scope boundary of `notes_api`. It does two things (EP-005; BR-014, BR-015, BR-016; NFR-010, NFR-011):

1. **Runtime refusal.** It refuses requests that belong to the excluded categories, which are attachment or file upload, real-time channels, and mobile-specific endpoints. It answers each with a structured `OUT_OF_SCOPE` error, not a silent failure or a bare 404 (US-005-02, US-005-03, US-005-04, BR-016).
2. **Build-time proof.** It supplies the automated contract tests that prove no excluded endpoint exists in the OpenAPI spec or the route registry. These tests run on every CI build and block a merge on a match (US-005-05, BR-014, BR-015, NFR-010).

**Not responsible for:**

- The Scope section of the published API documentation, its review gate and its docs-build lint (US-005-01). That belongs to `contract-docs`.
- Logging and classifying stakeholder requests for mobile, attachments or real-time collaboration (US-005-06, BR-017). This is a product-manager process (`GAP-BE-019`).
- Defining the error envelope or the registry of codes. `backend.error-model` owns them; this module only uses `OUT_OF_SCOPE`.
- Note create, edit and PATCH semantics, including concurrent-edit handling (US-005-03, second criterion). The `notes` module owns that behaviour (`ASM-BE-005`); this module only requires that no live broadcast or merge logic exists.
- Residency checks, authentication, login rate limiting and search behaviour.
<!-- /SECTION: backend.scope-guard.purpose -->
<!-- SECTION: backend.scope-guard.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**There are no tables, columns or indexes.** No input requires `scope-guard` to persist anything, and the module map describes it as middleware with no module dependencies (`ASM-BE-213`). It adds no migration to `backend.migration-plan`.

**Configuration (not persisted):** an exclusion registry held in code, with these logical attributes:

| Attribute | Type | Meaning | Source |
|---|---|---|---|
| `category` | enum: `attachment`, `real-time`, `mobile` | The excluded category | BR-014, BR-015, BR-016 |
| `matcher` | path pattern, header predicate or media-type predicate | How a request or route is recognised | US-005-02, US-005-03, US-005-05 |
| `reason` | string | The human-readable reason returned in the refusal | US-005-04 |

**Seed entries.** Only the examples named in the inputs are seeded (`ASM-BE-214`):

- `attachment`: a path ending in an `attachments` segment, for example `POST /notes/{id}/attachments` (US-005-02).
- `attachment`: a `multipart/form-data` request body to a notes create or update endpoint (US-005-02, `ASM-BE-217`).
- `real-time`: a WebSocket upgrade request, and an SSE connection request (US-005-03).
- `real-time`: the path `/ws` (US-005-05).

The `mobile` category has no supplied matcher, and neither does polling-push (`GAP-BE-228`, `GAP-BE-233`).

Nothing from a refused request is stored. This module's logs must follow `backend.observability` and `ASM-BE-012`.
<!-- /SECTION: backend.scope-guard.data-model -->
<!-- SECTION: backend.scope-guard.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

`scope-guard` owns no entity with a status column. It has a per-request classification flow, derived from US-005-02 to US-005-04. The flow is stateless between requests.

**States**

| State | Meaning | Terminal |
|---|---|---|
| `RECEIVED` | The request has arrived and nothing has been read from its body | no |
| `CLASSIFIED_IN_SCOPE` | No exclusion matcher matched | no |
| `CLASSIFIED_OUT_OF_SCOPE` | An exclusion matcher matched | no |
| `PASSED` | Handed to the next middleware or route handler | yes |
| `REFUSED` | A structured `OUT_OF_SCOPE` response was sent, or the connection was rejected | yes |

**Transitions**

| From | To | Trigger | Guard |
|---|---|---|---|
| `RECEIVED` | `CLASSIFIED_OUT_OF_SCOPE` | A matcher matches the path, the headers or the media type | Evaluated before body parsing (`ASM-BE-218`) |
| `RECEIVED` | `CLASSIFIED_IN_SCOPE` | No matcher matches | none |
| `CLASSIFIED_OUT_OF_SCOPE` | `REFUSED` | Always | No data is persisted. The refusal carries `OUT_OF_SCOPE` (`INV-BE-205`, `INV-BE-206`). |
| `CLASSIFIED_IN_SCOPE` | `PASSED` | Always | none |

**Illegal transitions.** `REFUSED` and `PASSED` are terminal, and a request never moves between them. This flow raises no illegal-transition error.
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

- `INV-BE-204`: After every build, zero routes in the route registry and zero paths in the published OpenAPI spec match an excluded category (BR-014, BR-015, NFR-010).
- `INV-BE-205`: A request refused by this module persists nothing: no file content, no note change, no new record (US-005-02, AC for non-existent data modification in EP-005).
- `INV-BE-206`: Every refusal this module sends over HTTP carries `code` `OUT_OF_SCOPE` and a human-readable reason, never an empty body (US-005-04, BR-016, NFR-011).
- `INV-BE-207`: No WebSocket protocol upgrade and no SSE stream is ever established by `notes_api` (US-005-03, BR-015).
<!-- /SECTION: backend.scope-guard.invariants -->
<!-- SECTION: backend.scope-guard.authorization | status: DRAFT-UNVERIFIED -->
## Authorization

**No role is required.** The guard classifies the request before any identity is known, so the same refusal applies to every caller, authenticated or not (`ASM-BE-219`). No role × operation matrix applies, because the module owns no operation (`GAP-BE-229`).

**Ownership predicate.** None. No resource is read or written.

**Interaction with authentication.** Whether the guard runs before or after authentication, and whether an unauthenticated caller of an excluded path receives `OUT_OF_SCOPE` or 401, depends on `Q-BE-003` and `backend.auth-model`, which is `BLOCKED` (`GAP-BE-231`). The default is that the guard runs first, so scope refusals do not disclose whether a path would otherwise need authentication.

**Disclosure.** A refusal body states the scope exclusion and nothing about other routes or internal state.
<!-- /SECTION: backend.scope-guard.authorization -->
<!-- SECTION: backend.scope-guard.jobs | status: DERIVED -->
## Jobs

**Scheduled work and sweeps:** none. The module holds no data, so there is nothing to sweep or retain (NFR-019 does not apply).

**Recurring check:** the contract suite (`BE-SCOPE-GUARD-FR-006`, `BE-SCOPE-GUARD-FR-007`) runs on every CI build, not on a schedule (NFR-010). The CI system is unnamed (`GAP-BE-011`).

**Retries:** none. A refusal is final for the request, and a client retrying the same excluded request receives the same refusal.

**Idempotency.** Classification and refusal are pure functions of the request and have no side effects (`INV-BE-205`), so no idempotency key is needed.
<!-- /SECTION: backend.scope-guard.jobs -->
<!-- SECTION: backend.scope-guard.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

**Unit**

- The matcher classifies the seeded patterns correctly: an `attachments` path segment, `multipart/form-data` on a notes write route, a WebSocket `Upgrade` header, an SSE-style request, and `/ws` (`ASM-BE-214`, `ASM-BE-215`, `ASM-BE-217`).
- Ordinary notes and search requests are classified in scope, including a path containing the text "attach" as a substring but not as a segment.
- The refusal builder always produces the `OUT_OF_SCOPE` code and a non-empty reason (`INV-BE-206`).

**Integration**

- `POST /notes/{id}/attachments` returns 404 with an `OUT_OF_SCOPE` body (`FR-001`).
- A multipart upload to a notes write endpoint returns 415, and a database check afterwards shows no change (`FR-002`, `INV-BE-205`).
- A WebSocket upgrade attempt and an SSE request are rejected with no upgrade (`FR-003`, `INV-BE-207`).
- Two concurrent PATCH requests to one note produce no broadcast (`FR-004`).
- The order relative to authentication is tested only after `Q-BE-003` is decided (`GAP-BE-231`).

**Contract**

- The suite reads the published OpenAPI document and the route registry, and fails if any path or media type matches an excluded category (`FR-006`).
- A planted route such as `/attachments` or `/ws` in a fixture makes the suite fail, which proves the gate works (`FR-007`).
- A malformed or unreadable spec makes the suite fail with a clear error and never passes by default. This follows the failure behaviour US-004-04 requires of the coverage check and is `ASM-BE-220`.
- Mobile and polling-push coverage is untestable until matchers exist (`GAP-BE-228`, `GAP-BE-233`).
<!-- /SECTION: backend.scope-guard.test-plan -->
<!-- SECTION: backend.scope-guard.gaps | status: DERIVED -->
## Gaps, contradictions and assumptions

**Contradictions**

- `CON-BE-002` (project level): US-005-02 says attachment paths return 404, while US-005-04 and BR-016 require a body referencing the scope exclusion. This module applies the project reading: status 404 with an `OUT_OF_SCOPE` body (`FR-001`). It mints no new contradiction.

**Degradable questions (defaults taken, unattended)**

- `Q-BE-208`: What status should a WebSocket or SSE rejection return? Default: 404 with an `OUT_OF_SCOPE` body where an HTTP response is possible (`ASM-BE-216`).
- `Q-BE-209`: What identifies a mobile-specific endpoint? Default: no matcher is enforced; the contract suite covers only the other two categories (`GAP-BE-228`).

**Assumptions**

- `ASM-BE-213`: `scope-guard` persists nothing. The module map describes request middleware with no module dependencies, and no input requires storage. Confirm: none needed unless a refusal log is required.
- `ASM-BE-214`: The seeded exclusion patterns are exactly the examples in the inputs (`/attachments`, `/ws`, the attachment path under a note). Confirm with an authoritative exclusion list.
- `ASM-BE-215`: An SSE connection request is recognised by an `Accept` header containing `text/event-stream`. No input defines the check.
- `ASM-BE-216`: A WebSocket or SSE rejection uses status 404, consistent with the attachment-path refusal. Confirm with `Q-BE-208` or an acceptance criterion (`GAP-BE-015`).
- `ASM-BE-217`: "Notes create or update endpoint" means every notes write operation, and a file payload is recognised by the `multipart/form-data` media type. Confirm with `GAP-BE-230`.
- `ASM-BE-218`: Classification runs before body parsing, so file bytes are never buffered or stored.
- `ASM-BE-219`: The guard applies to every caller regardless of identity.
- `ASM-BE-220`: The contract suite fails, rather than passes, when it cannot read the spec.

**Gaps**

- `GAP-BE-228`: No definition of a "mobile-specific endpoint" exists (BR-016, US-005-05). Closing artefact: a list of mobile path patterns or a rule.
- `GAP-BE-229`: No operation records were supplied for this module, so no `API-` id was minted and `scope` is empty. Closing artefact: DevAgent operation records, or confirmation that the module owns no endpoint.
- `GAP-BE-230`: Which routes count as "notes endpoints" for the file-payload check is unstated, including whether login and search routes are covered. Closing artefact: an acceptance criterion naming the routes.
- `GAP-BE-231`: The order of the guard relative to authentication and rate limiting is unstated, and depends on `Q-BE-003`. Closing artefact: the answer to `Q-BE-003`.
- `GAP-BE-232`: The exact reason text and any link to the Scope section in the `OUT_OF_SCOPE` body are not supplied (US-005-04). Closing artefact: an error-registry entry for `OUT_OF_SCOPE`.
- `GAP-BE-233`: BR-015 excludes polling-push, but no input says how it is recognised in a route or spec. Closing artefact: a matcher or example.
- Also open at project level and relevant here: `GAP-BE-001` (the four inbound handoff files were not supplied and are not answered here), `GAP-BE-011` (CI system unnamed), `GAP-BE-015` (rejection status), and `GAP-BE-019` (US-005-06 is outside the backend).
<!-- /SECTION: backend.scope-guard.gaps -->
