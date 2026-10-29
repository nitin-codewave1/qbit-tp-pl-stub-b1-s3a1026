---
id: agent-developer/notes_api/backend/modules/contract-docs-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.4.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/contract-docs-SPEC.md
depends_on: [notes_api:US-004-01, notes_api:US-004-02, notes_api:US-004-03, notes_api:US-004-04, notes_api:US-004-05, notes_api:US-004-06, notes_api:US-005-01]
commit_kind: fork
confidence: agent
run_id: df4d63c0-0252-56b1-8292-59d2f9d8aa48
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
scope: [API-CONTRACTDOCS-098363, API-CONTRACTDOCS-827943]
```
<!-- /SECTION: backend.contract-docs.frontmatter -->
<!-- SECTION: backend.contract-docs.purpose | status: DERIVED -->
## Purpose

**Responsibility.** The `contract-docs` module publishes and checks the machine-readable description of the `notes_api` HTTP surface. Its boundary is stated in `backend.module-map`. It has three duties:

1. **Publish** one OpenAPI 3.x document that covers every implemented endpoint. The document carries schemas, documented 4xx/5xx errors, example payloads, a data-residency note and a Scope section (US-004-01 to US-004-06, US-005-01; BR-012, BR-013; NFR-008, NFR-009). It is served by `API-CONTRACTDOCS-098363`.
2. **Measure** coverage, meaning the share of implemented routes present in the published document. It names the routes that are missing and fails loudly on a malformed document (US-004-04; BR-012; NFR-008). It is served by `API-CONTRACTDOCS-827943`.
3. **Guard** the document's integrity. A mismatch between code and spec fails the CI check, and a missing Scope section fails the docs build (US-004-02, US-005-01).

**Not responsible for:**
- **The definitions of other modules' operations.** Request and response shapes, field constraints, status codes and error codes belong to the owning module (`notes`, `search`, `auth`, `residency`). This module reads and assembles them and does not redefine them (core rule 6).
- **Refusing out-of-scope calls and testing that they are absent.** This is `scope-guard` (US-005-02 to US-005-05; BR-014 to BR-016; NFR-010, NFR-011). `contract-docs` only states the exclusions in the Scope section (US-005-01).
- **The global error envelope and the error-code registry.** These are in `backend.error-model`; this module documents them.
- **Authentication and the session model.** These are `auth` and the blocked `backend.auth-model` (`Q-BE-003`).
- **Minting `API-` ids, versioning, tagging or publishing the contract package.** DevAgent and the orchestrator own these.
- **Stakeholder request logging** (US-005-06, BR-017). This is a product-manager process (`GAP-BE-009`).
<!-- /SECTION: backend.contract-docs.purpose -->
<!-- SECTION: backend.contract-docs.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**No persistent tables or columns are defined.** The published document and the coverage result are derived from the route registry and the modules' operation records at build or serve time. This is `ASM-BE-153`: no input says the document or its coverage history is stored in PostgreSQL 16 (`platform.md`). A stored copy would be a second source of truth that could drift from the routes.

**Logical (non-persisted) entities**

| Entity | Attributes the inputs require | Source |
|---|---|---|
| Route registry entry | Method and path of an implemented route. Identity for matching is method plus path (`ASM-BE-155`). | BR-012 ("route registry") |
| Published spec document | An OpenAPI 3.x version string, the paths, the reusable schemas, the data-residency note, and a Scope section naming the exclusions. | US-004-01, US-004-06, US-005-01 |
| Coverage result | A percentage of implemented routes present in the document, and the names of any missing routes. | US-004-04 |

Types, nullability, defaults and indexes do not apply, because there is no storage. The shape of the coverage result is defined once, in the operation record for `API-CONTRACTDOCS-827943`, and is not restated here.

**Storage and PII.** Nothing is stored, so there is no at-rest treatment to state. Examples in the document contain no real PII and mask any email address (NFR-018 covers logs; the same masking is applied to examples).
<!-- /SECTION: backend.contract-docs.data-model -->
<!-- SECTION: backend.contract-docs.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

**No entity in this module has a status column, so there is no state machine.** This follows from `ASM-BE-153`. There are no states, no transitions and no terminal states.

The module's behaviour is a set of outcomes of the checks. These are not states, because each outcome is recomputed on every run:

| Check | Outcome | Source |
|---|---|---|
| Coverage | Every implemented route is documented, so coverage is 100%. | US-004-04 AC1 |
| Coverage | At least one route is missing, so coverage is below 100% and the missing route is named. | US-004-04 AC2 |
| Coverage | The spec file is malformed, so the check fails with a parsing error and reports no coverage figure (`ASM-BE-156`). | US-004-04 AC3 |
| Spec versus code | A documented type differs from the code, so the CI check fails. | US-004-02 AC3 |
| Docs build | The Scope section is missing, so the docs build fails with a lint or validation error. | US-005-01 AC3 |
| Accept header | The requested media type is unsupported, so the response is 406 (`ASM-BE-152`). | US-004-01 AC3 |
<!-- /SECTION: backend.contract-docs.state-machine -->
<!-- SECTION: backend.contract-docs.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Statuses and codes are cited from `backend.error-model`. No schema is restated.

**BE-CONTRACT-DOCS-FR-001: Serve a valid OpenAPI document listing all endpoints.**
- Given notes_api is deployed, when a client requests the OpenAPI document, then a valid OpenAPI 3.x document is returned listing all CRUD, tagging, search, archive, and email one-time-code login (request and verify) endpoints.
- Traces to. US-004-01 AC1; BR-012; NFR-008; NFR-015; NFR-016.
- Verification. (planned) `contract_docs_serves_valid_openapi_3x_with_all_routes`.
- API. `API-CONTRACTDOCS-098363`.

**BE-CONTRACT-DOCS-FR-002: A new endpoint is documented no later than it becomes callable.**
- Given a new endpoint is added to notes_api, when the spec is regenerated and published, then the endpoint appears in the document before or at the same time as it becomes callable.
- Traces to. US-004-01 AC2; BR-012.
- Verification. (planned) `contract_docs_new_route_present_at_first_callable_build`.
- API. `API-CONTRACTDOCS-098363`.

**BE-CONTRACT-DOCS-FR-003: Handle an unsupported Accept header.**
- Given the document is requested with an unsupported Accept header, when the request is processed, then the response is 406 with a clear content type (`ASM-BE-152`, default of `Q-BE-151`).
- Traces to. US-004-01 AC3.
- Verification. (planned) `contract_docs_unsupported_accept_returns_406`.
- API. `API-CONTRACTDOCS-098363`.

**BE-CONTRACT-DOCS-FR-004: Document request and response schemas with required fields, types and constraints.**
- Given an endpoint accepts a note payload, when the schema is inspected in the spec, then all required fields, types and constraints are documented.
- The constraints themselves (title length, tag format) are defined by the owning module and are undefined by any input (`GAP-BE-153`).
- Traces to. US-004-02 AC1; BR-012.
- Verification. (planned) `contract_docs_note_payload_schema_documents_required_fields_and_types`.
- API. `API-CONTRACTDOCS-098363`.

**BE-CONTRACT-DOCS-FR-005: Mark optional parameters and document their defaults.**
- Given an endpoint supports optional query parameters (for example search filters), when the schema is reviewed, then optional parameters are marked optional and their default values are documented.
- Traces to. US-004-02 AC2.
- Verification. (planned) `contract_docs_optional_query_params_marked_with_defaults`.
- API. `API-CONTRACTDOCS-098363`.

**BE-CONTRACT-DOCS-FR-006: Fail CI on spec-versus-code mismatch.**
- Given a documented field type is changed in code and the spec is not updated to match, when the CI spec-validation check runs, then the check fails and flags the mismatch.
- The detection mechanism is unspecified (`GAP-BE-156`).
- Traces to. US-004-02 AC3.
- Verification. (planned) `contract_docs_ci_fails_on_field_type_drift`.
- API. none.

**BE-CONTRACT-DOCS-FR-007: Document the 400 validation error.**
- Given an endpoint can return validation errors, when the spec is reviewed, then a 400 response schema with the error structure is documented.
- The envelope is `backend.error-model`, with final shape open (`GAP-BE-011`). This module documents it and does not redefine it.
- Traces to. US-004-03 AC1; BR-012.
- Verification. (planned) `contract_docs_every_validating_route_documents_400`.
- API. `API-CONTRACTDOCS-098363`.

**BE-CONTRACT-DOCS-FR-008: Document authentication-related and login-specific error responses.**
- Given an endpoint requires authentication or is a one-time-code login endpoint, when the spec is reviewed, then 401 and 403 responses are documented with the expected body shape.
- The login endpoints additionally document 429 for more than 3 requests within 10 minutes and the locked-account response after 5 wrong codes locked for 15 minutes.
- The documented verify statuses are 423 locked, 401 wrong, 410 expired and 409 already used (BR-028, BR-033).
- The 401/403 documentation for non-login endpoints is blocked until the session model is decided (`Q-BE-003`, `GAP-BE-155`).
- Traces to. US-004-03 AC2; BR-028; BR-033; NFR-015.
- Verification. (planned) `contract_docs_login_routes_document_423_401_410_409_429`.
- API. `API-CONTRACTDOCS-098363`.

**BE-CONTRACT-DOCS-FR-009: The documented 404 matches actual behaviour.**
- Given a route that returns 404, when the documented 404 response is checked against the API's behaviour, then they match.
- US-004-03 AC3 pairs this check with an unrelated field-type condition. The wording is unclear (`GAP-BE-154`), and this reading is the narrowest consistent one.
- Traces to. US-004-03 AC3.
- Verification. (planned) `contract_docs_documented_404_matches_runtime`.
- API. `API-CONTRACTDOCS-098363`.

**BE-CONTRACT-DOCS-FR-010: Compute and expose the coverage metric.**
- Given all implemented endpoints are documented, when the coverage check runs, then it reports 100% coverage.
- Given one endpoint is implemented but missing from the spec, when the coverage check runs, then it reports less than 100% and names the missing endpoint.
- Given the spec file is malformed, when the coverage check runs, then it fails with a clear parsing error and does not report a false coverage figure (`ASM-BE-156`).
- Traces to. US-004-04; BR-012; NFR-008.
- Verification. (planned) `contract_docs_coverage_100_when_complete`, `contract_docs_coverage_names_missing_route`, `contract_docs_coverage_fails_on_malformed_spec`.
- API. `API-CONTRACTDOCS-827943`.

**BE-CONTRACT-DOCS-FR-011: Provide examples for search and tagging operations.**
- Given the search endpoint is documented, when a developer views the spec, then an example request with query params and an example response body are shown.
- Given search returns zero results, when the example responses are reviewed, then an example empty-result response is included.
- Tagging operations also carry examples (NFR-009).
- Examples contain no real PII and no credentials.
- Traces to. US-004-05; NFR-009.
- Verification. (planned) `contract_docs_search_and_tagging_have_examples_incl_empty_result`.
- API. `API-CONTRACTDOCS-098363`.

**BE-CONTRACT-DOCS-FR-012: State where note data is stored and served.**
- Given the documentation includes a data-handling note, when an operations engineer reviews it, then it states that all note data is stored and served within the EU.
- Any difference in storage location between archive and active-note endpoints is explicitly called out. No input states a difference. Archive is a flag (BR-007), so the note says there is none (`GAP-BE-158`).
- Traces to. US-004-06; BR-013; BR-001.
- Verification. (planned) `contract_docs_residency_note_present_and_states_eu_storage`.
- API. `API-CONTRACTDOCS-098363`.

**BE-CONTRACT-DOCS-FR-013: Publish a Scope section.**
- Given the published API documentation, when a reader views the Scope section, then mobile apps, attachments, and real-time collaboration are listed as explicitly excluded.
- Traces to. US-005-01 AC1; BR-014; BR-015; BR-016.
- Verification. (planned) `contract_docs_scope_section_lists_three_exclusions`.
- API. `API-CONTRACTDOCS-098363`.

**BE-CONTRACT-DOCS-FR-014: Changes to the Scope section require review.**
- Given a documentation update is proposed, when an out-of-scope item is added or changed, then the change requires review and approval before merge.
- The review mechanism is not named by any input (`GAP-BE-160`).
- Traces to. US-005-01 AC2.
- Verification. (planned) `contract_docs_scope_change_requires_review` (process check).
- API. none.

**BE-CONTRACT-DOCS-FR-015: Fail the docs build when the Scope section is missing.**
- Given the docs build pipeline, when the Scope section is missing from a build, then the documentation build fails with a lint or validation error.
- Traces to. US-005-01 AC3.
- Verification. (planned) `contract_docs_build_fails_without_scope_section`.
- API. none.

**Story-to-requirement map (this module)**

| Story | Requirements |
|---|---|
| US-004-01 | FR-001, FR-002, FR-003 |
| US-004-02 | FR-004, FR-005, FR-006 |
| US-004-03 | FR-007, FR-008, FR-009 |
| US-004-04 | FR-010 |
| US-004-05 | FR-011 |
| US-004-06 | FR-012 |
| US-005-01 | FR-013, FR-014, FR-015 |
<!-- /SECTION: backend.contract-docs.requirements -->
<!-- SECTION: backend.contract-docs.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-CONTRACTDOCS-098363` | GET | `/openapi.json` | `contract-docs` | 200 | 406, 500 | Retrieve the published OpenAPI 3.x document covering every implemented endpoint, with schemas, 4xx/5xx errors, examples, the data-residency note and the Scope section (US-004-01 to US-004-06, US-005-01; BR-012, BR-013; NFR-008, NFR-009). |
| `API-CONTRACTDOCS-827943` | GET | `/openapi/coverage` | `contract-docs` | 200 | 500 | Run the spec-versus-route coverage check and report which implemented routes are missing from the published OpenAPI document (NFR-008, NFR-010). |

### `API-CONTRACTDOCS-098363` — GET `/openapi.json`

Retrieve the published OpenAPI 3.x document covering every implemented endpoint, with schemas, 4xx/5xx errors, examples, the data-residency note and the Scope section (US-004-01 to US-004-06, US-005-01; BR-012, BR-013; NFR-008, NFR-009).

**Request:** none
**Response:** 
- `openapi` (string, required): OpenAPI 3.x version string of the document.
- `info` (object, required): API title and version. Its description carries the data-residency note (BR-013, US-004-06) and the Scope section stating that attachments, real-time channels and mobile-specific endpoints are out of scope (US-005-01; BR-014 to BR-016).
- `paths` (object, required): Every implemented endpoint, including the login endpoints, each with documented 4xx/5xx responses and examples (BR-012, NFR-008, NFR-009).
- `components` (object, required): Reusable schemas, including the error body schema (US-004-02, US-004-03).

### `API-CONTRACTDOCS-827943` — GET `/openapi/coverage`

Run the spec-versus-route coverage check and report which implemented routes are missing from the published OpenAPI document (NFR-008, NFR-010).

**Request:** none
**Response:** 
- `implemented_routes` (integer, required): Number of implemented routes found in the route registry.
- `documented_routes` (integer, required): Number of those routes present in the published OpenAPI document.
- `coverage_percent` (number, required): Share of implemented endpoints present in the published spec. The target is 100% (NFR-008).
- `undocumented_routes` (array, required): Implemented routes absent from the published document, each as a method and path. Empty when coverage is complete.
- `out_of_scope_routes` (array, required): Implemented routes that fall into an excluded category (attachment, real-time, mobile). The target is none (NFR-010).
<!-- /SECTION: backend.contract-docs.api-contract -->
<!-- SECTION: backend.contract-docs.invariants | status: DERIVED -->
## Invariants

- **INV-BE-151.** The published document is always a valid OpenAPI 3.x document. A document that fails parsing is never served as valid, and it is never used to compute coverage (US-004-01 AC1, US-004-04 AC3).
- **INV-BE-152.** The coverage check never reports 100% while an implemented route is absent from the document. It never reports any coverage figure for a malformed document (US-004-04; `ASM-BE-156`).
- **INV-BE-153.** The published document always contains a Scope section listing mobile apps, attachments, and real-time collaboration as excluded. A build without it fails (US-005-01).
- **INV-BE-154.** The published document always contains the data-residency note required by BR-013, and it states that note data is stored and served within the EU (US-004-06).
- **INV-BE-155.** The published document never lists an attachment, real-time or mobile-specific endpoint. Their absence is verified by `scope-guard` (BR-014 to BR-016, NFR-010) and not re-specified here.
- **INV-BE-156.** The documented statuses for the login operations equal those in BR-028 and BR-033. A documented status never differs from the owning module's behaviour (US-004-03).
- **INV-BE-157.** No example, description or response in the document contains a credential, a real code, or an unmasked email address (NFR-018 applied to documents).
<!-- /SECTION: backend.contract-docs.invariants -->
<!-- SECTION: backend.contract-docs.authorization | status: BLOCKED -->
## Authorization

**Blocked.** The roles, permission matrix and ownership predicate for the two operations cannot be written.

- **Session model.** Whether the OpenAPI document and the coverage operation require authentication depends on the session model (`Q-BE-003`), which no input answers.
- **Intended callers.** The inputs name consumers only by persona: the integration developer building the web_app (US-004-01) and the backend developer maintaining notes_api (US-004-04). BR-012 describes the coverage check as an automated, CI-style check. Nothing states whether the coverage operation is exposed to external callers or only to CI (`GAP-BE-157`).
- **Ownership.** Neither operation reads user-scoped data, so no ownership predicate is expected. This is derived and not stated by any input.

**What would unblock it:** the answer to `Q-BE-003`, plus a decision on who may call each operation (`GAP-BE-157`).
<!-- /SECTION: backend.contract-docs.authorization -->
<!-- SECTION: backend.contract-docs.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

| Job | Trigger | Behaviour | Source |
|---|---|---|---|
| Spec regeneration and publish | A route is added or changed | Regenerates the document so a new endpoint appears no later than it becomes callable. | US-004-01 AC2 |
| Spec-versus-code validation | Each CI run | Fails the build on a documented-type mismatch. | US-004-02 AC3; BR-012 |
| Coverage check | Each CI run, and on demand through `API-CONTRACTDOCS-827943` | Compares the route registry with the document. | BR-012; US-004-04 |
| Scope-section lint | Each docs build | Fails the build when the Scope section is missing. | US-005-01 AC3 |

- **Schedule.** No input gives a cron-style schedule. "Each CI run" and "each docs build" are the only triggers stated.
- **CI platform.** No input names the CI system, so no tool is named (`GAP-BE-156`).
- **Idempotency.** Both operations are GET and read-only. The jobs are pure checks, and repeating one has no side effect.
- **Retries.** No input requires retries. A failed check is a failed build.
<!-- /SECTION: backend.contract-docs.jobs -->
<!-- SECTION: backend.contract-docs.test-plan | status: DERIVED -->
## Test plan

**Unit**
- Route registry versus document matching: complete, one missing route, and a malformed document. Covers `contract_docs_coverage_100_when_complete`, `contract_docs_coverage_names_missing_route` and `contract_docs_coverage_fails_on_malformed_spec`.
- Percentage computation (`ASM-BE-155`).
- Scope-section lint: present and absent.
- Accept handling: supported and unsupported media types.

**Integration**
- Serve the document, then validate it as OpenAPI 3.x and confirm it lists every registered route (`contract_docs_serves_valid_openapi_3x_with_all_routes`).
- Add a route and confirm it is documented by the first build in which it is callable (`contract_docs_new_route_present_at_first_callable_build`).
- Alter a field type in code without updating the spec, and confirm the CI check fails (`contract_docs_ci_fails_on_field_type_drift`).
- Confirm the residency note and the Scope section are present in the served document.

**Contract**
- Every documented status for the login routes equals BR-028 and BR-033 (`contract_docs_login_routes_document_423_401_410_409_429`).
- Search and tagging routes have examples, including an empty-result example (`contract_docs_search_and_tagging_have_examples_incl_empty_result`).
- The document contains no endpoint matching an excluded category, which complements the `scope-guard` suite (INV-BE-155).
- Examples contain no unmasked email address (INV-BE-157).
<!-- /SECTION: backend.contract-docs.test-plan -->
<!-- SECTION: backend.contract-docs.gaps | status: UNKNOWN -->
## Gaps

### Questions (degradable; defaults taken, run is unattended)

- **Q-BE-151.** Class: degradable. When the OpenAPI document is requested with an unsupported Accept header, should it return 406 or fall back to a default format? US-004-01 permits either. Default taken: 406, consistent with `backend.error-model` (`ASM-BE-152`).
- **Q-BE-152.** Class: degradable. Should YAML be served in addition to JSON? US-004-01 says "JSON/YAML", while the operation path ends in `.json`. Default taken: JSON only (`ASM-BE-151`).

### Assumptions

- **ASM-BE-151.** The paths `/openapi.json` and `/openapi/coverage` are those of the supplied operation records. No input names them. Confirmation would come from the integration developer.
- **ASM-BE-152.** An unsupported Accept header returns 406 (default of `Q-BE-151`).
- **ASM-BE-153.** The document and coverage results are derived at build or serve time and are not stored (`backend.contract-docs.data-model`).
- **ASM-BE-154.** The two operations of this module are themselves implemented routes, so they appear in the document (BR-012: every implemented endpoint).
- **ASM-BE-155.** Route identity for coverage matching is method plus path, and the percentage is documented implemented routes divided by implemented routes. US-004-04 states "% of implemented endpoints present in the published spec" without a formula.
- **ASM-BE-156.** A malformed document makes the coverage operation return a server-error status (5xx) with a parsing-error message, and never a coverage figure. US-004-04 AC3 says only "fails with a clear parsing error".

### Gaps

- **GAP-BE-152.** Whether the document is also offered as YAML, and how format selection relates to the `.json` path. Degrades FR-001 and FR-003. Closes with an answer to `Q-BE-152`.
- **GAP-BE-153.** The field constraints referred to by US-004-02 AC1 (title length, tag format) are defined by no input. Tag rules are `GAP-BE-010`. Degrades FR-004. Closes with a constraints decision from the `notes` module.
- **GAP-BE-154.** US-004-03 AC3 is unclear: it checks a 404 response against a field-type change. Degrades FR-009. Closes with a corrected acceptance criterion.
- **GAP-BE-155.** The 401/403 documentation for non-login endpoints depends on the session model. Degrades FR-008. Closes with the answer to `Q-BE-003`.
- **GAP-BE-156.** The spec-versus-code detection mechanism and the CI platform are unnamed (`platform.md` names only the runtime stack and hosting). Degrades FR-006 and `backend.contract-docs.jobs`. Closes with a CI and tooling input.
- **GAP-BE-157.** No input says who may call the document and coverage operations, or whether coverage is exposed outside CI. Degrades `backend.contract-docs.authorization`, which is BLOCKED. Closes with `Q-BE-003` plus a decision on exposure.
- **GAP-BE-158.** The approved EU region list, any storage-location difference between archived and active notes, and encryption at rest are not in any input. Degrades FR-012. Closes with a platform-owner statement.
- **GAP-BE-159.** Whether the `residency` operations count as "implemented notes_api endpoints" for coverage is unstated. `residency` is infrastructure tooling (`ASM-BE-011`). Degrades FR-010. Closes with a scope decision.
- **GAP-BE-160.** The review and approval mechanism for Scope-section changes (US-005-01 AC2) is unnamed. Degrades FR-014. Closes with a repository-governance input.
- **GAP-BE-161.** The approved earlier specification recorded this operation as `GET /openapi` under a different id (`feedback.md`). This run's records use `/openapi.json` and `/openapi/coverage`. The web_app lane must be told the new paths and ids. It cannot be answered from any input here. Closes with confirmation of the intended paths.
- **GAP-BE-001** (existing). The four inbound handoff bodies were not supplied. This module cites it and does not re-specify it.

### Contradiction checks

All four directions were checked and no `CON-` is raised.
- Story and design: the design brief repeats the acceptance criteria only.
- Story and requirement: US-004-01 AC1 lists login endpoints, which agrees with BR-032 and NFR-015.
- Requirement and requirement: BR-012 describes an automated CI check, while US-004-04 describes a metric that is exposed. The two are combined, in the jobs section and in `API-CONTRACTDOCS-827943`.
- Design and design: not applicable.
<!-- /SECTION: backend.contract-docs.gaps -->
