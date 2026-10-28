---
id: agent-design/web_app/output/F4-stories.yaml
type: design_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/output/F4-stories.yaml
depends_on: []
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: web_app:design-f4-stories
---

schemaVersion: 1
generated_at: '2026-10-28T19:29:37Z'
run: 18
version: v1_initial
ruleset_version: 1
inputs_hash: sha256:85db26965c31f55f10819c2ae5f6223d76e8d42be1c21ad289efd0229f0aec2e
stories:
- id: US-001-01
  text: 'As a Team member drafting a note, I want to create a new note via the API with title and body
    text, so that reliably persist new notes so create calls return 2xx success. - Given a valid title
    and body payload, when POST /notes is called, then API returns 201 with the created note including
    its id - Given a payload missing required title field, when POST /notes is called, then API returns
    400 with a validation error and no note is created - Given a body field at the maximum allowed length,
    when POST /notes is called, then API returns 201 and stores the full body without truncation - Test
    case: POST /notes is called - Test case: [error] POST /notes is called - Test case: [edge] POST /notes
    is called'
  group: EP-001
  covers: []
  screen: NoteEditorScreen
  status: active
  slug: team-member-drafting-note-want-create-new-note-via-api
- id: US-001-02
  text: 'As a Team member updating shared notes, I want to edit an existing note''s title or body via
    the API, so that keep notes accurate without data loss, supporting reliable edit success rate. - Given
    an existing note id and updated fields, when PUT/PATCH /notes/{id} is called, then API returns 200
    with the updated note reflecting changes - Given a note id that does not exist, when PUT/PATCH /notes/{id}
    is called, then API returns 404 and no data is altered - Given a concurrent edit request on the same
    note, when two update requests are sent in quick succession, then the last accepted write wins and
    no fields are partially corrupted - Test case: PUT/PATCH /notes/{id} is called - Test case: [error]
    PUT/PATCH /notes/{id} is called - Test case: [edge] two update requests are sent in quick succession'
  group: EP-001
  covers: []
  screen: NoteEditorScreen
  status: active
  slug: team-member-updating-shared-notes-want-edit-existing-note-s
- id: US-001-03
  text: 'As a Developer integrating with the notes API, I want to retrieve a single note by its id, so
    that confirm notes persisted correctly, supporting reliability as system-of-record. - Given a note
    exists with a given id, when GET /notes/{id} is called, then API returns 200 with the full note content
    - Given a note id that does not exist or was deleted, when GET /notes/{id} is called, then API returns
    404 - Test case: GET /notes/{id} is called - Test case: [error] GET /notes/{id} is called'
  group: EP-001
  covers: []
  screen: NoteDetailScreen
  status: active
  slug: developer-integrating-notes-api-want-retrieve-single-note-by-its
- id: US-001-04
  text: 'As a Team member reviewing shared notes, I want to list all existing notes via the API, so that
    verify notes are consistently retrievable as the system-of-record. - Given multiple notes exist, when
    GET /notes is called, then API returns 200 with a list of all notes - Given no notes exist yet, when
    GET /notes is called, then API returns 200 with an empty list, not an error - Test case: GET /notes
    is called - Test case: [edge] GET /notes is called'
  group: EP-001
  covers: []
  status: active
  slug: team-member-reviewing-shared-notes-want-list-all-existing-notes
- id: US-001-05
  text: 'As a Developer building client integrations, I want to receive clear field-level validation errors
    when note data is malformed, so that quickly fix bad requests, increasing the proportion of calls
    that return 2xx. - Given a request with an invalid data type for a field, when POST or PATCH /notes
    is called, then API returns 400 with a message naming the invalid field - Given an empty JSON body,
    when POST /notes is called, then API returns 400 identifying all missing required fields - Test case:
    [error] POST or PATCH /notes is called - Test case: [edge] POST /notes is called'
  group: EP-001
  covers: []
  screen: NoteEditorScreen
  status: active
  slug: developer-building-client-integrations-want-receive-clear-field-level-validation
- id: US-001-06
  text: 'As a Team member relying on note integrity, I want to have each note''s creation and last-modified
    timestamps tracked automatically, so that trust the API as a reliable system-of-record with no data
    loss. - Given a note is created, when POST /notes succeeds, then the response includes a created_at
    timestamp set by the server - Given a note is edited, when PATCH /notes/{id} succeeds, then the response
    includes an updated_at timestamp reflecting the edit time - Given a client sends its own created_at
    or updated_at value, when POST or PATCH is called, then API ignores client-supplied timestamps and
    uses server time - Test case: POST /notes succeeds - Test case: PATCH /notes/{id} succeeds - Test
    case: [edge] POST or PATCH is called'
  group: EP-001
  covers: []
  screen: NoteEditorScreen
  status: active
  slug: team-member-relying-note-integrity-want-have-each-note-s
- id: US-002-01
  text: 'As a team member tagging notes, I want to add one or more tags to a note via the API, so that
    notes become categorized so later searches return relevant results, improving the QA relevance KPI.
    - Given an existing note and valid tag strings, when client sends a request to add tags to the note,
    then API returns 200 with the note''s updated tag list - Given an existing note with no tags, when
    client submits a duplicate of an existing tag, then API deduplicates and returns the tag list without
    duplicates - Given a note id that does not exist, when client sends a tag-add request to that id,
    then API returns 404 with an error message - Test case: client sends a request to add tags to the
    note - Test case: [edge] client submits a duplicate of an existing tag - Test case: [error] client
    sends a tag-add request to that id'
  group: EP-002
  covers: []
  screen: NoteDetailScreen
  status: active
  slug: team-member-tagging-notes-want-add-one-more-tags-note
- id: US-002-02
  text: 'As a team member removing an outdated tag, I want to remove a tag from a note via the API, so
    that search results stay accurate as note categorization evolves, protecting relevance KPI. - Given
    a note with an existing tag, when client sends a request to remove that tag, then API returns 200
    and the tag is no longer in the note''s tag list - Given a note that does not have the specified tag,
    when client requests removal of that tag, then API returns 415 indicating tag not found on note -
    Test case: client sends a request to remove that tag - Test case: [error] client requests removal
    of that tag'
  group: EP-002
  covers: []
  screen: NoteDetailScreen
  status: active
  slug: team-member-removing-outdated-tag-want-remove-tag-note-via
- id: US-002-03
  text: 'As a team member searching for a note by keyword, I want to query the search endpoint with free-text
    terms, so that finds relevant notes quickly, directly measured by the % relevant results KPI. - Given
    notes exist whose title or body contains the query term, when client calls search endpoint with that
    term, then API returns 200 with matching notes ranked by relevance within 300ms median - Given a query
    term that matches no notes, when client calls search endpoint, then API returns 200 with an empty
    result list, not an error - Given a search request with an empty or missing query parameter, when
    client calls the search endpoint, then API returns 400 with a validation error message - Test case:
    client calls search endpoint with that term - Test case: [edge] client calls search endpoint - Test
    case: [error] client calls the search endpoint'
  group: EP-002
  covers: []
  screen: SearchScreen
  status: active
  slug: team-member-searching-note-by-keyword-want-query-search-endpoint
- id: US-002-04
  text: As a team member narrowing results by category, I want to filter search results by one or more
    tags, so that quickly locates notes within a known category, improving relevance KPI for tag-based
    queries. - Given notes tagged with a specific tag exist, when client calls search endpoint with that
    tag as a filter parameter, then API returns 200 with only notes containing that tag - Given notes
    exist matching both a keyword and a tag filter, when client calls search with both parameters combined,
    then API returns 200 with notes satisfying both conditions
  group: EP-002
  covers: []
  screen: SearchScreen
  status: active
  slug: team-member-narrowing-results-by-category-want-filter-search-results
- id: US-002-05
  text: As a team member browsing available categories, I want to list all distinct tags currently in
    use, so that discovers valid filter options, increasing likelihood searches are constructed well and
    return relevant results.
  group: EP-002
  covers: []
  screen: TagsListScreen
  status: active
  slug: team-member-browsing-available-categories-want-list-all-distinct-tags
- id: US-002-06
  text: 'As a team member performing a large or frequent search, I want to receive paginated search results,
    so that search stays fast and low-latency at scale, protecting the <=300ms median latency target.
    - Given a search query matches more results than the page size, when client requests a page via page/limit
    parameters, then API returns 200 with the correct page of results and total count - Given a requested
    page number beyond available results, when client calls search with that page number, then API returns
    200 with an empty result list and correct total count - Given a negative or non-numeric page or limit
    parameter, when client calls search endpoint, then API returns 400 with a validation error - Test
    case: client requests a page via page/limit parameters - Test case: [edge] client calls search with
    that page number - Test case: [error] client calls search endpoint'
  group: EP-002
  covers: []
  screen: SearchScreen
  status: active
  slug: team-member-performing-large-frequent-search-want-receive-paginated-search
- id: US-003-01
  text: 'As a team member closing out finished work, I want to archive a note via the API, so that remove
    completed notes from active view without deleting data, supporting lifecycle KPI. - Given an active
    note with a valid id exists, when client calls archive endpoint for that note, then API returns 200
    and note status is set to archived, content unchanged - Given note id does not exist, when client
    calls archive endpoint, then API returns 404 with error body - Given note is already archived, when
    client calls archive endpoint again, then API returns 200 idempotently without error or state change
    - Test case: client calls archive endpoint for that note - Test case: [error] client calls archive
    endpoint - Test case: [edge] client calls archive endpoint again'
  group: EP-003
  covers: []
  screen: NoteDetailScreen
  status: active
  slug: team-member-closing-out-finished-work-want-archive-note-via
- id: US-003-02
  text: 'As a team member resuming old work, I want to restore (unarchive) a previously archived note
    via the API, so that bring a note back to active status without data loss, supporting lifecycle KPI.
    - Given an archived note with a valid id exists, when client calls unarchive endpoint for that note,
    then API returns 200 and note status is set to active, content unchanged - Given note id does not
    exist, when client calls unarchive endpoint, then API returns 404 with error body - Given note is
    already active, when client calls unarchive endpoint, then API returns 200 idempotently without error
    or state change - Test case: client calls unarchive endpoint for that note - Test case: [error] client
    calls unarchive endpoint - Test case: [edge] client calls unarchive endpoint'
  group: EP-003
  covers: []
  screen: NoteDetailScreen
  status: active
  slug: team-member-resuming-old-work-want-restore-unarchive-previously-archived
- id: US-003-03
  text: 'As a team member managing their workspace, I want to list only archived notes via the API, so
    that separate old notes from active ones, confirming archive lifecycle works correctly. - Given some
    notes are archived and some are active, when client requests the list filtered by archived status,
    then API returns only archived notes with 200 - Given no notes are currently archived, when client
    requests the archived list, then API returns 200 with an empty list, not an error - Test case: client
    requests the list filtered by archived status - Test case: [edge] client requests the archived list'
  group: EP-003
  covers: []
  status: active
  slug: team-member-managing-their-workspace-want-list-only-archived-notes
- id: US-003-04
  text: 'As a team member reviewing current work, I want to list only active (non-archived) notes via
    the API, so that default views exclude archived clutter, keeping active note list clean. - Given some
    notes are archived and some are active, when client requests the default or active-filtered list,
    then API returns only active notes with 200 - Given all notes are archived, when client requests the
    active list, then API returns 200 with an empty list, not an error - Test case: client requests the
    default or active-filtered list - Test case: [edge] client requests the active list'
  group: EP-003
  covers: []
  status: active
  slug: team-member-reviewing-current-work-want-list-only-active-non
- id: US-003-05
  text: 'As a developer integrating with notes_api, I want to edit or tag a note regardless of its archive
    state, so that ensure archiving never causes data loss or blocks legitimate operations. - Given an
    archived note exists, when client sends an edit or tag update request for it, then API applies the
    update and returns 200, archive status unchanged - Given an archived note is updated, when update
    completes, then note remains archived and no data fields are lost - Test case: client sends an edit
    or tag update request for it - Test case: [edge] update completes'
  group: EP-003
  covers: []
  screen: NoteDetailScreen
  status: active
  slug: developer-integrating-notes-api-want-edit-tag-note-regardless-its
- id: US-003-06
  text: 'As a operations engineer validating reliability, I want to receive consistent error handling
    for malformed archive/unarchive requests, so that ensure high success rate of archive calls as measured
    by the KPI. - Given an archive request is sent with a malformed note id (non-UUID or invalid format),
    when client calls archive endpoint, then API returns 400 with a descriptive error body - Given an
    unarchive request is sent with an empty id field, when client calls unarchive endpoint, then API returns
    400 without server error - Test case: [error] client calls archive endpoint - Test case: [edge] client
    calls unarchive endpoint'
  group: EP-003
  covers: []
  screen: NoteDetailScreen
  status: active
  slug: operations-engineer-validating-reliability-want-receive-consistent-error-handling-malformed
- id: US-004-01
  text: 'As a platform operations engineer, I want to configure the database and application hosting region
    to an EU-only region at provisioning time, so that guarantees EU-only data storage, directly driving
    the % of infrastructure audits confirming EU residency. - Given a new deployment environment is being
    provisioned, when infrastructure is created, then database and compute resources are located in an
    approved EU region - Given an attempt to provision a resource in a non-EU region, when the provisioning
    request is submitted, then the request is rejected and logged - Test case: infrastructure is created
    - Test case: [error] the provisioning request is submitted'
  group: EP-004
  covers: []
  screen: ResidencyReportScreen
  status: active
  slug: platform-operations-engineer-want-configure-database-application-hosting-region-eu
- id: US-004-02
  text: 'As a compliance auditor, I want to retrieve a report listing the region of every data store and
    service used by notes_api, so that enables pre-launch audits to confirm 100% EU-only data storage.
    - Given notes_api infrastructure is deployed, when the auditor requests the residency report, then
    the report lists each data store/service with its region and confirms all are EU - Given a service''s
    region is undetermined, when the report is generated, then that service is flagged as unverified rather
    than assumed compliant - Test case: the auditor requests the residency report - Test case: [edge]
    the report is generated'
  group: EP-004
  covers: []
  screen: ResidencyReportScreen
  status: active
  slug: compliance-auditor-want-retrieve-report-listing-region-every-data-store
- id: US-004-03
  text: As a backend developer, I want to ensure note data persisted via the API is never written to backup
    or logging destinations outside the EU, so that prevents regulatory risk from data leakage outside
    audited EU storage. - Given a note is created or edited via the API, when the record is persisted
    and backed up, then both primary storage and backups reside in EU regions
  group: EP-004
  covers: []
  screen: ResidencyReportScreen
  status: active
  slug: backend-developer-want-ensure-note-data-persisted-via-api-never
- id: US-004-04
  text: 'As a compliance auditor, I want to verify that any third-party services or dependencies used
    by notes_api also store/process data within the EU, so that closes gaps in the residency chain that
    could fail the compliance KPI. - Given notes_api depends on a third-party service, when the auditor
    reviews the service''s data processing location, then the location is documented as EU-resident -
    Given a third-party service''s data residency cannot be confirmed, when the audit is run, then the
    dependency is flagged for remediation before launch - Test case: the auditor reviews the service''s
    data processing location - Test case: [edge] the audit is run'
  group: EP-004
  covers: []
  screen: ResidencyReportScreen
  status: active
  slug: compliance-auditor-want-verify-any-third-party-services-dependencies-used
- id: US-004-05
  text: 'As a platform operations engineer, I want to run an automated pre-launch residency check across
    all environments (dev, staging, prod), so that ensures the EU-only audit passes at 100% before go-live,
    satisfying the KPI. - Given all environments are configured, when the pre-launch check is run, then
    it reports pass/fail per environment with region details - Given a non-EU resource is detected in
    any environment, when the check runs, then launch is blocked until the resource is corrected - Test
    case: the pre-launch check is run - Test case: [error] the check runs'
  group: EP-004
  covers: []
  screen: ResidencyReportScreen
  status: active
  slug: platform-operations-engineer-want-run-automated-pre-launch-residency-check
- id: US-004-06
  text: 'As a compliance auditor, I want to access a historical log of residency audit results over time,
    so that demonstrates ongoing compliance for recurring audits, not just the initial pre-launch check.
    - Given multiple residency audits have been run, when the auditor views the audit history, then each
    past audit''s date, scope, and result are listed - Given no audits have been run yet, when the auditor
    views the audit history, then an empty state is shown rather than an error - Test case: the auditor
    views the audit history - Test case: [edge] the auditor views the audit history'
  group: EP-004
  covers: []
  status: active
  slug: compliance-auditor-want-access-historical-log-residency-audit-results-over
- id: US-005-01
  text: 'As a API tech lead, I want to define the API surface with exactly 5 endpoint groups (create,
    edit, tag, search, archive note), so that keeps final endpoint count matching brief-defined scope,
    directly measuring the KPI. - Given the API spec is generated, when all routes are listed, then only
    routes for create, edit, tag, search, and archive note exist - Given a developer proposes a new route
    outside the 5 capabilities, when the route is added to the codebase, then the spec-compliance check
    fails the build - Test case: all routes are listed - Test case: [error] the route is added to the
    codebase'
  group: EP-005
  covers: []
  screen: ScopeDocumentationScreen
  status: active
  slug: api-tech-lead-want-define-api-surface-exactly-5-endpoint
- id: US-005-02
  text: 'As a backend developer, I want to reject any request payload containing file/binary attachment
    data on the create or edit endpoints, so that prevents attachment features from creeping into the
    minimal-scope build. - Given a note is created with only title, body, and tags, when the request is
    submitted, then the note is saved successfully - Given a request includes a multipart/form-data file
    field or binary attachment, when the request hits create or edit endpoint, then the API returns 400
    with an ''attachments not supported'' error - Test case: the request is submitted - Test case: [error]
    the request hits create or edit endpoint'
  group: EP-005
  covers: []
  screen: NoteEditorScreen
  status: active
  slug: backend-developer-want-reject-any-request-payload-containing-file-binary
- id: US-005-03
  text: 'As a API tech lead, I want to ensure no WebSocket, SSE, or polling-for-live-updates endpoints
    are exposed, so that keeps the build free of real-time collaboration features not in brief scope.
    - Given the API''s route registry is inspected, when checked for protocol types, then only standard
    HTTP request/response endpoints are present - Given a pull request adds a WebSocket or SSE route,
    when CI scope-guardrail check runs, then the check fails and blocks merge - Test case: checked for
    protocol types - Test case: [error] CI scope-guardrail check runs'
  group: EP-005
  covers: []
  screen: ScopeDocumentationScreen
  status: active
  slug: api-tech-lead-want-ensure-no-websocket-sse-polling-live
- id: US-005-04
  text: 'As a QA engineer, I want to run an automated scope-compliance test suite against every build,
    so that gives a measurable count of endpoints/features vs. brief scope on each release. - Given a
    build is produced, when the scope-compliance suite runs, then it reports exactly 5 capability groups
    with zero extras - Given an endpoint exists that technically maps to ''search'' but accepts attachment-filter
    parameters, when the suite inspects its parameters, then it flags the parameter as out-of-scope -
    Test case: the scope-compliance suite runs - Test case: [edge] the suite inspects its parameters'
  group: EP-005
  covers: []
  screen: ScopeDocumentationScreen
  status: active
  slug: qa-engineer-want-run-automated-scope-compliance-test-suite-against
- id: US-005-05
  text: As a product owner, I want to maintain a documented out-of-scope list (attachments, mobile-specific
    endpoints, real-time) in the API spec, so that gives stakeholders a clear reference to prevent reintroducing
    descoped features later. - Given the published API documentation, when a reader views the scope section,
    then attachments, mobile endpoints, and real-time features are explicitly listed as out of scope
  group: EP-005
  covers: []
  screen: ScopeDocumentationScreen
  status: active
  slug: product-owner-want-maintain-documented-out-scope-list-attachments-mobile
- id: US-005-06
  text: 'As a backend developer, I want to block any endpoint or parameter designed solely to support
    a native mobile client (e.g. device push tokens), so that keeps the API free of mobile-specific features
    since no mobile app is in scope. - Given the API endpoints are reviewed, when checked for mobile-only
    parameters, then none are present - Given a request includes a device push-token field, when submitted
    to any endpoint, then the API returns 400 indicating the field is unsupported - Test case: checked
    for mobile-only parameters - Test case: [error] submitted to any endpoint'
  group: EP-005
  covers: []
  screen: ScopeDocumentationScreen
  status: active
  slug: backend-developer-want-block-any-endpoint-parameter-designed-solely-support
- id: US-006-01
  text: As a Team member, I want to view a list of all notes in the web app, so that browse existing notes
    without calling the API directly. - Given notes exist in notes_api, when the notes list page loads,
    then the web app calls GET /notes, renders each note's title and status, renders pinned notes first
    with a pin icon, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with
    Newest first selected by default, and shows a live result count above the list equal to the number
    of notes listed - Given no notes exist yet, when the notes list page loads, then the web app shows
    an empty-state message and a result count of 0 instead of an error
  group: EP-006
  covers: []
  status: active
  slug: team-member-want-view-list-all-notes-web-app-browse
- id: US-006-02
  text: As a Team member, I want to create a new note using the web editor, so that add notes without
    needing to call the API manually.
  group: EP-006
  covers: []
  screen: NoteEditorScreen
  status: active
  slug: team-member-want-create-new-note-using-web-editor-add
- id: US-006-03
  text: As a Team member, I want to edit an existing note's title or body using the web editor, so that
    keep notes accurate from the browser.
  group: EP-006
  covers: []
  screen: NoteEditorScreen
  status: active
  slug: team-member-want-edit-existing-note-s-title-body-using
- id: US-006-04
  text: As a Team member, I want to search notes using a search box with free-text and tag filters, so
    that quickly find relevant notes from the browser.
  group: EP-006
  covers: []
  screen: SearchScreen
  status: active
  slug: team-member-want-search-notes-using-search-box-free-text
- id: US-006-05
  text: As a Team member, I want to log in from the web app by requesting an email one-time code and entering
    it on a code-entry screen, so that sign in securely without a password and understand why a code was
    refused.
  group: EP-006
  covers: []
  screen: CodeEntryScreen
  status: active
  slug: team-member-want-log-web-app-by-requesting-email-one
- id: US-006-06
  text: As a Team member, I want to archive, restore or pin/unpin a note from the web app so that manage
    note lifecycle and ordering without calling the API directly, so that manage note lifecycle without
    calling the API directly.
  group: EP-006
  covers: []
  status: active
  slug: team-member-want-archive-restore-pin-unpin-note-web-app
coverage: {}
orphans:
- US-001-01
- US-001-02
- US-001-03
- US-001-04
- US-001-05
- US-001-06
- US-002-01
- US-002-02
- US-002-03
- US-002-04
- US-002-05
- US-002-06
- US-003-01
- US-003-02
- US-003-03
- US-003-04
- US-003-05
- US-003-06
- US-004-01
- US-004-02
- US-004-03
- US-004-04
- US-004-05
- US-004-06
- US-005-01
- US-005-02
- US-005-03
- US-005-04
- US-005-05
- US-005-06
- US-006-01
- US-006-02
- US-006-03
- US-006-04
- US-006-05
- US-006-06
