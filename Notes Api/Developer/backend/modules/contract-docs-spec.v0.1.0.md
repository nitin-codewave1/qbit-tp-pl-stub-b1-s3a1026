---
id: agent-developer/notes_api/backend/modules/contract-docs-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/contract-docs-SPEC.md
depends_on: [notes_api:US-004-01, notes_api:US-004-02, notes_api:US-004-03, notes_api:US-004-04, notes_api:US-004-05, notes_api:US-004-06, notes_api:US-005-01]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
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
scope: [API-CONTRACTDOCS-304307]
```
<!-- /SECTION: backend.contract-docs.frontmatter -->
<!-- SECTION: backend.contract-docs.purpose | status: VERIFIED -->
## Purpose

**Responsible for** (source: `backend.module-map`; EP-004, EP-005):

- Publishing one OpenAPI 3.x document that covers every implemented `notes_api` endpoint, with request and response schemas, 4xx/5xx errors, example payloads, a data-residency handling note and a Scope section (US-004-01 to US-004-06, US-005-01; BR-012, BR-013; NFR-008, NFR-009).
- Serving that document through `API-CONTRACTDOCS-304307`.
- A spec-versus-route coverage check, which reports the percentage of implemented endpoints present in the document and names any that are missing (US-004-04; BR-012; NFR-008).
- A spec-validation check that fails when documented types diverge from code (US-004-02).
- A documentation lint or build step that fails when the Scope section is missing (US-005-01).
- Recording the interface changes that consumers must adapt to: `pinned` and pinned-first ordering, and the email one-time-code login endpoints (NFR-014, NFR-015, NFR-016).

**Not responsible for:**

- The behaviour, data model or error semantics of any documented endpoint. These are owned by `notes`, `search` and `auth`. This module documents them and references `backend.error-model` and the other modules' contracts; it restates none of them (core rule 6).
- Detecting or refusing attachment, real-time and mobile-specific calls. That belongs to `scope-guard` (BR-014 to BR-016, NFR-010, NFR-011). This module only states the exclusions in the Scope section.
- Logging and classifying stakeholder requests (BR-017, US-005-06). See `GAP-BE-019` in `backend.coverage`.
- The residency infrastructure, scans and compliance report (`residency` module).
- Versioning, tagging or publishing the contract package. The orchestrator owns these (core §2, backend B3).
- Defining `API-` or `SCHEMA-` ids. DevAgent mints them.
<!-- /SECTION: backend.contract-docs.purpose -->
<!-- SECTION: backend.contract-docs.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**No tables, columns or indexes.** This module persists no data (`ASM-BE-171`). The OpenAPI document is a build artefact generated from the route registry and shipped with the deployment (`ASM-BE-167`). No input specifies storage for it.

**Logical outputs** (not persisted, `ASM-BE-170`): each CI run of the coverage check produces a report containing:

| Field | Meaning | Nullability | Source |
|---|---|---|---|
| implemented endpoint count | number of (method, path) pairs in the route registry | not null | US-004-04, BR-012 |
| documented endpoint count | number of those pairs present in the document | not null | US-004-04 |
| coverage percentage | documented ÷ implemented (`ASM-BE-168`) | null when the document cannot be parsed | US-004-04 |
| missing endpoints | list of (method, path) pairs not documented | empty when coverage is 100% | US-004-04 |
| parse error | description of why the document could not be read | not null only on parse failure | US-004-04 |

The report format and retention are unstated (`GAP-BE-181`).

**Tables belonging to other modules** (notes, tags, login challenges, attempts and locks) are described by those modules and by `backend.domain-model`. They are not repeated here.
<!-- /SECTION: backend.contract-docs.data-model -->
<!-- SECTION: backend.contract-docs.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

The entity with state is the generated document, per build. No input defines this machine; it is derived from the acceptance criteria listed in each row.

| State | Meaning |
|---|---|
| `GENERATED` | Document produced from the route registry |
| `VALIDATED` | Passed spec validation, the coverage check at 100%, and the Scope-section lint |
| `REJECTED` | A check failed; the build fails (**terminal** for that build) |
| `PUBLISHED` | Shipped and served by `API-CONTRACTDOCS-304307`; superseded only by the next build's document |

| From | To | Trigger | Guard | Source |
|---|---|---|---|---|
| `GENERATED` | `VALIDATED` | CI build runs all checks | document parses; every documented type matches code; coverage is 100%; Scope section present | US-004-02, US-004-04, US-005-01 |
| `GENERATED` | `REJECTED` | CI check fails | any guard above is false | US-004-02, US-004-04, US-005-01 |
| `VALIDATED` | `PUBLISHED` | deployment | new endpoints appear in the document before or at the same time they become callable | US-004-01 |

**Coverage-check outcomes** (all inside `GENERATED`):

| Outcome | Condition | Result |
|---|---|---|
| Pass | every implemented endpoint documented | reports 100% |
| Below 100% | at least one implemented endpoint is undocumented | reports less than 100% and names it |
| Parse error | the document is malformed | fails with a clear parsing error and never reports a coverage percentage |

**Terminal states:** `REJECTED`, and `PUBLISHED` once superseded. Publishing a document that skipped `VALIDATED` is not permitted. No HTTP status applies, because the transition is a pipeline decision, not a request (`ASM-BE-169`).
<!-- /SECTION: backend.contract-docs.state-machine -->
<!-- SECTION: backend.contract-docs.requirements | status: UNKNOWN -->
## Requirements

**BE-CONTRACT-DOCS-FR-001: Serve the published document**
- Given `notes_api` is deployed, when a client requests `API-CONTRACTDOCS-304307`, then a valid OpenAPI 3.x document is returned.
- The document lists the CRUD, tagging, search, archive and email one-time-code login (send and verify) endpoints.
- **Traces to.** US-004-01 (AC 1); BR-012; NFR-008.
- **Verification.** `contract_docs.serves_valid_openapi_3x` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-002: Document every implemented endpoint, including this one**
- Given the route registry, when the document is generated, then every implemented endpoint has an entry, including `API-CONTRACTDOCS-304307` itself (`ASM-BE-166`).
- **Traces to.** US-004-01; BR-012; NFR-008.
- **Verification.** `contract_docs.every_route_has_entry` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-003: New endpoints are documented no later than they are callable**
- Given a new endpoint is added, when the document is regenerated and published, then the endpoint appears in it before or at the same time it becomes callable.
- It is generated from the route registry and shipped in the same deployment (`ASM-BE-167`).
- **Traces to.** US-004-01 (AC 2).
- **Verification.** `contract_docs.new_route_documented_on_deploy` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-004: Content negotiation**
- Given a request with an `Accept` header, when the header names a supported representation (JSON or YAML, `ASM-BE-164`), then the document is returned in that representation.
- Given no preference is stated, the response is JSON.
- Given an unsupported `Accept` value, the response is 406 with code `NOT_ACCEPTABLE` (`ASM-BE-163`, `Q-BE-158`).
- The response states its content type clearly in every case.
- **Traces to.** US-004-01 (AC 3); `backend.error-model`.
- **Verification.** `contract_docs.accept_negotiation` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-005: Schemas carry required fields, types and constraints**
- Given an endpoint accepts a note payload, when its schema is inspected in the document, then all required fields, types and constraints are documented.
- Constraints are limited to those supplied; title length, body size and tag format are not (`GAP-BE-008`).
- **Traces to.** US-004-02 (AC 1); BR-012.
- **Verification.** `contract_docs.schemas_have_required_and_types` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-006: Optional parameters are marked with defaults**
- Given an endpoint has optional query parameters (for example search filters), when the schema is reviewed, then each is marked optional and its default value is documented.
- **Traces to.** US-004-02 (AC 2).
- **Verification.** `contract_docs.optional_params_have_defaults` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-007: Type drift fails CI**
- Given a documented field type is changed in code and the document is not updated, when the spec-validation check runs, then the CI check fails and flags the mismatch.
- **Traces to.** US-004-02 (AC 3).
- **Verification.** `contract_docs.type_drift_fails_ci` (planned).
- **API.** None.

**BE-CONTRACT-DOCS-FR-008: Documented error responses**
- Given an endpoint, the document includes its 4xx and 5xx responses, and includes the following:
  - (a) A 400 response with the error structure for any endpoint that can return validation errors.
  - (b) 401 and 403 with their body shape for any endpoint that requires authentication or authorisation. Applicability depends on `Q-BE-003`.
  - (c) For the request-code and verify-code endpoints, a 415 response for more than 3 requests within 10 minutes, and a locked-account response after 5 wrong codes, locked for 15 minutes.
  - (d) A 404 response that matches actual behaviour (`CON-BE-153`).
- The error envelope and code registry are referenced from `backend.error-model`, not restated.
- **Traces to.** US-004-03 (AC 1 to 3); BR-012; NFR-016.
- **Verification.** `contract_docs.error_responses_documented_and_match` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-009: Coverage metric**
- Given the coverage check runs, when all implemented endpoints are documented, then it reports 100%.
- Given an implemented endpoint is undocumented, then it reports less than 100% and names the missing endpoint.
- Given the document is malformed, then it fails with a parsing error and does not report coverage.
- The metric is exposed as the CI job's report (`ASM-BE-170`).
- **Traces to.** US-004-04 (AC 1 to 3); NFR-008.
- **Verification.** `contract_docs.coverage_full`, `contract_docs.coverage_names_missing`, `contract_docs.coverage_parse_error` (planned).
- **API.** None.

**BE-CONTRACT-DOCS-FR-010: Examples**
- Given the search and tagging operations, when the document is viewed, then each has an example request (with query parameters where applicable) and an example response body.
- An example empty-result response is included for search.
- Examples contain no credentials or real-looking PII (core §12).
- **Traces to.** US-004-05; NFR-009.
- **Verification.** `contract_docs.search_and_tagging_examples_present` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-011: Data-handling note**
- Given the document, when an operations engineer reviews it, then a data-handling note states that all note data is stored and served within the EU.
- The note calls out any difference between archive and active endpoints in storage location. The inputs show none: archive is a flag on the same record (BR-007; `backend.data-lifecycle`). The note states there is no difference.
- The note names no specific region (`GAP-BE-002` in `backend.coverage`).
- **Traces to.** US-004-06 (AC 1, 2); BR-013; BR-001.
- **Verification.** `contract_docs.data_handling_note_present` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-012: Scope section**
- Given the published API documentation, when a reader views the Scope section, then mobile apps, attachments and real-time collaboration are listed as explicitly excluded (`ASM-BE-172`).
- **Traces to.** US-005-01 (AC 1); BR-014 to BR-016.
- **Verification.** `contract_docs.scope_section_lists_exclusions` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-013: Scope changes need review; missing Scope fails the build**
- Given a proposed documentation change that adds or changes an out-of-scope item, then it requires review and approval before merge. The mechanism is unstated (`GAP-BE-179`).
- Given a docs build in which the Scope section is missing, then the build fails with a lint or validation error.
- **Traces to.** US-005-01 (AC 2, 3).
- **Verification.** `contract_docs.missing_scope_fails_build` (planned); the review gate is process-level.
- **API.** None.

**BE-CONTRACT-DOCS-FR-014: Interface-change notices**
- Given the interface changes `pinned` with pinned-first ordering, and email one-time-code login as additive with existing login methods unchanged, then the document records each as an interface change that consumers must adapt to.
- How consumers are notified is unstated (`GAP-BE-180`).
- **Traces to.** NFR-014, NFR-015, NFR-016; US-006-06 (consumer).
- **Verification.** `contract_docs.interface_change_notes_present` (planned).
- **API.** `API-CONTRACTDOCS-304307`.
<!-- /SECTION: backend.contract-docs.requirements -->
<!-- SECTION: backend.contract-docs.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-CONTRACTDOCS-304307` | GET | `/openapi` | `contract-docs` | 200 | 406 | Retrieve the published OpenAPI 3.x document covering every implemented endpoint, including schemas, 4xx/5xx errors, examples, the data-residency handling note and the Scope section. |

### `API-CONTRACTDOCS-304307` — GET `/openapi`

Retrieve the published OpenAPI 3.x document covering every implemented endpoint, including schemas, 4xx/5xx errors, examples, the data-residency handling note and the Scope section.

**Request:** 
- `Accept` (string): Content-negotiation header (not a body or query field) selecting the document format. An unsupported value yields 406 or a default-format fallback with a clear content type (US-004-01).
**Response:** 
- `openapi` (string, required): OpenAPI 3.x version string of the published document.
- `info` (object, required): Document metadata, including the Scope section (in-scope and excluded capabilities: attachments, real-time, mobile) and the data-residency handling note.
- `paths` (object, required): Every implemented endpoint with parameters, request bodies, success responses, 4xx/5xx error responses and examples; 100% of implemented endpoints must be present (NFR-008).
- `components` (object, required): Reusable schemas, including the error envelope, referenced by the paths.
<!-- /SECTION: backend.contract-docs.api-contract -->
<!-- SECTION: backend.contract-docs.invariants | status: DERIVED -->
## Invariants

- `INV-BE-154`: After every build that reaches `PUBLISHED`, every implemented endpoint appears in the document (BR-012, NFR-008).
- `INV-BE-155`: The published document is valid OpenAPI 3.x (US-004-01).
- `INV-BE-156`: The published document always contains the Scope section and the data-handling note (US-005-01, BR-013, US-004-06).
- `INV-BE-157`: Documented response statuses and field types match actual behaviour; a mismatch fails CI (US-004-02, US-004-03).
- `INV-BE-158`: The coverage check never reports a percentage for a document it could not parse (US-004-04).
- `INV-BE-159`: No attachment, real-time or mobile-specific endpoint is documented as available. The Scope section lists them only as excluded (BR-014 to BR-016, NFR-010).
- `INV-BE-160`: No example in the document contains a secret or real-looking PII (core §12).
<!-- /SECTION: backend.contract-docs.invariants -->
<!-- SECTION: backend.contract-docs.authorization | status: DRAFT-UNVERIFIED -->
## Authorization

| Operation | Roles permitted | Source |
|---|---|---|
| `API-CONTRACTDOCS-304307` | Any caller, with no credential (`ASM-BE-165`, `Q-BE-159`) | `ASM-BE-165` |

**Ownership predicate.** None. The document is not user-scoped.

**Dependency on blocked decisions.** `backend.auth-model` is `BLOCKED` pending `Q-BE-001`, `Q-BE-002` and `Q-BE-003`. If a credential is later required for all endpoints, this section changes. Nothing else in the module depends on the answer, except the 401/403 documentation in FR-008(b).
<!-- /SECTION: backend.contract-docs.authorization -->
<!-- SECTION: backend.contract-docs.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

There is no scheduled work and no retries. All jobs are CI-triggered (`ASM-BE-169`).

| Job | Trigger | Effect | Source |
|---|---|---|---|
| Generate document | each build | produces the document from the route registry (`ASM-BE-167`) | US-004-01 |
| Spec validation | each build | fails on documented-versus-code type mismatch | US-004-02 |
| Coverage check | each build | reports coverage; fails below 100% or on parse error | US-004-04 |
| Scope-section lint | each docs build | fails if the Scope section is missing | US-005-01 |

**Idempotency.** `API-CONTRACTDOCS-304307` is a GET and is idempotent. The CI jobs are pure functions of the code and document, so they need no idempotency keys.

The CI system, generation tool and mismatch-detection mechanism are unnamed (`GAP-BE-178`).
<!-- /SECTION: backend.contract-docs.jobs -->
<!-- SECTION: backend.contract-docs.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

**Unit**
- Coverage computation: 100%, below 100% naming the missing pair, and parse error (FR-009).
- Content negotiation: JSON, YAML, none, and unsupported (FR-004).
- Scope-section lint: present and absent (FR-013).

**Integration**
- `GET` on `API-CONTRACTDOCS-304307` returns a document that parses as OpenAPI 3.x (FR-001).
- A route added in a test build appears in the document (FR-003).
- Every route in the registry has an entry, including this endpoint (FR-002).
- A type changed in code without a document update fails spec validation (FR-007).

**Contract**
- Documented 400, 404 and 415 responses match actual behaviour (FR-008).
- Search and tagging operations have examples, including the empty search result (FR-010).
- The data-handling note and Scope section are present (FR-011, FR-012).
- No documented path matches an attachment, real-time or mobile pattern (`INV-BE-159`). The enforcement test belongs to `scope-guard`.
<!-- /SECTION: backend.contract-docs.test-plan -->
<!-- SECTION: backend.contract-docs.gaps | status: DERIVED -->
## Gaps, contradictions, assumptions and questions

**Contradiction (non-critical)**
- `CON-BE-153`: US-004-03 AC 3 pairs the precondition "a documented field type is changed in code" with the check "the 404 response matches the spec". The precondition and the check concern different things.
  - This spec reads the criterion as: the documented 404 response must match actual API behaviour (FR-008(d)).
  - The type-change precondition is covered separately by FR-007.

**Questions (degradable, default taken)**
- `Q-BE-158`: For an unsupported `Accept`, return 406 or fall back to a default format? Default: 406 (`ASM-BE-163`).
- `Q-BE-159`: Should `API-CONTRACTDOCS-304307` require a credential? Default: no (`ASM-BE-165`). This is related to the blocking `Q-BE-003`.

**Assumptions**
- `ASM-BE-163`: An unsupported `Accept` returns 406 with code `NOT_ACCEPTABLE`, as in `backend.error-model`.
- `ASM-BE-164`: JSON and YAML are both served, and JSON is returned when no preference is stated.
- `ASM-BE-165`: `API-CONTRACTDOCS-304307` needs no credential.
- `ASM-BE-166`: The endpoint documents itself.
- `ASM-BE-167`: The document is generated from the route registry at build time and shipped in the same deployment as the code.
- `ASM-BE-168`: The coverage unit is a (method, path) pair, and coverage is documented ÷ implemented.
- `ASM-BE-169`: Checks run on every CI build, in parallel with NFR-010's per-build contract test.
- `ASM-BE-170`: The coverage metric is exposed as the CI job report, not as a runtime endpoint. Only one operation is minted for this module.
- `ASM-BE-171`: This module has no persistent storage.
- `ASM-BE-172`: The Scope section lists exactly the three exclusions in US-005-01.

**Gaps** (each names its closing artefact)
- `GAP-BE-178`: The OpenAPI generation tool, CI system, and the mechanism that detects documented-versus-code type mismatch are unnamed. Closing artefact: a `platform.md` addition (see also `GAP-BE-011` in `backend.coverage`).
- `GAP-BE-179`: The review and approval mechanism for Scope-section changes (approver role, enforcement) is unstated. Closing artefact: a process or repository-policy input.
- `GAP-BE-180`: How consumers are notified of interface changes (NFR-014 to NFR-016) is unstated: channel, audience and timing. Closing artefact: an NFR or process input.
- `GAP-BE-181`: The coverage-report format and retention, and the rate-limit class and caching behaviour of `API-CONTRACTDOCS-304307`, are unstated. Closing artefact: an NFR.
- `GAP-BE-182`: The 5xx behaviour when the document is unavailable is unstated. Closing artefact: an acceptance criterion.
- `GAP-BE-183`: The relationship between the served document, the machine-readable `contracts/openapi.yaml` that the orchestrator publishes, and the document version is unstated. Closing artefact: an orchestrator or platform input.

**Referenced, not re-minted:** `GAP-BE-001` (the four inbound handoff files, not supplied), `GAP-BE-002`, `GAP-BE-003`, `GAP-BE-008` and `GAP-BE-011`, all in `backend.coverage`. `Q-BE-001`, `Q-BE-002` and `Q-BE-003` are blocking and sit in the project spec.
<!-- /SECTION: backend.contract-docs.gaps -->
