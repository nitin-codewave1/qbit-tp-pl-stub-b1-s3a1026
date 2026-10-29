---
id: agent-developer/notes_api/backend/contracts/openapi.yaml
type: openapi
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.4.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/contracts/openapi.yaml
depends_on: []
commit_kind: fork
confidence: agent
run_id: df4d63c0-0252-56b1-8292-59d2f9d8aa48
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
      summary: Request a 6-digit one-time login code to be emailed to an address.
        Limited to 3 requests per 10 minutes per email; still allowed while verification
        is locked (BR-028, BR-032).
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
                  description: Email address that will receive the one-time code.
                    Masked in logs and examples (NFR-018).
                  type: string
      responses:
        '202':
          description: Request a 6-digit one-time login code to be emailed to an address.
            Limited to 3 requests per 10 minutes per email; still allowed while verification
            is locked (BR-028, BR-032).
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
      summary: Verify a one-time login code for an email address. Codes are single
        use, expire 10 minutes after issue, and 5 wrong codes lock verification for
        15 minutes (BR-022, BR-023, BR-033). The credential issued on success is undecided
        (Q-BE-003).
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
                  description: Email address the code was sent to.
                  type: string
                code:
                  description: The 6-digit numeric one-time code (BR-029). Never logged
                    (NFR-018).
                  type: string
      responses:
        '200':
          description: Verify a one-time login code for an email address. Codes are
            single use, expire 10 minutes after issue, and 5 wrong codes lock verification
            for 15 minutes (BR-022, BR-023, BR-033). The credential issued on success
            is undecided (Q-BE-003).
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
      x-devagent-source:
        module: auth
        document: modules/auth-SPEC.md
        id: API-AUTH-592570
  /notes:
    post:
      operationId: API-NOTES-232390
      summary: Create a note with title, body and optional tags; assigns unique id,
        timestamps, pinned=false and status active (US-002-01, US-006-02).
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
                  description: Note title; required (BR-005).
                  type: string
                body:
                  description: Note body; required (BR-005).
                  type: string
                tags:
                  description: Tag names to attach; tag rules open (GAP-BE-010).
                  type: array
      responses:
        '201':
          description: Create a note with title, body and optional tags; assigns unique
            id, timestamps, pinned=false and status active (US-002-01, US-006-02).
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
                    description: Pinned flag; false by default.
                    type: boolean
                  status:
                    description: active or archived.
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
  /notes/{id}:
    get:
      operationId: API-NOTES-045877
      summary: Get one note including status, pinned flag and tags (US-002-02, US-002-06,
        US-006-02).
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      parameters:
      - name: id
        in: path
        required: true
        description: Note id (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Get one note including status, pinned flag and tags (US-002-02,
            US-002-06, US-006-02).
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
                    description: Pinned flag.
                    type: boolean
                  status:
                    description: active or archived.
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
      summary: Edit a note's title, body and/or pinned flag; pin and unpin are done
        here (US-002-02, BR-018, US-006-02).
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
                  description: New title; must not be empty if sent.
                  type: string
                body:
                  description: New body; must not be empty if sent.
                  type: string
                pinned:
                  description: Set or clear the pinned flag.
                  type: boolean
      parameters:
      - name: id
        in: path
        required: true
        description: Note id (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Edit a note's title, body and/or pinned flag; pin and unpin
            are done here (US-002-02, BR-018, US-006-02).
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
                    description: Pinned flag.
                    type: boolean
                  status:
                    description: active or archived.
                    type: string
                  tags:
                    description: Tag names on the note.
                    type: array
                  created_at:
                    description: Creation timestamp.
                    type: string
                  updated_at:
                    description: Updated timestamp.
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
      summary: Archive a note by setting its archived flag; never deletes (US-002-04,
        US-006-05, BR-007).
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      parameters:
      - name: id
        in: path
        required: true
        description: Note id (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Archive a note by setting its archived flag; never deletes
            (US-002-04, US-006-05, BR-007).
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
                    description: Note id.
                    type: string
                  status:
                    description: archived.
                    type: string
                  updated_at:
                    description: Updated timestamp.
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
      summary: Add a tag to a note; repeating the add creates no duplicate (US-002-03,
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
                  description: Tag name to add; rules open (GAP-BE-010).
                  type: string
      parameters:
      - name: id
        in: path
        required: true
        description: Note id (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Add a tag to a note; repeating the add creates no duplicate
            (US-002-03, US-006-04).
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
                    description: Note id.
                    type: string
                  tags:
                    description: Tag names on the note after the add, without duplicates.
                    type: array
                  updated_at:
                    description: Updated timestamp.
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
      summary: Restore an archived note to active (US-002-05, US-006-05, BR-008).
      description: Defined by module `notes` in `modules/notes-SPEC.md`.
      parameters:
      - name: id
        in: path
        required: true
        description: Note id (path parameter).
        schema:
          type: string
      responses:
        '200':
          description: Restore an archived note to active (US-002-05, US-006-05, BR-008).
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
                    description: Note id.
                    type: string
                  status:
                    description: active.
                    type: string
                  updated_at:
                    description: Updated timestamp.
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
      summary: Retrieve the published OpenAPI 3.x document covering every implemented
        endpoint, with schemas, 4xx/5xx errors, examples, the data-residency note
        and the Scope section (US-004-01 to US-004-06, US-005-01; BR-012, BR-013;
        NFR-008, NFR-009).
      description: Defined by module `contract-docs` in `modules/contract-docs-SPEC.md`.
      responses:
        '200':
          description: Retrieve the published OpenAPI 3.x document covering every
            implemented endpoint, with schemas, 4xx/5xx errors, examples, the data-residency
            note and the Scope section (US-004-01 to US-004-06, US-005-01; BR-012,
            BR-013; NFR-008, NFR-009).
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
                    description: OpenAPI 3.x version string of the document.
                    type: string
                  info:
                    description: API title and version. Its description carries the
                      data-residency note (BR-013, US-004-06) and the Scope section
                      stating that attachments, real-time channels and mobile-specific
                      endpoints are out of scope (US-005-01; BR-014 to BR-016).
                    type: object
                  paths:
                    description: Every implemented endpoint, including the login endpoints,
                      each with documented 4xx/5xx responses and examples (BR-012,
                      NFR-008, NFR-009).
                    type: object
                  components:
                    description: Reusable schemas, including the error body schema
                      (US-004-02, US-004-03).
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
      summary: Run the spec-versus-route coverage check and report which implemented
        routes are missing from the published OpenAPI document (NFR-008, NFR-010).
      description: Defined by module `contract-docs` in `modules/contract-docs-SPEC.md`.
      responses:
        '200':
          description: Run the spec-versus-route coverage check and report which implemented
            routes are missing from the published OpenAPI document (NFR-008, NFR-010).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - implemented_routes
                - documented_routes
                - coverage_percent
                - undocumented_routes
                - out_of_scope_routes
                properties:
                  implemented_routes:
                    description: Number of implemented routes found in the route registry.
                    type: integer
                  documented_routes:
                    description: Number of those routes present in the published OpenAPI
                      document.
                    type: integer
                  coverage_percent:
                    description: Share of implemented endpoints present in the published
                      spec. The target is 100% (NFR-008).
                    type: number
                  undocumented_routes:
                    description: Implemented routes absent from the published document,
                      each as a method and path. Empty when coverage is complete.
                    type: array
                  out_of_scope_routes:
                    description: Implemented routes that fall into an excluded category
                      (attachment, real-time, mobile). The target is none (NFR-010).
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
  /residency/backup-validations:
    post:
      operationId: API-RESIDENCY-174124
      summary: Validate a backup configuration. A non-EU destination fails within
        30 seconds and writes no data (US-001-02, BR-001, AC-001-02-02).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              additionalProperties: false
              required:
              - backup_name
              - destination_region
              properties:
                backup_name:
                  description: Name or identifier of the backup job or configuration
                    being validated.
                  type: string
                destination_region:
                  description: Region of the backup destination to validate.
                  type: string
                replica_regions:
                  description: Any additional regions the backup is copied to. Each
                    must also be an EU region.
                  type: array
      responses:
        '200':
          description: Validate a backup configuration. A non-EU destination fails
            within 30 seconds and writes no data (US-001-02, BR-001, AC-001-02-02).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - valid
                - backup_name
                - validated_regions
                - validated_at
                properties:
                  valid:
                    description: True only if every backup destination is in an approved
                      EU region.
                    type: boolean
                  backup_name:
                    description: Echo of the backup name.
                    type: string
                  validated_regions:
                    description: Regions that were checked.
                    type: array
                  validated_at:
                    description: Timestamp of the validation (UTC).
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
  /residency/ci-gate-checks:
    post:
      operationId: API-RESIDENCY-034853
      summary: Run the CI residency gate against a deployment's resource regions.
        The result lets the pipeline block a deploy that is not EU-only (US-001-03,
        BR-002, NFR-002).
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
              - resources
              properties:
                deployment_id:
                  description: Identifier of the deployment or pipeline run being
                    checked.
                  type: string
                resources:
                  description: Resources in the deployment, each with a name and a
                    region.
                  type: array
      responses:
        '200':
          description: Run the CI residency gate against a deployment's resource regions.
            The result lets the pipeline block a deploy that is not EU-only (US-001-03,
            BR-002, NFR-002).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - gate_result
                - deployment_id
                - violations
                - checked_at
                properties:
                  gate_result:
                    description: pass or fail. A fail means the deploy must not reach
                      production.
                    type: string
                  deployment_id:
                    description: Echo of the deployment identifier.
                    type: string
                  violations:
                    description: Resources found outside approved EU regions, each
                      with name and region. Empty on pass.
                    type: array
                  checked_at:
                    description: Timestamp of the check (UTC).
                    type: string
        '400':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-034853.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-034853.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
      x-devagent-source:
        module: residency
        document: modules/residency-SPEC.md
        id: API-RESIDENCY-034853
  /residency/compliance-report:
    get:
      operationId: API-RESIDENCY-913995
      summary: Get the current residency compliance report, listing each resource
        and its region (US-001-04, NFR-004).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      responses:
        '200':
          description: Get the current residency compliance report, listing each resource
            and its region (US-001-04, NFR-004).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - generated_at
                - total_resources
                - eu_resources
                - compliance_percentage
                - resources
                properties:
                  generated_at:
                    description: Timestamp when the report was generated (UTC).
                    type: string
                  total_resources:
                    description: Number of resources covered by the report.
                    type: integer
                  eu_resources:
                    description: Number of resources verified as being in an approved
                      EU region.
                    type: integer
                  compliance_percentage:
                    description: Share of resources verified as EU (NFR-001).
                    type: number
                  resources:
                    description: Per-resource entries, each with name, type, region
                      and an EU-verified flag.
                    type: array
        '404':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-913995.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
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
      summary: Trigger a residency drift scan across all resources. A non-EU resource
        is logged and alerts the DevOps team within 15 minutes (US-001-06, BR-004,
        NFR-003).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      responses:
        '201':
          description: Trigger a residency drift scan across all resources. A non-EU
            resource is logged and alerts the DevOps team within 15 minutes (US-001-06,
            BR-004, NFR-003).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - scan_id
                - status
                - started_at
                - resources_scanned
                - drift_detected
                - drifted_resources
                - alert_sent
                properties:
                  scan_id:
                    description: Identifier of the scan.
                    type: string
                  status:
                    description: Scan status, such as completed or failed.
                    type: string
                  started_at:
                    description: Timestamp when the scan started (UTC).
                    type: string
                  resources_scanned:
                    description: Number of resources inspected.
                    type: integer
                  drift_detected:
                    description: True if any resource was found outside an approved
                      EU region.
                    type: boolean
                  drifted_resources:
                    description: Resources found outside the EU, each with name, type
                      and region. Empty when none.
                    type: array
                  alert_sent:
                    description: True if an alert was raised to the DevOps team for
                      this scan.
                    type: boolean
        '500':
          description: See `modules/residency-SPEC.md`, API-RESIDENCY-922826.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResidencyError'
        '503':
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
      summary: Get the result of a drift scan by its identifier (US-001-06, AC-001-06-01,
        AC-001-06-02).
      description: Defined by module `residency` in `modules/residency-SPEC.md`.
      parameters:
      - name: id
        in: path
        required: true
        description: Identifier of the drift scan. This is the {id} path parameter.
        schema:
          type: string
      responses:
        '200':
          description: Get the result of a drift scan by its identifier (US-001-06,
            AC-001-06-01, AC-001-06-02).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - scan_id
                - status
                - started_at
                - resources_scanned
                - drift_detected
                - drifted_resources
                - alert_sent
                properties:
                  scan_id:
                    description: Identifier of the scan.
                    type: string
                  status:
                    description: Scan status.
                    type: string
                  started_at:
                    description: Timestamp when the scan started (UTC).
                    type: string
                  resources_scanned:
                    description: Number of resources inspected.
                    type: integer
                  drift_detected:
                    description: True if any resource was found outside an approved
                      EU region.
                    type: boolean
                  drifted_resources:
                    description: Resources found outside the EU, each with name, type
                      and region. Empty when none.
                    type: array
                  alert_sent:
                    description: True if an alert was raised to the DevOps team for
                      this scan.
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
      summary: Evaluate the launch gate. It refreshes the compliance report and reports
        whether any non-EU resource blocks launch (US-001-05, BR-003, NFR-004).
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
                  description: Identifier of the release being gated, for the record.
                  type: string
      responses:
        '200':
          description: Evaluate the launch gate. It refreshes the compliance report
            and reports whether any non-EU resource blocks launch (US-001-05, BR-003,
            NFR-004).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - gate_result
                - report_generated_at
                - compliance_percentage
                - blocking_findings
                properties:
                  gate_result:
                    description: pass or fail. A fail blocks launch.
                    type: string
                  report_generated_at:
                    description: Timestamp of the refreshed compliance report the
                      decision is based on (UTC).
                    type: string
                  compliance_percentage:
                    description: Share of resources verified as EU at the time of
                      the check.
                    type: number
                  blocking_findings:
                    description: Non-EU resources that block launch, each with name
                      and region. Empty on pass.
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
        '503':
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
      summary: Request provisioning of an infrastructure resource. The request is
        accepted only if the target region is an approved EU region (BR-001, US-001-01).
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
                  description: Kind of infrastructure resource to provision, such
                    as database, compute or backup storage.
                  type: string
                resource_name:
                  description: Name of the resource to provision.
                  type: string
                region:
                  description: Target region. Must be an approved EU region or the
                    request is refused.
                  type: string
      responses:
        '201':
          description: Request provisioning of an infrastructure resource. The request
            is accepted only if the target region is an approved EU region (BR-001,
            US-001-01).
          content:
            application/json:
              schema:
                type: object
                additionalProperties: false
                required:
                - provisioning_id
                - resource_type
                - resource_name
                - region
                - status
                - created_at
                properties:
                  provisioning_id:
                    description: Identifier of the accepted provisioning request.
                    type: string
                  resource_type:
                    description: Echo of the resource type.
                    type: string
                  resource_name:
                    description: Echo of the resource name.
                    type: string
                  region:
                    description: The approved EU region the resource was provisioned
                      in.
                    type: string
                  status:
                    description: Outcome of the request, for example accepted.
                    type: string
                  created_at:
                    description: Timestamp when the request was accepted (UTC).
                    type: string
        '400':
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
  /search:
    get:
      operationId: API-SEARCH-842161
      summary: Search notes by keyword, tag or both, with pagination, archived exclusion
        by default and pinned-first ordering; returns 503 with a retry-after header
        when the search index is degraded.
      description: Defined by module `search` in `modules/search-SPEC.md`.
      parameters:
      - name: q
        in: query
        required: false
        description: Keyword matched against note title and body (US-003-01). At least
          one of q or tag is required.
        schema:
          type: string
      - name: tag
        in: query
        required: false
        description: Tag filter (US-003-02). When combined with q, both filters apply
          together (US-003-04). At least one of q or tag is required.
        schema:
          type: string
      - name: include_archived
        in: query
        required: false
        description: When absent or false, archived notes are excluded from results
          (US-003-06, BR-009). Invalid values return 400.
        schema:
          type: boolean
      - name: page
        in: query
        required: false
        description: Page number for pagination (US-003-05, BR-011). Invalid values
          return 400.
        schema:
          type: integer
      - name: limit
        in: query
        required: false
        description: Page size, up to 100 (NFR-007, US-003-05). Invalid values return
          400.
        schema:
          type: integer
      responses:
        '200':
          description: Search notes by keyword, tag or both, with pagination, archived
            exclusion by default and pinned-first ordering; returns 503 with a retry-after
            header when the search index is degraded.
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
                    description: Matching notes for the requested page, pinned notes
                      ordered first (BR-019, US-003-01). Each item shows the note's
                      id, title, body, tags, pinned flag and status, as defined once
                      by the notes module.
                    type: array
                  page:
                    description: The page number returned.
                    type: integer
                  limit:
                    description: The page size applied.
                    type: integer
                  total:
                    description: Total number of notes matching the filters, so clients
                      can paginate.
                    type: integer
        '400':
          description: See `modules/search-SPEC.md`, API-SEARCH-842161.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/SearchError'
        '503':
          description: See `modules/search-SPEC.md`, API-SEARCH-842161.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/SearchError'
      x-devagent-source:
        module: search
        document: modules/search-SPEC.md
        id: API-SEARCH-842161
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
          description: Stable machine-readable error code, e.g. AUTH_CODE_LOCKED,
            AUTH_CODE_INVALID, AUTH_CODE_EXPIRED, AUTH_CODE_USED, AUTH_RATE_LIMITED,
            VALIDATION_ERROR. Names are proposed pending the final registry.
          type: string
        message:
          description: Human-readable explanation of the error (US-005-04). Never
            contains the code or an unmasked email address (NFR-018).
          type: string
    NotesError:
      type: object
      additionalProperties: false
      required:
      - code
      - message
      properties:
        code:
          description: Stable machine-readable error code, e.g. VALIDATION_ERROR (backend.error-model);
            final names pending GAP-BE-011.
          type: string
        message:
          description: Human-readable explanation of the failure (US-005-04).
          type: string
    ContractDocsError:
      type: object
      additionalProperties: false
      required:
      - code
      - message
      properties:
        code:
          description: Stable machine-readable error code. Final names are open under
            GAP-BE-011.
          type: string
        message:
          description: Human-readable explanation of the failure (US-005-04). Contains
            no unmasked email address or login code (NFR-018).
          type: string
    ResidencyError:
      type: object
      additionalProperties: false
      required:
      - code
      - message
      properties:
        code:
          description: Stable machine-readable error code from the error-code registry.
            Final names are still open (GAP-BE-011).
          type: string
        message:
          description: Human-readable explanation of the failure (US-005-04). Contains
            no secrets or unmasked PII (NFR-018).
          type: string
        retry_after_seconds:
          description: Retry hint in seconds. Present only where a retry is meaningful,
            such as a 503. Proposed in the error model.
          type: integer
    SearchError:
      type: object
      additionalProperties: false
      required:
      - code
      - message
      properties:
        code:
          description: Stable machine-readable error code (e.g. VALIDATION_ERROR for
            400). Final code names for search errors are open under GAP-BE-011.
          type: string
        message:
          description: Human-readable explanation of the error (US-005-04).
          type: string
