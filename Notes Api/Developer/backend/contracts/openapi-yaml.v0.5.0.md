---
id: agent-developer/notes_api/backend/contracts/openapi.yaml
type: openapi
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/contracts/openapi.yaml
depends_on: []
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
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
      summary: 'Request an email one-time login code: 6 digits, expires 10 minutes
        after issue, limited to 3 requests per 10 minutes per email (429 beyond that).
        Allowed even while verification is locked (BR-032).'
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
                  description: 'Email address that will receive the 6-digit one-time
                    login code. PII: masked in logs and examples.'
                  type: string
      responses:
        '202':
          description: 'Request an email one-time login code: 6 digits, expires 10
            minutes after issue, limited to 3 requests per 10 minutes per email (429
            beyond that). Allowed even while verification is locked (BR-032).'
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required: []
                properties:
                  message:
                    description: Confirmation that the code request was accepted and
                      a code email is being sent. Does not contain the code.
                    type: string
        '400':
          description: See `modules/auth-SPEC.md`, API-AUTH-015365.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthError'
        '429':
          description: See `modules/auth-SPEC.md`, API-AUTH-015365.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthError'
        '500':
          description: See `modules/auth-SPEC.md`, API-AUTH-015365.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthError'
      x-devagent-source:
        module: auth
        document: modules/auth-SPEC.md
        id: API-AUTH-015365
  /auth/verify-code:
    post:
      operationId: API-AUTH-592570
      summary: 'Verify an emailed one-time code. Errors: wrong code 401, already used
        409, expired 410, locked after 5 wrong codes for 15 minutes 423 (BR-033).'
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
                  description: 'Email address the code was issued to. PII: masked
                    in logs and examples.'
                  type: string
                code:
                  description: The 6-digit numeric one-time code received by email.
                    Single use; never logged.
                  type: string
      responses:
        '200':
          description: 'Verify an emailed one-time code. Errors: wrong code 401, already
            used 409, expired 410, locked after 5 wrong codes for 15 minutes 423 (BR-033).'
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required: []
                properties:
                  message:
                    description: Confirmation that the code was valid and login succeeded.
                      The session credential (type, lifetime, transport) is undecided
                      pending Q-BE-003, so no credential field is asserted.
                    type: string
        '400':
          description: See `modules/auth-SPEC.md`, API-AUTH-592570.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthError'
        '401':
          description: See `modules/auth-SPEC.md`, API-AUTH-592570.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthError'
        '409':
          description: See `modules/auth-SPEC.md`, API-AUTH-592570.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthError'
        '410':
          description: See `modules/auth-SPEC.md`, API-AUTH-592570.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthError'
        '423':
          description: See `modules/auth-SPEC.md`, API-AUTH-592570.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthError'
        '500':
          description: See `modules/auth-SPEC.md`, API-AUTH-592570.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthError'
      x-devagent-source:
        module: auth
        document: modules/auth-SPEC.md
        id: API-AUTH-592570
  /notes:
    post:
      operationId: API-NOTES-232390
      summary: Create a note with a required title and body, optional tags and pinned
        flag. The note gets a unique id, timestamps, pinned=false by default and active
        status (US-002-01, BR-005, BR-018).
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
                  description: Note title; missing or empty returns 400 (AC-002-01-03,
                    BR-005).
                  type: string
                body:
                  description: Note body text (BR-005).
                  type: string
                pinned:
                  description: Pinned flag; defaults to false (US-002-01, BR-018).
                  type: boolean
                tags:
                  description: Initial tag names for the note. Tag rules are undecided
                    (GAP-BE-003).
                  type: array
      responses:
        '201':
          description: Create a note with a required title and body, optional tags
            and pinned flag. The note gets a unique id, timestamps, pinned=false by
            default and active status (US-002-01, BR-005, BR-018).
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
                - status
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    description: Unique note id.
                    type: string
                  title:
                    description: Note title.
                    type: string
                  body:
                    description: Note body.
                    type: string
                  pinned:
                    description: Whether the note is pinned.
                    type: boolean
                  status:
                    description: Active or archived status of the note.
                    type: string
                  tags:
                    description: Tag names on the note, without duplicates.
                    type: array
                  created_at:
                    description: Creation timestamp.
                    type: string
                  updated_at:
                    description: Last update timestamp.
                    type: string
        '400':
          description: See `modules/notes-SPEC.md`, API-NOTES-232390.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-232390
  /notes/search:
    get:
      operationId: API-SEARCH-580505
      summary: Search notes by keyword, by tag, or both combined. Archived notes are
        excluded unless include_archived is set. Results are paginated with pinned
        notes first. Returns 503 when the search index is degraded.
      description: Defined by module `search` in `modules/search-SPEC.md`.
      parameters:
      - name: q
        in: query
        required: false
        description: Keyword to match against notes. Optional when at least one tag
          is given (ASM-BE-004, CON-BE-002). A request with neither q nor tags is
          rejected with 400 (US-003-01).
        schema:
          type: string
      - name: tags
        in: query
        required: false
        description: One or more tags to filter by. When given together with q, both
          filters apply (US-003-02, BR-010). Tag rules are unstated (GAP-BE-003).
        schema:
          type: array
      - name: include_archived
        in: query
        required: false
        description: When true, archived notes are included. Default is false, so
          archived notes are excluded (BR-009, BR-019). A non-boolean value returns
          400.
        schema:
          type: boolean
      - name: page
        in: query
        required: false
        description: 1-based page number for pagination (US-003-05, BR-011). The name
          and base are not stated by an input.
        schema:
          type: integer
      - name: page_size
        in: query
        required: false
        description: Number of results per page, up to 100 (NFR-007). Larger or invalid
          values return 400. The name and default are not stated by an input.
        schema:
          type: integer
      responses:
        '200':
          description: Search notes by keyword, by tag, or both combined. Archived
            notes are excluded unless include_archived is set. Results are paginated
            with pinned notes first. Returns 503 when the search index is degraded.
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
                    description: Matching notes for the requested page. Pinned notes
                      come first, then the remaining notes (BR-019). Each item shows
                      the note id, title, body, pinned flag, status, tags and timestamps.
                    type: array
                  page:
                    description: The page number returned.
                    type: integer
                  page_size:
                    description: The page size applied.
                    type: integer
                  total:
                    description: Total number of notes matching the filters across
                      all pages.
                    type: integer
        '400':
          description: See `modules/search-SPEC.md`, API-SEARCH-580505.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/SearchError'
        '503':
          description: See `modules/search-SPEC.md`, API-SEARCH-580505.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/SearchError'
      x-devagent-source:
        module: search
        document: modules/search-SPEC.md
        id: API-SEARCH-580505
  /notes/{id}:
    get:
      operationId: API-NOTES-045877
      summary: Get a single note by id, including its status, pinned flag and tags
        (US-002-02, BR-006).
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      parameters:
      - name: id
        in: path
        required: true
        description: Note id.
        schema:
          type: string
      responses:
        '200':
          description: Get a single note by id, including its status, pinned flag
            and tags (US-002-02, BR-006).
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
                - status
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    description: Unique note id.
                    type: string
                  title:
                    description: Note title.
                    type: string
                  body:
                    description: Note body.
                    type: string
                  pinned:
                    description: Whether the note is pinned.
                    type: boolean
                  status:
                    description: Active or archived status of the note.
                    type: string
                  tags:
                    description: Tag names on the note.
                    type: array
                  created_at:
                    description: Creation timestamp.
                    type: string
                  updated_at:
                    description: Last update timestamp.
                    type: string
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-045877.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-045877
    patch:
      operationId: API-NOTES-847551
      summary: Edit a note's title, body or pinned flag. Setting pinned pins or unpins
        the note (BR-006, BR-018).
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required: []
              properties:
                title:
                  description: New title; must not be empty if supplied (BR-005).
                  type: string
                body:
                  description: New body text.
                  type: string
                pinned:
                  description: New pinned flag value (BR-018).
                  type: boolean
      parameters:
      - name: id
        in: path
        required: true
        description: Note id.
        schema:
          type: string
      responses:
        '200':
          description: Edit a note's title, body or pinned flag. Setting pinned pins
            or unpins the note (BR-006, BR-018).
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
                - status
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    description: Unique note id.
                    type: string
                  title:
                    description: Note title.
                    type: string
                  body:
                    description: Note body.
                    type: string
                  pinned:
                    description: Whether the note is pinned.
                    type: boolean
                  status:
                    description: Active or archived status.
                    type: string
                  tags:
                    description: Tag names on the note.
                    type: array
                  created_at:
                    description: Creation timestamp.
                    type: string
                  updated_at:
                    description: Last update timestamp.
                    type: string
        '400':
          description: See `modules/notes-SPEC.md`, API-NOTES-847551.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-847551.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-847551
  /notes/{id}/archive:
    post:
      operationId: API-NOTES-810640
      summary: Archive a note by setting its archive flag. The note is never deleted
        (BR-007, US-002-04).
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      parameters:
      - name: id
        in: path
        required: true
        description: Note id.
        schema:
          type: string
      responses:
        '200':
          description: Archive a note by setting its archive flag. The note is never
            deleted (BR-007, US-002-04).
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
                - status
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    description: Unique note id.
                    type: string
                  title:
                    description: Note title.
                    type: string
                  body:
                    description: Note body.
                    type: string
                  pinned:
                    description: Whether the note is pinned.
                    type: boolean
                  status:
                    description: Archived status after the operation.
                    type: string
                  tags:
                    description: Tag names on the note.
                    type: array
                  created_at:
                    description: Creation timestamp.
                    type: string
                  updated_at:
                    description: Last update timestamp.
                    type: string
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-810640.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
        '409':
          description: See `modules/notes-SPEC.md`, API-NOTES-810640.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-810640
  /notes/{id}/tags:
    post:
      operationId: API-NOTES-359413
      summary: Add one or more tags to a note. Repeating an add creates no duplicate
        entry on that note (US-002-03).
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - tags
              properties:
                tags:
                  description: Tag names to add. Tag rules are undecided (GAP-BE-003).
                  type: array
      parameters:
      - name: id
        in: path
        required: true
        description: Note id.
        schema:
          type: string
      responses:
        '200':
          description: Add one or more tags to a note. Repeating an add creates no
            duplicate entry on that note (US-002-03).
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
                - status
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    description: Unique note id.
                    type: string
                  title:
                    description: Note title.
                    type: string
                  body:
                    description: Note body.
                    type: string
                  pinned:
                    description: Whether the note is pinned.
                    type: boolean
                  status:
                    description: Active or archived status.
                    type: string
                  tags:
                    description: Tag names on the note after the add, without duplicates.
                    type: array
                  created_at:
                    description: Creation timestamp.
                    type: string
                  updated_at:
                    description: Last update timestamp.
                    type: string
        '400':
          description: See `modules/notes-SPEC.md`, API-NOTES-359413.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-359413.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-359413
  /notes/{id}/unarchive:
    post:
      operationId: API-NOTES-917869
      summary: Unarchive a note, restoring it to active status without data loss (BR-008,
        US-002-05).
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      parameters:
      - name: id
        in: path
        required: true
        description: Note id.
        schema:
          type: string
      responses:
        '200':
          description: Unarchive a note, restoring it to active status without data
            loss (BR-008, US-002-05).
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
                - status
                - tags
                - created_at
                - updated_at
                properties:
                  id:
                    description: Unique note id.
                    type: string
                  title:
                    description: Note title.
                    type: string
                  body:
                    description: Note body.
                    type: string
                  pinned:
                    description: Whether the note is pinned.
                    type: boolean
                  status:
                    description: Active status after the operation.
                    type: string
                  tags:
                    description: Tag names on the note.
                    type: array
                  created_at:
                    description: Creation timestamp.
                    type: string
                  updated_at:
                    description: Last update timestamp.
                    type: string
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-917869.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-917869
  /openapi.json:
    get:
      operationId: API-CONTRACTDOCS-098363
      summary: Return the published OpenAPI 3.x document covering every implemented
        endpoint, including the data-residency note and Scope section.
      description: Defined by module `contract-docs` in `modules/contract-docs-SPEC.md`.
      responses:
        '200':
          description: Return the published OpenAPI 3.x document covering every implemented
            endpoint, including the data-residency note and Scope section.
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
                    description: OpenAPI version string of the published document
                      (3.x).
                    type: string
                  info:
                    description: API metadata (title, version) for the published document.
                    type: object
                  paths:
                    description: Every implemented endpoint with its schemas, 4xx/5xx
                      errors and examples (BR-012, NFR-008, NFR-009).
                    type: object
                  components:
                    description: Shared schemas, including the error body schema.
                    type: object
        '406':
          description: See `modules/contract-docs-SPEC.md`, API-CONTRACTDOCS-098363.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ContractDocsError'
        '500':
          description: See `modules/contract-docs-SPEC.md`, API-CONTRACTDOCS-098363.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ContractDocsError'
      x-devagent-source:
        module: contract-docs
        document: modules/contract-docs-SPEC.md
        id: API-CONTRACTDOCS-098363
  /openapi/coverage:
    get:
      operationId: API-CONTRACTDOCS-827943
      summary: Report the spec-versus-route coverage check result.
      description: Defined by module `contract-docs` in `modules/contract-docs-SPEC.md`.
      responses:
        '200':
          description: Report the spec-versus-route coverage check result.
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - implemented_count
                - documented_count
                - coverage_percent
                - missing_endpoints
                properties:
                  implemented_count:
                    description: Number of implemented routes found in the route registry.
                    type: integer
                  documented_count:
                    description: Number of those routes present in the published spec.
                    type: integer
                  coverage_percent:
                    description: Share of implemented endpoints present in the published
                      spec (target 100%, NFR-008).
                    type: number
                  missing_endpoints:
                    description: Implemented routes absent from the spec; the build
                      fails when non-empty (US-004-04).
                    type: array
        '500':
          description: See `modules/contract-docs-SPEC.md`, API-CONTRACTDOCS-827943.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ContractDocsError'
      x-devagent-source:
        module: contract-docs
        document: modules/contract-docs-SPEC.md
        id: API-CONTRACTDOCS-827943
  /openapi/data-residency:
    get:
      operationId: API-CONTRACTDOCS-446650
      summary: Return the data-residency note published with the API documentation.
      description: Defined by module `contract-docs` in `modules/contract-docs-SPEC.md`.
      responses:
        '200':
          description: Return the data-residency note published with the API documentation.
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - note
                properties:
                  note:
                    description: Text of the data-residency note published with the
                      API documentation (BR-013).
                    type: string
        '500':
          description: See `modules/contract-docs-SPEC.md`, API-CONTRACTDOCS-446650.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ContractDocsError'
      x-devagent-source:
        module: contract-docs
        document: modules/contract-docs-SPEC.md
        id: API-CONTRACTDOCS-446650
  /openapi/scope:
    get:
      operationId: API-CONTRACTDOCS-206969
      summary: Return the Scope section listing what the API does and explicitly does
        not cover.
      description: Defined by module `contract-docs` in `modules/contract-docs-SPEC.md`.
      responses:
        '200':
          description: Return the Scope section listing what the API does and explicitly
            does not cover.
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - scope
                - excluded
                properties:
                  scope:
                    description: Text of the Scope section stating what the API covers.
                    type: string
                  excluded:
                    description: 'Excluded capabilities: attachments, real-time channels,
                      mobile-specific endpoints (BR-014 to BR-016).'
                    type: array
        '500':
          description: See `modules/contract-docs-SPEC.md`, API-CONTRACTDOCS-206969.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ContractDocsError'
      x-devagent-source:
        module: contract-docs
        document: modules/contract-docs-SPEC.md
        id: API-CONTRACTDOCS-206969
  /residency/backup-validations:
    post:
      operationId: API-RESIDENCY-174124
      summary: Validate that a backup destination is EU-only; reject non-EU destinations
        without writing data (US-001-02).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - destination_region
              properties:
                destination_region:
                  description: Destination region or location of the backup. A non-EU
                    destination fails validation within 30 seconds and writes no data
                    (AC-001-02-01, AC-001-02-02).
                  type: string
                backup_id:
                  description: Identifier of the backup configuration or job being
                    validated.
                  type: string
      responses:
        '200':
          description: Validate that a backup destination is EU-only; reject non-EU
            destinations without writing data (US-001-02).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - valid
                - destination_region
                properties:
                  valid:
                    description: True when the backup destination is EU-only.
                    type: boolean
                  destination_region:
                    description: The destination region that was evaluated.
                    type: string
        '400':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-174124.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-174124.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-174124
  /residency/ci-gate-evaluations:
    post:
      operationId: API-RESIDENCY-685134
      summary: Evaluate the CI residency gate for a build, failing it when any resource
        is non-EU (US-001-03).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - resources
              properties:
                resources:
                  description: Deployment targets (resource identifiers with regions)
                    in the build under evaluation, so non-EU deploys can be blocked
                    from production (BR-002, NFR-002).
                  type: array
                build_id:
                  description: Identifier of the CI build being gated.
                  type: string
      responses:
        '200':
          description: Evaluate the CI residency gate for a build, failing it when
            any resource is non-EU (US-001-03).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - result
                - violations
                properties:
                  result:
                    description: 'Gate outcome: pass or fail.'
                    type: string
                  violations:
                    description: Resources found outside approved EU regions; empty
                      on pass.
                    type: array
        '400':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-685134.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-685134.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-685134
  /residency/compliance-report:
    get:
      operationId: API-RESIDENCY-913995
      summary: Return the current EU data-residency compliance report (US-001-04).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      responses:
        '200':
          description: Return the current EU data-residency compliance report (US-001-04).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - components
                - eu_verified_percent
                - generated_at
                properties:
                  components:
                    description: Per-component residency status of the infrastructure
                      inventory (NFR-004).
                    type: array
                  eu_verified_percent:
                    description: Share of infrastructure components EU-verified (NFR-001).
                    type: number
                  generated_at:
                    description: Timestamp at which the report was last refreshed.
                    type: string
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-913995.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-913995
  /residency/drift-scans:
    post:
      operationId: API-RESIDENCY-922826
      summary: Run a drift scan for non-EU resources and alert the DevOps team on
        detection (US-001-06).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      responses:
        '200':
          description: Run a drift scan for non-EU resources and alert the DevOps
            team on detection (US-001-06).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - result
                - drifted_resources
                - alert_sent
                - scanned_at
                properties:
                  result:
                    description: 'Scan outcome: clean or drift-detected.'
                    type: string
                  drifted_resources:
                    description: Resources found in a non-EU region, with resource
                      details; empty when clean (BR-004, AC-001-06-02).
                    type: array
                  alert_sent:
                    description: True when an alert was sent to the DevOps team (NFR-003).
                    type: boolean
                  scanned_at:
                    description: Timestamp of the scan.
                    type: string
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-922826.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-922826
  /residency/launch-gate-checks:
    post:
      operationId: API-RESIDENCY-648672
      summary: Run the launch gate check, refreshing the compliance report and blocking
        launch on non-EU resources (US-001-05).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required: []
              properties:
                release_id:
                  description: Identifier of the release or launch being checked.
                  type: string
      responses:
        '200':
          description: Run the launch gate check, refreshing the compliance report
            and blocking launch on non-EU resources (US-001-05).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - result
                - violations
                - report_generated_at
                properties:
                  result:
                    description: 'Launch gate outcome: pass or fail.'
                    type: string
                  violations:
                    description: Non-compliant resources blocking launch; empty on
                      pass.
                    type: array
                  report_generated_at:
                    description: Timestamp of the compliance report refreshed by this
                      check (NFR-004).
                    type: string
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-648672.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-648672
  /residency/provisioning-checks:
    post:
      operationId: API-RESIDENCY-892950
      summary: Validate that a resource provisioning request targets an approved EU
        region (US-001-01).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - region
              - resource_type
              properties:
                region:
                  description: Target region for the resource being provisioned. Must
                    be an approved EU region (BR-001, US-001-01). The approved region
                    list is open (GAP-BE-011).
                  type: string
                resource_type:
                  description: Kind of infrastructure resource being provisioned (database,
                    compute, backup storage).
                  type: string
      responses:
        '200':
          description: Validate that a resource provisioning request targets an approved
            EU region (US-001-01).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - allowed
                - region
                - resource_type
                properties:
                  allowed:
                    description: True when the region is an approved EU region and
                      provisioning may proceed.
                    type: boolean
                  region:
                    description: The region that was evaluated.
                    type: string
                  resource_type:
                    description: The resource type that was evaluated.
                    type: string
        '400':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-892950.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-892950.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-892950
components:
  schemas:
    AuthError:
      type: object
      additionalProperties: false
      required:
      - code
      - message
      properties:
        code:
          description: Machine-readable error code, e.g. CODE_WRONG, CODE_EXPIRED,
            CODE_ALREADY_USED, ACCOUNT_LOCKED, RATE_LIMITED, VALIDATION_ERROR (proposed
            names, ASM-BE-006).
          type: string
        message:
          description: Human-readable explanation of the error.
          type: string
        details:
          description: Optional structured detail about the error (field-level detail
            shape is still open, Q-BE-004).
          type: object
    NotesError:
      type: object
      additionalProperties: false
      required:
      - code
      - message
      properties:
        code:
          description: Machine-readable error code, for example VALIDATION_ERROR,
            NOT_FOUND. Envelope and names are proposed, not confirmed (ASM-BE-006).
          type: string
        message:
          description: Human-readable explanation of the error.
          type: string
        details:
          description: Optional field-level detail for validation failures. Its shape
            is undecided (Q-BE-004).
          type: array
    SearchError:
      type: object
      additionalProperties: false
      required:
      - code
      - message
      properties:
        code:
          description: Machine-readable error code, such as VALIDATION_ERROR for 400
            and SEARCH_UNAVAILABLE for 503 (proposed, ASM-BE-006).
          type: string
        message:
          description: Human-readable explanation of the error (US-005-04, ASM-BE-006).
          type: string
        details:
          description: Optional field-level detail for 400 validation errors. The
            shape is open (Q-BE-004, GAP-BE-004).
          type: array
    ContractDocsError:
      type: object
      additionalProperties: false
      required:
      - code
      - message
      properties:
        code:
          description: Machine-readable error code, e.g. OUT_OF_SCOPE (cited in US-005-04);
            other code names are proposed (ASM-BE-006).
          type: string
        message:
          description: Human-readable explanation of the error.
          type: string
        details:
          description: Optional structured detail about the error; shape is pending
            Q-BE-004.
          type: object
    ResidencyError:
      type: object
      additionalProperties: false
      required:
      - code
      - message
      properties:
        code:
          description: Machine-readable error code, e.g. RESIDENCY_VIOLATION or VALIDATION_ERROR
            (proposed envelope, ASM-BE-006).
          type: string
        message:
          description: Human-readable explanation of the failure, including the offending
            region or destination where relevant.
          type: string
        details:
          description: Optional structured detail, e.g. the offending resource and
            its region. Shape is open (Q-BE-004).
          type: object
