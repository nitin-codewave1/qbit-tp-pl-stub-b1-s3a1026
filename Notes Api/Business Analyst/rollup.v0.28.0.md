---
id: agent-ba/notes_api/rollup.md
type: spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.28.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-ba/notes_api/rollup.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 4d7dca16-87f3-5e90-80bb-50528bce009f
---

# Business Analysis — D-COPSCAR

## Discovery

**Brief Quality:** thin (Problem: weak, Outcomes: weak, Stakeholders: weak)

**Declaration:** Brief quality thin - missing: explicit business pain/cost of current note-handling, named stakeholders, success metrics, and performance/compliance targets. Assumptions made: (1) root pain is fragmented note storage plus EU compliance risk; (2) consuming client is a future web_app or similar integrator; (3) 60-day measurement window per framework default; (4) 'small' scope implies tight timeline/cost constraints as an implicit objective.

## Business Objectives

### OBJ-001: Guarantee EU data residency for all team note data
- **Leading KPI:** % of infrastructure/data stores verified EU-region at launch
- **Lagging KPI:** Number of data residency compliance violations post-launch
- **Target:** 100% EU-hosted infrastructure verified at launch; 0 violations within 60 days
- **Impact:** Removes compliance/legal risk, enabling the API to be trusted for EU team data

### OBJ-002: Provide a reliable, complete note lifecycle API (create, edit, tag, archive)
- **Leading KPI:** % of core CRUD/tag/archive endpoints passing functional tests
- **Lagging KPI:** Production API uptime and error rate
- **Target:** 100% endpoints implemented and tested by launch; 99.5% uptime, <1% error rate over 60 days
- **Impact:** Team notes can be fully managed without data loss or manual workarounds

### OBJ-003: Deliver fast, relevant note search
- **Leading KPI:** Search endpoint p95 response latency
- **Lagging KPI:** Search success rate (queries returning expected results, no fallback/manual lookup)
- **Target:** p95 search latency <300ms at launch, sustained through 60 days
- **Impact:** Users can locate notes quickly, reducing time lost to manual scanning

### OBJ-004: Make the API integration-ready for client applications
- **Leading KPI:** % of endpoints covered by published OpenAPI/API documentation
- **Lagging KPI:** Number of client integrations (e.g., web_app) completed without breaking changes
- **Target:** 100% documented spec at launch; ≥1 successful client integration within 60 days
- **Impact:** Future consumers (e.g., web_app) can build against the API with no backend rework

### OBJ-005: Keep delivery scope minimal to control cost and risk
- **Leading KPI:** Number of out-of-scope feature requests rejected (mobile, attachments, real-time collab)
- **Lagging KPI:** Delivery timeline adherence (planned vs actual ship date)
- **Target:** Zero scope additions beyond defined feature set; ship within planned timeline
- **Impact:** Faster, lower-risk delivery of a focused, maintainable API

### OBJ-006: 
- **Leading KPI:** 
- **Lagging KPI:** N/A
- **Target:** N/A
- **Impact:** N/A

## EP-001: EU Data Residency Infrastructure (→ OBJ-001)
Provision and verify all notes_api infrastructure (DB, hosting, backups) in EU-only regions; add residency checks to deployment pipeline. Removes compliance/legal risk, a hard launch gate.

### US-001-01: As a Platform/DevOps Engineer, I want to provision the notes_api database and hosting compute exclusively in approved EU regions, so that guarantees EU data residency at launch, driving the % of infrastructure verified EU-region toward 100%
**Priority:** Must Have

**Acceptance Criteria:**
- Given an infrastructure provisioning request for notes_api, when the DB and hosting resources are created, then both resources report a region within the approved EU region list
- Given a provisioning request specifying a non-EU region, when the request is submitted, then the request is rejected and no resource is created

**Test Cases:**
- **TC-001-01**: the DB and hosting resources are created
- **TC-001-02**: [error] the request is submitted

### US-001-02: As a Platform/DevOps Engineer, I want to configure automated backups of the notes database to EU-only storage locations, so that ensures backup copies don't leak data outside the EU, protecting the residency KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given a scheduled backup job runs for notes_api, when the backup completes, then the backup artifact is stored in an EU-region storage bucket/service
- Given a backup destination configuration pointing outside the EU, when the backup pipeline validates the config before running, then the job fails within 30 seconds with a residency violation error and does not write data

**Test Cases:**
- **TC-001-18**: the backup completes
- **TC-001-19**: [error] the backup pipeline validates the config before running

### US-001-03: As a Release/CI Pipeline Owner, I want to add an automated EU-residency check to the deployment pipeline that validates target region before each deploy, so that prevents non-compliant deploys from ever reaching production, acting as a hard launch gate for the residency KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given a deployment is triggered with all target resources in EU regions, when the residency check runs in the pipeline, then the check passes and the deployment proceeds
- Given a deployment is triggered with at least one target resource outside the EU, when the residency check runs, then the pipeline fails the build and blocks the deployment
- Given a resource's region metadata is missing or unreadable, when the residency check attempts to evaluate it, then the check treats it as a failure and blocks deployment rather than passing by default

**Test Cases:**
- **TC-001-05**: the residency check runs in the pipeline
- **TC-001-06**: [error] the residency check runs
- **TC-001-07**: [edge] the residency check attempts to evaluate it

### US-001-04: As a Compliance/Security Auditor, I want to generate a report listing every notes_api infrastructure component with its verified region and compliance status, so that provides measurable evidence of the % of infrastructure verified EU-region, supporting the launch gate sign-off
**Priority:** Must Have

**Acceptance Criteria:**
- Given all notes_api resources exist and have been scanned, when the compliance report is generated, then the report lists each resource, its region, and a pass/fail residency status with an overall % compliant metric
- Given a new resource type is added that the scanner doesn't yet recognize, when the report is generated, then the resource appears flagged as 'unverified' rather than silently omitted

**Test Cases:**
- **TC-001-08**: the compliance report is generated
- **TC-001-09**: [edge] the report is generated

### US-001-05: As a Engineering Lead, I want to block the notes_api launch until the residency compliance report shows 100% EU-verified infrastructure, so that enforces the hard launch gate, eliminating compliance/legal risk before go-live
**Priority:** Must Have

**Acceptance Criteria:**
- Given the residency compliance report shows 100% EU-verified resources, when the launch checklist is reviewed, then the launch gate is marked passed and release is authorized
- Given the residency compliance report shows less than 100% EU-verified resources, when the launch checklist is reviewed, then the launch gate is marked failed and release is blocked until resolved

**Test Cases:**
- **TC-001-10**: the launch checklist is reviewed
- **TC-001-11**: [error] the launch checklist is reviewed

### US-001-06: As a Platform/DevOps Engineer, I want to receive an alert if any running notes_api resource drifts to or is discovered in a non-EU region after launch, so that maintains the 100% EU-region verification KPI on an ongoing basis, not just at launch
**Priority:** Should Have

**Acceptance Criteria:**
- Given a periodic residency scan runs post-launch, when all resources remain in EU regions, then no alert is raised and the scan logs a pass
- Given a periodic residency scan runs post-launch, when a resource is found in a non-EU region (e.g., via misconfigured auto-scaling), then an alert is sent to the DevOps team immediately with resource details

**Test Cases:**
- **TC-001-12**: all resources remain in EU regions
- **TC-001-13**: [edge] a resource is found in a non-EU region (e.g., via misconfigured auto-scal

## EP-002: Note Lifecycle API (Create, Edit, Tag, Archive) (→ OBJ-002)
Implement relational-DB-backed CRUD endpoints for notes plus tagging and archive/unarchive operations, with functional test coverage. Core value: notes fully managed via API with no data loss.

### US-002-01: As a team member drafting notes, I want to create a new note via the API with title and body, so that notes are captured reliably with no data loss, supporting the CRUD functional-test KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given a valid title and body payload, when POST to the notes endpoint, then API returns 201 with the created note including a unique ID, timestamps, and pinned=false by default
- Given a valid payload with pinned=true, when POST to the notes endpoint, then API returns 201 and the stored note has pinned=true
- Given a payload missing required title, when POST to the notes endpoint, then API returns 400 with a validation error and no note is persisted

**Test Cases:**
- **TC-002-13**: POST to the notes endpoint
- **TC-002-14**: POST to the notes endpoint
- **TC-002-15**: POST to the notes endpoint

### US-002-02: As a team member updating existing work, I want to edit an existing note's title, body, or tags, so that notes stay accurate and current without losing prior content, supporting zero-data-loss lifecycle
**Priority:** Must Have

**Acceptance Criteria:**
- Given an existing note ID and valid updated fields, when PUT/PATCH to the note endpoint, then API returns 200 with the updated note and an updated timestamp
- Given a PATCH containing only the pinned field, when it is submitted, then API returns 200, pinned is updated, and title, body and tags are unchanged
- Given a non-existent note ID, when PUT/PATCH to that note endpoint, then API returns 404 and no data is modified

**Test Cases:**
- **TC-002-16**: PUT/PATCH to the note endpoint
- **TC-002-17**: it is submitted
- **TC-002-18**: PUT/PATCH to that note endpoint

### US-002-03: As a team member organizing notes by topic, I want to add or remove tags on a note, so that notes can be categorized for later retrieval, fulfilling the tagging portion of the lifecycle KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given an existing note and a tag name, when a tag is added via the API, then API returns 200 and the note's tag list includes the new tag
- Given a tag that already exists on the note, when the same tag is added again, then API returns 200 without creating a duplicate tag entry

**Test Cases:**
- **TC-002-05**: a tag is added via the API
- **TC-002-06**: [edge] the same tag is added again

### US-002-04: As a team member cleaning up old content, I want to archive a note so it's hidden from active views but not deleted, so that old notes are retained without data loss while keeping active lists relevant, per the archive lifecycle objective
**Priority:** Must Have

**Acceptance Criteria:**
- Given an active, existing note, when the archive endpoint is called, then API returns 200, marks the note archived, and its data remains intact
- Given an already-archived note, when the archive endpoint is called again, then API returns a conflict/error response indicating it's already archived

**Test Cases:**
- **TC-002-07**: the archive endpoint is called
- **TC-002-08**: [error] the archive endpoint is called again

### US-002-05: As a team member restoring previously archived content, I want to unarchive a note to make it active again, so that archived notes remain fully recoverable, ensuring the lifecycle is reversible with no data loss
**Priority:** Must Have

**Acceptance Criteria:**
- Given an archived note, when the unarchive endpoint is called, then API returns 200 and the note's status changes to active with all original data preserved
- Given a note ID that does not exist, when the unarchive endpoint is called, then API returns 404

**Test Cases:**
- **TC-002-09**: the unarchive endpoint is called
- **TC-002-10**: [error] the unarchive endpoint is called

### US-002-06: As a QA engineer validating API reliability, I want to retrieve a single note by ID to verify current state, so that confirms CRUD correctness and supports the functional-test-pass-rate KPI
**Priority:** Should Have

**Acceptance Criteria:**
- Given an existing note ID, when GET is called on the note endpoint, then API returns 200 with full note details including tags, archive status and the pinned flag
- Given an ID for a deleted or never-existing note, when GET is called on that note endpoint, then API returns 404 with no partial data exposed

**Test Cases:**
- **TC-002-19**: GET is called on the note endpoint
- **TC-002-20**: GET is called on that note endpoint

## EP-003: Note Search (→ OBJ-003)
Build a search endpoint over note content/tags optimized for low latency (p95 <300ms). Lets users find notes fast instead of manual scanning.

### US-003-01: As a Team member, I want to search notes by keyword across title and content, so that quickly locate relevant notes instead of manually scanning, enabling fast search
**Priority:** Must Have

**Acceptance Criteria:**
- Given notes exist matching the keyword, when GET /notes/search?q=keyword is called, then matching notes are returned with pinned notes first, then ordered by relevance, with 200 status
- Given no notes match the keyword, when the search is executed, then an empty array is returned with 200 status, not an error
- Given the q query parameter is missing or empty, when the search request is made, then API returns 400 with a validation error message

**Test Cases:**
- **TC-003-19**: GET /notes/search?q=keyword is called
- **TC-003-20**: the search is executed
- **TC-003-21**: the search request is made

### US-003-02: As a Team member, I want to filter search results by one or more tags, so that narrow results to a specific topic quickly, reducing time spent scanning
**Priority:** Must Have

**Acceptance Criteria:**
- Given notes tagged 'project-x' exist, when GET /notes/search?tags=project-x is called, then only notes with that tag are returned
- Given a tag with no matching notes, when the search is executed, then an empty result set is returned with 200 status
- Given the tags parameter contains invalid characters, when the search request is made, then API returns 400 with an error detail

**Test Cases:**
- **TC-003-04**: GET /notes/search?tags=project-x is called
- **TC-003-05**: [edge] the search is executed
- **TC-003-06**: [error] the search request is made

### US-003-03: As a Integration engineer consuming the search API, I want to receive search responses within the performance SLA under typical load, so that ensures search stays fast (p95 <300ms), directly meeting the objective's latency KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given a search request under 50 requests per second load, when the request is executed, then p95 response latency is under 300ms
- Given a large dataset (e.g., 100k notes), when a search is executed, then the latency SLA is still met via indexed lookup
- Given the search index is degraded or unavailable, when a search is requested, then API returns 503 with a retry-after header instead of hanging

**Test Cases:**
- **TC-003-28**: the request is executed
- **TC-003-29**: [edge] a search is executed
- **TC-003-30**: [error] a search is requested

### US-003-04: As a Team member, I want to combine keyword and tag filters in a single search request, so that get more precise results, reducing time to find the right note
**Priority:** Should Have

**Acceptance Criteria:**
- Given q=budget and tags=finance are both supplied, when the search is executed, then only notes matching both criteria are returned
- Given the keyword matches but the tag filter excludes all notes, when the search is executed, then an empty result set is returned
- Given a malformed filter combination is supplied, when the search request is made, then API returns 400 with error details

**Test Cases:**
- **TC-003-10**: the search is executed
- **TC-003-11**: [edge] the search is executed
- **TC-003-12**: [error] the search request is made

### US-003-05: As a Team member, I want to paginate through search results, so that efficiently browse large result sets without overwhelming responses, keeping search fast
**Priority:** Should Have

**Acceptance Criteria:**
- Given more results exist than the page size, when page and limit params are supplied, then the correct subset is returned along with a total count
- Given a page number beyond available results, when the search is executed, then an empty array is returned with 200 status
- Given negative or non-numeric page/limit values, when the search request is made, then API returns 400 with a validation error

**Test Cases:**
- **TC-003-13**: page and limit params are supplied
- **TC-003-14**: [edge] the search is executed
- **TC-003-15**: [error] the search request is made

### US-003-06: As a Team member, I want to exclude archived notes from default search results, so that see only active, relevant notes by default, reducing noise and scan time
**Priority:** Could Have

**Acceptance Criteria:**
- Given both archived and active notes exist, when a default search is executed, then only active notes are returned
- Given include_archived=true is passed, when the search is executed, then archived notes are included in results
- Given an invalid include_archived value is supplied, when the search request is made, then API returns 400 with an error message

**Test Cases:**
- **TC-003-16**: a default search is executed
- **TC-003-17**: [edge] the search is executed
- **TC-003-18**: [error] the search request is made

## EP-004: API Documentation & Integration Readiness (→ OBJ-004)
Publish a complete OpenAPI spec covering all endpoints so client apps (e.g., future web_app) can integrate without backend rework.

### US-004-01: As a integration developer building the web_app front end, I want to fetch a published OpenAPI spec that documents every notes_api endpoint, so that integrate without backend rework, directly raising % of endpoints covered by published API documentation
**Priority:** Should Have

**Acceptance Criteria:**
- Given notes_api is deployed, when client requests the OpenAPI document, then a valid OpenAPI 3.x JSON/YAML document is returned listing all CRUD, tagging, search, archive, and email one-time-code login (send and verify) endpoints
- Given a new endpoint is added to notes_api, when the spec is regenerated/published, then the new endpoint appears in the OpenAPI document before or at the same time it becomes callable
- Given the OpenAPI endpoint is requested with an unsupported Accept header, when client requests the spec, then API returns a 406 or a default-format fallback with a clear content-type

**Test Cases:**
- **TC-004-23**: client requests the OpenAPI document
- **TC-004-24**: the spec is regenerated/published
- **TC-004-25**: client requests the spec

### US-004-02: As a backend developer maintaining notes_api, I want to define request/response schemas (notes, tags, search, archive) in the OpenAPI spec with required fields and types, so that clients integrate correctly without guessing payload shapes, supporting full endpoint coverage KPI
**Priority:** Should Have

**Acceptance Criteria:**
- Given an endpoint accepts a note payload, when the schema is inspected in the spec, then all required fields, types, and constraints (e.g., title length, tag format) are documented
- Given an endpoint supports optional query parameters (e.g., search filters), when the schema is reviewed, then optional parameters are marked optional with default values documented
- Given a documented field type is changed in code, when the spec is not updated to match, then a spec-validation/CI check fails, flagging the mismatch

**Test Cases:**
- **TC-004-04**: the schema is inspected in the spec
- **TC-004-05**: [edge] the schema is reviewed
- **TC-004-06**: [error] the spec is not updated to match

### US-004-03: As a integration developer building the web_app front end, I want to see documented error responses (4xx/5xx) for each endpoint in the spec, so that handle failures predictably in the client, reducing integration rework and supporting coverage completeness
**Priority:** Should Have

**Acceptance Criteria:**
- Given an endpoint can return validation errors, when the spec is reviewed, then a 400 response schema with error structure/fields is documented
- Given an endpoint requires authentication/authorization or is an email one-time-code login endpoint, when the spec is reviewed, then 401/403 responses are documented with expected body shape, and the send/verify login endpoints document a 415 response for more than 3 requests within 10 minutes and a locked-account response after 5 wrong codes, locked for 15 minutes
- Given a documented field type is changed in code, when the 404 response is checked against the spec, then documented 404 response matches the actual API behavior

**Test Cases:**
- **TC-004-59**: the spec is reviewed
- **TC-004-60**: the spec is reviewed
- **TC-004-61**: the 404 response is checked against the spec

### US-004-04: As a backend developer maintaining notes_api, I want to compute and expose a coverage metric showing % of implemented endpoints present in the published spec, so that directly measure and track the KPI for integration readiness over time
**Priority:** Should Have

**Acceptance Criteria:**
- Given all implemented endpoints are documented, when the coverage check runs, then it reports 100% coverage
- Given one endpoint is implemented but missing from the spec, when the coverage check runs, then it reports less than 100% and names the missing endpoint
- Given the spec file is malformed, when the coverage check runs, then it fails with a clear parsing error rather than reporting false coverage

**Test Cases:**
- **TC-004-10**: the coverage check runs
- **TC-004-11**: [edge] the coverage check runs
- **TC-004-12**: [error] the coverage check runs

### US-004-05: As a integration developer building the web_app front end, I want to view example request/response payloads for search and tagging operations in the spec, so that quickly implement correct calls without trial-and-error against the live API, speeding integration readiness
**Priority:** Should Have

**Acceptance Criteria:**
- Given the search endpoint is documented, when a developer views the spec, then an example request with query params and an example response body are shown
- Given search returns zero results, when the example responses are reviewed, then an example empty-result response is included

**Test Cases:**
- **TC-004-13**: a developer views the spec
- **TC-004-14**: [edge] the example responses are reviewed

### US-004-06: As a operations engineer responsible for EU data residency compliance, I want to confirm from the spec/documentation where archived vs active note data resides and is served from, so that verify data handling matches EU residency requirements before client integration, avoiding compliance rework
**Priority:** Could Have

**Acceptance Criteria:**
- Given the API documentation includes a data-handling note, when an operations engineer reviews it, then it states that all note data is stored and served within the EU
- Given archive endpoints behave differently from active-note endpoints regarding storage location, when documentation is reviewed, then any difference is explicitly called out

**Test Cases:**
- **TC-004-15**: an operations engineer reviews it
- **TC-004-16**: [edge] documentation is reviewed

## EP-005: Scope Guardrails (No Mobile, Attachments, Real-Time Collab) (→ OBJ-005)
Enforce and document exclusion of mobile apps, attachments, and real-time collaboration from notes_api scope to protect timeline and cost.

### US-005-01: As a Engineering lead maintaining notes_api, I want to publish an explicit out-of-scope section in the API documentation listing mobile apps, attachments, and real-time collaboration as excluded, so that gives stakeholders a documented reference to point to when rejecting out-of-scope requests, driving the rejection-count KPI
**Priority:** Could Have

**Acceptance Criteria:**
- Given the published API documentation, when a reader views the 'Scope' section, then mobile apps, attachments, and real-time collaboration are listed as explicitly excluded
- Given a documentation update is proposed, when a new out-of-scope item is added or changed, then the change requires review/approval before merge
- Given the docs build pipeline, when the Scope section is missing from a build, then the documentation build fails with a lint/validation error

**Test Cases:**
- **TC-005-01**: a reader views the 'Scope' section
- **TC-005-02**: [edge] a new out-of-scope item is added or changed
- **TC-005-03**: [error] the Scope section is missing from a build

### US-005-02: As a Backend developer implementing notes endpoints, I want to ensure no attachment or file-upload endpoint exists in notes_api, so that prevents accidental scope creep into attachments, protecting cost/timeline per the guardrail KPI
**Priority:** Could Have

**Acceptance Criteria:**
- Given the deployed API route registry, when a request is sent to any attachment path (e.g. POST /notes/{id}/attachments), then the API returns 404 Not Found
- Given a note create/update endpoint, when a multipart/form-data request with a file payload is submitted, then the API returns 415 and persists no file content

**Test Cases:**
- **TC-005-18**: a request is sent to any attachment path (e.g. POST /notes/{id}/attachments)
- **TC-005-19**: [error] a multipart/form-data request with a file payload is submitted

### US-005-03: As a Backend developer implementing notes endpoints, I want to ensure no real-time collaboration channel (WebSocket/SSE) exists on notes_api, so that avoids building concurrent-editing infrastructure, keeping scope and cost minimal per the KPI
**Priority:** Could Have

**Acceptance Criteria:**
- Given the running API server, when a client attempts a WebSocket upgrade or SSE connection to any notes_api endpoint, then the connection is rejected (no protocol upgrade, standard error status returned)
- Given two clients submit PATCH requests to the same note concurrently, when both requests are processed, then the API applies a standard last-write/conflict response with no live broadcast or merge logic

**Test Cases:**
- **TC-005-06**: a client attempts a WebSocket upgrade or SSE connection to any notes_api endpoin
- **TC-005-07**: [edge] both requests are processed

### US-005-04: As a Third-party client integrator consuming notes_api, I want to receive a clear, documented error when calling an unsupported out-of-scope endpoint (mobile-specific, attachment, or real-time), so that avoids wasted integration effort on excluded features and reinforces the documented scope guardrail
**Priority:** Could Have

**Acceptance Criteria:**
- Given an integrator calls an out-of-scope endpoint, when the request is made, then the response body includes a message referencing the scope exclusion rather than a bare 404
- Given an integrator sends a multipart file upload to a notes endpoint, when the request is validated, then the response returns a structured error code (e.g. OUT_OF_SCOPE) with a human-readable reason

**Test Cases:**
- **TC-005-08**: the request is made
- **TC-005-09**: [error] the request is validated

### US-005-05: As a QA engineer responsible for notes_api regression testing, I want to run an automated contract test suite verifying no mobile-specific, attachment, or real-time endpoints exist in the API surface, so that continuously enforces the minimal-scope boundary, directly supporting measurement of the rejected-requests KPI
**Priority:** Could Have

**Acceptance Criteria:**
- Given the CI pipeline runs, when the scope-guardrail test suite executes against the OpenAPI spec, then it passes only when zero endpoints match excluded categories (mobile, attachments, real-time)
- Given a developer adds a new endpoint matching an excluded pattern (e.g. /attachments or /ws), when CI runs on the pull request, then the guardrail test fails and blocks the merge

**Test Cases:**
- **TC-005-10**: the scope-guardrail test suite executes against the OpenAPI spec
- **TC-005-11**: [edge] CI runs on the pull request

### US-005-06: As a Product manager fielding stakeholder feature requests, I want to log and classify each incoming request for mobile, attachments, or real-time collaboration against the documented exclusion list, so that produces the countable 'rejected out-of-scope requests' metric required by the epic's KPI
**Priority:** Could Have

**Acceptance Criteria:**
- Given a stakeholder submits a request for mobile app support, when the PM logs the request, then it is recorded with status 'rejected - out of scope' and linked to the EP-005 guardrail documentation
- Given a request is ambiguous (e.g. 'notes with images' could imply attachments), when the PM triages it, then the request is classified against the documented exclusion criteria before a decision is logged

**Test Cases:**
- **TC-005-12**: the PM logs the request
- **TC-005-13**: [edge] the PM triages it

## EP-006: Web Application Front End (Notes List, Editor, Search) (→ OBJ-006)


### US-006-01: As a team member using the web app, I want to view a list of my active notes, so that quickly see what notes exist without calling the API directly
**Priority:** Must Have

**Acceptance Criteria:**
- Given notes exist in the system, when I open the web app notes list page, then I see each note's title and an archived/active indicator
- Given no notes exist, when I open the notes list page, then I see an empty-state message rather than an error
- Given the API request for the notes list fails, when the list page loads, then an error message is shown instead of a blank/broken page

**Test Cases:**
- **TC-006-01**: I open the web app notes list page
- **TC-006-02**: I open the notes list page
- **TC-006-03**: the list page loads

### US-006-02: As a team member drafting content, I want to create and edit notes using an in-browser editor, so that author and update notes without using the raw API
**Priority:** Must Have

**Acceptance Criteria:**
- Given I open the editor and enter a title and body, when I save, then the note is created via the API and appears in the notes list
- Given I open an existing note in the editor and change its body, when I save, then the note is updated via the API and the editor shows the updated content
- Given I submit the editor with an empty title, when I try to save, then the editor shows a validation error and no API call is made

**Test Cases:**
- **TC-006-04**: I save
- **TC-006-05**: I save
- **TC-006-06**: I try to save

### US-006-03: As a team member locating content, I want to use a search box in the web app to search by keyword and tag, so that find relevant notes without constructing API calls manually
**Priority:** Must Have

**Acceptance Criteria:**
- Given I enter a keyword into the search box, when I submit the search, then the web app calls the search endpoint and displays matching notes
- Given I filter by a tag in the search box, when I submit the search, then only notes with that tag are displayed
- Given my search returns no results, when the search completes, then the web app shows a 'no results' message rather than an empty blank area

**Test Cases:**
- **TC-006-07**: I submit the search
- **TC-006-08**: I submit the search
- **TC-006-09**: the search completes

### US-006-04: As a team member organizing notes, I want to add, remove, and view tags on a note from the editor, so that categorize notes without leaving the web app
**Priority:** Should Have

**Acceptance Criteria:**
- Given I am editing a note, when I add a tag and save, then the tag appears on the note in both the editor and the notes list
- Given I remove a tag from a note in the editor, when I save, then the tag no longer appears on that note
- Given I try to add a tag that already exists on the note, when I save, then no duplicate tag is created and the UI reflects a single instance

**Test Cases:**
- **TC-006-10**: I add a tag and save
- **TC-006-11**: I save
- **TC-006-12**: I save

### US-006-05: As a team member managing old notes, I want to archive and unarchive notes from the web app, so that hide or restore notes without calling the API directly
**Priority:** Should Have

**Acceptance Criteria:**
- Given an active note is open, when I click archive, then the note is marked archived via the API and removed from the default notes list view
- Given an archived note is viewed in an archived-notes view, when I click unarchive, then the note becomes active again and reappears in the default list
- Given an archive or unarchive API call fails, when the action is attempted, then the web app shows an error and the note's displayed status remains unchanged

**Test Cases:**
- **TC-006-13**: I click archive
- **TC-006-14**: I click unarchive
- **TC-006-15**: the action is attempted

### US-006-06: As a team member signing in to the web app, I want to sign in with an email one-time code through the notes_api send and verify endpoints, so that so that access my notes securely without a password, with the web app adapting to the new login interface change from CR-125
**Priority:** Must Have

**Acceptance Criteria:**
- Given I enter my email on the sign-in page, when I request a new code more than 3 requests within 10 minutes, then the app shows a rate-limit message and sends no further code
- Given I submit a one-time code more than 10 minutes after it was issued, when I try to sign in, then sign-in is rejected with an expired-code message
- Given I have entered 5 wrong codes, when I try again, then my account is locked for 15 minutes and the app shows a locked-account message

**Test Cases:**
- **TC-006-58**: I request a new code more than 3 requests within 10 minutes
- **TC-006-59**: I try to sign in
- **TC-006-60**: I try again

## Business Rules

### BR-001: All notes_api infrastructure (database, compute, backups) must be provisioned exclusively in approved EU regions
**Rationale:** Guarantees EU data residency at launch and ongoing | **Enforcement:** Infrastructure-as-code region constraints + compliance report

### BR-002: Deployment pipeline must block any deploy targeting a non-EU region
**Rationale:** Prevents non-compliant deploys reaching production | **Enforcement:** Automated CI/CD residency check gate

### BR-003: notes_api launch is blocked until compliance report shows 100% EU-verified infrastructure
**Rationale:** Eliminates compliance/legal risk before go-live | **Enforcement:** Launch gate sign-off by Engineering Lead

### BR-004: Any running resource detected outside EU region post-launch triggers an immediate alert
**Rationale:** Maintains 100% EU-region KPI on an ongoing basis | **Enforcement:** Automated drift-detection monitoring

### BR-005: A note must always have a title and body at creation; empty/null payloads are rejected
**Rationale:** Ensures no data loss and valid note structure | **Enforcement:** API request validation layer

### BR-006: Editing a note must never overwrite fields not included in the update request (partial update semantics)
**Rationale:** Prevents accidental loss of prior content | **Enforcement:** PATCH/PUT handler logic preserving unspecified fields

### BR-007: Archiving a note hides it from active views but never deletes underlying data
**Rationale:** Retention without data loss per lifecycle objective | **Enforcement:** Soft-delete/archive flag, not hard delete

### BR-008: Archived notes must always be unarchivable back to active state without data loss
**Rationale:** Lifecycle must be fully reversible | **Enforcement:** Unarchive endpoint restores full prior state

### BR-009: Default search results exclude archived notes unless explicitly requested
**Rationale:** Keeps default results relevant, reduces noise | **Enforcement:** Search query default filter: status=active

### BR-010: Search requests may combine keyword and tag filters in a single query
**Rationale:** Enables precise, combined filtering | **Enforcement:** Search endpoint supports combined query params

### BR-011: Search results must be paginated; unbounded result sets are not permitted
**Rationale:** Keeps responses fast and manageable | **Enforcement:** API enforces default/max page size

### BR-012: Every implemented notes_api endpoint must have a corresponding entry in the published OpenAPI spec, including request/response schemas and 4xx/5xx errors
**Rationale:** Full endpoint coverage required for integration readiness KPI | **Enforcement:** Automated coverage-check against OpenAPI spec vs. route registry

### BR-013: OpenAPI spec must document where archived vs active note data resides and is served from
**Rationale:** Lets integrators verify EU residency compliance before integration | **Enforcement:** Spec documentation review as part of publish process

### BR-014: notes_api must not implement any attachment/file-upload endpoints
**Rationale:** Prevents scope creep into attachments | **Enforcement:** Contract test suite asserts absence of such routes

### BR-015: notes_api must not implement any real-time channel (WebSocket/SSE/polling-push) for collaboration
**Rationale:** Avoids concurrent-editing infrastructure, keeps scope/cost minimal | **Enforcement:** Contract test suite asserts absence of such endpoints

### BR-016: Calls to unsupported out-of-scope endpoints (mobile-specific, attachment, real-time) must return a clear, documented error, not a silent failure or 404 ambiguity
**Rationale:** Avoids wasted integration effort, reinforces documented scope boundary | **Enforcement:** API gateway/error-handler returns standardized out-of-scope error response

### BR-017: Every stakeholder request for mobile, attachments, or real-time collaboration must be logged and classified against the documented exclusion list
**Rationale:** Produces the countable rejected out-of-scope requests metric | **Enforcement:** Product manager intake/classification log

### BR-018: Every note carries a boolean pinned flag that defaults to false and may be set on create or changed via PATCH
**Rationale:**  | **Enforcement:** 

### BR-019: Pinned notes are returned before unpinned notes in search and list results; ordering among notes with the same pinned value is unchanged
**Rationale:**  | **Enforcement:** 

### BR-020: Codes expire 10 minutes after issue. An emailed one-time login code cannot be used after expiry.
**Rationale:**  | **Enforcement:** 

### BR-021: Resending a login code is limited to 3 within 10 minutes; requests beyond the limit are rejected.
**Rationale:**  | **Enforcement:** 

### BR-022: After 5 wrong codes, the account is locked for 15 minutes and further code verification is refused until the lock expires.
**Rationale:**  | **Enforcement:** 

### BR-023: A login code is single-use: once it has been verified successfully it cannot be used again
**Rationale:**  | **Enforcement:** 

### BR-024: A one-time login code is valid for 10 minutes from issue; a code submitted after that is rejected as expired.
**Rationale:**  | **Enforcement:** 

### BR-025: After 5 wrong codes, the account is locked for 15 minutes.
**Rationale:**  | **Enforcement:** 

### BR-026: A one-time email login code expires 10 minutes after it is issued; an expired code is rejected.
**Rationale:**  | **Enforcement:** 

### BR-027: After 5 Lock the account after 5 wrong codes, for 15 minutes. wrong codes, the account is locked for 15 minutes; login attempts during the lock are rejected with a locked-account response.
**Rationale:**  | **Enforcement:** 

### BR-028: Requests to resend a one-time login code are limited to 3 within 10 minutes; requests beyond the limit return 415.
**Rationale:**  | **Enforcement:** 

### BR-029: A one-time login code has a length of 6 digits, all numeric.
**Rationale:** Project decision for the email one-time-code login: codes are 6 digits. | **Enforcement:** Code generation in notes_api for the email login send endpoint

### BR-030: Only Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. may request, verify or receive a one-time login code by email.
**Rationale:** Under CR-125 any address that verifies can obtain a credential, which may conflict with the shared-team-workspace default. The rule stays open until decided (HANDOFF-NOTESAPI-939780). | **Enforcement:** notes_api checks login-code eligibility before it sends a code and before it verifies one.

### BR-031: Access to each note follows this rule: Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes..
**Rationale:** Note ownership columns and the note access rule cannot be written until this is decided. The shared-workspace default is only a placeholder (HANDOFF-NOTESAPI-981573). | **Enforcement:** Note ownership fields and the access check on every note read and write follow this rule.

### BR-032: notes_api exposes a request-code endpoint and a verify-code endpoint for email one-time-code login. The request-code endpoint refuses requests beyond 3 requests within 10 minutes. The verify-code endpoint refuses a code that is wrong, already used, or older than 10 minutes, and refuses all verification for 15 minutes after 5 wrong codes.
**Rationale:** CR-127 adds request and verify one-time-code endpoints with lockout, resend limits and code expiry (interface change for consumers) | **Enforcement:** Server-side on each request-code and verify-code call

## Non-Functional Requirements

### NFR-001: All notes_api infrastructure components (compute, DB, backups) verified in approved EU regions
**Category:** Compliance | **Target:** 100% of infrastructure components EU-verified | **Priority:** Must Have

### NFR-002: Automated residency check runs in CI/CD pipeline before every production deploy
**Category:** Compliance | **Target:** 0 non-EU deploys reach production | **Priority:** Must Have

### NFR-003: Post-launch non-EU region drift detection and alerting
**Category:** Availability | **Target:** Alert fired within 15 minutes of drift detection | **Priority:** Must Have

### NFR-004: EU-residency compliance report generated for audit sign-off
**Category:** Compliance | **Target:** Report refreshed and 100% accurate at each launch gate check | **Priority:** Must Have

### NFR-005: CRUD operations (create, edit, get, archive, unarchive) complete with zero data loss across all functional tests
**Category:** Performance | **Target:** 100% functional-test pass rate for CRUD operations | **Priority:** Must Have

### NFR-006: Search API responds within SLA under 50 requests per second load
**Category:** Performance | **Target:** p95 latency < 300ms | **Priority:** Must Have

### NFR-007: Search endpoint supports pagination to handle large result sets without degrading response time
**Category:** Scalability | **Target:** p95 latency < 300ms maintained up to page sizes of 100 results | **Priority:** Should Have

### NFR-008: Published OpenAPI spec covers every implemented notes_api endpoint with schemas and error responses
**Category:** Compliance | **Target:** 100% endpoint coverage in published spec | **Priority:** Must Have

### NFR-009: OpenAPI spec includes example request/response payloads for search and tagging operations
**Category:** Usability | **Target:** 100% of search/tagging endpoints have example payloads | **Priority:** Should Have

### NFR-010: notes_api contract test suite verifies absence of mobile-specific, attachment, and real-time endpoints on every build
**Category:** Compliance | **Target:** 0 out-of-scope endpoints detected per build, run on every CI build | **Priority:** Must Have

### NFR-011: Standardized error response returned for calls to any out-of-scope/unsupported endpoint
**Category:** Usability | **Target:** 100% of out-of-scope calls return documented 4xx error with explanatory message | **Priority:** Should Have

### NFR-012: API requests validated and sanitized to prevent injection attacks on note title/body/tag fields
**Category:** Security | **Target:** 0 critical injection vulnerabilities in security scan per release | **Priority:** Must Have

### NFR-013: notes_api maintains high availability for CRUD and search operations
**Category:** Availability | **Target:** 99.9% uptime monthly | **Priority:** Should Have

### NFR-014: The pinned field and the pinned-first ordering are documented in the published OpenAPI spec as an interface change, and consumers are notified that they must adapt
**Category:**  | **Target:**  | **Priority:** 

### NFR-015: Email one-time-code login is an interface change: consumers of notes_api are notified that they must adapt to the login flow (CR-125)
**Category:**  | **Target:**  | **Priority:** 

### NFR-016: The email one-time-code login endpoints (send and verify) are an additive interface change: existing login methods remain unchanged. The endpoints are documented in the published OpenAPI spec, and consumers are notified that they must adapt.
**Category:**  | **Target:**  | **Priority:** 

### NFR-017: One-time login code emails are sent through the transactional email service in AWS region Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes., and the email address is transmitted outside the EU only if Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes..
**Category:** Compliance | **Target:** Share of one-time login code emails sent from the approved region under the residency answer | **Priority:** Must Have

### NFR-018: Email addresses and one-time login codes are Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. in all application logs.
**Category:** Security | **Target:** Share of log entries containing an unredacted email address or one-time login code | **Priority:** Must Have

### NFR-019: Code challenges are kept TBD (asked: Q-BA-3ec6da), send attempts TBD (asked: Q-BA-3fc1ce), account locks TBD (asked: Q-BA-ee85cf), and notes TBD (asked: Q-BA-84b4f3), after which they are deleted.
**Category:** Compliance | **Target:** Share of records older than their retention period that are deleted | **Priority:** Must Have


## Open questions

- **Q-BA-3ec6da** (NFR-019): How many days are one-time login code challenges kept?
- **Q-BA-3fc1ce** (NFR-019): How many days are one-time login code send attempts kept?
- **Q-BA-ee85cf** (NFR-019): How many days are account lock records kept?
- **Q-BA-84b4f3** (NFR-019): How many days are notes kept after archiving or deletion?
