---
id: agent-ba/web_app/rollup.md
type: spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.6.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-ba/web_app/rollup.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 6a137249-ccab-5a0a-bd2d-8ea991e30fa2
---

# Business Analysis — D-COPSCAR

## Discovery

**Brief Quality:** thin (Problem: weak, Outcomes: weak, Stakeholders: weak)

**Declaration:** N/A

## Business Objectives

### OBJ-001: Enable reliable programmatic creation and editing of short team notes
- **Leading KPI:** % of create/edit API calls returning success (2xx) in testing/staging
- **Lagging KPI:** Note create/edit error rate in production over rolling 30 days
- **Target:** >=99% success rate on create/edit endpoints within 60 days of launch
- **Impact:** Teams can trust the API as the system of record for notes without data loss or corruption

### OBJ-002: Enable tagging and search so notes are findable
- **Leading KPI:** % of search queries returning relevant results in QA test set
- **Lagging KPI:** Median search response time and search success rate in production
- **Target:** Search endpoint returns correct tagged/filtered results with <=300ms median latency by 60 days
- **Impact:** Users can retrieve relevant notes quickly instead of scanning unstructured text

### OBJ-003: Enable archiving so notes have a clear lifecycle
- **Leading KPI:** % of archive/unarchive API calls succeeding in testing
- **Lagging KPI:** Ratio of archived-but-recoverable notes vs. accidental data loss incidents
- **Target:** Archive/restore functionality live and error-free (0 data-loss incidents) by 60 days
- **Impact:** Old notes are cleanly separated from active ones without being deleted, keeping the dataset manageable

### OBJ-004: Guarantee EU data residency for compliance
- **Leading KPI:** % of infrastructure/config audits confirming EU-only data storage pre-launch
- **Lagging KPI:** Number of confirmed residency violations post-launch (rolling 60/90 days)
- **Target:** 100% of data storage and processing verified EU-resident at launch; zero violations at 60 days
- **Impact:** Organization avoids regulatory risk and can market the API as compliant for EU customers

### OBJ-005: Ship a minimal-scope API quickly without feature creep
- **Leading KPI:** Number of endpoints/features in final build vs. brief-defined scope (create, edit, tag, search, archive only)
- **Lagging KPI:** Time-to-ship (days from start to production-ready API) and post-launch scope-change requests
- **Target:** Launch within agreed timeline with exactly the 5 core capabilities (create, edit, tag, search, archive) and zero added-scope items (no attachments, no mobile, no real-time) within 60 days
- **Impact:** Development stays fast and maintainable, validating that minimal scope was the right call

### OBJ-006: 
- **Leading KPI:** 
- **Lagging KPI:** N/A
- **Target:** N/A
- **Impact:** N/A

## EP-001: Core Note CRUD API (→ OBJ-001)
Endpoints to create and edit short team notes with reliable 2xx success and no data loss; serves as system-of-record for notes.

### US-001-01: As a Team member drafting a note, I want to create a new note via the API with title and body text, so that reliably persist new notes so create calls return 2xx success
**Priority:** Must Have

**Acceptance Criteria:**
- Given a valid title and body payload, when POST /notes is called, then API returns 201 with the created note including its id
- Given a payload missing required title field, when POST /notes is called, then API returns 400 with a validation error and no note is created
- Given a body field at the maximum allowed length, when POST /notes is called, then API returns 201 and stores the full body without truncation

**Test Cases:**
- **TC-001-01**: POST /notes is called
- **TC-001-02**: [error] POST /notes is called
- **TC-001-03**: [edge] POST /notes is called

### US-001-02: As a Team member updating shared notes, I want to edit an existing note's title or body via the API, so that keep notes accurate without data loss, supporting reliable edit success rate
**Priority:** Must Have

**Acceptance Criteria:**
- Given an existing note id and updated fields, when PUT/PATCH /notes/{id} is called, then API returns 200 with the updated note reflecting changes
- Given a note id that does not exist, when PUT/PATCH /notes/{id} is called, then API returns 404 and no data is altered
- Given a concurrent edit request on the same note, when two update requests are sent in quick succession, then the last accepted write wins and no fields are partially corrupted

**Test Cases:**
- **TC-001-04**: PUT/PATCH /notes/{id} is called
- **TC-001-05**: [error] PUT/PATCH /notes/{id} is called
- **TC-001-06**: [edge] two update requests are sent in quick succession

### US-001-03: As a Developer integrating with the notes API, I want to retrieve a single note by its id, so that confirm notes persisted correctly, supporting reliability as system-of-record
**Priority:** Must Have

**Acceptance Criteria:**
- Given a note exists with a given id, when GET /notes/{id} is called, then API returns 200 with the full note content
- Given a note id that does not exist or was deleted, when GET /notes/{id} is called, then API returns 404

**Test Cases:**
- **TC-001-07**: GET /notes/{id} is called
- **TC-001-08**: [error] GET /notes/{id} is called

### US-001-04: As a Team member reviewing shared notes, I want to list all existing notes via the API, so that verify notes are consistently retrievable as the system-of-record
**Priority:** Must Have

**Acceptance Criteria:**
- Given multiple notes exist, when GET /notes is called, then API returns 200 with a list of all notes
- Given no notes exist yet, when GET /notes is called, then API returns 200 with an empty list, not an error

**Test Cases:**
- **TC-001-09**: GET /notes is called
- **TC-001-10**: [edge] GET /notes is called

### US-001-05: As a Developer building client integrations, I want to receive clear field-level validation errors when note data is malformed, so that quickly fix bad requests, increasing the proportion of calls that return 2xx
**Priority:** Should Have

**Acceptance Criteria:**
- Given a request with an invalid data type for a field, when POST or PATCH /notes is called, then API returns 400 with a message naming the invalid field
- Given an empty JSON body, when POST /notes is called, then API returns 400 identifying all missing required fields

**Test Cases:**
- **TC-001-11**: [error] POST or PATCH /notes is called
- **TC-001-12**: [edge] POST /notes is called

### US-001-06: As a Team member relying on note integrity, I want to have each note's creation and last-modified timestamps tracked automatically, so that trust the API as a reliable system-of-record with no data loss
**Priority:** Should Have

**Acceptance Criteria:**
- Given a note is created, when POST /notes succeeds, then the response includes a created_at timestamp set by the server
- Given a note is edited, when PATCH /notes/{id} succeeds, then the response includes an updated_at timestamp reflecting the edit time
- Given a client sends its own created_at or updated_at value, when POST or PATCH is called, then API ignores client-supplied timestamps and uses server time

**Test Cases:**
- **TC-001-13**: POST /notes succeeds
- **TC-001-14**: PATCH /notes/{id} succeeds
- **TC-001-15**: [edge] POST or PATCH is called

## EP-002: Tagging & Search API (→ OBJ-002)
Tagging model plus search/filter endpoint so notes are findable with relevant results and low latency (<=300ms median).

### US-002-01: As a team member tagging notes, I want to add one or more tags to a note via the API, so that notes become categorized so later searches return relevant results, improving the QA relevance KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given an existing note and valid tag strings, when client sends a request to add tags to the note, then API returns 200 with the note's updated tag list
- Given an existing note with no tags, when client submits a duplicate of an existing tag, then API deduplicates and returns the tag list without duplicates
- Given a note id that does not exist, when client sends a tag-add request to that id, then API returns 404 with an error message

**Test Cases:**
- **TC-002-01**: client sends a request to add tags to the note
- **TC-002-02**: [edge] client submits a duplicate of an existing tag
- **TC-002-03**: [error] client sends a tag-add request to that id

### US-002-02: As a team member removing an outdated tag, I want to remove a tag from a note via the API, so that search results stay accurate as note categorization evolves, protecting relevance KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given a note with an existing tag, when client sends a request to remove that tag, then API returns 200 and the tag is no longer in the note's tag list
- Given a note that does not have the specified tag, when client requests removal of that tag, then API returns Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. indicating tag not found on note

**Test Cases:**
- **TC-002-04**: client sends a request to remove that tag
- **TC-002-05**: [error] client requests removal of that tag

### US-002-03: As a team member searching for a note by keyword, I want to query the search endpoint with free-text terms, so that finds relevant notes quickly, directly measured by the % relevant results KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given notes exist whose title or body contains the query term, when client calls search endpoint with that term, then API returns 200 with matching notes ranked by relevance within 300ms median
- Given a query term that matches no notes, when client calls search endpoint, then API returns 200 with an empty result list, not an error
- Given a search request with an empty or missing query parameter, when client calls the search endpoint, then API returns 400 with a validation error message

**Test Cases:**
- **TC-002-06**: client calls search endpoint with that term
- **TC-002-07**: [edge] client calls search endpoint
- **TC-002-08**: [error] client calls the search endpoint

### US-002-04: As a team member narrowing results by category, I want to filter search results by one or more tags, so that quickly locates notes within a known category, improving relevance KPI for tag-based queries
**Priority:** Must Have

**Acceptance Criteria:**
- Given notes tagged with a specific tag exist, when client calls search endpoint with that tag as a filter parameter, then API returns 200 with only notes containing that tag
- Given notes exist matching both a keyword and a tag filter, when client calls search with both parameters combined, then API returns 200 with notes satisfying both conditions
- Given a filter request referencing a tag that does not exist in the system, when client calls search endpoint with that tag, then API returns 200 with an empty result list

**Test Cases:**
- **TC-002-09**: client calls search endpoint with that tag as a filter parameter
- **TC-002-10**: client calls search with both parameters combined
- **TC-002-11**: [error] client calls search endpoint with that tag

### US-002-05: As a team member browsing available categories, I want to list all distinct tags currently in use, so that discovers valid filter options, increasing likelihood searches are constructed well and return relevant results
**Priority:** Should Have

**Acceptance Criteria:**
- Given notes with various tags exist in the system, when client calls the list-tags endpoint, then API returns 200 with a deduplicated list of all tags in use
- Given no notes have any tags yet, when client calls the list-tags endpoint, then API returns 200 with an empty list

**Test Cases:**
- **TC-002-12**: client calls the list-tags endpoint
- **TC-002-13**: [edge] client calls the list-tags endpoint

### US-002-06: As a team member performing a large or frequent search, I want to receive paginated search results, so that search stays fast and low-latency at scale, protecting the <=300ms median latency target
**Priority:** Should Have

**Acceptance Criteria:**
- Given a search query matches more results than the page size, when client requests a page via page/limit parameters, then API returns 200 with the correct page of results and total count
- Given a requested page number beyond available results, when client calls search with that page number, then API returns 200 with an empty result list and correct total count
- Given a negative or non-numeric page or limit parameter, when client calls search endpoint, then API returns 400 with a validation error

**Test Cases:**
- **TC-002-14**: client requests a page via page/limit parameters
- **TC-002-15**: [edge] client calls search with that page number
- **TC-002-16**: [error] client calls search endpoint

## EP-003: Archive & Restore Lifecycle (→ OBJ-003)
Archive/unarchive endpoints giving notes a clear lifecycle, separating old notes from active ones without deletion or data loss.

### US-003-01: As a team member closing out finished work, I want to archive a note via the API, so that remove completed notes from active view without deleting data, supporting lifecycle KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given an active note with a valid id exists, when client calls archive endpoint for that note, then API returns 200 and note status is set to archived, content unchanged
- Given note id does not exist, when client calls archive endpoint, then API returns 404 with error body
- Given note is already archived, when client calls archive endpoint again, then API returns 200 idempotently without error or state change

**Test Cases:**
- **TC-003-01**: client calls archive endpoint for that note
- **TC-003-02**: [error] client calls archive endpoint
- **TC-003-03**: [edge] client calls archive endpoint again

### US-003-02: As a team member resuming old work, I want to restore (unarchive) a previously archived note via the API, so that bring a note back to active status without data loss, supporting lifecycle KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given an archived note with a valid id exists, when client calls unarchive endpoint for that note, then API returns 200 and note status is set to active, content unchanged
- Given note id does not exist, when client calls unarchive endpoint, then API returns 404 with error body
- Given note is already active, when client calls unarchive endpoint, then API returns 200 idempotently without error or state change

**Test Cases:**
- **TC-003-04**: client calls unarchive endpoint for that note
- **TC-003-05**: [error] client calls unarchive endpoint
- **TC-003-06**: [edge] client calls unarchive endpoint

### US-003-03: As a team member managing their workspace, I want to list only archived notes via the API, so that separate old notes from active ones, confirming archive lifecycle works correctly
**Priority:** Must Have

**Acceptance Criteria:**
- Given some notes are archived and some are active, when client requests the list filtered by archived status, then API returns only archived notes with 200
- Given no notes are currently archived, when client requests the archived list, then API returns 200 with an empty list, not an error

**Test Cases:**
- **TC-003-07**: client requests the list filtered by archived status
- **TC-003-08**: [edge] client requests the archived list

### US-003-04: As a team member reviewing current work, I want to list only active (non-archived) notes via the API, so that default views exclude archived clutter, keeping active note list clean
**Priority:** Must Have

**Acceptance Criteria:**
- Given some notes are archived and some are active, when client requests the default or active-filtered list, then API returns only active notes with 200
- Given all notes are archived, when client requests the active list, then API returns 200 with an empty list, not an error

**Test Cases:**
- **TC-003-09**: client requests the default or active-filtered list
- **TC-003-10**: [edge] client requests the active list

### US-003-05: As a developer integrating with notes_api, I want to edit or tag a note regardless of its archive state, so that ensure archiving never causes data loss or blocks legitimate operations
**Priority:** Should Have

**Acceptance Criteria:**
- Given an archived note exists, when client sends an edit or tag update request for it, then API applies the update and returns 200, archive status unchanged
- Given an archived note is updated, when update completes, then note remains archived and no data fields are lost

**Test Cases:**
- **TC-003-11**: client sends an edit or tag update request for it
- **TC-003-12**: [edge] update completes

### US-003-06: As a operations engineer validating reliability, I want to receive consistent error handling for malformed archive/unarchive requests, so that ensure high success rate of archive calls as measured by the KPI
**Priority:** Should Have

**Acceptance Criteria:**
- Given an archive request is sent with a malformed note id (non-UUID or invalid format), when client calls archive endpoint, then API returns 400 with a descriptive error body
- Given an unarchive request is sent with an empty id field, when client calls unarchive endpoint, then API returns 400 without server error

**Test Cases:**
- **TC-003-13**: [error] client calls archive endpoint
- **TC-003-14**: [edge] client calls unarchive endpoint

## EP-004: EU Data Residency Compliance (→ OBJ-004)
Ensure all data storage/processing infrastructure is EU-resident and auditable, avoiding regulatory risk.

### US-004-01: As a platform operations engineer, I want to configure the database and application hosting region to an EU-only region at provisioning time, so that guarantees EU-only data storage, directly driving the % of infrastructure audits confirming EU residency
**Priority:** Must Have

**Acceptance Criteria:**
- Given a new deployment environment is being provisioned, when infrastructure is created, then database and compute resources are located in an approved EU region
- Given an attempt to provision a resource in a non-EU region, when the provisioning request is submitted, then the request is rejected and logged

**Test Cases:**
- **TC-004-01**: infrastructure is created
- **TC-004-02**: [error] the provisioning request is submitted

### US-004-02: As a compliance auditor, I want to retrieve a report listing the region of every data store and service used by notes_api, so that enables pre-launch audits to confirm 100% EU-only data storage
**Priority:** Must Have

**Acceptance Criteria:**
- Given notes_api infrastructure is deployed, when the auditor requests the residency report, then the report lists each data store/service with its region and confirms all are EU
- Given a service's region is undetermined, when the report is generated, then that service is flagged as unverified rather than assumed compliant

**Test Cases:**
- **TC-004-03**: the auditor requests the residency report
- **TC-004-04**: [edge] the report is generated

### US-004-03: As a backend developer, I want to ensure note data persisted via the API is never written to backup or logging destinations outside the EU, so that prevents regulatory risk from data leakage outside audited EU storage
**Priority:** Must Have

**Acceptance Criteria:**
- Given a note is created or edited via the API, when the record is persisted and backed up, then both primary storage and backups reside in EU regions
- Given a logging or backup destination configured outside the EU, when the system attempts to write to it, then the write is blocked and an alert is raised

**Test Cases:**
- **TC-004-05**: the record is persisted and backed up
- **TC-004-06**: [error] the system attempts to write to it

### US-004-04: As a compliance auditor, I want to verify that any third-party services or dependencies used by notes_api also store/process data within the EU, so that closes gaps in the residency chain that could fail the compliance KPI
**Priority:** Should Have

**Acceptance Criteria:**
- Given notes_api depends on a third-party service, when the auditor reviews the service's data processing location, then the location is documented as EU-resident
- Given a third-party service's data residency cannot be confirmed, when the audit is run, then the dependency is flagged for remediation before launch

**Test Cases:**
- **TC-004-07**: the auditor reviews the service's data processing location
- **TC-004-08**: [edge] the audit is run

### US-004-05: As a platform operations engineer, I want to run an automated pre-launch residency check across all environments (dev, staging, prod), so that ensures the EU-only audit passes at 100% before go-live, satisfying the KPI
**Priority:** Should Have

**Acceptance Criteria:**
- Given all environments are configured, when the pre-launch check is run, then it reports pass/fail per environment with region details
- Given a non-EU resource is detected in any environment, when the check runs, then launch is blocked until the resource is corrected

**Test Cases:**
- **TC-004-09**: the pre-launch check is run
- **TC-004-10**: [error] the check runs

### US-004-06: As a compliance auditor, I want to access a historical log of residency audit results over time, so that demonstrates ongoing compliance for recurring audits, not just the initial pre-launch check
**Priority:** Could Have

**Acceptance Criteria:**
- Given multiple residency audits have been run, when the auditor views the audit history, then each past audit's date, scope, and result are listed
- Given no audits have been run yet, when the auditor views the audit history, then an empty state is shown rather than an error

**Test Cases:**
- **TC-004-11**: the auditor views the audit history
- **TC-004-12**: [edge] the auditor views the audit history

## EP-005: Scope Guardrails & Minimal Footprint (→ OBJ-005)
Enforce exactly the 5 core capabilities (create, edit, tag, search, archive) with no attachments/mobile/real-time, to keep delivery fast and maintainable.

### US-005-01: As a API tech lead, I want to define the API surface with exactly 5 endpoint groups (create, edit, tag, search, archive note), so that keeps final endpoint count matching brief-defined scope, directly measuring the KPI
**Priority:** Should Have

**Acceptance Criteria:**
- Given the API spec is generated, when all routes are listed, then only routes for create, edit, tag, search, and archive note exist
- Given a developer proposes a new route outside the 5 capabilities, when the route is added to the codebase, then the spec-compliance check fails the build

**Test Cases:**
- **TC-005-01**: all routes are listed
- **TC-005-02**: [error] the route is added to the codebase

### US-005-02: As a backend developer, I want to reject any request payload containing file/binary attachment data on the create or edit endpoints, so that prevents attachment features from creeping into the minimal-scope build
**Priority:** Should Have

**Acceptance Criteria:**
- Given a note is created with only title, body, and tags, when the request is submitted, then the note is saved successfully
- Given a request includes a multipart/form-data file field or binary attachment, when the request hits create or edit endpoint, then the API returns 400 with an 'attachments not supported' error

**Test Cases:**
- **TC-005-03**: the request is submitted
- **TC-005-04**: [error] the request hits create or edit endpoint

### US-005-03: As a API tech lead, I want to ensure no WebSocket, SSE, or polling-for-live-updates endpoints are exposed, so that keeps the build free of real-time collaboration features not in brief scope
**Priority:** Should Have

**Acceptance Criteria:**
- Given the API's route registry is inspected, when checked for protocol types, then only standard HTTP request/response endpoints are present
- Given a pull request adds a WebSocket or SSE route, when CI scope-guardrail check runs, then the check fails and blocks merge

**Test Cases:**
- **TC-005-05**: checked for protocol types
- **TC-005-06**: [error] CI scope-guardrail check runs

### US-005-04: As a QA engineer, I want to run an automated scope-compliance test suite against every build, so that gives a measurable count of endpoints/features vs. brief scope on each release
**Priority:** Should Have

**Acceptance Criteria:**
- Given a build is produced, when the scope-compliance suite runs, then it reports exactly 5 capability groups with zero extras
- Given an endpoint exists that technically maps to 'search' but accepts attachment-filter parameters, when the suite inspects its parameters, then it flags the parameter as out-of-scope

**Test Cases:**
- **TC-005-07**: the scope-compliance suite runs
- **TC-005-08**: [edge] the suite inspects its parameters

### US-005-05: As a product owner, I want to maintain a documented out-of-scope list (attachments, mobile-specific endpoints, real-time) in the API spec, so that gives stakeholders a clear reference to prevent reintroducing descoped features later
**Priority:** Should Have

**Acceptance Criteria:**
- Given the published API documentation, when a reader views the scope section, then attachments, mobile endpoints, and real-time features are explicitly listed as out of scope
- Given a future contributor proposes an attachment endpoint, when they check the documented out-of-scope list first, then they see the feature is explicitly excluded before writing code

**Test Cases:**
- **TC-005-09**: a reader views the scope section
- **TC-005-10**: [edge] they check the documented out-of-scope list first

### US-005-06: As a backend developer, I want to block any endpoint or parameter designed solely to support a native mobile client (e.g. device push tokens), so that keeps the API free of mobile-specific features since no mobile app is in scope
**Priority:** Could Have

**Acceptance Criteria:**
- Given the API endpoints are reviewed, when checked for mobile-only parameters, then none are present
- Given a request includes a device push-token field, when submitted to any endpoint, then the API returns 400 indicating the field is unsupported

**Test Cases:**
- **TC-005-11**: checked for mobile-only parameters
- **TC-005-12**: [error] submitted to any endpoint

## EP-006: Web Frontend for Notes (→ OBJ-006)


### US-006-01: As a Team member, I want to view a list of all notes in the web app, so that browse existing notes without calling the API directly
**Priority:** Must Have

**Acceptance Criteria:**
- Given notes exist in notes_api, when the notes list page loads, then the web app calls GET /notes, renders each note's title and status, renders pinned notes first with a pin icon, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with Newest first selected by default, and shows a live result count above the list equal to the number of notes listed
- Given no notes exist yet, when the notes list page loads, then the web app shows an empty-state message and a result count of 0 instead of an error
- Given notes_api returns an error, when the notes list page loads, then the web app shows a user-facing error state instead of a blank screen

**Test Cases:**
- **TC-006-29**: the notes list page loads
- **TC-006-30**: the notes list page loads
- **TC-006-31**: the notes list page loads

### US-006-02: As a Team member, I want to create a new note using the web editor, so that add notes without needing to call the API manually
**Priority:** Must Have

**Acceptance Criteria:**
- Given a title and body are entered in the editor, when the user submits, then the web app calls POST /notes and navigates to the saved note
- Given the title field is left empty, when the user submits, then the web app shows the API's validation error inline without creating a note
- Given the create request succeeds, when the response is received, then the note's server-set created_at is reflected in the UI

**Test Cases:**
- **TC-006-04**: the user submits
- **TC-006-05**: the user submits
- **TC-006-06**: the response is received

### US-006-03: As a Team member, I want to edit an existing note's title or body using the web editor, so that keep notes accurate from the browser
**Priority:** Must Have

**Acceptance Criteria:**
- Given an existing note is opened in the editor, when the user changes the title or body and saves, then the web app calls PUT/PATCH /notes/{id} and shows the updated content
- Given the save request returns 404, when the response is received, then the web app shows a 'note not found' message and does not apply local changes
- Given a save request returns a field-level validation error, when the response is received, then the web app highlights the invalid field

**Test Cases:**
- **TC-006-07**: the user changes the title or body and saves
- **TC-006-08**: the response is received
- **TC-006-09**: the response is received

### US-006-04: As a Team member, I want to search notes using a search box with free-text and tag filters, so that quickly find relevant notes from the browser
**Priority:** Must Have

**Acceptance Criteria:**
- Given the user enters a keyword in the search box, when they submit, then the web app calls the search endpoint, renders matching notes in the currently selected sort order (Newest first, Oldest first, or Title A-Z) without changing the sort selection, and updates the live result count above the list to the number of matches
- Given the search returns no matches, when results load, then the web app shows an empty-results message and a result count of 0 instead of an error
- Given the user selects a tag filter, when they apply it, then the web app includes the tag filter in the search request, renders only matching notes in the selected sort order, and updates the live result count to match

**Test Cases:**
- **TC-006-26**: they submit
- **TC-006-27**: results load
- **TC-006-28**: they apply it

### US-006-05: As a Team member, I want to log in from the web app by requesting an email one-time code and entering it on a code-entry screen, so that sign in securely without a password and understand why a code was refused
**Priority:** Must Have

**Acceptance Criteria:**
- Given the user is on the login screen and enters their email address, when they submit the email and then enter a valid code on the code-entry screen, then the web app calls the notes_api request-code endpoint, shows the code-entry screen, calls the verify-code endpoint with the entered code and signs the user in
- Given the user has entered a wrong code 5 attempts times, when the verify-code endpoint reports the account is locked, then the web app shows a lockout error stating the account is locked for 15 minutes and disables code submission
- Given the code was issued more than 10 minutes ago, or the resend limit of 3 resends per 10 minutes has been reached, when the user submits the code or asks for a new code, then the web app shows an expired-code error with an option to request a new code, or a resend-limit error when no more resends are allowed

**Test Cases:**
- **TC-006-38**: they submit the email and then enter a valid code on the code-entry screen
- **TC-006-39**: [error] the verify-code endpoint reports the account is locked
- **TC-006-40**: [error] the user submits the code or asks for a new code

### US-006-06: As a Team member, I want to archive, restore or pin/unpin a note from the web app so that manage note lifecycle and ordering without calling the API directly, so that manage note lifecycle without calling the API directly
**Priority:** Should Have

**Acceptance Criteria:**
- Given an active note is open, when the user clicks archive, then the web app calls the archive endpoint and the note disappears from the default active list
- Given an archived note is viewed, when the user clicks restore, then the web app calls the unarchive endpoint and the note reappears in the active list
- Given a notes row in the list, when the user clicks its pin toggle, then the web app calls the API to set the note's pinned flag and the row moves to the top with a pin icon (or returns to its sort position when unpinned)

**Test Cases:**
- **TC-006-32**: the user clicks archive
- **TC-006-33**: the user clicks restore
- **TC-006-34**: the user clicks its pin toggle

## Business Rules

### BR-001: A note must have a non-empty title and body at creation time
**Rationale:** Prevents empty/garbage records from being persisted as system-of-record | **Enforcement:** API validation layer on create endpoint

### BR-002: Note id is immutable once assigned and never reused
**Rationale:** Guarantees reliable retrieval and referential integrity | **Enforcement:** Database primary key constraint

### BR-003: created_at is set once on creation and never modified; updated_at is set automatically on every successful edit
**Rationale:** Ensures trustworthy audit trail without manual timestamp handling | **Enforcement:** Server-side logic, not client-supplied

### BR-004: Malformed create/edit requests return field-level validation errors with HTTP 4xx, never a generic 500
**Rationale:** Lets integrators quickly fix bad requests, raising 2xx success rate | **Enforcement:** API validation middleware

### BR-005: Tags are case-insensitive and de-duplicated per note
**Rationale:** Prevents fragmented categorization that degrades search relevance | **Enforcement:** Tag write logic normalizes to lowercase before persisting

### BR-006: Removing a tag from a note never deletes the note or other tags
**Rationale:** Protects data integrity while categorization evolves | **Enforcement:** Tag removal endpoint scoped to single tag-note association

### BR-007: Search requires at least one of: keyword query or tag filter
**Rationale:** Avoids unbounded full-table scans that hurt latency KPI | **Enforcement:** Search endpoint input validation

### BR-008: Distinct tags list endpoint returns only tags currently attached to at least one note
**Rationale:** Keeps filter discovery accurate and relevant | **Enforcement:** Query excludes orphaned/unused tag values

### BR-009: Archiving a note is a soft-delete (status flag) and never removes underlying data
**Rationale:** Guarantees no data loss during lifecycle transitions | **Enforcement:** Archive endpoint sets is_archived flag only

### BR-010: Edit and tag operations are permitted on a note regardless of its archive state
**Rationale:** Archiving must never block legitimate operations | **Enforcement:** Edit/tag endpoints do not check archive state as a precondition

### BR-011: Default note list endpoint excludes archived notes unless explicitly requested
**Rationale:** Keeps active workspace view clean of clutter | **Enforcement:** List endpoint default filter is_archived=false

### BR-012: Archive/unarchive operations are idempotent and return a consistent error for invalid state transitions (e.g., archiving an already-archived note)
**Rationale:** Ensures predictable, high success-rate error handling | **Enforcement:** Archive/unarchive endpoint state-transition check

### BR-013: All application hosting, databases, backups, and logs for notes_api must reside exclusively in an EU region
**Rationale:** Mandatory for 100% EU data residency compliance KPI | **Enforcement:** Infrastructure-as-code region constraints + automated residency checks

### BR-014: Any third-party service or dependency integrated with notes_api must contractually and technically store/process data only within the EU
**Rationale:** Closes residency-chain gaps that could fail compliance audits | **Enforcement:** Vendor review during integration + residency audit report

### BR-015: notes_api exposes exactly 5 endpoint groups: create, edit, tag, search, archive
**Rationale:** Enforces brief-defined minimal scope | **Enforcement:** Automated scope-compliance test suite run on every build

### BR-016: Create/edit endpoints reject any payload containing file or binary attachment data
**Rationale:** Prevents attachment features from entering minimal-scope build | **Enforcement:** Payload schema validation rejects multipart/binary fields

### BR-017: No WebSocket, SSE, or polling-for-live-updates endpoints may be exposed by notes_api
**Rationale:** Keeps build free of out-of-scope real-time collaboration features | **Enforcement:** Scope-compliance test suite + API gateway route allowlist

### BR-018: No endpoint or parameter may exist solely to support native mobile clients (e.g., device push tokens)
**Rationale:** No mobile app is in scope per brief | **Enforcement:** API spec review + scope-compliance test suite

### BR-019: The live result count above the web notes list equals the number of notes currently rendered for the active list, or for the current search and tag filters
**Rationale:**  | **Enforcement:** 

### BR-020: Sorting of the web notes list (Newest first, Oldest first, Title A-Z) is performed client-side in the web app and adds no parameters to the existing notes API
**Rationale:**  | **Enforcement:** 

### BR-021: Pinned notes are always rendered above unpinned notes in the web notes list, within whichever sort order is selected, including filtered and search results
**Rationale:**  | **Enforcement:** 

### BR-022: An emailed login code is valid for 10 minutes, a code can be resent at most 3 resends times per 10 minutes, and 5 attempts wrong codes lock the account for 15 minutes; the web app shows the matching error for each case
**Rationale:** Limits abuse of email one-time-code login and keeps web errors consistent with notes_api behaviour | **Enforcement:** notes_api enforces the limits on request-code and verify-code; the web app displays the returned errors

## Non-Functional Requirements

### NFR-001: Search endpoint (keyword and/or tag filter) responds within median latency target under normal load
**Category:** Performance | **Target:** <=300ms median response time | **Priority:** Must Have

### NFR-002: Core CRUD endpoints (create, retrieve, edit, list) respond Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. under normal load
**Category:** Performance | **Target:** <=200ms p95 response time | **Priority:** Should Have

### NFR-003: Search results are paginated to maintain performance at scale
**Category:** Scalability | **Target:** Default page size 20, max page size 100, configurable via query param | **Priority:** Must Have

### NFR-004: Create and edit calls succeed reliably under normal conditions
**Category:** Availability | **Target:** >=99% of valid requests return 2xx | **Priority:** Must Have

### NFR-005: Archive and unarchive calls succeed reliably under normal conditions
**Category:** Availability | **Target:** >=99% of valid requests return 2xx | **Priority:** Must Have

### NFR-006: Create/edit endpoints reject oversized or binary/file-attachment payloads before processing
**Category:** Security | **Target:** 100% of attachment-containing payloads rejected with 4xx | **Priority:** Must Have

### NFR-007: All infrastructure (DB, app hosting, backups, logs) for notes_api is provisioned in EU-only regions
**Category:** Compliance | **Target:** 100% of audited components report EU region | **Priority:** Must Have

### NFR-008: Automated residency check runs across dev, staging, and prod before go-live and on a recurring schedule
**Category:** Compliance | **Target:** 100% pass rate pre-launch; results logged for every run | **Priority:** Must Have

### NFR-009: Historical residency audit log is retained and queryable for recurring compliance reporting
**Category:** Compliance | **Target:** 100% of past audit runs retrievable, retained for required audit period | **Priority:** Should Have

### NFR-010: notes_api endpoint surface is limited to the brief-defined scope, verified automatically on every release
**Category:** Scalability | **Target:** Exactly 5 endpoint groups; 0 out-of-scope endpoints detected per build | **Priority:** Must Have

### NFR-011: Validation errors on malformed requests include field-level detail identifying the invalid field(s)
**Category:** Usability | **Target:** 100% of 4xx validation responses include field-level error detail | **Priority:** Must Have

### NFR-012: Tag listing and tag-based filtering remain performant as tag volume grows
**Category:** Scalability | **Target:** <=300ms median latency for tag-filtered search at up to 10,000 notes | **Priority:** Should Have


## Open questions

- **Q-BAC-78104** (US-006-06): US-006-06 states a benefit that only repeats the action: what does the user gain? (As a Team member, I want to archive, restore or pin/unpin a note from the web app so that manage note lifecycle and ordering without calling the API directly) Default: Keep as written.
