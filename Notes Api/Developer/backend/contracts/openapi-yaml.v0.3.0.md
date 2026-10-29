---
id: agent-developer/notes_api/backend/contracts/openapi.yaml
type: openapi
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/contracts/openapi.yaml
depends_on: []
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
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
      summary: Request a 6-digit one-time login code to be emailed to the given address.
        Limited to 3 requests within 10 minutes; allowed while verification is locked
        (BR-032).
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
                  description: Email address to send the one-time login code to (BR-020,
                    ASM-BE-001; trimmed and compared case-insensitively).
                  type: string
      responses:
        '202':
          description: Request a 6-digit one-time login code to be emailed to the
            given address. Limited to 3 requests within 10 minutes; allowed while
            verification is locked (BR-032).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - message
                properties:
                  message:
                    description: Confirmation that a code has been sent, without revealing
                      the code or an unmasked email address (NFR-018).
                    type: string
                  expires_in_seconds:
                    description: Seconds until the issued code expires; 10 minutes
                      per BR-020.
                    type: integer
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
      summary: 'Verify a one-time login code. Codes are single use, expire after 10
        minutes, and 5 wrong codes lock verification for 15 minutes (BR-033). Refusal
        precedence: lock, used, expired, wrong (ASM-BE-003).'
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
                  description: Email address the code was issued to (ASM-BE-001).
                  type: string
                code:
                  description: The 6-digit numeric one-time code (BR-029).
                  type: string
      responses:
        '200':
          description: 'Verify a one-time login code. Codes are single use, expire
            after 10 minutes, and 5 wrong codes lock verification for 15 minutes (BR-033).
            Refusal precedence: lock, used, expired, wrong (ASM-BE-003).'
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - verified
                properties:
                  verified:
                    description: True when the code was accepted. The credential issued
                      on success is undecided (Q-BE-003), so no session or token field
                      is defined.
                    type: boolean
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
      summary: Create a note with a title and body; the service assigns a unique ID,
        timestamps and pinned=false by default (US-002-01, US-006-02; BR-005, BR-018).
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
                  description: Note title; required, missing or empty returns 400
                    (BR-005).
                  type: string
                body:
                  description: Note body; required, missing or empty returns 400 (BR-005).
                  type: string
                pinned:
                  description: Initial pinned flag; defaults to false when omitted
                    (US-002-01, BR-018).
                  type: boolean
      responses:
        '201':
          description: Create a note with a title and body; the service assigns a
            unique ID, timestamps and pinned=false by default (US-002-01, US-006-02;
            BR-005, BR-018).
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
                    description: Unique note ID assigned by the service (US-002-01).
                    type: string
                  title:
                    description: Note title.
                    type: string
                  body:
                    description: Note body.
                    type: string
                  pinned:
                    description: Pinned flag; false by default.
                    type: boolean
                  status:
                    description: Active or archived status of the note (US-002-05,
                      US-002-06); a new note is active.
                    type: string
                  tags:
                    description: Tags carried by the note; empty on creation.
                    type: array
                  created_at:
                    description: Creation timestamp (US-002-01).
                    type: string
                  updated_at:
                    description: Last-update timestamp (US-002-01).
                    type: string
        '400':
          description: See `modules/notes-SPEC.md`, API-NOTES-232390.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
        '415':
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
      summary: Search notes by keyword, by tag, or by both combined. Results are paginated,
        exclude archived notes unless include_archived is set, and list pinned notes
        first. Returns 503 when the search index is degraded. At least one of q or
        tags is required (ASM-BE-009, CON-BE-003).
      description: Defined by module `search` in `modules/search-SPEC.md`.
      parameters:
      - name: q
        in: query
        required: false
        description: Keyword matched against note title and body. At least one of
          q or tags must be supplied. A present but empty q returns 400 (US-003-01).
        schema:
          type: string
      - name: tags
        in: query
        required: false
        description: One or more tag names to filter by. When combined with q, both
          filters apply (US-003-02, US-003-04). Tag rules are open (GAP-BE-010).
        schema:
          type: array
      - name: include_archived
        in: query
        required: false
        description: When true, archived notes are included. The default is false,
          so archived notes are excluded (BR-009). A non-boolean value returns 400.
        schema:
          type: boolean
      - name: page
        in: query
        required: false
        description: 1-based page number. An invalid or non-numeric value returns
          400 (US-003-05).
        schema:
          type: integer
      - name: page_size
        in: query
        required: false
        description: Results per page, maximum 100 (NFR-007). The default of 20 is
          an assumption (ASM-BE-009). A value over the maximum or a non-numeric value
          returns 400.
        schema:
          type: integer
      responses:
        '200':
          description: Search notes by keyword, by tag, or by both combined. Results
            are paginated, exclude archived notes unless include_archived is set,
            and list pinned notes first. Returns 503 when the search index is degraded.
            At least one of q or tags is required (ASM-BE-009, CON-BE-003).
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
                    description: Matching notes for the requested page, with pinned
                      notes before unpinned ones (BR-010). Each item shows the note's
                      id, title, body, pinned flag, status (active or archived), tags
                      and timestamps.
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
        '500':
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
      summary: Get a single note by ID, including its pinned flag, status and tags
        (US-002-06, US-006-02, US-006-05).
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      parameters:
      - name: id
        in: path
        required: true
        description: Note ID (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Get a single note by ID, including its pinned flag, status
            and tags (US-002-06, US-006-02, US-006-05).
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
                    description: Note ID.
                    type: string
                  title:
                    description: Note title.
                    type: string
                  body:
                    description: Note body.
                    type: string
                  pinned:
                    description: Pinned flag.
                    type: boolean
                  status:
                    description: Active or archived status.
                    type: string
                  tags:
                    description: Tags carried by the note.
                    type: array
                  created_at:
                    description: Creation timestamp.
                    type: string
                  updated_at:
                    description: Last-update timestamp.
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
      summary: Edit a note's title, body and/or pinned flag; updates the updated_at
        timestamp (US-002-02, US-006-02; BR-006, BR-018). Concurrent PATCH gets last-write
        or conflict (US-005-03).
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
                  description: New title; if supplied must not be empty.
                  type: string
                body:
                  description: New body; if supplied must not be empty.
                  type: string
                pinned:
                  description: New pinned flag value (BR-018).
                  type: boolean
      parameters:
      - name: id
        in: path
        required: true
        description: Note ID (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Edit a note's title, body and/or pinned flag; updates the updated_at
            timestamp (US-002-02, US-006-02; BR-006, BR-018). Concurrent PATCH gets
            last-write or conflict (US-005-03).
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
                    description: Note ID.
                    type: string
                  title:
                    description: Note title after the edit.
                    type: string
                  body:
                    description: Note body after the edit.
                    type: string
                  pinned:
                    description: Pinned flag after the edit.
                    type: boolean
                  status:
                    description: Active or archived status.
                    type: string
                  tags:
                    description: Tags carried by the note.
                    type: array
                  created_at:
                    description: Creation timestamp.
                    type: string
                  updated_at:
                    description: Updated last-update timestamp.
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
        '409':
          description: See `modules/notes-SPEC.md`, API-NOTES-847551.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
        '415':
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
      summary: Archive a note by setting the archive flag; the note is kept, never
        deleted (US-002-04, US-006-05; BR-007).
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      parameters:
      - name: id
        in: path
        required: true
        description: Note ID (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Archive a note by setting the archive flag; the note is kept,
            never deleted (US-002-04, US-006-05; BR-007).
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
                    description: Note ID.
                    type: string
                  status:
                    description: Archived status after the operation.
                    type: string
                  updated_at:
                    description: Last-update timestamp.
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
      summary: Add one or more tags to a note; a repeated add creates no duplicate
        entry on that note (US-002-03, US-006-04). Tag rules are open (GAP-BE-010).
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
                  description: Tag names to add to the note; names already on the
                    note are not duplicated.
                  type: array
      parameters:
      - name: id
        in: path
        required: true
        description: Note ID (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Add one or more tags to a note; a repeated add creates no duplicate
            entry on that note (US-002-03, US-006-04). Tag rules are open (GAP-BE-010).
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
                    description: Note ID.
                    type: string
                  tags:
                    description: The note's full tag set after the add, without duplicates.
                    type: array
                  updated_at:
                    description: Last-update timestamp.
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
      summary: Unarchive a note, returning it to active status (US-002-05, US-006-05;
        BR-008).
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      parameters:
      - name: id
        in: path
        required: true
        description: Note ID (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Unarchive a note, returning it to active status (US-002-05,
            US-006-05; BR-008).
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
                    description: Note ID.
                    type: string
                  status:
                    description: Active status after the operation.
                    type: string
                  updated_at:
                    description: Last-update timestamp.
                    type: string
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-917869.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
        '409':
          description: See `modules/notes-SPEC.md`, API-NOTES-917869.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-917869
  /openapi:
    get:
      operationId: API-CONTRACTDOCS-304307
      summary: Return the published OpenAPI 3.x document covering every implemented
        endpoint, with schemas, 4xx/5xx error responses, example payloads, the data-residency
        note and the Scope section. The format is negotiated through the Accept header
        (US-004-01 to US-004-06, US-005-01). No input names the path; it is an assumption
        to be confirmed.
      description: Defined by module `contract-docs` in `modules/contract-docs-SPEC.md`.
      parameters:
      - name: Accept
        in: query
        required: false
        description: Header, not a body field. Requested media type of the document.
          If it is unsupported, the service either returns 406 or falls back to the
          default format; this choice is open (US-004-01).
        schema:
          type: string
      responses:
        '200':
          description: Return the published OpenAPI 3.x document covering every implemented
            endpoint, with schemas, 4xx/5xx error responses, example payloads, the
            data-residency note and the Scope section. The format is negotiated through
            the Accept header (US-004-01 to US-004-06, US-005-01). No input names
            the path; it is an assumption to be confirmed.
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
                    description: OpenAPI 3.x version string of the published document.
                    type: string
                  info:
                    description: Document metadata. It carries the data-residency
                      note (US-004-06, BR-013) and the Scope section stating that
                      attachments, real-time channels and mobile-specific endpoints
                      are out of scope (US-005-01, BR-014 to BR-016).
                    type: object
                  paths:
                    description: Every implemented endpoint with its parameters, request
                      bodies, success responses and documented 4xx/5xx error responses.
                      Covers 100% of implemented routes (NFR-008).
                    type: object
                  components:
                    description: Reusable schemas, including the error body, plus
                      example payloads. Search and tagging endpoints have examples
                      (NFR-009).
                    type: object
        '406':
          description: See `modules/contract-docs-SPEC.md`, API-CONTRACTDOCS-304307.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ContractDocsError'
        '500':
          description: See `modules/contract-docs-SPEC.md`, API-CONTRACTDOCS-304307.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ContractDocsError'
      x-devagent-source:
        module: contract-docs
        document: modules/contract-docs-SPEC.md
        id: API-CONTRACTDOCS-304307
  /residency/backup-validations:
    post:
      operationId: API-RESIDENCY-174124
      summary: Validate a backup destination; a non-EU destination fails the job within
        30 seconds and writes no data (US-001-02, AC-001-02-02).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - backup_job_id
              - destination_region
              properties:
                backup_job_id:
                  description: Identifier of the backup job being validated.
                  type: string
                destination_region:
                  description: Region of the backup destination.
                  type: string
      responses:
        '200':
          description: Validate a backup destination; a non-EU destination fails the
            job within 30 seconds and writes no data (US-001-02, AC-001-02-02).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - backup_job_id
                - destination_region
                - valid
                properties:
                  backup_job_id:
                    description: Identifier of the validated backup job.
                    type: string
                  destination_region:
                    description: Validated destination region.
                    type: string
                  valid:
                    description: True when the destination is in an EU region.
                    type: boolean
        '400':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-174124.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
        '422':
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
      summary: Evaluate the CI residency gate for a build; fails when any resource
        is outside the EU (US-001-03, BR-002).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - build_id
              - resources
              properties:
                build_id:
                  description: CI build identifier.
                  type: string
                resources:
                  description: Resources in the deployment, each with an identifier
                    and region.
                  type: array
      responses:
        '200':
          description: Evaluate the CI residency gate for a build; fails when any
            resource is outside the EU (US-001-03, BR-002).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - build_id
                - passed
                - violations
                properties:
                  build_id:
                    description: CI build identifier evaluated.
                    type: string
                  passed:
                    description: True when every resource is in an EU region.
                    type: boolean
                  violations:
                    description: Resources found outside the EU; empty when passed.
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
      summary: Get the current compliance report of EU residency per component (US-001-04,
        NFR-004).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      responses:
        '200':
          description: Get the current compliance report of EU residency per component
            (US-001-04, NFR-004).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - generated_at
                - eu_verified_percent
                - components
                properties:
                  generated_at:
                    description: Timestamp of the last refresh.
                    type: string
                  eu_verified_percent:
                    description: Share of components verified as EU-resident.
                    type: number
                  components:
                    description: Each component with its region and verification status.
                    type: array
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
      summary: Run a drift scan of all resources; non-EU resources trigger an alert
        to DevOps within 15 minutes (US-001-06, BR-004, NFR-003).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      responses:
        '201':
          description: Run a drift scan of all resources; non-EU resources trigger
            an alert to DevOps within 15 minutes (US-001-06, BR-004, NFR-003).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - scan_id
                - scanned_at
                - all_eu
                - non_eu_resources
                - alert_sent
                properties:
                  scan_id:
                    description: Identifier of the scan.
                    type: string
                  scanned_at:
                    description: Timestamp of the scan.
                    type: string
                  all_eu:
                    description: True when every resource is in an EU region.
                    type: boolean
                  non_eu_resources:
                    description: Resources found outside the EU, with details.
                    type: array
                  alert_sent:
                    description: True when an alert was sent to the DevOps team.
                    type: boolean
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
  /residency/drift-scans/{id}:
    get:
      operationId: API-RESIDENCY-202775
      summary: Get the result of a previous drift scan (US-001-06).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      parameters:
      - name: id
        in: path
        required: true
        description: Drift scan identifier (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Get the result of a previous drift scan (US-001-06).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - scan_id
                - scanned_at
                - all_eu
                - non_eu_resources
                - alert_sent
                properties:
                  scan_id:
                    description: Identifier of the scan.
                    type: string
                  scanned_at:
                    description: Timestamp of the scan.
                    type: string
                  all_eu:
                    description: True when every resource was in an EU region.
                    type: boolean
                  non_eu_resources:
                    description: Resources found outside the EU.
                    type: array
                  alert_sent:
                    description: True when an alert was sent.
                    type: boolean
        '404':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-202775.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-202775.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-202775
  /residency/launch-gate-checks:
    post:
      operationId: API-RESIDENCY-648672
      summary: Run the launch gate check; refreshes the compliance report and blocks
        release on failure (US-001-05, BR-003, AC-001-05-02).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - release_id
              properties:
                release_id:
                  description: Identifier of the release being gated.
                  type: string
      responses:
        '200':
          description: Run the launch gate check; refreshes the compliance report
            and blocks release on failure (US-001-05, BR-003, AC-001-05-02).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - release_id
                - passed
                - report_generated_at
                - blocking_resources
                properties:
                  release_id:
                    description: Release identifier checked.
                    type: string
                  passed:
                    description: True when the release may proceed.
                    type: boolean
                  report_generated_at:
                    description: Timestamp of the compliance report used for the check.
                    type: string
                  blocking_resources:
                    description: Non-EU resources blocking release; empty when passed.
                    type: array
        '400':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-648672.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
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
  /residency/provisioning-requests:
    post:
      operationId: API-RESIDENCY-026899
      summary: Request provisioning of a resource; refused unless the target region
        is an EU region (US-001-01, BR-001).
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
              - region
              properties:
                resource_type:
                  description: Kind of resource to provision (database, compute, backup
                    storage).
                  type: string
                region:
                  description: Target cloud region for the resource.
                  type: string
      responses:
        '201':
          description: Request provisioning of a resource; refused unless the target
            region is an EU region (US-001-01, BR-001).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - resource_id
                - resource_type
                - region
                - status
                properties:
                  resource_id:
                    description: Identifier of the provisioned resource.
                    type: string
                  resource_type:
                    description: Kind of resource provisioned.
                    type: string
                  region:
                    description: EU region the resource was provisioned in.
                    type: string
                  status:
                    description: Provisioning status.
                    type: string
        '400':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-026899.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
        '422':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-026899.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-026899.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-026899
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
          description: Machine-readable error code, e.g. INVALID_CODE, CODE_ALREADY_USED,
            CODE_EXPIRED, ACCOUNT_LOCKED, RESEND_LIMIT_EXCEEDED, VALIDATION_ERROR,
            INTERNAL_ERROR (proposed names, ASM-BE-007).
          type: string
        message:
          description: Human-readable error message. Never contains a login code or
            an unmasked email address (NFR-018).
          type: string
        details:
          description: Optional per-field validation details for 400 VALIDATION_ERROR
            responses (ASM-BE-007).
          type: array
    NotesError:
      type: object
      additionalProperties: false
      required:
      - code
      - message
      properties:
        code:
          description: Machine-readable error code from the proposed registry in backend.error-model
            (ASM-BE-007), for example VALIDATION_ERROR, NOTE_NOT_FOUND, ALREADY_ARCHIVED.
          type: string
        message:
          description: Human-readable explanation of the error. Never contains a login
            code or an unmasked email address (NFR-018).
          type: string
        details:
          description: Optional per-field validation details, present on validation
            errors (backend.error-model).
          type: array
    SearchError:
      type: object
      additionalProperties: false
      required:
      - code
      - message
      properties:
        code:
          description: Machine-readable error code, for example VALIDATION_ERROR or
            SEARCH_UNAVAILABLE. Code names are proposals (ASM-BE-007).
          type: string
        message:
          description: Human-readable error message. It never contains a login code
            or an unmasked email address (NFR-018).
          type: string
        details:
          description: Optional per-field validation details, present on 400 validation
            errors.
          type: array
    ContractDocsError:
      type: object
      additionalProperties: false
      required:
      - code
      - message
      properties:
        code:
          description: Machine-readable error code from the error-code registry, for
            example NOT_ACCEPTABLE or INTERNAL_ERROR.
          type: string
        message:
          description: Human-readable explanation of the error. Never contains a login
            code or an unmasked email address.
          type: string
        details:
          description: Optional field-level details, used mainly for validation errors.
          type: array
    ResidencyError:
      type: object
      additionalProperties: false
      required:
      - code
      - message
      properties:
        code:
          description: Machine-readable error code (proposed names, ASM-BE-007).
          type: string
        message:
          description: Human-readable error message. Never contains credentials or
            unmasked email addresses.
          type: string
        details:
          description: Optional field-level validation details.
          type: array
