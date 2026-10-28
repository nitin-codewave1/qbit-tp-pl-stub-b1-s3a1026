---
id: agent-developer/notes_api/backend/modules/api-docs-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/api-docs-SPEC.md
depends_on: [notes_api:US-004-01, notes_api:US-004-02, notes_api:US-004-04, notes_api:US-004-05, notes_api:US-004-06, notes_api:US-005-01]
commit_kind: append
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: notes_api:backend/modules/api-docs-SPEC.md
---

<!-- SECTION: backend.api-docs.frontmatter | status: DERIVED -->
```yaml
id: api-docs
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-005, CON-BE-006, Q-BE-151]
scope: [API-APIDOCS-098363]
```
<!-- /SECTION: backend.api-docs.frontmatter -->
<!-- SECTION: backend.api-docs.purpose | status: DERIVED -->
## 1. Purpose

**Responsibility.** The `api-docs` module owns the published OpenAPI document for notes_api and the checks that keep it complete and accurate. [backend.module-map; stories.md US-004-01, US-004-02, US-004-04, US-004-05, US-004-06, US-005-01]

1. **Serve the document.** `API-APIDOCS-098363` returns an OpenAPI 3.x document that lists all CRUD, tagging, search, archive and email one-time-code login (send and verify) endpoints. [US-004-01]
2. **Completeness.** Every implemented endpoint has an entry with request and response schemas and its 4xx/5xx errors. [BR-012, NFR-008]
3. **Schema documentation.** Required fields, types and constraints are documented, and optional parameters are marked optional with their defaults. [US-004-02]
4. **Spec-versus-code integrity.** A documented field type that differs from the code fails a validation check. [US-004-02]
5. **Coverage metric.** The module computes the share of implemented endpoints present in the spec and names any that are missing. [US-004-04]
6. **Examples.** Search and tagging operations carry example requests and responses, including an empty-result example. [US-004-05, NFR-009]
7. **Data-handling note.** The document states where active and archived note data is stored and served, and calls out any difference between them. [US-004-06, BR-013]
8. **Scope section.** The document lists mobile apps, attachments and real-time collaboration as explicitly excluded. [US-005-01]
9. **Interface-change documentation.** The document describes the `pinned` field and the pinned-first ordering [NFR-014], and the email one-time-code endpoints as an additive change [NFR-016].

**Not responsible for.**

- Defining any endpoint, schema, status code or error code. These belong to the module that owns the operation and to `backend.error-model`. This module documents them by reading those records and restates none (core rule 6).
- Minting `API-` or `SCHEMA-` identifiers. DevAgent mints them.
- The scope-guardrail contract test suite and runtime rejection of out-of-scope calls (`scope-guardrail`). It reads this module's output. [module-map]
- Residency enforcement, scanning, the compliance report and the launch gate (`residency-compliance`). This module only states residency facts supplied by inputs.
- Notifying consumers of interface changes (`GAP-BE-822`).
- Logging or classifying stakeholder requests. [BR-017]
- Any UI.

**Operation owned.** `API-APIDOCS-098363`, referenced by id only.

**Shared story.** US-004-03 (documented 4xx/5xx for each endpoint) is mapped to `api-docs`, `auth` and `error-model` in `backend.coverage`. This module requires that errors be documented (BE-API-DOCS-FR-006). It defines no status.
<!-- /SECTION: backend.api-docs.purpose -->
<!-- SECTION: backend.api-docs.data-model | status: DRAFT-UNVERIFIED -->
## 2. Data model

**No database tables.** No input says the document or the coverage result is persisted in PostgreSQL. This module appears in no migration of `backend.migration-plan`. The document is treated as a generated build artefact (`ASM-BE-601`).

The logical artefacts below are not tables. The coverage report is a build result. Where it is stored and how it is exposed are `GAP-BE-817`.

**Published OpenAPI document (artefact)**

| Part | Content | Nullability | Source |
|---|---|---|---|
| OpenAPI version | a 3.x version | required | US-004-01 |
| Paths | one entry per implemented endpoint | required, non-empty | BR-012 |
| Schemas | request and response shapes, with required fields, types and constraints | required per endpoint | US-004-02 |
| Error responses | 4xx/5xx per endpoint, referencing `backend.error-model` | required per endpoint | BR-012, US-004-03 |
| Examples | search and tagging request and response examples, including an empty search result | required for search and tagging | NFR-009, US-004-05 |
| Data-handling note | where active and archived note data is stored and served | required | BR-013, US-004-06 |
| Scope section | the exclusions: mobile apps, attachments, real-time collaboration | required | US-005-01 |
| Interface-change notes | `pinned` field and ordering; additive login endpoints | required | NFR-014, NFR-016 |

**Coverage report (build result)**

| Field | Meaning | Constraint |
|---|---|---|
| implemented endpoint count | endpoints in the route registry | integer, at least 0 |
| documented endpoint count | those present in the spec | integer, at most the implemented count |
| coverage percent | documented over implemented | 100 only when no implemented endpoint is missing and the spec parsed |
| missing endpoints | names of implemented endpoints absent from the spec | empty when coverage is 100 |
| outcome | `COVERED`, `INCOMPLETE` or `PARSE_ERROR` | see `backend.api-docs.state-machine` |
| example coverage | share of search and tagging endpoints with example payloads | target 100% [NFR-009] |

**Constraints.**

- Every example is masked and contains no real-looking passport number, date of birth, phone number or email address, and no credential. [core rule 12]
- Field-level constraints such as title length and tag format cannot be documented until they exist (`GAP-BE-035`).
- Page size defaults and maximum are `GAP-BE-500`.

**Indexes.** None.

**PII and encryption.** The artefact stores no personal data. No encryption claim is made (`GAP-BE-304`).
<!-- /SECTION: backend.api-docs.data-model -->
<!-- SECTION: backend.api-docs.state-machine | status: DRAFT-UNVERIFIED -->
## 3. State machine

The entity is a **documentation build**. It is a derived lifecycle, because the inputs state outcomes but name no states (`ASM-BE-601`, `ASM-BE-605`).

States: `GENERATED`, `VALIDATING`, `REJECTED` (terminal), `PUBLISHED` (terminal for that build).

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| none | `GENERATED` | build runs | none | document built from the route registry and operation records |
| `GENERATED` | `VALIDATING` | checks start | none | checks run (`ASM-BE-605`) |
| `VALIDATING` | `REJECTED` | spec does not parse | none | fails with a clear parsing error; no coverage figure is reported [US-004-04] |
| `VALIDATING` | `REJECTED` | Scope section missing | none | fails with a lint/validation error [US-005-01] |
| `VALIDATING` | `REJECTED` | documented field type differs from code | none | CI check fails and flags the mismatch [US-004-02] |
| `VALIDATING` | `REJECTED` | coverage below 100% | `ASM-BE-606` | the missing endpoint is named [US-004-04] |
| `VALIDATING` | `PUBLISHED` | all checks pass | coverage 100%, parses, Scope section present, no type mismatch | document becomes the one served by `API-APIDOCS-098363` |

- `REJECTED` and `PUBLISHED` are terminal for a build. The next build starts at `GENERATED`.
- The document served is the latest `PUBLISHED` one.
- A new endpoint must appear in the published document before or at the same time it becomes callable. [US-004-01, `INV-BE-210`]
- What happens when no document has ever been published is `GAP-BE-824`.
- An illegal transition has no HTTP response, because a build is not a request.
<!-- /SECTION: backend.api-docs.state-machine -->
<!-- SECTION: backend.api-docs.requirements | status: DRAFT-UNVERIFIED -->
## 4. Requirements

### BE-API-DOCS-FR-001 Serve the OpenAPI document
**Given** a deployed notes_api,
**When** a client calls `API-APIDOCS-098363`,
**Then** a valid OpenAPI 3.x document is returned with status 200, listing all CRUD, tagging, search, archive, and email one-time-code login (send and verify) endpoints, and itself.
- **Traces to.** US-004-01; BR-012; NFR-008; `backend.error-model` (200 for a successful read).
- **Verification.** `test-apidocs-serve-valid-document` (planned).
- **API.** `API-APIDOCS-098363`.
- **Status.** Format and content type are `ASM-BE-604` and `GAP-BE-819`. Authentication is `GAP-BE-820`.

### BE-API-DOCS-FR-002 Unsupported Accept header
**Given** a request with an unsupported Accept header,
**When** `API-APIDOCS-098363` is called,
**Then** the response is 406 with the error code `NOT_ACCEPTABLE` (`ASM-BE-603`), or a default-format fallback with a clear content type.
- **Traces to.** US-004-01; `backend.error-model`.
- **Verification.** `test-apidocs-accept-unsupported` (planned).
- **API.** `API-APIDOCS-098363`.
- **Status.** The choice between 406 and fallback is `GAP-BE-309`.

### BE-API-DOCS-FR-003 New endpoint appears no later than it is callable
**Given** a new endpoint added to notes_api,
**When** the spec is regenerated and published,
**Then** the endpoint appears in the document before or at the same time it becomes callable.
- **Traces to.** US-004-01; `INV-BE-210`.
- **Verification.** `test-apidocs-new-endpoint-listed` (planned).
- **API.** `API-APIDOCS-098363`.
- **Status.** Generation method is `GAP-BE-818`.

### BE-API-DOCS-FR-004 Document schemas and constraints
**Given** an endpoint that accepts a note payload,
**When** its schema is inspected in the spec,
**Then** all required fields, types and constraints are documented.
- **Traces to.** US-004-02; BR-012.
- **Verification.** `test-apidocs-note-schema-complete` (planned).
- **API.** `API-APIDOCS-098363`.
- **Status.** Title length and tag format are `GAP-BE-035`.

### BE-API-DOCS-FR-005 Document optional parameters
**Given** an endpoint with optional query parameters,
**When** its schema is reviewed,
**Then** each is marked optional and its default is documented. For search, `include_archived` defaults to excluding archived notes.
- **Traces to.** US-004-02; BR-009.
- **Verification.** `test-apidocs-optional-params-defaults` (planned).
- **API.** `API-APIDOCS-098363`.
- **Status.** Page size defaults are `GAP-BE-500`.

### BE-API-DOCS-FR-006 Document errors per endpoint
**Given** any implemented endpoint,
**When** the spec is reviewed,
**Then** it carries the 4xx/5xx responses that the owning module and `backend.error-model` define for it, each with its error structure.
- This module restates no status and defines no error code.
- **Traces to.** BR-012; NFR-008; US-004-03 (shared).
- **Verification.** `test-apidocs-errors-match-records` (planned).
- **API.** `API-APIDOCS-098363`.

### BE-API-DOCS-FR-007 Detect spec-versus-code drift
**Given** a documented field type is changed in code and the spec is not updated,
**When** the spec-validation CI check runs,
**Then** it fails and flags the mismatch.
- **Traces to.** US-004-02.
- **Verification.** `test-apidocs-type-drift-fails` (planned).
- **API.** `API-APIDOCS-098363`.
- **Status.** Trigger is `ASM-BE-605`. Mechanism is `GAP-BE-818`.

### BE-API-DOCS-FR-008 Coverage metric
**Given** the route registry and the published spec,
**When** the coverage check runs,
**Then** it reports 100% if every implemented endpoint is documented. Otherwise it reports less than 100% and names each missing endpoint. If the spec is malformed it fails with a parsing error and reports no coverage figure.
- **Traces to.** US-004-04; BR-012; NFR-008.
- **Verification.** `test-apidocs-coverage-full`, `test-apidocs-coverage-missing-named`, `test-apidocs-coverage-malformed` (planned).
- **API.** `API-APIDOCS-098363`.
- **Status.** Exposure is `GAP-BE-817`. Effect of less than 100% on publication is `ASM-BE-606`.

### BE-API-DOCS-FR-009 Examples for search and tagging
**Given** the search and tagging operations,
**When** the spec is viewed,
**Then** each carries an example request (with query parameters for search) and an example response body, and search also carries an example empty-result response. All examples are masked and contain no real-looking PII or credential.
- **Traces to.** US-004-05; NFR-009.
- **Verification.** `test-apidocs-examples-present`, `test-apidocs-examples-no-pii` (planned).
- **API.** `API-APIDOCS-098363`.

### BE-API-DOCS-FR-010 Data-handling note
**Given** the published document,
**When** an operations engineer reviews its data-handling note,
**Then** it states that all note data is stored and served within the EU, and states any difference in storage location between archive endpoints and active-note endpoints. Under `ASM-BE-602` there is no difference.
- **Traces to.** US-004-06; BR-013; BR-001; BR-007.
- **Verification.** `test-apidocs-data-handling-note` (planned).
- **API.** `API-APIDOCS-098363`.
- **Status.** Naming the approved regions is `BLOCKED` on `Q-BE-151`. The note covers note data only. Whether the email address leaves the EU is `Q-BE-005` and `CON-BE-006`, and the note makes no claim about it.

### BE-API-DOCS-FR-011 Scope section
**Given** the published document,
**When** a reader views the Scope section,
**Then** mobile apps, attachments and real-time collaboration are listed as explicitly excluded.
- **Traces to.** US-005-01; BR-014 to BR-016.
- **Verification.** `test-apidocs-scope-section-present` (planned).
- **API.** `API-APIDOCS-098363`.

### BE-API-DOCS-FR-012 Scope section is build-enforced and change-reviewed
**Given** a docs build whose output lacks the Scope section,
**When** the build runs,
**Then** the build fails with a lint/validation error. **Given** a proposed addition or change to an out-of-scope item, **when** it is submitted, **then** it requires review and approval before merge.
- **Traces to.** US-005-01.
- **Verification.** `test-apidocs-scope-missing-fails-build` (planned). The approval rule is process, not code.
- **API.** `API-APIDOCS-098363`.
- **Status.** The approval mechanism is `GAP-BE-821`.

### BE-API-DOCS-FR-013 Interface-change documentation
**Given** the `pinned` field and pinned-first ordering, and the email one-time-code endpoints,
**When** the document is published,
**Then** the `pinned` field and the ordering are documented as an interface change [NFR-014]. The send and verify endpoints are documented as additive, with existing login methods unchanged [NFR-016].
- **Traces to.** NFR-014; NFR-015; NFR-016.
- **Verification.** `test-apidocs-interface-change-notes` (planned).
- **API.** `API-APIDOCS-098363`.
- **Status.** Consumer notification is `GAP-BE-822`.

### Story coverage

| Story | Requirements |
|---|---|
| US-004-01 | BE-API-DOCS-FR-001, 002, 003, 013 |
| US-004-02 | BE-API-DOCS-FR-004, 005, 007 |
| US-004-04 | BE-API-DOCS-FR-008 |
| US-004-05 | BE-API-DOCS-FR-009 |
| US-004-06 | BE-API-DOCS-FR-010 |
| US-005-01 | BE-API-DOCS-FR-011, 012 |
<!-- /SECTION: backend.api-docs.requirements -->
<!-- SECTION: backend.api-docs.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-APIDOCS-098363` | GET | `/openapi.json` | `api-docs` | 200 | 406 | Fetch the published OpenAPI document for notes_api. The document covers every endpoint, with examples for search and tagging endpoints. It also carries the data-handling note (EU residency of active and archived note data) and the Scope section (attachments, real-time and mobile-specific calls excluded). The path is proposed because no input supplies one (GAP-BE-303). |

### `API-APIDOCS-098363` — GET `/openapi.json`

Fetch the published OpenAPI document for notes_api. The document covers every endpoint, with examples for search and tagging endpoints. It also carries the data-handling note (EU residency of active and archived note data) and the Scope section (attachments, real-time and mobile-specific calls excluded). The path is proposed because no input supplies one (GAP-BE-303).

**Request:** none
**Response:** 
- `openapi` (string, required): OpenAPI specification version the document conforms to.
- `info` (object, required): API metadata. It carries the title, the version, the data-handling note stating where active and archived note data is stored and served (BR-013, US-004-06), and the Scope section stating the excluded attachment, real-time and mobile-specific calls (BR-014 to BR-016, US-005-01).
- `paths` (object, required): Every endpoint with parameters, request and response schemas, and the documented error statuses (BR-012, NFR-008). Search and tagging endpoints include examples (NFR-009).
- `components` (object): Shared schemas, including the error envelope and the error-code registry, referenced from paths.
<!-- /SECTION: backend.api-docs.api-contract -->
<!-- SECTION: backend.api-docs.invariants | status: DRAFT-UNVERIFIED -->
## 6. Invariants

- `INV-BE-210`. After every publication, each endpoint that is callable appears in the published document. A new endpoint is never callable before it is documented. [US-004-01, BR-012]
- `INV-BE-211`. The coverage check never reports 100% when the spec is malformed or when any implemented endpoint is missing from it. [US-004-04]
- `INV-BE-212`. A published document always contains the Scope section listing mobile apps, attachments and real-time collaboration. A build without it is not published. [US-005-01]
- `INV-BE-213`. No published document carries a field type that differs from the code. A mismatch blocks the build. [US-004-02]
- `INV-BE-214`. Every search and tagging operation in a published document carries an example payload, and search carries an empty-result example. [NFR-009, US-004-05]
- `INV-BE-215`. The published document and its examples contain no credential and no real-looking PII. [core rule 12]
- `INV-BE-216`. `API-APIDOCS-098363` changes no state. The document served is the latest published one. [derived from `backend.api-docs.state-machine`]
<!-- /SECTION: backend.api-docs.invariants -->
<!-- SECTION: backend.api-docs.authorization | status: BLOCKED -->
## 7. Authorization

**Blocked.** Whether `API-APIDOCS-098363` is public or requires a credential is not stated in any input (`GAP-BE-820`). It is an authorisation rule, so it is not defaulted.

- **Role matrix.** No input defines API roles, and `backend.auth-model` is itself `BLOCKED`.
- **Default.** The default `ASM-BE-302` (no permission unless an input grants one) would deny every caller. That cannot be right for US-004-01, whose integrator fetches the document to learn the API. This is not recorded as a contradiction, because no input says the document is protected.
- **Ownership predicate.** None applies. The document is not user-scoped and holds no user data (`backend.api-docs.data-model`).

**Unblocked by:** a decision on whether the spec endpoint is public, and the role definitions that `backend.auth-model` waits on.
<!-- /SECTION: backend.api-docs.authorization -->
<!-- SECTION: backend.api-docs.jobs | status: UNKNOWN -->
## 8. Jobs

No scheduled work, sweeps or retries are specified by any input for this module.

- **Build-time checks.** These run per build, not on a schedule:
  - spec parse and validation
  - the type-drift check [US-004-02]
  - the coverage check [US-004-04]
  - Scope section lint [US-005-01]
  - example presence [NFR-009]
- **Trigger.** Every CI build (`ASM-BE-605`). The cadence is not stated by any input for these checks (`GAP-BE-823`).
- **Retries.** None specified.
- **Idempotency.** `API-APIDOCS-098363` is a GET. No idempotency key applies. Each check is a pure function of its inputs.
<!-- /SECTION: backend.api-docs.jobs -->
<!-- SECTION: backend.api-docs.test-plan | status: DRAFT-UNVERIFIED -->
## 9. Test plan

**Unit** (planned)
- `test-apidocs-coverage-percent-calc`: zero, partial and full coverage, and the empty registry.
- `test-apidocs-coverage-missing-named`
- `test-apidocs-scope-lint`: Scope section present, missing, or listing fewer than the three exclusions.
- `test-apidocs-examples-no-pii`: no real-looking PII or credential in any example.

**Integration** (planned)
- `test-apidocs-serve-valid-document`: valid OpenAPI 3.x, with all endpoint groups present.
- `test-apidocs-accept-unsupported`
- `test-apidocs-new-endpoint-listed`: add an endpoint and assert it is documented no later than it is callable.
- `test-apidocs-coverage-malformed`: a malformed spec produces a parse error and no coverage figure.
- `test-apidocs-type-drift-fails`: change a field type in code without updating the spec.
- `test-apidocs-scope-missing-fails-build`

**Contract** (planned)
- `test-apidocs-errors-match-records`: documented errors equal the operation records and `backend.error-model`.
- `test-apidocs-optional-params-defaults`
- `test-apidocs-data-handling-note`: the note states the EU residency claim and any archive-versus-active difference.
- `test-apidocs-interface-change-notes`: the `pinned` field, ordering, and the additive login endpoints.
- Validate the served document with an OpenAPI 3.x validator. The tool is unnamed (`GAP-BE-089`).

**Boundary.** The expiry boundary of login codes is documented elsewhere (`GAP-BE-306`). This module only checks that the document states what the owning module decided.
<!-- /SECTION: backend.api-docs.test-plan -->
<!-- SECTION: backend.api-docs.gaps | status: VERIFIED -->
## 10. Gaps, contradictions and assumptions

**Contradiction check.**
- Story to design: none found. The brief covers only US-001-01 to US-001-06 and US-002-01.
- Story to requirement: none new for this module.
- Requirement to requirement: none new for this module.
- Design to design: none.
- No `CON-` is minted by this module. `CON-BE-006` affects only the data-handling note.

**New gaps**

| ID | Gap | Closing artefact |
|---|---|---|
| `GAP-BE-817` | US-004-04 says to "compute and expose" the coverage metric. No operation is minted for it, so exposure is undecided (CI output, build artefact or endpoint). Degrades BE-API-DOCS-FR-008. | A decision on exposure and, if an endpoint, an operation record. |
| `GAP-BE-818` | No input says how the document is generated from the code or how the route registry is read. This degrades FR-003 and FR-007. | A decision on the generation method and tooling (see `GAP-BE-089`). |
| `GAP-BE-819` | US-004-01 allows "JSON/YAML". The format served at `/openapi.json`, its exact 3.x version and its content type are not stated. | A decision on format, version and content type. |
| `GAP-BE-820` | Whether `API-APIDOCS-098363` requires authentication is not stated. Section `authorization` is `BLOCKED`. | A decision on public versus protected access. |
| `GAP-BE-821` | The review/approval mechanism for Scope section changes is not named. | A statement of the review process. |
| `GAP-BE-822` | How and by whom consumers are notified of interface changes (NFR-014 to NFR-016) is not stated. | A notification owner and channel. |
| `GAP-BE-823` | The cadence and trigger of the coverage, drift and lint checks are not stated. | A CI pipeline definition. |
| `GAP-BE-824` | The behaviour of `API-APIDOCS-098363` before any document has been published is not stated. | A decision on that behaviour. |

**Assumptions**

| ID | Assumption | Basis and what would confirm it |
|---|---|---|
| `ASM-BE-601` | The document is a generated build artefact built from the route registry and operation records, not hand-authored and not stored in PostgreSQL. | BR-012 refers to a "route registry" and an automated coverage check. Confirm by `GAP-BE-818`. |
| `ASM-BE-602` | Active and archived notes share one store, distinguished by a status flag. The documentation therefore states no storage-location difference between archive and active endpoints. | BR-007: "Soft-delete/archive flag, not hard delete". Confirm by the data-model decision for `notes`. |
| `ASM-BE-603` | An unsupported Accept header returns 406 with the proposed error code `NOT_ACCEPTABLE`, not a fallback. | `backend.error-model` lists 406 for the spec endpoint. `GAP-BE-309` is open. |
| `ASM-BE-604` | The endpoint serves JSON, because the path ends in `.json`. The content type is the JSON media type. | The path is itself proposed (`GAP-BE-303`). Confirm by `GAP-BE-819`. |
| `ASM-BE-605` | The checks in `backend.api-docs.jobs` run on every CI build. | NFR-010 runs the scope-guardrail suite on every CI build. No input states this for the documentation checks (`GAP-BE-823`). |
| `ASM-BE-606` | Coverage below 100% rejects the build (the build is not published). | NFR-008 targets 100% endpoint coverage and US-004-01 requires an endpoint to be documented no later than it is callable. US-004-04 only requires reporting. |

**Open items from the project spec that touch this module**

- `Q-BE-005` and `CON-BE-006` (email transport region and PII path).
- `Q-BE-151` (the approved EU region list).
- `GAP-BE-035` (field constraints).
- `GAP-BE-089` (no tooling named).
- `GAP-BE-303` (the path is proposed).
- `GAP-BE-309` (Accept fallback).
- `GAP-BE-500` (page size).
- `GAP-BE-602` (the inbound handoffs: their content was not supplied to this module, so none could be read or answered).
<!-- /SECTION: backend.api-docs.gaps -->
