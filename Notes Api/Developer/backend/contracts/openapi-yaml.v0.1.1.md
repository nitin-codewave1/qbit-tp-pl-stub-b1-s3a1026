---
id: agent-developer/notes_api/backend/contracts/openapi.yaml
type: openapi
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/contracts/openapi.yaml
depends_on: []
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: notes_api:backend/contracts/openapi.yaml
---

openapi: 3.1.0
info:
  title: Backend API
  version: 1.0.0
  description: Operations the backend module specifications define, rendered from
    their structured operation records. None was inferred.
paths:
  /auth/request-code:
    post:
      operationId: API-AUTH-015365
      summary: Request a one-time login code, delivered by email through AWS SES.
        Limited to 3 requests per 10 minutes; not refused while the account is locked.
        The code is valid for 10 minutes and single-use.
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
                  description: Email address the one-time login code is sent to. Must
                    be a valid email address.
      responses:
        '202':
          description: Request a one-time login code, delivered by email through AWS
            SES. Limited to 3 requests per 10 minutes; not refused while the account
            is locked. The code is valid for 10 minutes and single-use.
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - message
                properties:
                  message:
                    type: string
                    description: Human-readable confirmation that a code was sent.
                      Never contains the code.
                  expires_in_seconds:
                    type: integer
                    description: Validity window of the issued code in seconds (10
                      minutes per BR-024).
        '400':
          description: See `modules/auth-SPEC.md`, API-AUTH-015365.
        '415':
          description: See `modules/auth-SPEC.md`, API-AUTH-015365.
      x-devagent-source:
        module: auth
        document: modules/auth-SPEC.md
        id: API-AUTH-015365
  /auth/verify-code:
    post:
      operationId: API-AUTH-592570
      summary: Verify a one-time login code for an email address. Rejects wrong, expired,
        already-used codes, and refuses verification while the account is locked (15
        minutes after 5 wrong codes). A successful verify consumes the code.
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
                  description: Email address the code was issued for.
                code:
                  type: string
                  description: Six-digit one-time code received by email.
      responses:
        '200':
          description: Verify a one-time login code for an email address. Rejects
            wrong, expired, already-used codes, and refuses verification while the
            account is locked (15 minutes after 5 wrong codes). A successful verify
            consumes the code.
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - verified
                properties:
                  verified:
                    type: boolean
                    description: True when the code was accepted. The credential type,
                      lifetime and transport issued on success are undecided (Q-BE-003)
                      and are not specified here.
        '400':
          description: See `modules/auth-SPEC.md`, API-AUTH-592570.
        '415':
          description: See `modules/auth-SPEC.md`, API-AUTH-592570.
      x-devagent-source:
        module: auth
        document: modules/auth-SPEC.md
        id: API-AUTH-592570
  /notes:
    post:
      operationId: API-NOTES-232390
      summary: Create a note with title, body and optional tags; pinned defaults to
        false
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
                  description: Note title; must be non-empty
                body:
                  type: string
                  description: Note body; must be non-empty
                tags:
                  type: array
                  description: Optional list of tag names to attach
                pinned:
                  type: boolean
                  description: Pinned flag; defaults to false
      responses:
        '201':
          description: Create a note with title, body and optional tags; pinned defaults
            to false
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - title
                - body
                - pinned
                - archived
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    type: string
                    description: Unique note identifier
                  title:
                    type: string
                    description: Note title
                  body:
                    type: string
                    description: Note body
                  pinned:
                    type: boolean
                    description: Whether the note is pinned
                  archived:
                    type: boolean
                    description: Whether the note is archived
                  tags:
                    type: array
                    description: Tag names on the note
                  created_at:
                    type: string
                    description: Creation timestamp
                  updated_at:
                    type: string
                    description: Last update timestamp
        '400':
          description: See `modules/notes-SPEC.md`, API-NOTES-232390.
        '415':
          description: See `modules/notes-SPEC.md`, API-NOTES-232390.
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-232390
  /notes/search:
    get:
      operationId: API-SEARCH-580505
      summary: Search notes by keyword, tag, or both combined. Excludes archived notes
        unless requested. Pinned notes come first. Results are paginated. Returns
        503 with Retry-After when the search index is degraded.
      description: Defined by module `search` in `modules/search-SPEC.md`.
      parameters:
      - name: q
        in: query
        required: false
        description: Keyword matched against note title and body. Malformed values
          return 400 VALIDATION_ERROR.
        schema:
          type: string
      - name: tags
        in: query
        required: false
        description: Tag filter. Combined with q when both are given. A note must
          carry all supplied tags (ASM-BE-009, pending Q-BE-004). Sent as a query
          parameter.
        schema:
          type: array
      - name: page
        in: query
        required: false
        description: Page number for pagination. Invalid values return 400 VALIDATION_ERROR.
        schema:
          type: integer
      - name: limit
        in: query
        required: false
        description: Page size. Default 20 and maximum 100 are assumed (ASM-BE-011,
          pending Q-BE-005). Invalid values return 400 VALIDATION_ERROR.
        schema:
          type: integer
      - name: include_archived
        in: query
        required: false
        description: When true, archived notes are included in results. Default false,
          so archived notes are excluded.
        schema:
          type: boolean
      responses:
        '200':
          description: Search notes by keyword, tag, or both combined. Excludes archived
            notes unless requested. Pinned notes come first. Results are paginated.
            Returns 503 with Retry-After when the search index is degraded.
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - items
                - page
                - limit
                - total
                properties:
                  items:
                    type: array
                    description: Matching notes on the requested page, pinned notes
                      first. Each item shows the note id, title, body, tags, pinned
                      flag, archived state and timestamps.
                  page:
                    type: integer
                    description: Page number of this result set.
                  limit:
                    type: integer
                    description: Page size applied to this result set.
                  total:
                    type: integer
                    description: Total number of notes matching the filters across
                      all pages (proposed; the total count is not stated in the inputs).
        '400':
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
      summary: Get a single note by id, including its tags and archived state
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
          description: Get a single note by id, including its tags and archived state
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - title
                - body
                - pinned
                - archived
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    type: string
                    description: Unique note identifier
                  title:
                    type: string
                    description: Note title
                  body:
                    type: string
                    description: Note body
                  pinned:
                    type: boolean
                    description: Whether the note is pinned
                  archived:
                    type: boolean
                    description: Whether the note is archived
                  tags:
                    type: array
                    description: Tag names on the note
                  created_at:
                    type: string
                    description: Creation timestamp
                  updated_at:
                    type: string
                    description: Last update timestamp
        '400':
          description: See `modules/notes-SPEC.md`, API-NOTES-045877.
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-045877.
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-045877
    patch:
      operationId: API-NOTES-847551
      summary: Partially edit a note's title, body or pinned flag (pin and unpin)
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
                  description: New title; must be non-empty if supplied
                body:
                  type: string
                  description: New body; must be non-empty if supplied
                pinned:
                  type: boolean
                  description: Set true to pin, false to unpin
      responses:
        '200':
          description: Partially edit a note's title, body or pinned flag (pin and
            unpin)
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - title
                - body
                - pinned
                - archived
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    type: string
                    description: Unique note identifier
                  title:
                    type: string
                    description: Note title
                  body:
                    type: string
                    description: Note body
                  pinned:
                    type: boolean
                    description: Whether the note is pinned
                  archived:
                    type: boolean
                    description: Whether the note is archived
                  tags:
                    type: array
                    description: Tag names on the note
                  created_at:
                    type: string
                    description: Creation timestamp
                  updated_at:
                    type: string
                    description: Updated timestamp, refreshed by the edit
        '400':
          description: See `modules/notes-SPEC.md`, API-NOTES-847551.
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-847551.
        '415':
          description: See `modules/notes-SPEC.md`, API-NOTES-847551.
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-847551
  /notes/{id}/archive:
    post:
      operationId: API-NOTES-810640
      summary: Archive a note (flag only, no hard delete)
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
          description: Archive a note (flag only, no hard delete)
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - title
                - body
                - pinned
                - archived
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    type: string
                    description: Unique note identifier
                  title:
                    type: string
                    description: Note title
                  body:
                    type: string
                    description: Note body
                  pinned:
                    type: boolean
                    description: Whether the note is pinned
                  archived:
                    type: boolean
                    description: True after archiving
                  tags:
                    type: array
                    description: Tag names on the note
                  created_at:
                    type: string
                    description: Creation timestamp
                  updated_at:
                    type: string
                    description: Last update timestamp
        '400':
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
      summary: Add a tag to a note; adding a duplicate creates no second entry
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
              - tag
              properties:
                id:
                  type: string
                  description: 'Path parameter: note identifier'
                tag:
                  type: string
                  description: Tag name to attach
      responses:
        '200':
          description: Add a tag to a note; adding a duplicate creates no second entry
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - title
                - body
                - pinned
                - archived
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    type: string
                    description: Unique note identifier
                  title:
                    type: string
                    description: Note title
                  body:
                    type: string
                    description: Note body
                  pinned:
                    type: boolean
                    description: Whether the note is pinned
                  archived:
                    type: boolean
                    description: Whether the note is archived
                  tags:
                    type: array
                    description: Tag names on the note after the add
                  created_at:
                    type: string
                    description: Creation timestamp
                  updated_at:
                    type: string
                    description: Last update timestamp
        '400':
          description: See `modules/notes-SPEC.md`, API-NOTES-359413.
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-359413.
        '415':
          description: See `modules/notes-SPEC.md`, API-NOTES-359413.
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-359413
  /notes/{id}/tags/{tag}:
    delete:
      operationId: API-NOTES-150096
      summary: Remove a tag from a note
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      parameters:
      - name: id
        in: query
        required: true
        description: 'Path parameter: note identifier'
        schema:
          type: string
      - name: tag
        in: query
        required: true
        description: 'Path parameter: tag name to remove'
        schema:
          type: string
      responses:
        '200':
          description: Remove a tag from a note
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - title
                - body
                - pinned
                - archived
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    type: string
                    description: Unique note identifier
                  title:
                    type: string
                    description: Note title
                  body:
                    type: string
                    description: Note body
                  pinned:
                    type: boolean
                    description: Whether the note is pinned
                  archived:
                    type: boolean
                    description: Whether the note is archived
                  tags:
                    type: array
                    description: Tag names on the note after the removal
                  created_at:
                    type: string
                    description: Creation timestamp
                  updated_at:
                    type: string
                    description: Last update timestamp
        '400':
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
      summary: Unarchive a note, restoring it with all data intact
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
          description: Unarchive a note, restoring it with all data intact
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - title
                - body
                - pinned
                - archived
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    type: string
                    description: Unique note identifier
                  title:
                    type: string
                    description: Note title
                  body:
                    type: string
                    description: Note body
                  pinned:
                    type: boolean
                    description: Whether the note is pinned
                  archived:
                    type: boolean
                    description: False after unarchiving
                  tags:
                    type: array
                    description: Tag names on the note
                  created_at:
                    type: string
                    description: Creation timestamp
                  updated_at:
                    type: string
                    description: Last update timestamp
        '400':
          description: See `modules/notes-SPEC.md`, API-NOTES-917869.
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-917869.
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-917869
  /openapi:
    get:
      operationId: API-CONTRACTDOCS-304307
      summary: Retrieve the published OpenAPI 3.x document covering every implemented
        endpoint, including schemas, 4xx/5xx errors, examples, the data-residency
        handling note and the Scope section.
      description: Defined by module `contract-docs` in `modules/contract-docs-SPEC.md`.
      parameters:
      - name: Accept
        in: query
        required: false
        description: Content-negotiation header (not a body or query field) selecting
          the document format. An unsupported value yields 406 or a default-format
          fallback with a clear content type (US-004-01).
        schema:
          type: string
      responses:
        '200':
          description: Retrieve the published OpenAPI 3.x document covering every
            implemented endpoint, including schemas, 4xx/5xx errors, examples, the
            data-residency handling note and the Scope section.
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - openapi
                - info
                - paths
                - components
                properties:
                  openapi:
                    type: string
                    description: OpenAPI 3.x version string of the published document.
                  info:
                    type: object
                    description: 'Document metadata, including the Scope section (in-scope
                      and excluded capabilities: attachments, real-time, mobile) and
                      the data-residency handling note.'
                  paths:
                    type: object
                    description: Every implemented endpoint with parameters, request
                      bodies, success responses, 4xx/5xx error responses and examples;
                      100% of implemented endpoints must be present (NFR-008).
                  components:
                    type: object
                    description: Reusable schemas, including the error envelope, referenced
                      by the paths.
        '406':
          description: See `modules/contract-docs-SPEC.md`, API-CONTRACTDOCS-304307.
      x-devagent-source:
        module: contract-docs
        document: modules/contract-docs-SPEC.md
        id: API-CONTRACTDOCS-304307
  /residency/backup-validations:
    post:
      operationId: API-RESIDENCY-174124
      summary: Validate a backup configuration is EU-only; a non-EU configuration
        fails fast and writes no data (US-001-02).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - backup_target
              - region
              properties:
                backup_target:
                  type: string
                  description: Backup storage bucket or service identifier.
                region:
                  type: string
                  description: Region of the backup target.
                replica_regions:
                  type: array
                  description: Regions of any backup replicas or copies.
      responses:
        '200':
          description: Validate a backup configuration is EU-only; a non-EU configuration
            fails fast and writes no data (US-001-02).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - valid
                - regions_checked
                properties:
                  valid:
                    type: boolean
                    description: True when all backup locations are in approved EU
                      regions.
                  regions_checked:
                    type: array
                    description: Regions that were evaluated.
        '400':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-174124.
        '422':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-174124.
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-174124.
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-174124
  /residency/ci-gate-checks:
    post:
      operationId: API-RESIDENCY-034853
      summary: Evaluate a deployment against the CI residency gate, blocking non-EU
        deploys (US-001-03).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - deployment_id
              - target_regions
              properties:
                deployment_id:
                  type: string
                  description: Identifier of the CI deployment being evaluated.
                target_regions:
                  type: array
                  description: Regions the deployment would touch.
      responses:
        '200':
          description: Evaluate a deployment against the CI residency gate, blocking
            non-EU deploys (US-001-03).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - deployment_id
                - verdict
                - violations
                properties:
                  deployment_id:
                    type: string
                    description: Identifier of the evaluated deployment.
                  verdict:
                    type: string
                    description: 'Gate result: pass or fail.'
                  violations:
                    type: array
                    description: Non-EU resources or regions causing a failure; empty
                      on pass.
        '400':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-034853.
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-034853.
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-034853
  /residency/compliance-report:
    get:
      operationId: API-RESIDENCY-913995
      summary: Get the residency compliance report listing each resource, its region,
        pass/fail status and overall percent compliant (US-001-04).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      responses:
        '200':
          description: Get the residency compliance report listing each resource,
            its region, pass/fail status and overall percent compliant (US-001-04).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - resources
                - percent_compliant
                - generated_at
                properties:
                  resources:
                    type: array
                    description: Each resource with its region and pass/fail status.
                  percent_compliant:
                    type: number
                    description: Overall percentage of infrastructure verified in
                      EU regions.
                  generated_at:
                    type: string
                    description: Timestamp when the report was generated.
        '404':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-913995.
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-913995.
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-913995
  /residency/drift-scans:
    post:
      operationId: API-RESIDENCY-922826
      summary: Run a residency drift scan; non-EU resources are recorded and trigger
        an alert to the DevOps team (US-001-06).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      responses:
        '201':
          description: Run a residency drift scan; non-EU resources are recorded and
            trigger an alert to the DevOps team (US-001-06).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - scan_id
                - scanned_at
                - drift_detected
                - non_eu_resources
                - alert_sent
                properties:
                  scan_id:
                    type: string
                    description: Identifier of the scan run.
                  scanned_at:
                    type: string
                    description: Timestamp of the scan.
                  drift_detected:
                    type: boolean
                    description: True when any resource was found in a non-EU region.
                  non_eu_resources:
                    type: array
                    description: Details of resources found outside EU regions; empty
                      when none.
                  alert_sent:
                    type: boolean
                    description: Whether an alert was raised for this scan.
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-922826.
        '503':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-922826.
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-922826
  /residency/drift-scans/latest:
    get:
      operationId: API-RESIDENCY-726277
      summary: Get the result of the most recent drift scan (US-001-06).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      responses:
        '200':
          description: Get the result of the most recent drift scan (US-001-06).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - scan_id
                - scanned_at
                - drift_detected
                - non_eu_resources
                properties:
                  scan_id:
                    type: string
                    description: Identifier of the scan run.
                  scanned_at:
                    type: string
                    description: Timestamp of the scan.
                  drift_detected:
                    type: boolean
                    description: True when any resource was found in a non-EU region.
                  non_eu_resources:
                    type: array
                    description: Resources found outside EU regions; empty when none.
        '404':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-726277.
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-726277.
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-726277
  /residency/launch-gate:
    get:
      operationId: API-RESIDENCY-395094
      summary: Get the launch gate status, passing only when the residency report
        shows full EU compliance (US-001-05).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      responses:
        '200':
          description: Get the launch gate status, passing only when the residency
            report shows full EU compliance (US-001-05).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - verdict
                - percent_compliant
                - blocking_resources
                properties:
                  verdict:
                    type: string
                    description: 'Launch gate result: pass or fail.'
                  percent_compliant:
                    type: number
                    description: Compliance percentage from the current report.
                  blocking_resources:
                    type: array
                    description: Non-compliant resources blocking launch; empty on
                      pass.
        '404':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-395094.
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-395094.
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-395094
  /residency/provisioning-requests:
    post:
      operationId: API-RESIDENCY-026899
      summary: Submit a resource provisioning request; accepted only if the target
        region is an approved EU region, otherwise rejected (US-001-01).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - resource_type
              - resource_name
              - region
              properties:
                resource_type:
                  type: string
                  description: Kind of infrastructure resource to provision (database,
                    compute, backup storage).
                resource_name:
                  type: string
                  description: Name of the resource to provision.
                region:
                  type: string
                  description: Target cloud region; must be an approved EU region.
      responses:
        '201':
          description: Submit a resource provisioning request; accepted only if the
            target region is an approved EU region, otherwise rejected (US-001-01).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - resource_name
                - region
                - status
                properties:
                  resource_name:
                    type: string
                    description: Name of the accepted resource.
                  region:
                    type: string
                    description: Region the resource was accepted for.
                  status:
                    type: string
                    description: Outcome of the provisioning request.
        '400':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-026899.
        '422':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-026899.
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-026899.
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-026899
