---
id: agent-developer/notes_api/backend/contracts/openapi.yaml
type: openapi
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/contracts/openapi.yaml
depends_on: []
commit_kind: fork
confidence: agent
run_id: e1e628bb-b234-5a13-b948-b4689dcc8eab
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
      summary: Request a 6-digit one-time login code, emailed to the supplied address.
        Limited to 3 requests per rolling 10 minutes per email; not refused during
        an account lock (BR-032).
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
                  description: Email address to which the one-time login code is sent.
                    PII; always masked in examples and logs.
      responses:
        '202':
          description: Request a 6-digit one-time login code, emailed to the supplied
            address. Limited to 3 requests per rolling 10 minutes per email; not refused
            during an account lock (BR-032).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required: []
                properties:
                  message:
                    type: string
                    description: Human-readable confirmation that a code was issued
                      and emailed. Never contains the code or an unmasked email address.
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
      summary: 'Verify a one-time login code. Codes expire 10 minutes after issue
        and are single use. Five wrong codes lock verification for 15 minutes. Precedence:
        lock, then used, then expired, then invalid.'
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
                  description: Email address the code was issued to. PII; always masked
                    in examples and logs.
                code:
                  type: string
                  description: The 6-digit numeric one-time code received by email.
      responses:
        '200':
          description: 'Verify a one-time login code. Codes expire 10 minutes after
            issue and are single use. Five wrong codes lock verification for 15 minutes.
            Precedence: lock, then used, then expired, then invalid.'
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required: []
                properties:
                  verified:
                    type: boolean
                    description: True when the code was accepted and consumed. What
                      credential a successful verify issues (type, lifetime, transport)
                      is undecided and blocked on Q-BE-003, so no credential field
                      is defined.
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
      summary: Create a note with title, body, optional pinned flag and optional tags
        (US-002-01, US-006-02).
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
                  description: Note title; required (BR-005).
                body:
                  type: string
                  description: Note body; required (BR-005).
                pinned:
                  type: boolean
                  description: Pinned flag; defaults to false when omitted (US-002-01,
                    BR-018).
                tags:
                  type: array
                  description: Initial tag names to attach to the note; duplicates
                    create no duplicate entry (US-002-03). Tag rules open in GAP-BE-010.
      responses:
        '201':
          description: Create a note with title, body, optional pinned flag and optional
            tags (US-002-01, US-006-02).
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
                    type: string
                    description: Unique note ID assigned on creation.
                  title:
                    type: string
                    description: Note title.
                  body:
                    type: string
                    description: Note body.
                  pinned:
                    type: boolean
                    description: Pinned flag.
                  status:
                    type: string
                    description: Archive status of the note; active on creation.
                  tags:
                    type: array
                    description: Tag names on the note.
                  created_at:
                    type: string
                    description: Creation timestamp.
                  updated_at:
                    type: string
                    description: Last update timestamp.
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
        '500':
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
      summary: Search active notes by keyword, tag or both, with pagination. Archived
        notes are excluded and pinned notes are ordered first. Returns 503 with Retry-After
        when the search index is degraded. Path is a proposal; no input names it.
      description: Defined by module `search` in `modules/search-SPEC.md`.
      parameters:
      - name: q
        in: query
        required: false
        description: Keyword matched against note title and body. At least one of
          q or tags is required (ASM-BE-015); a missing or empty q with no tags is
          a 400.
        schema:
          type: string
      - name: tags
        in: query
        required: false
        description: Tag names to filter by, combinable with q (combined filter).
          Sent as a repeated or comma-separated query parameter.
        schema:
          type: array
      - name: page
        in: query
        required: false
        description: 1-based page number. Invalid or out-of-range values return 400.
        schema:
          type: integer
      - name: page_size
        in: query
        required: false
        description: Results per page; default 20, maximum 100 (ASM-BE-009). Values
          over the maximum or below 1 return 400.
        schema:
          type: integer
      responses:
        '200':
          description: Search active notes by keyword, tag or both, with pagination.
            Archived notes are excluded and pinned notes are ordered first. Returns
            503 with Retry-After when the search index is degraded. Path is a proposal;
            no input names it.
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
                    description: Matching non-archived notes for the requested page,
                      pinned notes first. Each item has id, title, body, pinned, tags,
                      status, created_at, updated_at.
                  page:
                    type: integer
                    description: Current page number.
                  page_size:
                    type: integer
                    description: Page size applied to this response.
                  total:
                    type: integer
                    description: Total number of matching non-archived notes across
                      all pages.
        '400':
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
      summary: Get a single note, including its archive status and tags (US-002-06).
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
          description: Get a single note, including its archive status and tags (US-002-06).
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
                    type: string
                    description: Note ID.
                  title:
                    type: string
                    description: Note title.
                  body:
                    type: string
                    description: Note body.
                  pinned:
                    type: boolean
                    description: Pinned flag.
                  status:
                    type: string
                    description: Archive status of the note.
                  tags:
                    type: array
                    description: Tag names on the note.
                  created_at:
                    type: string
                    description: Creation timestamp.
                  updated_at:
                    type: string
                    description: Last update timestamp.
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-045877.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
        '500':
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
      summary: Edit a note's title, body and pinned flag (US-002-02, US-006-02).
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
                  type: string
                  description: New title; if supplied it must be valid (BR-005).
                body:
                  type: string
                  description: New body; if supplied it must be valid (BR-005).
                pinned:
                  type: boolean
                  description: New pinned flag value (BR-018).
      parameters:
      - name: id
        in: path
        required: true
        description: Note ID (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Edit a note's title, body and pinned flag (US-002-02, US-006-02).
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
                    type: string
                    description: Note ID.
                  title:
                    type: string
                    description: Note title after the edit.
                  body:
                    type: string
                    description: Note body after the edit.
                  pinned:
                    type: boolean
                    description: Pinned flag after the edit.
                  status:
                    type: string
                    description: Archive status of the note.
                  tags:
                    type: array
                    description: Tag names on the note.
                  created_at:
                    type: string
                    description: Creation timestamp.
                  updated_at:
                    type: string
                    description: Updated timestamp, refreshed by the edit.
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
        '415':
          description: See `modules/notes-SPEC.md`, API-NOTES-847551.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
        '500':
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
      summary: Archive a note by setting its archive flag; no data is deleted (US-002-04,
        US-006-05).
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
          description: Archive a note by setting its archive flag; no data is deleted
            (US-002-04, US-006-05).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - status
                - pinned
                - updated_at
                properties:
                  id:
                    type: string
                    description: Note ID.
                  status:
                    type: string
                    description: Archive status, now archived.
                  pinned:
                    type: boolean
                    description: Pinned flag, unchanged.
                  updated_at:
                    type: string
                    description: Last update timestamp.
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
        '500':
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
      summary: Add a tag to a note; a repeated add creates no duplicate (US-002-03,
        US-006-04).
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - tag
              properties:
                tag:
                  type: string
                  description: Tag name to add. Tag rules open in GAP-BE-010.
      parameters:
      - name: id
        in: path
        required: true
        description: Note ID (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Add a tag to a note; a repeated add creates no duplicate (US-002-03,
            US-006-04).
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
                    description: Note ID.
                  tags:
                    type: array
                    description: Tag names on the note after the add, without duplicates.
                  updated_at:
                    type: string
                    description: Last update timestamp.
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
        '415':
          description: See `modules/notes-SPEC.md`, API-NOTES-359413.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
        '500':
          description: See `modules/notes-SPEC.md`, API-NOTES-359413.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-359413
  /notes/{id}/tags/{tag}:
    delete:
      operationId: API-NOTES-150096
      summary: Remove a tag from a note (US-006-04).
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      parameters:
      - name: id
        in: path
        required: true
        description: Note ID (path parameter).
        schema:
          type: string
      - name: tag
        in: path
        required: true
        description: Tag name to remove (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Remove a tag from a note (US-006-04).
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
                    description: Note ID.
                  tags:
                    type: array
                    description: Tag names on the note after the removal.
                  updated_at:
                    type: string
                    description: Last update timestamp.
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-150096.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
        '500':
          description: See `modules/notes-SPEC.md`, API-NOTES-150096.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
      x-devagent-source:
        module: notes
        document: modules/notes-SPEC.md
        id: API-NOTES-150096
  /notes/{id}/unarchive:
    post:
      operationId: API-NOTES-917869
      summary: Unarchive a note, restoring it to active with all data intact (US-002-05,
        US-006-05).
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
          description: Unarchive a note, restoring it to active with all data intact
            (US-002-05, US-006-05).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - id
                - status
                - pinned
                - updated_at
                properties:
                  id:
                    type: string
                    description: Note ID.
                  status:
                    type: string
                    description: Archive status, now active.
                  pinned:
                    type: boolean
                    description: Pinned flag, unchanged.
                  updated_at:
                    type: string
                    description: Last update timestamp.
        '404':
          description: See `modules/notes-SPEC.md`, API-NOTES-917869.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotesError'
        '500':
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
        endpoint, with schemas, 4xx/5xx errors, examples, the data-residency note
        and the Scope section. Format is chosen by the Accept header; an unsupported
        Accept returns 406 or falls back to the default format (US-004-01).
      description: Defined by module `contract-docs` in `modules/contract-docs-SPEC.md`.
      responses:
        '200':
          description: Return the published OpenAPI 3.x document covering every implemented
            endpoint, with schemas, 4xx/5xx errors, examples, the data-residency note
            and the Scope section. Format is chosen by the Accept header; an unsupported
            Accept returns 406 or falls back to the default format (US-004-01).
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
                    description: OpenAPI 3.x version string of the document.
                  info:
                    type: object
                    description: 'API title and version, including the data-residency
                      note (US-004-06) and the Scope section (US-005-01: no attachments,
                      no real-time channel, no mobile-specific endpoints).'
                  paths:
                    type: object
                    description: Every implemented endpoint with parameters, request
                      and response schemas, 4xx/5xx error responses and example payloads
                      (US-004-02, US-004-03, US-004-05).
                  components:
                    type: object
                    description: Shared schemas, including the error envelope.
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
  /openapi/coverage:
    get:
      operationId: API-CONTRACTDOCS-827943
      summary: 'Return the spec-versus-route coverage check: the share of implemented
        routes present in the published document (US-004-04, NFR-008).'
      description: Defined by module `contract-docs` in `modules/contract-docs-SPEC.md`.
      responses:
        '200':
          description: 'Return the spec-versus-route coverage check: the share of
            implemented routes present in the published document (US-004-04, NFR-008).'
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - coverage_percent
                - implemented_count
                - documented_count
                - undocumented_endpoints
                properties:
                  coverage_percent:
                    type: number
                    description: Percentage of implemented endpoints documented in
                      the published spec; target 100.
                  implemented_count:
                    type: integer
                    description: Number of endpoints registered in the route registry.
                  documented_count:
                    type: integer
                    description: Number of those endpoints present in the published
                      OpenAPI document.
                  undocumented_endpoints:
                    type: array
                    description: Method and path of each implemented endpoint missing
                      from the spec; empty when coverage is 100%.
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
components:
  schemas:
    AuthError:
      type: object
      additionalProperties: false
      required:
      - error
      - error.code
      - error.message
      properties:
        error:
          type: object
          description: Error envelope object wrapping the fields below (ASM-BE-001
            proposal).
        error.code:
          type: string
          description: Stable machine-readable code from the error registry, e.g.
            VALIDATION_ERROR, OTP_CODE_INVALID, OTP_CODE_EXPIRED, OTP_CODE_ALREADY_USED,
            OTP_ACCOUNT_LOCKED, OTP_RATE_LIMITED, INTERNAL_ERROR.
        error.message:
          type: string
          description: Human-readable reason. May change; never contains a one-time
            code or an unmasked email address.
        error.details:
          type: array
          description: Optional list of per-field validation details; empty when not
            applicable.
    NotesError:
      type: object
      additionalProperties: false
      required:
      - error
      - error.code
      - error.message
      properties:
        error:
          type: object
          description: Error envelope wrapping every 4xx and 5xx response body (ASM-BE-001).
        error.code:
          type: string
          description: Stable machine-readable registry code, e.g. VALIDATION_ERROR,
            NOTE_NOT_FOUND, NOTE_ALREADY_ARCHIVED, UNSUPPORTED_MEDIA_TYPE, INTERNAL_ERROR.
        error.message:
          type: string
          description: Human-readable reason; may change between releases.
        error.details:
          type: array
          description: Optional list of per-field validation details; empty when not
            applicable.
    SearchError:
      type: object
      additionalProperties: false
      required:
      - error
      - error.code
      - error.message
      properties:
        error:
          type: object
          description: Error envelope wrapping every 4xx and 5xx response (ASM-BE-001).
        error.code:
          type: string
          description: Stable machine-readable registry code, e.g. VALIDATION_ERROR,
            SEARCH_UNAVAILABLE, INTERNAL_ERROR.
        error.message:
          type: string
          description: Human-readable reason; may change between releases.
        error.details:
          type: array
          description: Optional per-field validation details, e.g. which query parameter
            was invalid.
    ContractDocsError:
      type: object
      additionalProperties: false
      required:
      - error
      properties:
        error:
          type: object
          description: 'Error envelope per backend.error-model (ASM-BE-001): contains
            code (stable machine-readable registry code, e.g. NOT_ACCEPTABLE or INTERNAL_ERROR),
            message (human-readable reason) and details (array).'
