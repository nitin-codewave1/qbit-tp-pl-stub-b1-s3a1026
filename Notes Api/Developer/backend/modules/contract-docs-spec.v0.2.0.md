---
id: agent-developer/notes_api/backend/modules/contract-docs-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/contract-docs-SPEC.md
depends_on: [notes_api:US-004-01, notes_api:US-004-02, notes_api:US-004-03, notes_api:US-004-04, notes_api:US-004-05, notes_api:US-004-06, notes_api:US-005-01]
commit_kind: fork
confidence: agent
run_id: e1e628bb-b234-5a13-b948-b4689dcc8eab
okf_id: notes_api:backend/modules/contract-docs-SPEC.md
---

<!-- SECTION: backend.contract-docs.frontmatter | status: VERIFIED -->
## Frontmatter

```yaml
id: contract-docs
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-003]
scope: [API-CONTRACTDOCS-304307, API-CONTRACTDOCS-827943]
```

`blocked_by` names `Q-BE-003`. Until it is answered, the 401/403 documentation required by US-004-03 is not final (see `BE-CONTRACT-DOCS-FR-008`).
<!-- /SECTION: backend.contract-docs.frontmatter -->
<!-- SECTION: backend.contract-docs.purpose | status: DERIVED -->
## Purpose

**Responsible for.** The boundary comes from the project spec's module map (`backend.module-map`, row `contract-docs`). Every item below traces to a story or rule.

- Publishing the OpenAPI 3.x document that covers every implemented endpoint. This includes the CRUD, tagging, search, archive and email one-time-code login (request and verify) endpoints (US-004-01, BR-012, NFR-008).
- Documenting schemas, required and optional fields, defaults, and 4xx/5xx error responses in that document (US-004-02, US-004-03).
- Carrying example payloads for search and tagging, including an empty-result example (US-004-05, NFR-009).
- Carrying a data-residency handling note that covers where active and archived note data lives (US-004-06, BR-013).
- Carrying a Scope section that lists mobile apps, attachments and real-time collaboration as excluded (US-005-01).
- Marking interface changes: the `pinned` field and pinned-first ordering (NFR-014), and the login endpoints (NFR-015, NFR-016).
- Computing and exposing the spec-versus-route coverage metric (US-004-04, BR-012, NFR-008).
- Failing the docs build or CI when the document drifts from the code, or when the Scope section is missing (US-004-02, US-005-01).

**Not responsible for.**

- Defining any endpoint's request, response or error contract. Each module owns its own operations, and this module renders them without restating them.
- Refusing out-of-scope calls at runtime, and the contract tests for their absence. Both belong to `scope-guard` (BR-014 to BR-016, NFR-010, NFR-011).
- Provisioning, backup and drift enforcement of EU residency. These belong to `residency` (BR-001 to BR-004).
- Login behaviour (codes, lock, resend limit). It belongs to `auth`; this module only documents it.
- Publishing a contract package, versioning it, or approving it. The orchestrator does these (core section 2).
- Logging and classifying stakeholder requests (US-005-06, BR-017). This is a product-manager process (`GAP-BE-009`).
- The web_app screens that consume the document (US-006-01 to US-006-06).
<!-- /SECTION: backend.contract-docs.purpose -->
<!-- SECTION: backend.contract-docs.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**No database tables, columns, constraints or indexes are defined for this module.** No input requires the published document or the coverage result to be stored in PostgreSQL, and the project spec's migration plan has no migration for this module (`backend.migration-plan`). This rests on `ASM-BE-167`.

The module handles two logical artefacts. Neither is persisted by the application database.

| Artefact | Content | Source of content | Stored where |
|---|---|---|---|
| Published OpenAPI document | The whole document, in the formats under `ASM-BE-170` | The route registry and the modules' operation records (BR-012; `ASM-BE-171`) | A build and publish artefact served as-is by `API-CONTRACTDOCS-304307` |
| Coverage result | Count of documented implemented routes, count of implemented routes, the percentage, the identity of each missing route, and the evaluation outcome | The route registry compared with the published document (BR-012, US-004-04) | Computed per evaluation, not stored (`ASM-BE-167`) |

- A route is identified by method plus path (`ASM-BE-174`).
- Example payloads in the document use masked placeholders for any email address and never contain a real-looking value or a credential (core section 12).
- The retention period of the published document and of coverage results is not stated by any input (`GAP-BE-006`). No retention claim is made.
- The artefacts sit in the EU like every other notes_api resource (BR-001). No input places the published document anywhere else.
<!-- /SECTION: backend.contract-docs.data-model -->
<!-- SECTION: backend.contract-docs.state-machine | status: DERIVED -->
## State machine

No entity in this module has a status column, so there is no persisted state machine. The coverage check does have a defined set of outcomes (US-004-04). They are listed here so every consumer reads them the same way.

| State | Meaning | Reached when | Terminal |
|---|---|---|---|
| `EVALUATING` | transient: registry and document are being compared | `API-CONTRACTDOCS-827943` is called, or the CI check starts | no |
| `COMPLETE` | every implemented route is in the document; reported as 100% | the document parses and the missing set is empty | yes |
| `INCOMPLETE` | below 100%; each missing route is named | the document parses and the missing set is non-empty | yes |
| `UNPARSEABLE` | the document is malformed; no percentage is reported | the document fails to parse | yes |

**Transitions.**

- `EVALUATING → UNPARSEABLE`. Guard: the document fails to parse. This check runs first.
- `EVALUATING → INCOMPLETE`. Guard: the document parses and at least one implemented route is absent from it.
- `EVALUATING → COMPLETE`. Guard: the document parses and no implemented route is absent.

Each evaluation starts fresh and carries no memory of a previous one, and every outcome is terminal for that evaluation. There is therefore no illegal transition and no illegal-transition error.

**Docs-build gate.** The build either passes or fails. It fails when:

- the Scope section is missing (US-005-01);
- a documented field type no longer matches the code (US-004-02).

Whether `INCOMPLETE` also fails the build is not stated by any input (`GAP-BE-174`).
<!-- /SECTION: backend.contract-docs.state-machine -->
<!-- SECTION: backend.contract-docs.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

**BE-CONTRACT-DOCS-FR-001** Serve the published document.
- **Given** notes_api is deployed, **when** a client requests the OpenAPI document, **then** a valid OpenAPI 3.x document is returned in the requested format with status 200.
- **Traces to.** US-004-01, BR-012.
- **Verification.** `contractdocs_openapi_returns_valid_3x_document` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-002** The document lists every implemented endpoint.
- **Given** the implemented route registry, **when** the document is returned, **then** it lists all CRUD, tagging, search, archive and email one-time-code login (request-code and verify-code) endpoints. It also lists both operations of this module.
- **Traces to.** US-004-01 (first criterion), BR-012, NFR-008.
- **Verification.** `contractdocs_every_registry_route_is_documented` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-003** Unsupported Accept header.
- **Given** a request whose Accept header names a format this module cannot produce, **when** the document is requested, **then** the response is 406 with the registry code `NOT_ACCEPTABLE` (`backend.error-model`), and no document body is returned.
- **Traces to.** US-004-01 (third criterion).
- **Verification.** `contractdocs_unsupported_accept_returns_406` (planned).
- **API.** `API-CONTRACTDOCS-304307`.
- **Note.** The story allows "406 or a default-format fallback". The 406 choice is `ASM-BE-169`. Supported formats and the default are `ASM-BE-170`.

**BE-CONTRACT-DOCS-FR-004** New endpoints appear no later than they become callable.
- **Given** a new endpoint is added to notes_api, **when** the document is regenerated and published, **then** the endpoint appears in the document before or at the same time it becomes callable.
- **Traces to.** US-004-01 (second criterion), BR-012.
- **Verification.** `contractdocs_ci_new_route_must_appear_in_spec` (planned).
- **API.** none (build and CI behaviour, `ASM-BE-171`).

**BE-CONTRACT-DOCS-FR-005** Required and optional fields, types and defaults.
- **Given** an endpoint accepts a note payload or optional query parameters, **when** its schema is inspected in the document, **then** required fields, types and constraints are documented. Optional parameters are marked optional with defaults documented.
- **Traces to.** US-004-02 (first and second criteria), BR-012.
- **Verification.** `contractdocs_schemas_state_required_optional_and_defaults` (planned).
- **API.** `API-CONTRACTDOCS-304307`.
- **Note.** Constraints are taken from the owning module and are not restated here. Constraints no module defines, such as title length and tag format, cannot be documented (`GAP-BE-171`).

**BE-CONTRACT-DOCS-FR-006** Schema drift fails CI.
- **Given** a documented field type is changed in code and the document is not updated, **when** the spec-validation check runs, **then** the check fails and names the mismatch.
- **Traces to.** US-004-02 (third criterion).
- **Verification.** `contractdocs_ci_fails_on_field_type_drift` (planned).
- **API.** none.

**BE-CONTRACT-DOCS-FR-007** Validation errors documented.
- **Given** an endpoint can return validation errors, **when** the document is reviewed, **then** a 400 response with the error structure is documented. The structure is the one defined in `backend.error-model` and is not redefined here.
- **Traces to.** US-004-03 (first criterion), BR-012.
- **Verification.** `contractdocs_every_validating_endpoint_documents_400` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-008** Authentication and login refusals documented.
- **Given** an endpoint requires authentication or authorisation, **when** the document is reviewed, **then** 401 and 403 responses are documented with their body shape. Whether any endpoint requires credentials is undecided (`Q-BE-003`), so this part stays `DRAFT-UNVERIFIED`.
- **Given** the login endpoints, **when** the document is reviewed, **then** the request-code endpoint documents 429 for more than 3 requests within 10 minutes (BR-028). The verify-code endpoint documents 423 for a locked account, 401 for a wrong code, 410 for an expired code and 409 for an already-used code (BR-033). The lock is 15 minutes after 5 wrong codes (BR-022).
- **Traces to.** US-004-03 (second criterion), BR-022, BR-028, BR-032, BR-033.
- **Verification.** `contractdocs_login_endpoints_document_429_and_423` (planned).
- **API.** `API-CONTRACTDOCS-304307`.
- **Note.** The story attaches both the 429 and the locked-account response to "send/verify". BR-032 says request-code is not refused during a lock. This is `CON-BE-154`.

**BE-CONTRACT-DOCS-FR-009** Documented 404 matches actual behaviour.
- **Given** an endpoint that can return 404, **when** the documented 404 response is compared with the actual response, **then** they match.
- **Traces to.** US-004-03 (third criterion; its precondition text is garbled, `GAP-BE-170`).
- **Verification.** `contractdocs_documented_404_matches_actual_responses` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-010** Example payloads.
- **Given** the search endpoint and the tagging endpoints, **when** a developer views the document, **then** each has an example request with its query parameters or body and an example response body. Search also has an example of an empty result.
- **Traces to.** US-004-05 (both criteria), NFR-009.
- **Verification.** `contractdocs_search_and_tagging_have_examples_including_empty` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-011** Data-residency handling note.
- **Given** the published document, **when** an operations engineer reads the data-handling note, **then** it states that all note data is stored and served within the EU. Any difference in storage location between archived and active notes is called out. The current inputs describe none, because archiving is a flag on the same record (BR-007).
- **Traces to.** US-004-06 (both criteria), BR-013, BR-001, BR-007.
- **Verification.** `contractdocs_residency_note_states_eu_and_archive_location` (planned).
- **API.** `API-CONTRACTDOCS-304307`.
- **Note.** The note covers note data only. It makes no claim about the login-email address leaving the EU, because NFR-017 is garbled on that point (`GAP-BE-003`).

**BE-CONTRACT-DOCS-FR-012** Scope section content.
- **Given** the published document, **when** a reader views its Scope section, **then** mobile apps, attachments and real-time collaboration are listed as explicitly excluded.
- **Traces to.** US-005-01 (first criterion), BR-014, BR-015, BR-016.
- **Verification.** `contractdocs_scope_section_lists_three_exclusions` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-013** Scope changes need review.
- **Given** a documentation update adds or changes an out-of-scope item, **when** it is proposed, **then** it requires review and approval before merge.
- **Traces to.** US-005-01 (second criterion).
- **Verification.** `contractdocs_scope_change_requires_review` (planned).
- **API.** none.
- **Note.** The review mechanism is not named by any input (`GAP-BE-173`).

**BE-CONTRACT-DOCS-FR-014** Missing Scope section fails the docs build.
- **Given** a docs build, **when** the Scope section is missing, **then** the build fails with a lint or validation error.
- **Traces to.** US-005-01 (third criterion).
- **Verification.** `contractdocs_build_fails_without_scope_section` (planned).
- **API.** none.

**BE-CONTRACT-DOCS-FR-015** Full coverage.
- **Given** all implemented endpoints are documented, **when** the coverage check runs, **then** it reports 100%.
- **Traces to.** US-004-04 (first criterion), BR-012, NFR-008.
- **Verification.** `contractdocs_coverage_reports_100_when_all_documented` (planned).
- **API.** `API-CONTRACTDOCS-827943`.

**BE-CONTRACT-DOCS-FR-016** Partial coverage names the missing endpoint.
- **Given** one implemented endpoint is missing from the document, **when** the coverage check runs, **then** it reports less than 100% and names the missing endpoint.
- **Traces to.** US-004-04 (second criterion), BR-012.
- **Verification.** `contractdocs_coverage_names_missing_endpoint` (planned).
- **API.** `API-CONTRACTDOCS-827943`.

**BE-CONTRACT-DOCS-FR-017** Malformed document.
- **Given** the document is malformed, **when** the coverage check runs, **then** it fails with a clear parsing error and reports no percentage. The failure is 500 with the code `SPEC_MALFORMED` (`ASM-BE-173`).
- **Traces to.** US-004-04 (third criterion).
- **Verification.** `contractdocs_coverage_fails_on_malformed_spec_without_false_percent` (planned).
- **API.** `API-CONTRACTDOCS-827943`.

**BE-CONTRACT-DOCS-FR-018** Interface changes are marked.
- **Given** the `pinned` field, the pinned-first ordering, and the email one-time-code login endpoints, **when** the document is reviewed, **then** each is marked as an interface change that consumers must adapt to. The login endpoints are marked additive, because existing login methods are unchanged.
- **Traces to.** NFR-014, NFR-015, NFR-016, BR-032.
- **Verification.** `contractdocs_interface_changes_are_marked` (planned).
- **API.** `API-CONTRACTDOCS-304307`.
- **Note.** How consumers are notified is not defined (`GAP-BE-173`).
<!-- /SECTION: backend.contract-docs.requirements -->
<!-- SECTION: backend.contract-docs.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-CONTRACTDOCS-304307` | GET | `/openapi` | `contract-docs` | 200 | 406, 500 | Return the published OpenAPI 3.x document covering every implemented endpoint, with schemas, 4xx/5xx errors, examples, the data-residency note and the Scope section. Format is chosen by the Accept header; an unsupported Accept returns 406 or falls back to the default format (US-004-01). |
| `API-CONTRACTDOCS-827943` | GET | `/openapi/coverage` | `contract-docs` | 200 | 500 | Return the spec-versus-route coverage check: the share of implemented routes present in the published document (US-004-04, NFR-008). |

### `API-CONTRACTDOCS-304307` — GET `/openapi`

Return the published OpenAPI 3.x document covering every implemented endpoint, with schemas, 4xx/5xx errors, examples, the data-residency note and the Scope section. Format is chosen by the Accept header; an unsupported Accept returns 406 or falls back to the default format (US-004-01).

**Request:** none
**Response:** 
- `openapi` (string, required): OpenAPI 3.x version string of the document.
- `info` (object, required): API title and version, including the data-residency note (US-004-06) and the Scope section (US-005-01: no attachments, no real-time channel, no mobile-specific endpoints).
- `paths` (object, required): Every implemented endpoint with parameters, request and response schemas, 4xx/5xx error responses and example payloads (US-004-02, US-004-03, US-004-05).
- `components` (object, required): Shared schemas, including the error envelope.

### `API-CONTRACTDOCS-827943` — GET `/openapi/coverage`

Return the spec-versus-route coverage check: the share of implemented routes present in the published document (US-004-04, NFR-008).

**Request:** none
**Response:** 
- `coverage_percent` (number, required): Percentage of implemented endpoints documented in the published spec; target 100.
- `implemented_count` (integer, required): Number of endpoints registered in the route registry.
- `documented_count` (integer, required): Number of those endpoints present in the published OpenAPI document.
- `undocumented_endpoints` (array, required): Method and path of each implemented endpoint missing from the spec; empty when coverage is 100%.
<!-- /SECTION: backend.contract-docs.api-contract -->
<!-- SECTION: backend.contract-docs.invariants | status: DERIVED -->
## Invariants

- **INV-BE-151.** The coverage check reports 100% only when the document parsed successfully and no implemented route is missing from it. It never reports 100% by default or on failure. Derivation: US-004-04 criteria 1 and 3, BR-012.
- **INV-BE-152.** A malformed document never produces a coverage percentage. Derivation: US-004-04 (third criterion).
- **INV-BE-153.** Every published document contains a Scope section listing mobile apps, attachments and real-time collaboration as excluded. A build without it fails and is not published. Derivation: US-005-01.
- **INV-BE-154.** The statuses and error responses documented for an endpoint equal what the endpoint actually returns. Derivation: US-004-03 (third criterion), US-004-02 (third criterion).
- **INV-BE-155.** The residency note states EU storage and serving for note data and never names a location outside the EU for it. Derivation: US-004-06, BR-001, BR-013.
- **INV-BE-156.** Neither operation changes any state. Both are GET operations over derived data, so repeating either returns the same result for the same registry and document (`ASM-BE-167`).
- **INV-BE-157.** No example, note, or published artefact contains a credential, a real-looking email address or other PII. Derivation: core section 12.
- **INV-BE-158.** A path refused as out of scope is never listed as an implemented endpoint, and is not counted in the coverage figure (`ASM-BE-172`). Derivation: BR-014 to BR-016, US-005-02.
<!-- /SECTION: backend.contract-docs.invariants -->
<!-- SECTION: backend.contract-docs.authorization | status: DRAFT-UNVERIFIED -->
## Authorization

No input defines roles, and the project's `backend.auth-model` is `BLOCKED` on `Q-BE-001`, `Q-BE-002` and `Q-BE-003`. The permission matrix below is therefore minimal.

| Operation | Caller | Allowed |
|---|---|---|
| `API-CONTRACTDOCS-304307` | any caller (`ASM-BE-168`) | yes |
| `API-CONTRACTDOCS-827943` | any caller (`ASM-BE-168`, default taken for `Q-BE-154`) | yes |

- **Intended consumers.** Per the stories these are integration developers building the web_app, backend developers maintaining notes_api, and operations engineers reviewing residency (US-004-01, US-004-04, US-004-06).
- **Ownership predicate.** None. Neither resource is user-scoped, and both read system-level artefacts.
- **401 and 403.** Neither operation emits them while `ASM-BE-168` stands. If `Q-BE-003` resolves to credentials being required on every endpoint, this section and `BE-CONTRACT-DOCS-FR-008` must be revised.
<!-- /SECTION: backend.contract-docs.authorization -->
<!-- SECTION: backend.contract-docs.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

**Scheduled work and sweeps.** None. No input requires periodic regeneration, retention sweeps or retries for this module.

**Build and CI steps.** These run on build, not on a timer. The pipeline tool is not named by any input (`GAP-BE-175`).

| Step | Fails when | Source |
|---|---|---|
| Regenerate and publish the document from the route registry and operation records | the document cannot be generated | US-004-01 (second criterion), `ASM-BE-171` |
| Spec-validation check | a documented field type disagrees with the code | US-004-02 (third criterion) |
| Scope-section lint | the Scope section is missing | US-005-01 (third criterion) |
| Coverage check | the document is malformed. Whether `INCOMPLETE` also fails the build is open (`GAP-BE-174`). | US-004-04, BR-012 |

**Retries.** None specified.

**Idempotency.** Both operations are GET. They are idempotent, change no state, and need no idempotency key.
<!-- /SECTION: backend.contract-docs.jobs -->
<!-- SECTION: backend.contract-docs.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

**Unit.**
- Accept negotiation maps to a supported format or to 406 (`BE-CONTRACT-DOCS-FR-003`).
- Coverage computation returns `COMPLETE`, `INCOMPLETE` or `UNPARSEABLE` for fixtures with no routes missing, one route missing, and a malformed document (FR-015 to FR-017).
- The route identity key is method plus path (`ASM-BE-174`).
- Examples contain only masked placeholders (INV-BE-157).

**Integration.**
- `API-CONTRACTDOCS-304307` returns a valid OpenAPI 3.x document in each supported format (FR-001).
- `API-CONTRACTDOCS-827943` reports 100%, below 100% with the missing route named, and the parse-error failure (FR-015 to FR-017).
- The registry-versus-document comparison includes this module's own two routes and excludes refused out-of-scope paths (FR-002, INV-BE-158).

**Contract.**
- The document passes OpenAPI 3.x validation, and every implemented route appears in it (FR-002, FR-004).
- Documented statuses, including 404, 400 and the login refusals, equal actual responses (FR-007 to FR-009, INV-BE-154).
- The Scope section is present, and its absence fails the build (FR-012, FR-014).
- The residency note and the interface-change markings are present (FR-011, FR-018).
- Search and tagging examples exist, including the empty-result one (FR-010).

Test names are proposed under each requirement in `backend.contract-docs.requirements`.
<!-- /SECTION: backend.contract-docs.test-plan -->
<!-- SECTION: backend.contract-docs.gaps | status: VERIFIED -->
## Gaps

IDs minted by this module are defined below. IDs from the project spec are cited and not redefined.

### Questions

- **Q-BE-154** (`degradable`). Should `API-CONTRACTDOCS-827943` be access-restricted, given it exposes the route inventory and any undocumented routes? Default taken, unattended: it follows the same access rule as `API-CONTRACTDOCS-304307` (`ASM-BE-168`).
- **Q-BE-155** (`degradable`). For an unsupported Accept header, should the response be 406 or a default-format fallback? Default taken, unattended: 406 (`ASM-BE-169`).

### Contradictions

- **CON-BE-154** (non-critical).
  - **Claim A.** US-004-03 (second criterion) says the send and verify login endpoints document a 429 for more than 3 requests within 10 minutes and a locked-account response after 5 wrong codes.
  - **Claim B.** BR-028 attaches 429 to requests for a code. BR-032 says that during a lock only verification is refused, and BR-033 attaches 423 to verify-code.
  - **Reading used.** Request-code documents 429 and no lock refusal. Verify-code documents the lock refusal (423) and no 429.
  - **Consequence of the other side.** Documenting 429 on verify-code or a lock refusal on request-code would misstate behaviour that `auth` implements.

### Assumptions

- **ASM-BE-167.** No database table is needed. The published document is a build artefact, and the coverage result is derived per request and not stored. Confirm with the platform owner.
- **ASM-BE-168.** Both operations are read-only and callable without credentials, pending `Q-BE-003`. This also covers the default taken for `Q-BE-154`.
- **ASM-BE-169.** An unsupported Accept returns 406 with `NOT_ACCEPTABLE`, using the proposed registry in `backend.error-model` (`ASM-BE-001`). This is the default taken for `Q-BE-155`.
- **ASM-BE-170.** The supported formats are JSON and YAML, because US-004-01 names "JSON/YAML". JSON is the default when no Accept header is sent. No input states the default.
- **ASM-BE-171.** The document is generated at build and publish time from the route registry and the operation records, then served as-is. US-004-01 speaks of the spec being "regenerated/published", and BR-012 names the registry as the comparison source.
- **ASM-BE-172.** "Implemented endpoints" means routes that perform an operation. Paths refused with `OUT_OF_SCOPE` by `scope-guard` are not implemented endpoints. This module's own two routes are implemented endpoints and are counted.
- **ASM-BE-173.** A malformed document makes `API-CONTRACTDOCS-827943` return 500 with the proposed code `SPEC_MALFORMED`. The failure is server-side, since the artefact is the service's own.
- **ASM-BE-174.** A route is identified by method plus path, and coverage is the documented implemented routes divided by the implemented routes, as a percentage.
- **ASM-BE-175.** The Scope section is a named part of the document's top-level descriptive content. Review before merge is a repository review gate.

### Gaps

- **GAP-BE-170.** The precondition of US-004-03's third criterion is garbled. It reads "a documented field type is changed in code" but then checks the 404 response. Reading used: the documented 404 must match the actual one (`BE-CONTRACT-DOCS-FR-009`). Closing artefact: clean story text.
- **GAP-BE-171.** US-004-02 mentions constraints such as title length and tag format, but no input defines them (see also `GAP-BE-010`). The document can only state constraints a module defines. Closing artefact: a requirement giving field constraints.
- **GAP-BE-172.** No latency, throughput or availability target exists for the two operations of this module. NFR-013 covers CRUD and search only. Closing artefact: an NFR input.
- **GAP-BE-173.** The mechanism for "consumers are notified that they must adapt" (NFR-014 to NFR-016) and the review and approval mechanism for Scope changes (US-005-01) are not defined. Closing artefact: a process or tooling input.
- **GAP-BE-174.** Not stated: whether coverage below 100% blocks the build or only reports, and how a documented entry with no matching route is treated. The coverage definition runs in one direction only. Closing artefact: a rule from the BR-012 owner.
- **GAP-BE-175.** No input names the CI or docs-build tooling, the OpenAPI validator, or the exact OpenAPI 3.x minor version. `platform.md` names none. Closing artefact: a platform input.

### Cited from the project spec

- `GAP-BE-001`: the four inbound web_app handoffs were listed but their contents were not supplied, so this module could not read or answer them. The lane-needs step must record each as answered or unanswerable.
- `GAP-BE-003`: NFR-017 is garbled on whether the email address leaves the EU (`BE-CONTRACT-DOCS-FR-011`).
- `GAP-BE-005`: no operation lists active or archived notes, so the document cannot show one.
- `GAP-BE-006`: encryption at rest and retention of residency artefacts are unstated.
- `GAP-BE-009`: US-005-06 is a product-manager process.
- `GAP-BE-010`: tag rules are unstated.
- `Q-BE-003`: what a successful verify issues, and whether endpoints require it. It blocks the 401/403 documentation.
- `ASM-BE-001`: the error envelope and registry names are proposals.
<!-- /SECTION: backend.contract-docs.gaps -->
