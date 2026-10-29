---
id: agent-developer/notes_api/backend/modules/contract-docs-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/contract-docs-SPEC.md
depends_on: [notes_api:US-004-01, notes_api:US-004-02, notes_api:US-004-03, notes_api:US-004-04, notes_api:US-004-05, notes_api:US-004-06, notes_api:US-005-01]
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
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
scope: [API-CONTRACTDOCS-304307]
```

`blocked_by` lists `Q-BE-003` only for the part of `BE-CONTRACT-DOCS-FR-008` that documents 401/403. The rest of the module is not blocked.
<!-- /SECTION: backend.contract-docs.frontmatter -->
<!-- SECTION: backend.contract-docs.purpose | status: DERIVED -->
## Purpose

**Responsibility.** This module publishes the machine-readable contract of `notes_api` and keeps it truthful. The module map (`backend.module-map`) assigns it US-004-01 to US-004-06 and US-005-01, with BR-012, BR-013, NFR-008 and NFR-009. It does the following:

- Serves one published OpenAPI 3.x document that covers every implemented endpoint. This includes CRUD, tagging, search, archive and the email one-time-code login operations (US-004-01).
- Ensures the document carries request and response schemas, documented 4xx/5xx errors, example payloads, a data-residency note and a Scope section (US-004-02, US-004-03, US-004-05, US-004-06, US-005-01).
- Computes the spec-versus-route coverage metric and fails the build on a mismatch (US-004-04, US-004-02, BR-012, NFR-008).
- Fails the documentation build when the Scope section is missing (US-005-01).

Per `backend.module-map`, the module reads the route registry only. It does not call the `notes`, `search`, `auth` or `scope-guard` modules at runtime.

**Not responsible for:**

- Defining any endpoint, schema or error code. Each owning module defines its own; this module documents them and restates none (core rule 6). The error vocabulary lives in `backend.error-model`.
- Enforcing the absence of attachment, real-time or mobile-specific endpoints, and refusing calls to them. That belongs to `scope-guard` (BR-014 to BR-016, NFR-010, NFR-011). This module only lists them as excluded in the Scope section.
- Logging and classifying stakeholder requests (BR-017, US-005-06). This is `GAP-BE-009`.
- Provisioning or verifying infrastructure regions. That belongs to `residency`. This module only states the residency fact for documentation (US-004-06, BR-013).
- Authentication and session behaviour, which are blocked on `Q-BE-003`.
- Rendering the `api-contract` section and `contracts/openapi.yaml`. DevAgent renders them from the operation records.
<!-- /SECTION: backend.contract-docs.purpose -->
<!-- SECTION: backend.contract-docs.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**This module owns no database tables** (`ASM-BE-164`). No input defines persistent state for it. It owns build artefacts only, and none is a database entity, so there are no columns, constraints or indexes.

| Artefact | Content | Nullable / default | Source |
|---|---|---|---|
| Published OpenAPI document | One OpenAPI 3.x document with paths, schemas, 4xx/5xx errors, examples, data-residency note and Scope section | Always present once a build has published. Before the first publish it is absent (`GAP-BE-170`). | US-004-01, US-004-02, US-004-03, US-004-05, US-004-06, US-005-01 |
| Coverage result | Percentage of implemented endpoints present in the published document; implemented and documented counts; the method and path of each missing endpoint; a parse error when the document is malformed | The missing-endpoint list is empty at 100% coverage. The metric is not produced if the document does not parse. | US-004-04, NFR-008 |
| Route registry | The set of implemented endpoints, as the comparison source | Read-only | BR-012 |

Field names and types of the coverage result are not given by any input. They are fixed when the coverage check is implemented (`GAP-BE-167`).

**Timestamps and PII.** No timestamp column exists. Documents and examples must hold no PII or credential values. Masking rules are in `BE-CONTRACT-DOCS-FR-010` and `INV-BE-155`.
<!-- /SECTION: backend.contract-docs.data-model -->
<!-- SECTION: backend.contract-docs.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

The stateful entity is a **documentation build**, not a database row. The inputs imply these states (`ASM-BE-168`):

| State | Meaning |
|---|---|
| `GENERATED` | A candidate document was produced from the code and the route registry. |
| `VALIDATING` | The candidate is checked for parse validity, spec-versus-code type match, Scope section presence and coverage. |
| `FAILED` | A check failed. The build stops. |
| `PUBLISHED` | The candidate passed all checks and is the document the endpoint serves. |

| Transition | Trigger | Guard | Source |
|---|---|---|---|
| `GENERATED` → `VALIDATING` | Build runs | Document parses as OpenAPI 3.x | US-004-01 |
| `VALIDATING` → `PUBLISHED` | All checks pass | Scope section present; every implemented endpoint documented; documented types match code | US-004-02, US-004-04, US-005-01 |
| `VALIDATING` → `FAILED` | Malformed document | A parsing error is reported, never a false coverage figure | US-004-04 |
| `VALIDATING` → `FAILED` | A documented field type differs from code | CI check fails and names the mismatch | US-004-02 |
| `VALIDATING` → `FAILED` | Scope section missing | The documentation build fails with a lint/validation error | US-005-01 |
| `VALIDATING` → `FAILED` | An implemented endpoint is missing from the spec | Coverage is below 100% and the missing endpoint is named | US-004-04 |
| `PUBLISHED` → `PUBLISHED` (new build) | A new endpoint is added and the document is regenerated | The new endpoint appears no later than it becomes callable | US-004-01 |

**Terminal states.** `FAILED` is terminal for that build, and a later build starts again from `GENERATED`. No input makes `PUBLISHED` final. A new passing build supersedes it.

**Illegal transitions.** A `FAILED` build never becomes `PUBLISHED`, and it never replaces the previously published document (`INV-BE-154`). This is a build-pipeline outcome, not an HTTP response, so no status or error code applies. The endpoint's behaviour before any publish is open (`GAP-BE-170`).
<!-- /SECTION: backend.contract-docs.state-machine -->
<!-- SECTION: backend.contract-docs.requirements | status: UNKNOWN -->
## Requirements

<!-- status note: this section is DRAFT-UNVERIFIED because FR-004, FR-005 and FR-008 rest on assumptions or open gaps. -->

All requirements are `DRAFT-UNVERIFIED` where they cite an `ASM-` or `GAP-`. Each carries Traces to, Verification and API as core rule 7 requires.

**BE-CONTRACT-DOCS-FR-001: serve the published document.**
- Given `notes_api` is deployed and a document has been published, when a client requests `API-CONTRACTDOCS-304307`, then the response is 200 with a valid OpenAPI 3.x document.
- **Traces to.** US-004-01.
- **Verification.** `contract_docs.serves_valid_openapi_3x` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-002: complete endpoint listing.**
- Given the route registry, when the document is published, then it lists all CRUD, tagging, search, archive and email one-time-code login (request-code and verify-code) operations (INV-BE-151).
- **Traces to.** US-004-01, BR-012, NFR-008.
- **Verification.** `contract_docs.lists_every_registered_route` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-003: new endpoint appears with or before its availability.**
- Given a new endpoint is added to `notes_api`, when the spec is regenerated and published, then the endpoint appears in the document before or at the same time it becomes callable.
- **Traces to.** US-004-01.
- **Verification.** `contract_docs.new_endpoint_documented_before_callable` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-004: Accept negotiation.**
- Given the requested Accept header names a format the service cannot produce, when the document is requested, then the response is 406 (`ASM-BE-170`, `Q-BE-155`). US-004-01 also allows a default-format fallback with a clear content type, and the 406 reading is the one the operation record lists.
- **Traces to.** US-004-01.
- **Verification.** `contract_docs.unsupported_accept_returns_406` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-005: schemas with required fields and types.**
- Given an endpoint accepts a note payload, when its schema is inspected, then all required fields, types and constraints are documented (US-004-02 cites title length and tag format as examples).
- The constraint values come from the owning modules. They are open for tags (`GAP-BE-010`) and are never invented here (`GAP-BE-171`).
- **Traces to.** US-004-02, BR-012.
- **Verification.** `contract_docs.schemas_declare_required_fields_and_types` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-006: optional parameters and defaults.**
- Given an endpoint has optional query parameters such as search filters, when its schema is reviewed, then each is marked optional with its default documented.
- **Traces to.** US-004-02.
- **Verification.** `contract_docs.optional_params_marked_with_defaults` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-007: spec-versus-code drift fails CI.**
- Given a documented field type is changed in code and the spec is not updated, when the spec-validation check runs, then it fails and flags the mismatch.
- **Traces to.** US-004-02.
- **Verification.** `contract_docs.type_drift_fails_ci` (planned).
- **API.** none.

**BE-CONTRACT-DOCS-FR-008: documented errors.**
- Given an endpoint can return validation errors, when the spec is reviewed, then a 400 response schema is documented.
- Given an endpoint requires authentication or authorisation, then 401/403 responses are documented with the expected body shape. This part is blocked on `Q-BE-003`.
- Given the email one-time-code login operations, then the spec documents a 429 for more than 3 requests within 10 minutes and a locked-account response after 5 wrong codes, locked for 15 minutes. The statuses are those in `backend.error-model` (BR-033). 429 is documented on the request-code operation only (`CON-BE-155`).
- Given the 404 response is checked, then it matches the actual API behaviour. The source criterion is garbled (`GAP-BE-168`).
- Error bodies use the envelope in `backend.error-model` (`GAP-BE-011`).
- **Traces to.** US-004-03, BR-028, BR-033.
- **Verification.** `contract_docs.error_responses_match_runtime` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-009: coverage check.**
- Given all implemented endpoints are documented, when the coverage check runs, then it reports 100%.
- Given one implemented endpoint is missing from the spec, then it reports less than 100% and names the missing endpoint.
- Given the spec file is malformed, then the check fails with a clear parsing error and never reports false coverage.
- The check runs in the build (`ASM-BE-167`). Whether it is also exposed at runtime is open (`GAP-BE-167`).
- **Traces to.** US-004-04, BR-012, NFR-008.
- **Verification.** `contract_docs.coverage_full`, `contract_docs.coverage_names_missing`, `contract_docs.coverage_malformed_spec_errors` (planned).
- **API.** none.

**BE-CONTRACT-DOCS-FR-010: example payloads.**
- Given the search endpoint is documented, when the spec is viewed, then it shows an example request with query parameters and an example response body.
- It also includes an example empty-result response.
- Tagging operations carry example request and response payloads (NFR-009 target: 100% of search and tagging endpoints).
- Examples use masked or placeholder values for email addresses and never show a real code (`ASM-BE-172`, standing security constraint).
- **Traces to.** US-004-05, NFR-009.
- **Verification.** `contract_docs.search_and_tagging_have_examples`, `contract_docs.examples_hold_no_pii_or_codes` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-011: data-residency note.**
- Given the API documentation, when an operations engineer reads it, then it states that all note data is stored and served within the EU.
- Any difference in storage location between archived and active notes is explicitly called out. None is expected (`ASM-BE-169`), and the note says so explicitly.
- **Traces to.** US-004-06, BR-013.
- **Verification.** `contract_docs.residency_note_present` (planned).
- **API.** `API-CONTRACTDOCS-304307`.

**BE-CONTRACT-DOCS-FR-012: Scope section.**
- Given the published documentation, when a reader views the Scope section, then mobile apps, attachments and real-time collaboration are listed as explicitly excluded.
- Given the Scope section is missing from a build, then the documentation build fails with a lint/validation error.
- Given a change to the out-of-scope list is proposed, then it requires review and approval before merge. This is a repository process and not service behaviour (`ASM-BE-171`).
- **Traces to.** US-005-01, BR-014, BR-015, BR-016.
- **Verification.** `contract_docs.scope_section_lists_exclusions`, `contract_docs.missing_scope_fails_build` (planned).
- **API.** `API-CONTRACTDOCS-304307`.
<!-- /SECTION: backend.contract-docs.requirements -->
<!-- SECTION: backend.contract-docs.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-CONTRACTDOCS-304307` | GET | `/openapi` | `contract-docs` | 200 | 406, 500 | Return the published OpenAPI 3.x document covering every implemented endpoint, with schemas, 4xx/5xx error responses, example payloads, the data-residency note and the Scope section. The format is negotiated through the Accept header (US-004-01 to US-004-06, US-005-01). No input names the path; it is an assumption to be confirmed. |

### `API-CONTRACTDOCS-304307` — GET `/openapi`

Return the published OpenAPI 3.x document covering every implemented endpoint, with schemas, 4xx/5xx error responses, example payloads, the data-residency note and the Scope section. The format is negotiated through the Accept header (US-004-01 to US-004-06, US-005-01). No input names the path; it is an assumption to be confirmed.

**Request:** 
- `Accept` (string): Header, not a body field. Requested media type of the document. If it is unsupported, the service either returns 406 or falls back to the default format; this choice is open (US-004-01).
**Response:** 
- `openapi` (string, required): OpenAPI 3.x version string of the published document.
- `info` (object, required): Document metadata. It carries the data-residency note (US-004-06, BR-013) and the Scope section stating that attachments, real-time channels and mobile-specific endpoints are out of scope (US-005-01, BR-014 to BR-016).
- `paths` (object, required): Every implemented endpoint with its parameters, request bodies, success responses and documented 4xx/5xx error responses. Covers 100% of implemented routes (NFR-008).
- `components` (object, required): Reusable schemas, including the error body, plus example payloads. Search and tagging endpoints have examples (NFR-009).
<!-- /SECTION: backend.contract-docs.api-contract -->
<!-- SECTION: backend.contract-docs.invariants | status: DERIVED -->
## Invariants

- **INV-BE-151.** After every published build, every endpoint in the route registry appears in the published document (BR-012, NFR-008).
- **INV-BE-152.** After every published build, the document contains a Scope section listing mobile apps, attachments and real-time collaboration as excluded (US-005-01).
- **INV-BE-153.** After every published build, the document parses as valid OpenAPI 3.x (US-004-01, US-004-04).
- **INV-BE-154.** A `FAILED` build never replaces the currently published document (derived from the state machine: only `VALIDATING` → `PUBLISHED` publishes).
- **INV-BE-155.** No published document, example or coverage result contains a login code, a credential, or an unmasked email address or other real-looking PII (standing security constraint; NFR-018).
- **INV-BE-156.** The coverage result never reports a percentage computed from a document that failed to parse (US-004-04).
<!-- /SECTION: backend.contract-docs.invariants -->
<!-- SECTION: backend.contract-docs.authorization | status: DRAFT-UNVERIFIED -->
## Authorization

No input says who may fetch the published document. US-004-01 names the requester as the "integration developer building the web_app front end" and says only "client requests the OpenAPI document". Roles are undefined in `backend.auth-model` (BLOCKED).

| Operation | Caller | Permitted |
|---|---|---|
| `API-CONTRACTDOCS-304307` | Any caller, unauthenticated | Yes (`ASM-BE-166`, `Q-BE-154`) |

**Ownership predicate.** None. The document is not user-scoped.

The assumption is revisited once `Q-BE-003` fixes whether other endpoints require a session. The document must never describe credentials or secrets (`INV-BE-155`).
<!-- /SECTION: backend.contract-docs.authorization -->
<!-- SECTION: backend.contract-docs.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

**No scheduled jobs.** No input asks for periodic work in this module.

**Build-time jobs** (`ASM-BE-167`), run on each documentation build:

| Job | Trigger | Output |
|---|---|---|
| Generate document | Build | Candidate OpenAPI document (US-004-01) |
| Validate | Build, after generation | Parse validity, spec-versus-code type match, Scope section presence (US-004-02, US-005-01) |
| Coverage check | Build, after validation | Coverage result (US-004-04) |

**Retries.** None specified. A failing build is fixed by changing the input and building again.

**Idempotency.**
- `GET` on the document is read-only and idempotent.
- Regenerating from an unchanged route registry yields the same document.
- No idempotency key is needed.

The CI system that runs these jobs is not named in any input (`GAP-BE-169`).
<!-- /SECTION: backend.contract-docs.jobs -->
<!-- SECTION: backend.contract-docs.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

**Unit**
- Coverage computation: full, partial, empty registry, malformed document (FR-009).
- Scope-section lint: present, missing, partial (FR-012).
- Example masking check: rejects an unmasked email address or a login code (FR-010).
- Accept parsing: supported, unsupported, absent (FR-004).

**Integration**
- Fetch the document from a deployed `notes_api` and validate it as OpenAPI 3.x (FR-001).
- Add a route without documenting it. The build fails and names it (FR-002, FR-003, FR-009).
- Change a field type in code without updating the spec. CI fails (FR-007).
- Remove the Scope section. The documentation build fails (FR-012).
- Check that documented login error responses match runtime behaviour for the status mapping in `backend.error-model` (FR-008).

**Contract**
- Round-trip: every route in the route registry maps to a path and method in the document, and no path in the document lacks a route.
- Search and tagging operations have example requests and responses, including an empty search result (FR-010).
- The residency note is present and states EU storage and serving (FR-011).
- Documented 4xx/5xx responses match observed responses for each endpoint (FR-008). Run after `Q-BE-003` is answered for 401/403.
- `scope-guard` owns the test that no excluded endpoint exists. This module's contract test only checks that none is *documented*, to avoid re-specifying `scope-guard`.
<!-- /SECTION: backend.contract-docs.test-plan -->
<!-- SECTION: backend.contract-docs.gaps | status: DRAFT-UNVERIFIED -->
## Gaps, contradictions, assumptions and questions

### Questions (both `degradable`, unattended: defaults taken)

- **Q-BE-154** (degradable): Must the published document endpoint require authentication? Default taken: no (`ASM-BE-166`).
- **Q-BE-155** (degradable): For an unsupported Accept header, return 406 or fall back to a default format? Default taken: 406 (`ASM-BE-170`).

### Contradictions

- **CON-BE-155** (non-critical): US-004-03 says "the send/verify login endpoints document a 429 HTTP status response for more than 3 requests within 10 minutes". BR-028 and BR-032 limit only requests for a code. The verify refusals in BR-033 are 423, 401, 410 and 409, and none is a rate limit. Documenting 429 on verify would invent behaviour; omitting it breaks the story. Reading adopted: 429 on request-code only, the most specific rules (BR-028, BR-032, BR-033). This also appears as part of `GAP-BE-008`.
- Existing and relevant: `CON-BE-002`. The status of out-of-scope refusal (404 versus a structured body) affects what the Scope section and error documentation say. Referenced, not re-specified.

### Gaps

| ID | What is missing | Degrades | Fallback | Closing artefact |
|---|---|---|---|---|
| `GAP-BE-167` | Field names of the coverage result, and whether it is exposed at runtime or only in the build report. US-004-04 says "expose", but no operation record exists for it. | FR-009 | Build-time check only (`ASM-BE-167`) | A decision on exposure and the result shape |
| `GAP-BE-168` | The third criterion of US-004-03 is garbled ("a documented field type is changed in code, when the 404 response is checked"). Its intended check is unclear. | FR-008 | 404 documentation must match runtime behaviour | A clean US-004-03 criterion |
| `GAP-BE-169` | No input names the CI system, the OpenAPI generation tool or the validator. `platform.md` names Node.js 20, Express, PostgreSQL 16 and AWS only. | Jobs, state machine | Tooling left unnamed | Platform input naming them |
| `GAP-BE-170` | What the endpoint returns when no document has yet been published. | State machine, FR-001 | Not specified | A decision on this response |
| `GAP-BE-171` | Constraint values for the schema (title length, tag format) are not given. US-004-02 says they must be documented. | FR-005 | None stated; not invented here (also `GAP-BE-010`) | Constraint decisions from the owning modules |
| `GAP-BE-172` | Media-type names for the formats, the default format when Accept is absent, and the OpenAPI 3.x minor version. US-004-01 says "JSON/YAML" and "3.x" only. | FR-001, FR-004 | JSON assumed as default (`ASM-BE-165`) | A format decision |

Also referenced and unchanged: `GAP-BE-009`, `GAP-BE-010`, `GAP-BE-011`, `GAP-BE-001` (the inbound handoff bodies were not supplied) and `Q-BE-003`.

### Assumptions

- **ASM-BE-164.** This module owns no database tables. Its state is build artefacts. To confirm: the platform owner.
- **ASM-BE-165.** When Accept is absent or accepts any type, the default format is JSON. US-004-01 names "JSON/YAML" but no default (`GAP-BE-172`).
- **ASM-BE-166.** The published document endpoint is unauthenticated (`Q-BE-154`).
- **ASM-BE-167.** The document is generated, validated and its coverage computed during the documentation build. The endpoint serves the artefact of the last passing build and does not generate per request.
- **ASM-BE-168.** The four-state build lifecycle in `backend.contract-docs.state-machine` is derived from the build failures US-004-02, US-004-04 and US-005-01 name.
- **ASM-BE-169.** Archived and active notes live in the same EU-region store, because archive is a flag and not a move (BR-007). The residency note therefore states no difference between them.
- **ASM-BE-170.** An unsupported Accept header returns 406. The operation record lists 406 and US-004-01 allows either reading (`Q-BE-155`).
- **ASM-BE-171.** Review and approval of changes to the out-of-scope list is a repository process (code review), not behaviour of this service.
- **ASM-BE-172.** Examples in the document use masked placeholders for email addresses and codes. No input supplies example values, so none are reproduced here.
- **ASM-BE-173.** The path `/openapi` is taken from the operation record, which marks it an assumption to be confirmed. No input names the path.
<!-- /SECTION: backend.contract-docs.gaps -->
