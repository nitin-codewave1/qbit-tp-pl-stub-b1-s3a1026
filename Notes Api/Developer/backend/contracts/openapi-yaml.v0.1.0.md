---
id: agent-developer/notes_api/backend/contracts/openapi.yaml
type: openapi
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/contracts/openapi.yaml
depends_on: []
commit_kind: append
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: notes_api:backend/contracts/openapi.yaml
---

openapi: 3.1.0
info:
  title: Backend API
  version: 1.0.0
  description: Operations the backend module specifications define, rendered from
    their structured operation records. None was inferred.
paths:
  /auth/otp/send:
    post:
      operationId: API-AUTH-707260
      summary: Request a 6-digit email one-time code for sign-in. Accepts at most
        3 requests per email within a trailing 10 minutes (every request counts);
        further requests return 415 and create no code and send no email. Not refused
        during an account lock, but still subject to the resend limit. Response must
        not reveal whether the email has a User.
      description: Defined by module `auth` in `modules/auth-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - email
              properties:
                email:
                  type: string
                  description: Email address to which the code is sent. Compared in
                    normalised form (ASM-BE-015). Personal data; never logged unredacted.
                    Format validation rule is unstated (GAP-BE-035).
      responses:
        '200':
          description: Request a 6-digit email one-time code for sign-in. Accepts
            at most 3 requests per email within a trailing 10 minutes (every request
            counts); further requests return 415 and create no code and send no email.
            Not refused during an account lock, but still subject to the resend limit.
            Response must not reveal whether the email has a User.
        '400':
          description: See `modules/auth-SPEC.md`, API-AUTH-707260.
        '415':
          description: See `modules/auth-SPEC.md`, API-AUTH-707260.
        '500':
          description: See `modules/auth-SPEC.md`, API-AUTH-707260.
        '503':
          description: See `modules/auth-SPEC.md`, API-AUTH-707260.
      x-devagent-source:
        module: auth
        document: modules/auth-SPEC.md
        id: API-AUTH-707260
  /auth/otp/verify:
    post:
      operationId: API-AUTH-692907
      summary: Verify a 6-digit email one-time code. A correct, unexpired, unused
        code on an unlocked account succeeds, consumes the code (single use, safe
        under concurrent submission) and creates the User on first successful verification.
        Locked account (15 minutes after 5 wrong codes), wrong code, expired code
        (older than 10 minutes) and already-used code are each refused with 415, distinguished
        only by the error code. Success status and credential returned are not fixed
        by inputs (GAP-BE-604, Q-BE-012).
      description: Defined by module `auth` in `modules/auth-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - email
              - code
              properties:
                email:
                  type: string
                  description: Email address the code was sent to. Compared in normalised
                    form (ASM-BE-015). Personal data.
                code:
                  type: string
                  description: The 6-digit numeric one-time code received by email
                    (BR-029). Sent as a string so leading zeros survive.
      responses:
        '200':
          description: Verify a 6-digit email one-time code. A correct, unexpired,
            unused code on an unlocked account succeeds, consumes the code (single
            use, safe under concurrent submission) and creates the User on first successful
            verification. Locked account (15 minutes after 5 wrong codes), wrong code,
            expired code (older than 10 minutes) and already-used code are each refused
            with 415, distinguished only by the error code. Success status and credential
            returned are not fixed by inputs (GAP-BE-604, Q-BE-012).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required: []
                properties:
                  credential:
                    type: string
                    description: Credential granting authenticated access after successful
                      verification. Form (body token, cookie or other) and lifetime
                      are undecided (Q-BE-012, GAP-BE-302); may instead be delivered
                      outside the body.
        '400':
          description: See `modules/auth-SPEC.md`, API-AUTH-692907.
        '415':
          description: See `modules/auth-SPEC.md`, API-AUTH-692907.
        '500':
          description: See `modules/auth-SPEC.md`, API-AUTH-692907.
      x-devagent-source:
        module: auth
        document: modules/auth-SPEC.md
        id: API-AUTH-692907
  /notes:
    post:
      operationId: API-NOTES-232390
      summary: Create a note with a mandatory non-empty title and body, optionally
        pinned (US-002-01, BR-005, BR-018)
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - title
              - body
              properties:
                title:
                  type: string
                  description: Note title; mandatory and non-empty at creation (BR-005)
                body:
                  type: string
                  description: Note body; mandatory and non-empty at creation (BR-005)
                pinned:
                  type: boolean
                  description: Whether the note is pinned; defaults to false (BR-018)
      responses:
        '201':
          description: Create a note with a mandatory non-empty title and body, optionally
            pinned (US-002-01, BR-005, BR-018)
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - title
                - body
                - status
                - pinned
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    type: string
                    description: Identifier of the created note
                  title:
                    type: string
                    description: Note title
                  body:
                    type: string
                    description: Note body
                  status:
                    type: string
                    description: Note status, active or archived; a new note is active
                  pinned:
                    type: boolean
                    description: Whether the note is pinned
                  tags:
                    type: array
                    description: Tags applied to the note
                  created_at:
                    type: string
                    description: Creation timestamp
                  updated_at:
                    type: string
                    description: Last update timestamp
        '400':
          description: See `modules/notes-SPEC.md`, API-NOTES-232390.
        '401':
          description: See `modules/notes-SPEC.md`, API-NOTES-232390.
        '403':
          description: See `modules/notes-SPEC.md`, API-NOTES-232390.
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-232390
  /notes/search:
    get:
      operationId: API-SEARCH-580505
      summary: Search notes and tags with pagination and ordering; pinned notes precede
        unpinned notes in results. Path and parameter names are proposed (no input
        supplies them, see GAP-BE-303).
      description: Defined by module `search` in `modules/search-SPEC.md`.
      parameters:
      - name: q
        in: query
        required: false
        description: Free-text search term matched against note title, body and tag
          names. Proposed name; format and length limits unstated (GAP-BE-035).
        schema:
          type: string
      - name: tag
        in: query
        required: false
        description: Restrict results to notes carrying this tag name. Proposed name;
          tag format unstated (GAP-BE-035).
        schema:
          type: string
      - name: include_archived
        in: query
        required: false
        description: When true, archived notes are included in results; an invalid
          value returns a 400 validation failure.
        schema:
          type: boolean
      - name: page
        in: query
        required: false
        description: Page number to return. Proposed name; invalid values return 400.
        schema:
          type: integer
      - name: page_size
        in: query
        required: false
        description: Number of results per page. Default and maximum not stated (GAP-BE-500);
          NFR-007 names page sizes of 100. Invalid values return 400.
        schema:
          type: integer
      responses:
        '200':
          description: Search notes and tags with pagination and ordering; pinned
            notes precede unpinned notes in results. Path and parameter names are
            proposed (no input supplies them, see GAP-BE-303).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - items
                - page
                - page_size
                - total
                properties:
                  items:
                    type: array
                    description: Matching notes for the requested page, with every
                      pinned note before every unpinned note (INV-BE-001). Empty array
                      with 200 when nothing matches.
                  page:
                    type: integer
                    description: The page number returned.
                  page_size:
                    type: integer
                    description: The page size applied to this response.
                  total:
                    type: integer
                    description: Total number of notes matching the query across all
                      pages.
        '400':
          description: See `modules/search-SPEC.md`, API-SEARCH-580505.
        '401':
          description: See `modules/search-SPEC.md`, API-SEARCH-580505.
        '403':
          description: See `modules/search-SPEC.md`, API-SEARCH-580505.
        '503':
          description: See `modules/search-SPEC.md`, API-SEARCH-580505.
      x-devagent-source:
        module: search
        document: modules/search-SPEC.md
        id: API-SEARCH-580505
  /notes/{id}:
    get:
      operationId: API-NOTES-045877
      summary: Get a single note by id, including archived notes (US-002-06)
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      parameters:
      - name: id
        in: query
        required: true
        description: 'Path parameter: note identifier'
        schema:
          type: string
      responses:
        '200':
          description: Get a single note by id, including archived notes (US-002-06)
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - title
                - body
                - status
                - pinned
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    type: string
                    description: Note identifier
                  title:
                    type: string
                    description: Note title
                  body:
                    type: string
                    description: Note body
                  status:
                    type: string
                    description: active or archived
                  pinned:
                    type: boolean
                    description: Whether the note is pinned
                  tags:
                    type: array
                    description: Tags applied to the note
                  created_at:
                    type: string
                    description: Creation timestamp
                  updated_at:
                    type: string
                    description: Last update timestamp
        '401':
          description: See `modules/notes-SPEC.md`, API-NOTES-045877.
        '403':
          description: See `modules/notes-SPEC.md`, API-NOTES-045877.
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-045877.
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-045877
    patch:
      operationId: API-NOTES-847551
      summary: Partially edit a note; fields not sent are never overwritten (US-002-02,
        BR-006, BR-018)
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - id
              properties:
                id:
                  type: string
                  description: 'Path parameter: note identifier'
                title:
                  type: string
                  description: New title; omitted fields stay unchanged
                body:
                  type: string
                  description: New body; omitted fields stay unchanged
                pinned:
                  type: boolean
                  description: Pin or unpin the note (BR-018)
      responses:
        '200':
          description: Partially edit a note; fields not sent are never overwritten
            (US-002-02, BR-006, BR-018)
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - title
                - body
                - status
                - pinned
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    type: string
                    description: Note identifier
                  title:
                    type: string
                    description: Note title after edit
                  body:
                    type: string
                    description: Note body after edit
                  status:
                    type: string
                    description: active or archived
                  pinned:
                    type: boolean
                    description: Whether the note is pinned
                  tags:
                    type: array
                    description: Tags applied to the note
                  created_at:
                    type: string
                    description: Creation timestamp
                  updated_at:
                    type: string
                    description: Last update timestamp
        '400':
          description: See `modules/notes-SPEC.md`, API-NOTES-847551.
        '401':
          description: See `modules/notes-SPEC.md`, API-NOTES-847551.
        '403':
          description: See `modules/notes-SPEC.md`, API-NOTES-847551.
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-847551.
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-847551
  /notes/{id}/archive:
    post:
      operationId: API-NOTES-810640
      summary: Archive a note without deleting data (US-002-04, BR-007)
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - id
              properties:
                id:
                  type: string
                  description: 'Path parameter: note identifier'
      responses:
        '200':
          description: Archive a note without deleting data (US-002-04, BR-007)
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - status
                - updated_at
                properties:
                  id:
                    type: string
                    description: Note identifier
                  status:
                    type: string
                    description: archived after success
                  updated_at:
                    type: string
                    description: Last update timestamp
        '401':
          description: See `modules/notes-SPEC.md`, API-NOTES-810640.
        '403':
          description: See `modules/notes-SPEC.md`, API-NOTES-810640.
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-810640.
        '409':
          description: See `modules/notes-SPEC.md`, API-NOTES-810640.
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-810640
  /notes/{id}/tags:
    post:
      operationId: API-NOTES-359413
      summary: Add a tag to a note; an existing tag creates no duplicate (US-002-03)
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - id
              - name
              properties:
                id:
                  type: string
                  description: 'Path parameter: note identifier'
                name:
                  type: string
                  description: Tag name to apply; format rules are unspecified
      responses:
        '200':
          description: Add a tag to a note; an existing tag creates no duplicate (US-002-03)
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - tags
                - updated_at
                properties:
                  id:
                    type: string
                    description: Note identifier
                  tags:
                    type: array
                    description: Tags now applied to the note, without duplicates
                  updated_at:
                    type: string
                    description: Last update timestamp
        '400':
          description: See `modules/notes-SPEC.md`, API-NOTES-359413.
        '401':
          description: See `modules/notes-SPEC.md`, API-NOTES-359413.
        '403':
          description: See `modules/notes-SPEC.md`, API-NOTES-359413.
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-359413.
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-359413
  /notes/{id}/tags/{name}:
    delete:
      operationId: API-NOTES-150096
      summary: Remove a tag from a note (US-002-03)
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      parameters:
      - name: id
        in: query
        required: true
        description: 'Path parameter: note identifier'
        schema:
          type: string
      - name: name
        in: query
        required: true
        description: 'Path parameter: tag name to remove'
        schema:
          type: string
      responses:
        '200':
          description: Remove a tag from a note (US-002-03)
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - tags
                - updated_at
                properties:
                  id:
                    type: string
                    description: Note identifier
                  tags:
                    type: array
                    description: Tags remaining on the note
                  updated_at:
                    type: string
                    description: Last update timestamp
        '401':
          description: See `modules/notes-SPEC.md`, API-NOTES-150096.
        '403':
          description: See `modules/notes-SPEC.md`, API-NOTES-150096.
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-150096.
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-150096
  /notes/{id}/unarchive:
    post:
      operationId: API-NOTES-917869
      summary: Restore an archived note to active with all data preserved (US-002-05,
        BR-008)
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - id
              properties:
                id:
                  type: string
                  description: 'Path parameter: note identifier'
      responses:
        '200':
          description: Restore an archived note to active with all data preserved
            (US-002-05, BR-008)
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - status
                - updated_at
                properties:
                  id:
                    type: string
                    description: Note identifier
                  status:
                    type: string
                    description: active after success
                  updated_at:
                    type: string
                    description: Last update timestamp
        '401':
          description: See `modules/notes-SPEC.md`, API-NOTES-917869.
        '403':
          description: See `modules/notes-SPEC.md`, API-NOTES-917869.
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-917869.
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-917869
  /openapi.json:
    get:
      operationId: API-APIDOCS-098363
      summary: Fetch the published OpenAPI document for notes_api. The document covers
        every endpoint, with examples for search and tagging endpoints. It also carries
        the data-handling note (EU residency of active and archived note data) and
        the Scope section (attachments, real-time and mobile-specific calls excluded).
        The path is proposed because no input supplies one (GAP-BE-303).
      description: Defined by module `api-docs` in `modules/api-docs-SPEC.md`.
      responses:
        '200':
          description: Fetch the published OpenAPI document for notes_api. The document
            covers every endpoint, with examples for search and tagging endpoints.
            It also carries the data-handling note (EU residency of active and archived
            note data) and the Scope section (attachments, real-time and mobile-specific
            calls excluded). The path is proposed because no input supplies one (GAP-BE-303).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - openapi
                - info
                - paths
                properties:
                  openapi:
                    type: string
                    description: OpenAPI specification version the document conforms
                      to.
                  info:
                    type: object
                    description: API metadata. It carries the title, the version,
                      the data-handling note stating where active and archived note
                      data is stored and served (BR-013, US-004-06), and the Scope
                      section stating the excluded attachment, real-time and mobile-specific
                      calls (BR-014 to BR-016, US-005-01).
                  paths:
                    type: object
                    description: Every endpoint with parameters, request and response
                      schemas, and the documented error statuses (BR-012, NFR-008).
                      Search and tagging endpoints include examples (NFR-009).
                  components:
                    type: object
                    description: Shared schemas, including the error envelope and
                      the error-code registry, referenced from paths.
        '406':
          description: See `modules/api-docs-SPEC.md`, API-APIDOCS-098363.
      x-devagent-source:
        module: api-docs
        document: modules/api-docs-SPEC.md
        id: API-APIDOCS-098363
  /residency/drift-alerts:
    get:
      operationId: API-RESIDENCYCOMPLIANCE-047792
      summary: List drift alerts raised when a resource is detected outside approved
        EU regions, to be raised within 15 minutes of detection (NFR-003). Alert channel
        and recipients are open (GAP-BE-308). Proposed path (GAP-BE-303).
      description: Defined by module `residency-compliance` in `modules/residency-compliance-SPEC.md`.
      parameters:
      - name: resource_id
        in: query
        required: false
        description: Query filter by resource.
        schema:
          type: string
      - name: raised_after
        in: query
        required: false
        description: 'Query filter: only alerts raised after this timestamp.'
        schema:
          type: string
      - name: page
        in: query
        required: false
        description: Page number.
        schema:
          type: integer
      - name: page_size
        in: query
        required: false
        description: Items per page.
        schema:
          type: integer
      responses:
        '200':
          description: List drift alerts raised when a resource is detected outside
            approved EU regions, to be raised within 15 minutes of detection (NFR-003).
            Alert channel and recipients are open (GAP-BE-308). Proposed path (GAP-BE-303).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - items
                - total
                properties:
                  items:
                    type: array
                    description: Drift alerts, each with id, resource_id, detected_region
                      and raised_at.
                  total:
                    type: integer
                    description: Total number of matching alerts.
        '400':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-047792.
        '401':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-047792.
        '403':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-047792.
      x-devagent-source:
        module: residency-compliance
        document: modules/residency-compliance-SPEC.md
        id: API-RESIDENCYCOMPLIANCE-047792
  /residency/launch-gate:
    get:
      operationId: API-RESIDENCYCOMPLIANCE-395094
      summary: Evaluate the launch gate from the current compliance report. Read-only.
        Whether a pass authorises release or Engineering Lead sign-off is required
        is unresolved (CON-BE-151, Q-BE-152), so no sign-off operation is defined.
        Proposed path (GAP-BE-303).
      description: Defined by module `residency-compliance` in `modules/residency-compliance-SPEC.md`.
      responses:
        '200':
          description: Evaluate the launch gate from the current compliance report.
            Read-only. Whether a pass authorises release or Engineering Lead sign-off
            is required is unresolved (CON-BE-151, Q-BE-152), so no sign-off operation
            is defined. Proposed path (GAP-BE-303).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - gate_passed
                - evaluated_at
                - blocking_resources
                properties:
                  gate_passed:
                    type: boolean
                    description: True only when every component is compliant.
                  evaluated_at:
                    type: string
                    description: Timestamp of the evaluation against a freshly refreshed
                      report.
                  blocking_resources:
                    type: array
                    description: Resources that are failing or unverified and prevent
                      the gate passing; empty when passed.
        '401':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-395094.
        '403':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-395094.
        '503':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-395094.
      x-devagent-source:
        module: residency-compliance
        document: modules/residency-compliance-SPEC.md
        id: API-RESIDENCYCOMPLIANCE-395094
  /residency/report:
    get:
      operationId: API-RESIDENCYCOMPLIANCE-894798
      summary: Return the residency compliance report covering all infrastructure
        components, refreshed at each request so it is accurate at launch gate checks
        (NFR-004). Proposed path (GAP-BE-303); storage of report data is open (GAP-BE-305).
      description: Defined by module `residency-compliance` in `modules/residency-compliance-SPEC.md`.
      responses:
        '200':
          description: Return the residency compliance report covering all infrastructure
            components, refreshed at each request so it is accurate at launch gate
            checks (NFR-004). Proposed path (GAP-BE-303); storage of report data is
            open (GAP-BE-305).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - generated_at
                - total_components
                - verified_eu_components
                - non_compliant_components
                - unverified_components
                - components
                properties:
                  generated_at:
                    type: string
                    description: Timestamp at which the report was generated.
                  total_components:
                    type: integer
                    description: Number of infrastructure components assessed.
                  verified_eu_components:
                    type: integer
                    description: Number of components verified as being in approved
                      EU regions.
                  non_compliant_components:
                    type: integer
                    description: Number of components in the fail state.
                  unverified_components:
                    type: integer
                    description: Number of components flagged unverified, including
                      unrecognised types.
                  components:
                    type: array
                    description: Per-component entries with resource_id, resource_type,
                      region, compliance_status and last_scanned_at.
        '401':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-894798.
        '403':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-894798.
        '503':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-894798.
      x-devagent-source:
        module: residency-compliance
        document: modules/residency-compliance-SPEC.md
        id: API-RESIDENCYCOMPLIANCE-894798
  /residency/resources:
    get:
      operationId: API-RESIDENCYCOMPLIANCE-381444
      summary: List inventoried infrastructure resources with region, compliance status
        and last scan time. Resource types the scanner does not recognise appear with
        status unverified (AC-001-04-02). Proposed path (GAP-BE-303).
      description: Defined by module `residency-compliance` in `modules/residency-compliance-SPEC.md`.
      parameters:
      - name: compliance_status
        in: query
        required: false
        description: 'Query filter: pass, fail or unverified.'
        schema:
          type: string
      - name: resource_type
        in: query
        required: false
        description: 'Query filter: database, compute or backup storage.'
        schema:
          type: string
      - name: page
        in: query
        required: false
        description: Page number; default and maximum page size are not stated (GAP-BE-500).
        schema:
          type: integer
      - name: page_size
        in: query
        required: false
        description: Items per page.
        schema:
          type: integer
      responses:
        '200':
          description: List inventoried infrastructure resources with region, compliance
            status and last scan time. Resource types the scanner does not recognise
            appear with status unverified (AC-001-04-02). Proposed path (GAP-BE-303).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - items
                - total
                properties:
                  items:
                    type: array
                    description: Resources, each with resource_id, resource_type,
                      region, compliance_status and last_scanned_at.
                  total:
                    type: integer
                    description: Total number of matching resources.
        '400':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-381444.
        '401':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-381444.
        '403':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-381444.
      x-devagent-source:
        module: residency-compliance
        document: modules/residency-compliance-SPEC.md
        id: API-RESIDENCYCOMPLIANCE-381444
  /residency/scans:
    post:
      operationId: API-RESIDENCYCOMPLIANCE-466056
      summary: Trigger a residency scan of provisioned infrastructure, recording region
        and compliance status per resource. Proposed path (GAP-BE-303). Drift alerts
        are raised for any resource found outside approved regions.
      description: Defined by module `residency-compliance` in `modules/residency-compliance-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required: []
              properties:
                resource_ids:
                  type: array
                  description: Optional subset of resource identifiers to scan; all
                    resources are scanned when omitted.
      responses:
        '202':
          description: Trigger a residency scan of provisioned infrastructure, recording
            region and compliance status per resource. Proposed path (GAP-BE-303).
            Drift alerts are raised for any resource found outside approved regions.
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - scan_started_at
                - resources_queued
                properties:
                  scan_started_at:
                    type: string
                    description: Timestamp at which the scan was accepted.
                  resources_queued:
                    type: integer
                    description: Number of resources included in the scan.
        '400':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-466056.
        '401':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-466056.
        '403':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-466056.
        '404':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-466056.
        '409':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-466056.
        '503':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-466056.
      x-devagent-source:
        module: residency-compliance
        document: modules/residency-compliance-SPEC.md
        id: API-RESIDENCYCOMPLIANCE-466056
  /residency/validations:
    post:
      operationId: API-RESIDENCYCOMPLIANCE-405210
      summary: Validate a resource's declared region against the approved EU region
        list (CI/CD residency gate and provisioning check). Proposed path (GAP-BE-303);
        approved region list pending (Q-BE-151). A violation is reported in the body,
        as pipeline failure handling is outside HTTP.
      description: Defined by module `residency-compliance` in `modules/residency-compliance-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - resource_id
              - resource_type
              properties:
                resource_id:
                  type: string
                  description: Identifier of the infrastructure component being checked.
                resource_type:
                  type: string
                  description: 'Component type: database, compute or backup storage.
                    Unrecognised types are flagged unverified, not rejected.'
                region:
                  type: string
                  description: Declared region of the resource. If missing or unreadable
                    the check fails and deployment is blocked (AC-001-03-03).
      responses:
        '200':
          description: Validate a resource's declared region against the approved
            EU region list (CI/CD residency gate and provisioning check). Proposed
            path (GAP-BE-303); approved region list pending (Q-BE-151). A violation
            is reported in the body, as pipeline failure handling is outside HTTP.
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - resource_id
                - compliance_status
                - deployment_blocked
                properties:
                  resource_id:
                    type: string
                    description: Identifier of the checked resource.
                  region:
                    type: string
                    description: Region as read for the resource; absent when metadata
                      is missing or unreadable.
                  compliance_status:
                    type: string
                    description: 'Result: pass, fail or unverified.'
                  deployment_blocked:
                    type: boolean
                    description: True when the check fails, including missing or unreadable
                      region metadata.
                  violation_reason:
                    type: string
                    description: Residency violation reason present when status is
                      fail.
        '400':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-405210.
        '401':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-405210.
        '403':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-405210.
        '503':
          description: See `modules/residency-compliance-SPEC.md`, API-RESIDENCYCOMPLIANCE-405210.
      x-devagent-source:
        module: residency-compliance
        document: modules/residency-compliance-SPEC.md
        id: API-RESIDENCYCOMPLIANCE-405210
