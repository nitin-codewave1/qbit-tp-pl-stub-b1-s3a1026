---
id: agent-developer/notes_api/backend/modules/contract-docs-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/contract-docs-SPEC.md
depends_on: [notes_api:US-004-01, notes_api:US-004-02, notes_api:US-004-03, notes_api:US-004-04, notes_api:US-004-05, notes_api:US-004-06, notes_api:US-005-01]
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: notes_api:backend/modules/contract-docs-SPEC.md
---

<!-- SECTION: backend.contract-docs.frontmatter | status: VERIFIED -->
## Frontmatter

```yaml
id: contract-docs
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: []
scope: [API-CONTRACTDOCS-098363, API-CONTRACTDOCS-827943, API-CONTRACTDOCS-446650, API-CONTRACTDOCS-206969]
```
<!-- /SECTION: backend.contract-docs.frontmatter -->
<!-- SECTION: backend.contract-docs.purpose | status: DERIVED -->
## Purpose

**Responsibility.** `contract-docs` publishes the machine-readable description of the API and measures how complete that description is. Source: `backend.module-map`; US-004-01 to US-004-06, US-005-01; BR-012, BR-013; NFR-008, NFR-009.

It is responsible for:
- The published OpenAPI 3.x document covering every implemented endpoint (US-004-01, BR-012), served by `API-CONTRACTDOCS-098363`.
- The spec-versus-route coverage check and its result (US-004-04, NFR-008), served by `API-CONTRACTDOCS-827943`.
- The data-residency note (US-004-06, BR-013), served by `API-CONTRACTDOCS-446650` and carried in the document.
- The Scope section stating what the API does not cover (US-005-01), served by `API-CONTRACTDOCS-206969` and carried in the document.
- The CI checks that fail a build when the spec and the code diverge, when the spec is malformed, or when the Scope section is missing (US-004-02, US-004-04, US-005-01).

**Not responsible for:**
- Defining any endpoint, request or response shape, status code or error code. Those belong to the owning module and to `backend.error-model`. This module renders what they define and restates nothing (core rule 6).
- Enforcing the exclusion of attachment, real-time and mobile endpoints, or refusing calls to them. That belongs to `scope-guard`. This module only documents the exclusion.
- Deciding the data-residency facts. They belong to `backend.data-lifecycle` and `residency`. This module publishes them.
- The review and approval workflow for documentation changes (US-005-01, second criterion). This is a repository process, not a runtime capability (`GAP-BE-179`).
- Product-manager request logging (BR-017). It has no backend owner (`GAP-BE-009`).
- Any UI. The consuming web app is a separate surface.

**Inbound handoffs.** The four web-app handoffs are listed in `RUN-CONTEXT.upstream.handoffs`. Their bodies were not supplied (`GAP-BE-001`). `feedback.md` summarises them. The summary of `HANDOFF-WEBAPP-733353` (retry-after values, remaining lock time, the 400 field-detail shape) touches the error documentation this module renders. It cannot be answered here, because the values are decided in `backend.error-model` and not supplied (`GAP-BE-004`). The other three concern modules this one does not own. That reading rests on `ASM-BE-010`.
<!-- /SECTION: backend.contract-docs.purpose -->
<!-- SECTION: backend.contract-docs.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**This module owns no tables, columns or indexes** (`ASM-BE-161`). Every artefact it works with is derived at build or request time from the route registry and the module specifications. Nothing in the inputs requires persisting any of them.

| Logical artefact | Derived from | Persisted | Source |
|---|---|---|---|
| Route registry | The implemented routes of the running service | No, it is the mounted route set | BR-012 (enforcement: "route registry") |
| Published OpenAPI document | The route registry and the owning modules' operation records | No (`ASM-BE-161`, `ASM-BE-169`) | US-004-01, BR-012 |
| Data-residency note | One source text shared by the document and `API-CONTRACTDOCS-446650` (`ASM-BE-166`) | No | BR-013, US-004-06 |
| Scope section | One source text shared by the document and `API-CONTRACTDOCS-206969` (`ASM-BE-166`) | No | US-005-01 |
| Coverage result | A comparison of registry and document | No, computed per call or per build | US-004-04 |

**Schemas.** No `SCHEMA-` id is minted by this module. The shapes of the four operations' bodies are rendered into `backend.contract-docs.api-contract` from the operation records.

**Retention.** No retention applies, because nothing is stored. The history of coverage results over time is not specified by any input (`GAP-BE-177`).
<!-- /SECTION: backend.contract-docs.data-model -->
<!-- SECTION: backend.contract-docs.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

No entity has a status column. The only lifecycle here is that of one documentation build. It is derived from the checks that US-004-02, US-004-04 and US-005-01 require to fail a build, and it is not an input-stated state set.

| State | Meaning | Terminal |
|---|---|---|
| `Generated` | The document has been produced from the route registry (`ASM-BE-169`). | no |
| `Validated` | The document parses, matches the code, and contains the Scope section. | no |
| `Published` | The document is served by `API-CONTRACTDOCS-098363`. | yes, for that build |
| `Rejected` | A required check failed and the build failed. | yes, for that build |

| From | To | Trigger | Guard |
|---|---|---|---|
| `Generated` | `Validated` | the validation checks run | the document parses as OpenAPI 3.x (US-004-04), the documented types match the code (US-004-02), and the Scope section is present (US-005-01) |
| `Generated` | `Rejected` | the validation checks run | the document is malformed |
| `Generated` | `Rejected` | the validation checks run | a documented field type differs from the code (US-004-02) |
| `Generated` | `Rejected` | the validation checks run | the Scope section is missing (US-005-01) |
| `Validated` | `Published` | the build completes | none beyond the above. Whether a coverage below 100% also blocks this transition is unstated (`GAP-BE-177`). |

A new build starts again at `Generated`. Terminal states are terminal per build, not per document.

**Illegal transitions.** The lifecycle runs in the build pipeline, not behind an HTTP operation. No status or error code is defined for an illegal transition, and none is needed.
<!-- /SECTION: backend.contract-docs.state-machine -->
<!-- SECTION: backend.contract-docs.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Requirement ids use the module name `CONTRACT-DOCS`.

### BE-CONTRACT-DOCS-FR-001: the document is published and complete
- **Given** notes_api is deployed,
- **When** a client requests the OpenAPI document,
- **Then** a valid OpenAPI 3.x JSON document is returned listing all CRUD, tagging, search, archive, and email one-time-code login (request-code and verify-code) endpoints. Only JSON is offered by this path (`ASM-BE-167`).
- **Traces to.** US-004-01, BR-012, NFR-008
- **Verification.** (planned) `contract_docs_openapi_document_is_valid_and_lists_all_routes`
- **API.** → `API-CONTRACTDOCS-098363`

### BE-CONTRACT-DOCS-FR-002: a new endpoint is documented no later than it is callable
- **Given** a new endpoint is added to notes_api,
- **When** the spec is regenerated and published,
- **Then** the new endpoint appears in the document before or at the same time it becomes callable. The mechanism is that the document and the mounted routes come from one registry (`ASM-BE-169`).
- **Traces to.** US-004-01, BR-012
- **Verification.** (planned) `contract_docs_new_route_appears_in_document_on_same_build`
- **API.** → `API-CONTRACTDOCS-098363`

### BE-CONTRACT-DOCS-FR-003: unsupported Accept header
- **Given** the OpenAPI document is requested with an unsupported `Accept` header,
- **When** the request is handled,
- **Then** the response is 406 with a clear content type for the error body. A default-format fallback is the other reading the input allows (`ASM-BE-162`, `Q-BE-161`).
- **Traces to.** US-004-01
- **Verification.** (planned) `contract_docs_unsupported_accept_returns_406`
- **API.** → `API-CONTRACTDOCS-098363`

### BE-CONTRACT-DOCS-FR-004: request and response schemas are documented
- **Given** an endpoint accepts a note payload,
- **When** its schema is inspected in the document,
- **Then** all required fields, types and constraints are documented, taken from the owning module's operation record and not defined here. Constraints are limited to those some input states (`GAP-BE-175`).
- **Traces to.** US-004-02, BR-012
- **Verification.** (planned) `contract_docs_schemas_declare_required_fields_types_constraints`
- **API.** → `API-CONTRACTDOCS-098363`

### BE-CONTRACT-DOCS-FR-005: optional parameters and their defaults
- **Given** an endpoint supports optional query parameters, such as search filters,
- **When** the schema is reviewed,
- **Then** optional parameters are marked optional and their default values are documented.
- **Traces to.** US-004-02
- **Verification.** (planned) `contract_docs_optional_params_marked_with_defaults`
- **API.** → `API-CONTRACTDOCS-098363`

### BE-CONTRACT-DOCS-FR-006: type drift fails CI
- **Given** a documented field type is changed in code,
- **When** the document is not updated to match,
- **Then** the spec-validation CI check fails and flags the mismatch.
- **Traces to.** US-004-02
- **Verification.** (planned) `contract_docs_ci_fails_on_type_mismatch`
- **API.** none (build-time check, see `backend.contract-docs.jobs`)

### BE-CONTRACT-DOCS-FR-007: validation and server errors are documented
- **Given** an endpoint can return validation errors,
- **When** the document is reviewed,
- **Then** a 400 response with the error structure is documented, and every endpoint documents its 4xx and 5xx responses. The structure is the envelope in `backend.error-model` (`ASM-BE-006`, `GAP-BE-004`), referenced and not redefined here.
- **Traces to.** US-004-03, BR-012
- **Verification.** (planned) `contract_docs_every_operation_documents_400_and_5xx`
- **API.** → `API-CONTRACTDOCS-098363`

### BE-CONTRACT-DOCS-FR-008: authorization and login errors are documented
- **Given** an endpoint requires authentication or authorization, or is a login endpoint,
- **When** the document is reviewed,
- **Then** 401 and 403 responses are documented with the expected body shape. The request-code operation documents the 429 response for more than 3 requests within 10 minutes (BR-028). The verify-code operation documents the locked-account response, 423 (BR-033). Which note and search endpoints require authentication is undecided (`Q-BE-002`, `Q-BE-003`), so no 401 or 403 is asserted for them here.
- **Traces to.** US-004-03, BR-028, BR-033
- **Verification.** (planned) `contract_docs_login_operations_document_429_and_423`
- **API.** → `API-CONTRACTDOCS-098363`

### BE-CONTRACT-DOCS-FR-009: the 404 documentation matches behaviour
- **Given** an endpoint documents a 404 response,
- **When** the documented response is checked against the actual API behaviour,
- **Then** they match. The input sentence for this criterion is garbled (`GAP-BE-180`), so this reading is the intent that survives its text.
- **Traces to.** US-004-03
- **Verification.** (planned) `contract_docs_documented_404_matches_runtime`
- **API.** → `API-CONTRACTDOCS-098363`

### BE-CONTRACT-DOCS-FR-010: complete coverage reports 100%
- **Given** all implemented endpoints are documented,
- **When** the coverage check runs,
- **Then** it reports 100% coverage (NFR-008). The check counts every route in the registry, including this module's own four (`ASM-BE-163`).
- **Traces to.** US-004-04, NFR-008
- **Verification.** (planned) `contract_docs_coverage_reports_100_when_complete`
- **API.** → `API-CONTRACTDOCS-827943`

### BE-CONTRACT-DOCS-FR-011: a missing endpoint is named
- **Given** one endpoint is implemented but missing from the document,
- **When** the coverage check runs,
- **Then** it reports less than 100% and names the missing endpoint.
- **Traces to.** US-004-04
- **Verification.** (planned) `contract_docs_coverage_names_missing_endpoint`
- **API.** → `API-CONTRACTDOCS-827943`

### BE-CONTRACT-DOCS-FR-012: a malformed document never yields a coverage figure
- **Given** the spec file is malformed,
- **When** the coverage check runs,
- **Then** it fails with a clear parsing error and reports no coverage figure. Over HTTP the failure is a 5xx error response (`ASM-BE-165`, `Q-BE-162`).
- **Traces to.** US-004-04
- **Verification.** (planned) `contract_docs_malformed_spec_fails_with_parse_error`
- **API.** → `API-CONTRACTDOCS-827943`

### BE-CONTRACT-DOCS-FR-013: example payloads
- **Given** the search and tagging endpoints are documented,
- **When** a developer views the document,
- **Then** an example request with its query parameters and an example response body are shown for search. Tagging operations have examples as well, and an example empty-result search response is included. All examples use synthetic values (`ASM-BE-168`).
- **Traces to.** US-004-05, NFR-009
- **Verification.** (planned) `contract_docs_search_and_tagging_have_examples_including_empty_result`
- **API.** → `API-CONTRACTDOCS-098363`

### BE-CONTRACT-DOCS-FR-014: the data-residency note
- **Given** the API documentation includes a data-handling note,
- **When** an operations engineer reviews it,
- **Then** it states that all note data is stored and served within the EU. It calls out any difference between archived and active note storage. `backend.data-lifecycle` records notes, including archived ones, as one database in one EU region, so the note states that no difference exists (`GAP-BE-178`). The same text is returned by `API-CONTRACTDOCS-446650` and embedded in the document (`ASM-BE-166`).
- **Traces to.** US-004-06, BR-013, BR-001
- **Verification.** (planned) `contract_docs_residency_note_states_eu_and_archive_difference`
- **API.** → `API-CONTRACTDOCS-446650`, → `API-CONTRACTDOCS-098363`

### BE-CONTRACT-DOCS-FR-015: the Scope section
- **Given** the published documentation,
- **When** a reader views the Scope section,
- **Then** mobile apps, attachments and real-time collaboration are listed as explicitly excluded (BR-014, BR-015, BR-016). The same text is returned by `API-CONTRACTDOCS-206969` and embedded in the document (`ASM-BE-166`).
- **Traces to.** US-005-01, BR-014, BR-015, BR-016
- **Verification.** (planned) `contract_docs_scope_lists_three_exclusions`
- **API.** → `API-CONTRACTDOCS-206969`, → `API-CONTRACTDOCS-098363`

### BE-CONTRACT-DOCS-FR-016: a missing Scope section fails the build
- **Given** the documentation build pipeline,
- **When** the Scope section is missing from a build,
- **Then** the documentation build fails with a lint or validation error.
- **Traces to.** US-005-01
- **Verification.** (planned) `contract_docs_build_fails_without_scope_section`
- **API.** none (build-time check)

### BE-CONTRACT-DOCS-FR-017: changes to the exclusion list require review
- **Given** a documentation update is proposed,
- **When** a new out-of-scope item is added or changed,
- **Then** the change requires review and approval before merge. The enforcement mechanism is a repository process and no input defines it (`GAP-BE-179`).
- **Traces to.** US-005-01
- **Verification.** (planned) none possible until the process is defined (`GAP-BE-179`)
- **API.** none

### BE-CONTRACT-DOCS-FR-018: the interface-change notice is documented
- **Given** the login endpoints and the `pinned` flag are interface changes,
- **When** the document is published,
- **Then** it states that consumers must adapt (NFR-014, NFR-015, NFR-016). The login endpoints are additive and existing login methods remain unchanged (NFR-016; those methods are unspecified, `GAP-BE-008`).
- **Traces to.** NFR-014, NFR-015, NFR-016, US-004-01
- **Verification.** (planned) `contract_docs_document_carries_interface_change_notice`
- **API.** → `API-CONTRACTDOCS-098363`

### BE-CONTRACT-DOCS-FR-019: examples carry no secrets or real-looking PII
- **Given** any example in the document, the residency note or the Scope section,
- **When** the document is published,
- **Then** no secret, key or token appears, and email addresses are masked (NFR-018).
- **Traces to.** NFR-018, US-004-05
- **Verification.** (planned) `contract_docs_examples_contain_no_secrets_or_unmasked_emails`
- **API.** → `API-CONTRACTDOCS-098363`
<!-- /SECTION: backend.contract-docs.requirements -->
<!-- SECTION: backend.contract-docs.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-CONTRACTDOCS-098363` | GET | `/openapi.json` | `contract-docs` | 200 | 406, 500 | Return the published OpenAPI 3.x document covering every implemented endpoint, including the data-residency note and Scope section. |
| `API-CONTRACTDOCS-827943` | GET | `/openapi/coverage` | `contract-docs` | 200 | 500 | Report the spec-versus-route coverage check result. |
| `API-CONTRACTDOCS-446650` | GET | `/openapi/data-residency` | `contract-docs` | 200 | 500 | Return the data-residency note published with the API documentation. |
| `API-CONTRACTDOCS-206969` | GET | `/openapi/scope` | `contract-docs` | 200 | 500 | Return the Scope section listing what the API does and explicitly does not cover. |

### `API-CONTRACTDOCS-098363` — GET `/openapi.json`

Return the published OpenAPI 3.x document covering every implemented endpoint, including the data-residency note and Scope section.

**Request:** none
**Response:** 
- `openapi` (string, required): OpenAPI version string of the published document (3.x).
- `info` (object, required): API metadata (title, version) for the published document.
- `paths` (object, required): Every implemented endpoint with its schemas, 4xx/5xx errors and examples (BR-012, NFR-008, NFR-009).
- `components` (object, required): Shared schemas, including the error body schema.

### `API-CONTRACTDOCS-827943` — GET `/openapi/coverage`

Report the spec-versus-route coverage check result.

**Request:** none
**Response:** 
- `implemented_count` (integer, required): Number of implemented routes found in the route registry.
- `documented_count` (integer, required): Number of those routes present in the published spec.
- `coverage_percent` (number, required): Share of implemented endpoints present in the published spec (target 100%, NFR-008).
- `missing_endpoints` (array, required): Implemented routes absent from the spec; the build fails when non-empty (US-004-04).

### `API-CONTRACTDOCS-446650` — GET `/openapi/data-residency`

Return the data-residency note published with the API documentation.

**Request:** none
**Response:** 
- `note` (string, required): Text of the data-residency note published with the API documentation (BR-013).

### `API-CONTRACTDOCS-206969` — GET `/openapi/scope`

Return the Scope section listing what the API does and explicitly does not cover.

**Request:** none
**Response:** 
- `scope` (string, required): Text of the Scope section stating what the API covers.
- `excluded` (array, required): Excluded capabilities: attachments, real-time channels, mobile-specific endpoints (BR-014 to BR-016).
<!-- /SECTION: backend.contract-docs.api-contract -->
<!-- SECTION: backend.contract-docs.invariants | status: DERIVED -->
## Invariants

- **INV-BE-151.** After every build that reaches `Published`, every route in the registry appears in the document, or the coverage result is below 100% and names each missing route. Derived from BR-012 and US-004-04.
- **INV-BE-152.** A document that cannot be parsed never produces a coverage percentage. Derived from US-004-04, third criterion.
- **INV-BE-153.** No build reaches `Published` without a Scope section. Derived from US-005-01, third criterion.
- **INV-BE-154.** The data-residency note and the Scope section served by their own operations are identical in content to the copies embedded in the document, because they share one source text (`ASM-BE-166`).
- **INV-BE-155.** Every documented operation lists its 4xx and 5xx responses. Derived from BR-012 and US-004-03.
- **INV-BE-156.** The four operations of this module change no state: they read and compute only. Derived from their definition as GET operations.
- **INV-BE-157.** No example in any artefact served by this module contains a secret or an unmasked email address. Derived from NFR-018 and core rule 12.
<!-- /SECTION: backend.contract-docs.invariants -->
<!-- SECTION: backend.contract-docs.authorization | status: DRAFT-UNVERIFIED -->
## Authorization

No role is defined by any input, and `backend.auth-model` is `BLOCKED` pending `Q-BE-001`, `Q-BE-002` and `Q-BE-003`. The matrix below therefore carries one assumption.

| Operation | Permitted callers |
|---|---|
| `API-CONTRACTDOCS-098363` | any caller, without a session (`ASM-BE-164`) |
| `API-CONTRACTDOCS-827943` | any caller, without a session (`ASM-BE-164`) |
| `API-CONTRACTDOCS-446650` | any caller, without a session (`ASM-BE-164`) |
| `API-CONTRACTDOCS-206969` | any caller, without a session (`ASM-BE-164`) |

**Basis.** The only input about access is US-004-01: "client requests the OpenAPI document". Integrators fetch it before they can hold a credential. If the session decision (`Q-BE-003`) requires a session for these operations, this section and the 401 documentation for them change (`Q-BE-163`).

**Ownership predicate.** None. The document, the coverage result, the residency note and the Scope section are not user-scoped resources.

**Rate limiting.** No rate-limit class is stated by any input for these operations (`GAP-BE-176`).
<!-- /SECTION: backend.contract-docs.authorization -->
<!-- SECTION: backend.contract-docs.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

No scheduled sweep, retry or background worker is required by any input. The work is build-time checks and request-time reads.

| Job | Trigger | Does | Fails when | Source |
|---|---|---|---|---|
| Spec-versus-code validation | every CI build | compares documented types and responses with the code | a documented field type differs from the code (`BE-CONTRACT-DOCS-FR-006`) | US-004-02 |
| Coverage check | every CI build, and on demand via `API-CONTRACTDOCS-827943` | compares registry and document | the document is malformed (`BE-CONTRACT-DOCS-FR-012`). A coverage below 100% is reported and named. Whether it also fails the build is unstated (`GAP-BE-177`). | US-004-04, BR-012 |
| Scope lint | every documentation build | checks that the Scope section exists | the section is missing (`BE-CONTRACT-DOCS-FR-016`) | US-005-01 |

**Cadence.** The inputs name "every build" for the scope guard (NFR-010, owned by `scope-guard`) and "CI" for the checks above. A run frequency beyond "each CI build" is not stated.

**Idempotency.** All four operations are GETs and are idempotent. The coverage check is a pure computation over the current registry and document. No idempotency key is needed.

**Retries.** None specified.
<!-- /SECTION: backend.contract-docs.jobs -->
<!-- SECTION: backend.contract-docs.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All tests are planned, none exist yet.

### Unit
- `contract_docs_coverage_computes_documented_over_implemented`: the arithmetic and the empty and full cases (`BE-CONTRACT-DOCS-FR-010`, `BE-CONTRACT-DOCS-FR-011`).
- `contract_docs_malformed_spec_fails_with_parse_error` (`BE-CONTRACT-DOCS-FR-012`).
- `contract_docs_accept_header_negotiation` (`BE-CONTRACT-DOCS-FR-003`).
- `contract_docs_examples_contain_no_secrets_or_unmasked_emails` (`BE-CONTRACT-DOCS-FR-019`).

### Integration
- `contract_docs_openapi_document_is_valid_and_lists_all_routes` against the running service (`BE-CONTRACT-DOCS-FR-001`).
- `contract_docs_new_route_appears_in_document_on_same_build` (`BE-CONTRACT-DOCS-FR-002`).
- `contract_docs_residency_note_states_eu_and_archive_difference` and `contract_docs_scope_lists_three_exclusions`, each asserting equality between the dedicated operation and the embedded copy (`INV-BE-154`).
- `contract_docs_build_fails_without_scope_section` (`BE-CONTRACT-DOCS-FR-016`).

### Contract
- `contract_docs_ci_fails_on_type_mismatch` (`BE-CONTRACT-DOCS-FR-006`).
- `contract_docs_documented_404_matches_runtime` and `contract_docs_every_operation_documents_400_and_5xx` (`BE-CONTRACT-DOCS-FR-007`, `BE-CONTRACT-DOCS-FR-009`).
- `contract_docs_login_operations_document_429_and_423` (`BE-CONTRACT-DOCS-FR-008`).
- `contract_docs_search_and_tagging_have_examples_including_empty_result` (`BE-CONTRACT-DOCS-FR-013`).
- `contract_docs_document_carries_interface_change_notice` (`BE-CONTRACT-DOCS-FR-018`).

**Coverage of the owned stories:** US-004-01 (FR-001 to FR-003, FR-018), US-004-02 (FR-004 to FR-006), US-004-03 (FR-007 to FR-009), US-004-04 (FR-010 to FR-012), US-004-05 (FR-013, FR-019), US-004-06 (FR-014), US-005-01 (FR-015 to FR-017). Every owned story has at least one requirement. FR-017 has no possible test until the review process is defined.
<!-- /SECTION: backend.contract-docs.test-plan -->
<!-- SECTION: backend.contract-docs.gaps | status: VERIFIED -->
## Gaps, contradictions, assumptions and questions

### Gaps

| Id | What is missing | Degrades | Did instead | Closing artefact |
|---|---|---|---|---|
| `GAP-BE-173` | The outcome for an unsupported Accept header: US-004-01 allows 406 or a default-format fallback | `BE-CONTRACT-DOCS-FR-003` | Assumed 406 (`ASM-BE-162`) | The answer to `Q-BE-161` |
| `GAP-BE-174` | The status code and error-code name when the coverage check meets a malformed spec | `BE-CONTRACT-DOCS-FR-012` | Assumed a 5xx error response with a parse-error message (`ASM-BE-165`) | The answer to `Q-BE-162`, or an error-model decision |
| `GAP-BE-175` | The constraints the document should declare, such as title length and tag format. US-004-02 gives them only as "e.g." and no input states values. Tag rules are already open as `GAP-BE-003`. | `BE-CONTRACT-DOCS-FR-004` | Declared only constraints some input states; none was invented | A statement of title and tag constraints |
| `GAP-BE-176` | A rate-limit class for the four operations | `backend.contract-docs.authorization` | None asserted | A rate-limit decision |
| `GAP-BE-177` | Whether a coverage below 100% blocks publication or only reports. Also unstated: the rounding or precision of the figure, and whether coverage results are kept as history. | `backend.contract-docs.state-machine`, `backend.contract-docs.jobs` | Treated a coverage below 100% as reported and named, not as a build failure. History is not kept. | A build-policy statement |
| `GAP-BE-178` | Whether archived and active note data are stored or served differently. US-004-06 asks the note to call out any difference, and no input says one exists. | `BE-CONTRACT-DOCS-FR-014` | Followed `backend.data-lifecycle`, which records one database in one EU region, and stated no difference | Confirmation from the platform or compliance owner |
| `GAP-BE-179` | The review and approval process for changes to the exclusion list, and where it is enforced | `BE-CONTRACT-DOCS-FR-017` | Recorded as a repository process outside the service | A statement of the process, or a decision that this surface does not serve it |
| `GAP-BE-180` | The third acceptance criterion of US-004-03 is garbled: it names a changed field type and then checks a 404 response | `BE-CONTRACT-DOCS-FR-009` | Read it as "the documented 404 matches actual behaviour" | A restated criterion |
| `GAP-BE-181` | Which tooling generates the document and detects route-to-document drift. The platform input names Node.js 20 and Express and no OpenAPI library, and none is invented. | `BE-CONTRACT-DOCS-FR-002`, `BE-CONTRACT-DOCS-FR-006` | Specified behaviour only | A platform input naming the tooling |

**Referenced from the project specification, not re-raised:**
- `GAP-BE-001` (handoff files not supplied)
- `GAP-BE-003` (tag rules)
- `GAP-BE-004` (error envelope and code names)
- `GAP-BE-008` (existing login methods)
- `GAP-BE-009` (no backend owner for BR-017)
- `GAP-BE-013` (no `api_docs` input)

### Contradictions

None raised. The four directions were checked:
- story ↔ design: the design brief holds only acceptance criteria and no visual design, so nothing to contradict.
- story ↔ requirement: US-004-01 to US-004-06 and US-005-01 agree with BR-012, BR-013, NFR-008, NFR-009 and NFR-010.
- requirement ↔ requirement: consistent for this module. The related conflicts in the project specification are `CON-BE-001`, `CON-BE-002` and `CON-BE-003`.
- design ↔ design: the acceptance criteria in `design-brief.md` repeat the stories without conflict.

### Assumptions

| Id | Assumption | What would confirm it |
|---|---|---|
| `ASM-BE-161` | This module stores nothing in PostgreSQL 16. The document, the notes and the coverage result are derived at build or request time. | A decision on persisting coverage history (`GAP-BE-177`) |
| `ASM-BE-162` | An unsupported Accept header returns 406. | `Q-BE-161` |
| `ASM-BE-163` | The coverage check counts every route in the registry, including this module's four. The percentage is documented routes over implemented routes (US-004-04). | A counting rule from the product owner |
| `ASM-BE-164` | The four operations are readable without a session. | `Q-BE-163` and the answer to `Q-BE-003` |
| `ASM-BE-165` | A malformed spec makes the coverage operation return a 5xx error response carrying a parse-error message, and no coverage figure. | `Q-BE-162` |
| `ASM-BE-166` | The data-residency note and the Scope section each have one source text, used both for the dedicated operation and for the embedded copy. | Implementation review |
| `ASM-BE-167` | `API-CONTRACTDOCS-098363` serves JSON only. US-004-01 says "JSON/YAML", and the operation path ends in `.json`. | A statement that a YAML rendition is required |
| `ASM-BE-168` | Examples use synthetic values with masked email addresses. | None needed (core rule 12, NFR-018) |
| `ASM-BE-169` | The document is generated from the same route registry that mounts the routes, so a callable route is always in the document. | Tooling decision (`GAP-BE-181`) |

### Questions

| Id | Class | Question | Default if unanswered |
|---|---|---|---|
| `Q-BE-161` | degradable | Should an unsupported Accept header on the OpenAPI document return 406 or a default-format fallback? | 406 (`ASM-BE-162`) |
| `Q-BE-162` | degradable | Which status code and error-code name should the coverage operation return when the spec is malformed? | 5xx with a parse-error message (`ASM-BE-165`) |
| `Q-BE-163` | degradable | Do the four documentation operations require a session? This follows the blocking `Q-BE-003`. | No session required (`ASM-BE-164`) |

No new blocking question is raised by this module. The three degradable questions take their defaults, because the run is unattended.
<!-- /SECTION: backend.contract-docs.gaps -->
