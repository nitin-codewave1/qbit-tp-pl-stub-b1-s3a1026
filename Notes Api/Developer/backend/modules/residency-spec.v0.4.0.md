---
id: agent-developer/notes_api/backend/modules/residency-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.4.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/residency-SPEC.md
depends_on: [notes_api:US-001-01, notes_api:US-001-02, notes_api:US-001-03, notes_api:US-001-04, notes_api:US-001-05, notes_api:US-001-06]
commit_kind: fork
confidence: agent
run_id: df4d63c0-0252-56b1-8292-59d2f9d8aa48
okf_id: notes_api:backend/modules/residency-SPEC.md
---

<!-- SECTION: backend.residency.frontmatter | status: VERIFIED -->
## Frontmatter

```yaml
id: residency
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-003]
scope: [API-RESIDENCY-026899, API-RESIDENCY-174124, API-RESIDENCY-034853, API-RESIDENCY-913995, API-RESIDENCY-648672, API-RESIDENCY-922826, API-RESIDENCY-202775]
```

`Q-BE-003` is defined in the project spec. It blocks only the authentication mechanism in `backend.residency.authorization`. Every other section is written without it.
<!-- /SECTION: backend.residency.frontmatter -->
<!-- SECTION: backend.residency.purpose | status: DERIVED -->
## Purpose

**Responsible for.** The module keeps EU data residency of `notes_api` infrastructure enforceable and provable, which is `NFR-001`, `BR-001` to `BR-004` and `US-001-01` to `US-001-06`. It does this by:
- Deciding whether a requested resource region is an approved EU region (`US-001-01`, `BR-001`).
- Validating backup destinations before a backup runs (`US-001-02`).
- Evaluating the CI residency gate for a deployment (`US-001-03`, `BR-002`).
- Producing the compliance report with an overall percent-compliant figure (`US-001-04`, `NFR-004`).
- Evaluating the launch gate (`US-001-05`, `BR-003`).
- Running drift scans and raising alerts (`US-001-06`, `BR-004`, `NFR-003`).

The module follows `ASM-BE-011` from the project spec: residency is infrastructure tooling and not on the note request path.

**Not responsible for.**
- Note, search, auth and scope-guard behaviour. Those are owned by their modules, and no `residency` operation reads or writes note data.
- Performing the cloud provisioning itself, or executing the backup job (`ASM-BE-254`, `GAP-BE-258`). The module decides, records and reports.
- The infrastructure-as-code constraint that enforces EU regions at provisioning time (`BR-001` enforcement). That lives outside the request path.
- Product-manager request logging (`BR-017`).
- The human launch sign-off by the Engineering Lead (`BR-003`). The module reports the automatic gate result only (`GAP-BE-259`).
- The contents of the approved EU region list. The module consumes it as configuration (`ASM-BE-251`, `GAP-BE-252`).
- Encryption, retention and the alert transport. No input states them (`GAP-BE-256`, `GAP-BE-257`, `GAP-BE-255`).

**Design input.** `design-brief.md` holds only acceptance criteria (`AC-001-01-01` to `AC-001-06-02`), so there is no UI claim. Figma is not connected.
<!-- /SECTION: backend.residency.purpose -->
<!-- SECTION: backend.residency.data-model | status: DRAFT-UNVERIFIED -->
## Data model

Store: PostgreSQL 16 (`platform.md`), provisioned in an approved EU region (`BR-001`). Identifiers are `uuid` (`ASM-BE-252`). Timestamps are `timestamptz` in UTC (`ASM-BE-253`). Rows are never updated after they reach a terminal state, except `residency_alerts.delivery_state`. No credential, secret or connection string is stored in any column (`ASM-BE-260`).

### `residency_scans`
One row per drift scan or launch-gate refresh scan. A compliance report is the findings of the latest completed scan (`ASM-BE-258`).

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | no | generated | PK |
| `kind` | text | no | none | `CHECK (kind IN ('drift','launch_refresh'))` |
| `state` | text | no | `'requested'` | `CHECK (state IN ('requested','running','completed','failed'))` |
| `requested_at` | timestamptz | no | now | |
| `started_at` | timestamptz | yes | null | |
| `completed_at` | timestamptz | yes | null | set only when state is terminal |
| `resources_total` | integer | yes | null | `>= 0`, set at completion |
| `resources_pass` | integer | yes | null | `>= 0` |
| `resources_fail` | integer | yes | null | `>= 0` |
| `resources_unverified` | integer | yes | null | `>= 0`; pass + fail + unverified = total |
| `failure_reason` | text | yes | null | set only when state is `failed`; contains no secret |
| `idempotency_key` | text | yes | null | `UNIQUE` where not null (`ASM-BE-261`) |

Indexes:
- `(state, requested_at)`, to find scans to run or sweep.
- `(kind, completed_at DESC) WHERE state = 'completed'`, to serve "latest completed scan" for the report and the gate.

### `residency_scan_findings`
One row per resource per scan.

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | no | generated | PK |
| `scan_id` | uuid | no | none | FK → `residency_scans.id` `ON DELETE RESTRICT` |
| `resource_type` | text | no | none | |
| `resource_ref` | text | no | none | |
| `region` | text | yes | null | null means missing or unreadable |
| `recognised` | boolean | no | none | false when the scanner does not know the type |
| `residency_status` | text | no | none | `CHECK (residency_status IN ('pass','fail','unverified'))` |
| `recorded_at` | timestamptz | no | now | |

Constraint: `UNIQUE (scan_id, resource_type, resource_ref)`.

Classification, which is the single definition used everywhere in this module:
- `unverified`: the type is not recognised (`AC-001-04-02`).
- `fail`: the region is non-EU, or null, missing or unreadable (`AC-001-03-02`, `AC-001-03-03`, `ASM-BE-259`).
- `pass`: the type is recognised and the region is in the approved list.

Indexes:
- `(scan_id, residency_status)`, for report counts and to list non-passing findings for alerts.

### `residency_alerts`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | no | generated | PK |
| `finding_id` | uuid | no | none | FK → `residency_scan_findings.id` `ON DELETE RESTRICT`, `UNIQUE` |
| `detected_at` | timestamptz | no | now | start of the 15-minute bound (`NFR-003`) |
| `delivery_state` | text | no | `'pending'` | `CHECK (delivery_state IN ('pending','sent','failed'))` |
| `sent_at` | timestamptz | yes | null | set only when `sent` |
| `attempts` | integer | no | 0 | `>= 0` |

Index: `(delivery_state, detected_at) WHERE delivery_state <> 'sent'`, for the delivery sweep.

### `residency_gate_evaluations`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | no | generated | PK |
| `gate_kind` | text | no | none | `CHECK (gate_kind IN ('ci','launch'))` |
| `outcome` | text | no | none | `CHECK (outcome IN ('pass','fail'))` |
| `scan_id` | uuid | yes | null | FK → `residency_scans.id` `ON DELETE RESTRICT`; required when `gate_kind = 'launch'` |
| `deployment_ref` | text | yes | null | used when `gate_kind = 'ci'` |
| `resources_evaluated` | integer | no | none | `>= 0` |
| `resources_not_passing` | integer | no | none | `>= 0` |
| `evaluated_at` | timestamptz | no | now | |

Index: `(gate_kind, evaluated_at DESC)`, to find the latest launch-gate result.

### `residency_gate_evaluation_items`
CI gate inputs as evaluated.

| Column | Type | Null | Constraint |
|---|---|---|---|
| `id` | uuid | no | PK |
| `evaluation_id` | uuid | no | FK → `residency_gate_evaluations.id` `ON DELETE RESTRICT` |
| `resource_ref` | text | no | |
| `region` | text | yes | null means missing |
| `residency_status` | text | no | `CHECK (... IN ('pass','fail','unverified'))` |

Index: `(evaluation_id)`, to load an evaluation's items.

### `residency_provisioning_requests`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | no | generated | PK |
| `resource_type` | text | no | none | |
| `target_region` | text | yes | null | null when none was supplied |
| `decision` | text | no | none | `CHECK (decision IN ('accepted','rejected'))` |
| `decision_reason` | text | no | none | |
| `decided_at` | timestamptz | no | now | |
| `idempotency_key` | text | yes | null | `UNIQUE` where not null |

Index: `(decided_at DESC)`, for audit listing.

### `residency_backup_validations`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | no | generated | PK |
| `destination_ref` | text | no | none | a reference, never a connection string |
| `destination_region` | text | yes | null | null means unreadable |
| `outcome` | text | no | none | `CHECK (outcome IN ('pass','fail'))` |
| `duration_ms` | integer | no | none | `>= 0` |
| `validated_at` | timestamptz | no | now | |

Index: `(validated_at DESC)`, for audit listing.

**Not stated by any input.** Encryption at rest, retention periods, and the region list. These are `GAP-BE-256`, `GAP-BE-257` and `GAP-BE-252`. No PII column exists in this module.
<!-- /SECTION: backend.residency.data-model -->
<!-- SECTION: backend.residency.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

### Scan (`residency_scans.state`)

| From | To | Trigger | Guard |
|---|---|---|---|
| none | `requested` | `API-RESIDENCY-922826` (kind `drift`), the periodic job, or `API-RESIDENCY-648672` (kind `launch_refresh`) | |
| `requested` | `running` | the scan worker picks it up | none other running for the same kind (`ASM-BE-262`) |
| `running` | `completed` | the inventory was read and every finding recorded | counts sum to `resources_total` |
| `running` | `failed` | the inventory source could not be read | `failure_reason` set |
| `requested` | `failed` | a sweep abandons it | a timeout exists (`GAP-BE-264`) |

Terminal states: `completed` and `failed`. A terminal scan is immutable (`INV-BE-256`). There is no operation that moves a scan between states from outside; transitions are system-driven only. A caller can only read it through `API-RESIDENCY-202775`. If a worker attempts an illegal transition, it is rejected internally and logged. No HTTP status applies, because no operation exposes a transition.

A `failed` scan never produces a launch-gate pass (`INV-BE-254`). A failed refresh makes the launch gate evaluation fail closed.

### Alert (`residency_alerts.delivery_state`)

| From | To | Trigger | Guard |
|---|---|---|---|
| none | `pending` | a scan finding with a non-`pass` status of `fail` is recorded | one alert per finding (`ASM-BE-263`) |
| `pending` | `sent` | the alert is delivered | `sent_at` set |
| `pending` | `failed` | a delivery attempt fails | `attempts` incremented |
| `failed` | `pending` | the delivery sweep retries | before the 15-minute bound is exceeded, then still retried (`GAP-BE-255`) |

Terminal: `sent`. `failed` is not terminal. The retry cap is not stated by any input (`GAP-BE-255`).

### Decisions
Provisioning requests, backup validations and gate evaluations have no lifecycle. Each is one immutable decision record created in one step.
<!-- /SECTION: backend.residency.state-machine -->
<!-- SECTION: backend.residency.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Status codes below are assumptions where marked. The rendered operation records and the final error model (`GAP-BE-011`) govern.

**BE-RESIDENCY-FR-001**
- Given a provisioning request whose target region is in the approved EU region list,
- When `API-RESIDENCY-026899` is called,
- Then the request is accepted, a `residency_provisioning_requests` row with `decision = 'accepted'` is written, and the resource is subsequently expected to report a region in the approved list (`ASM-BE-254`).
- **Traces to.** `US-001-01`; `AC-001-01-01`; `BR-001`
- **Verification.** (planned) `provisioning_accepts_eu_region`
- **API.** `API-RESIDENCY-026899`

**BE-RESIDENCY-FR-002**
- Given a provisioning request whose target region is outside the approved EU list,
- When `API-RESIDENCY-026899` is called,
- Then the request is rejected (422, `ASM-BE-255`), a `rejected` decision is recorded with its reason, and no resource creation is triggered.
- **Traces to.** `US-001-01`; `AC-001-01-02`; `BR-001`
- **Verification.** (planned) `provisioning_rejects_non_eu_region_creates_nothing`
- **API.** `API-RESIDENCY-026899`

**BE-RESIDENCY-FR-003**
- Given a provisioning request with no target region,
- When `API-RESIDENCY-026899` is called,
- Then it is rejected as a validation failure (400) and no resource is created. A region that cannot be proven EU is never accepted.
- **Traces to.** `US-001-01`; `BR-001`; fail-closed reading of `AC-001-03-03` (`ASM-BE-259`)
- **Verification.** (planned) `provisioning_rejects_missing_region`
- **API.** `API-RESIDENCY-026899`

**BE-RESIDENCY-FR-004**
- Given a backup destination in an approved EU region,
- When `API-RESIDENCY-174124` is called,
- Then validation passes, a `pass` record is written, and the backup may proceed.
- **Traces to.** `US-001-02`; `AC-001-02-01`; `BR-001`
- **Verification.** (planned) `backup_validation_passes_eu_destination`
- **API.** `API-RESIDENCY-174124`

**BE-RESIDENCY-FR-005**
- Given a backup destination outside the EU,
- When `API-RESIDENCY-174124` is called,
- Then it fails with a residency violation within 30 seconds of receipt, and no backup data is written (422, `ASM-BE-255`). The `fail` validation record is audit metadata and not backup data (`ASM-BE-256`).
- **Traces to.** `US-001-02`; `AC-001-02-02`; `BR-001`
- **Verification.** (planned) `backup_validation_non_eu_fails_within_30s_writes_no_data`
- **API.** `API-RESIDENCY-174124`

**BE-RESIDENCY-FR-006**
- Given a backup destination whose region cannot be determined,
- When `API-RESIDENCY-174124` is called,
- Then it fails the validation and does not pass by default.
- **Traces to.** `US-001-02`; `AC-001-02-02`; fail-closed reading of `AC-001-03-03` (`ASM-BE-259`)
- **Verification.** (planned) `backup_validation_unreadable_region_fails`
- **API.** `API-RESIDENCY-174124`

**BE-RESIDENCY-FR-007**
- Given a deployment whose target resources are all in approved EU regions,
- When `API-RESIDENCY-034853` is called,
- Then the gate outcome is `pass` and the deployment may proceed.
- **Traces to.** `US-001-03`; `AC-001-03-01`; `BR-002`; `NFR-002`
- **Verification.** (planned) `ci_gate_passes_all_eu`
- **API.** `API-RESIDENCY-034853`

**BE-RESIDENCY-FR-008**
- Given a deployment with at least one target resource outside the EU,
- When `API-RESIDENCY-034853` is called,
- Then the gate outcome is `fail`, so the pipeline can fail the build and block the deployment.
- **Traces to.** `US-001-03`; `AC-001-03-02`; `BR-002`; `NFR-002`
- **Verification.** (planned) `ci_gate_fails_any_non_eu`
- **API.** `API-RESIDENCY-034853`

**BE-RESIDENCY-FR-009**
- Given a target resource whose region metadata is missing or unreadable,
- When `API-RESIDENCY-034853` is called,
- Then that resource is `fail` and the gate outcome is `fail`.
- **Traces to.** `US-001-03`; `AC-001-03-03`
- **Verification.** (planned) `ci_gate_missing_region_fails_not_passes`
- **API.** `API-RESIDENCY-034853`

**BE-RESIDENCY-FR-010**
- Given scanned resources exist,
- When `API-RESIDENCY-913995` is called,
- Then it returns every resource with its region and `pass`, `fail` or `unverified` status, plus an overall percent-compliant figure (`INV-BE-258`).
- **Traces to.** `US-001-04`; `AC-001-04-01`; `NFR-004`
- **Verification.** (planned) `report_lists_each_resource_with_region_and_status`
- **API.** `API-RESIDENCY-913995`

**BE-RESIDENCY-FR-011**
- Given a resource whose type the scanner does not recognise,
- When the report is generated,
- Then the resource is listed as `unverified` and never omitted.
- **Traces to.** `US-001-04`; `AC-001-04-02`
- **Verification.** (planned) `report_flags_unrecognised_type_unverified`
- **API.** `API-RESIDENCY-913995`

**BE-RESIDENCY-FR-012**
- Given the refreshed report shows 100% EU-verified resources with none `unverified`,
- When `API-RESIDENCY-648672` is called,
- Then the launch gate is `pass` and release is authorised by the automatic check.
- **Traces to.** `US-001-05`; `AC-001-05-01`; `BR-003`
- **Verification.** (planned) `launch_gate_passes_at_100_percent`
- **API.** `API-RESIDENCY-648672`

**BE-RESIDENCY-FR-013**
- Given the refreshed report shows less than 100% EU-verified resources, or any `unverified` resource, or a failed refresh scan, or an empty inventory,
- When `API-RESIDENCY-648672` is called,
- Then the launch gate is `fail` and release is blocked until resolved (`ASM-BE-257`, `Q-BE-251`).
- **Traces to.** `US-001-05`; `AC-001-05-02`; `BR-003`
- **Verification.** (planned) `launch_gate_fails_below_100_percent_or_unverified_or_empty`
- **API.** `API-RESIDENCY-648672`

**BE-RESIDENCY-FR-014**
- Given a launch-gate check is requested,
- When it runs,
- Then a fresh `launch_refresh` scan completes first, and the gate decision is computed from that scan and no older one.
- **Traces to.** `US-001-05`; `NFR-004` ("refreshed and 100% accurate at each launch gate check")
- **Verification.** (planned) `launch_gate_uses_fresh_scan`
- **API.** `API-RESIDENCY-648672`

**BE-RESIDENCY-FR-015**
- Given a drift scan runs and all resources are in EU regions,
- When the scan completes,
- Then no alert is raised and the scan records a pass.
- **Traces to.** `US-001-06`; `AC-001-06-01`
- **Verification.** (planned) `drift_scan_all_eu_no_alert_logs_pass`
- **API.** `API-RESIDENCY-922826`

**BE-RESIDENCY-FR-016**
- Given a drift scan finds a resource in a non-EU region,
- When the finding is recorded,
- Then an alert carrying the resource details is delivered to the DevOps team no later than 15 minutes after detection (`CON-BE-251`).
- **Traces to.** `US-001-06`; `AC-001-06-02`; `BR-004`; `NFR-003`
- **Verification.** (planned) `drift_scan_non_eu_alerts_within_15_minutes`
- **API.** `API-RESIDENCY-922826`

**BE-RESIDENCY-FR-017**
- Given a scan identifier,
- When `API-RESIDENCY-202775` is called,
- Then it returns that scan's state and, once completed, its findings. An unknown identifier returns 404 (`ASM-BE-264`).
- **Traces to.** `US-001-06`; `AC-001-06-01`; `AC-001-06-02`
- **Verification.** (planned) `get_drift_scan_returns_result_and_404_unknown`
- **API.** `API-RESIDENCY-202775`
<!-- /SECTION: backend.residency.requirements -->
<!-- SECTION: backend.residency.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-RESIDENCY-174124` | POST | `/residency/backup-validations` | `residency` | 200 | 400, 500 | Validate a backup configuration. A non-EU destination fails within 30 seconds and writes no data (US-001-02, BR-001, AC-001-02-02). |
| `API-RESIDENCY-034853` | POST | `/residency/ci-gate-checks` | `residency` | 200 | 400, 500 | Run the CI residency gate against a deployment's resource regions. The result lets the pipeline block a deploy that is not EU-only (US-001-03, BR-002, NFR-002). |
| `API-RESIDENCY-913995` | GET | `/residency/compliance-report` | `residency` | 200 | 404, 500 | Get the current residency compliance report, listing each resource and its region (US-001-04, NFR-004). |
| `API-RESIDENCY-922826` | POST | `/residency/drift-scans` | `residency` | 201 | 500, 503 | Trigger a residency drift scan across all resources. A non-EU resource is logged and alerts the DevOps team within 15 minutes (US-001-06, BR-004, NFR-003). |
| `API-RESIDENCY-202775` | GET | `/residency/drift-scans/{id}` | `residency` | 200 | 404, 500 | Get the result of a drift scan by its identifier (US-001-06, AC-001-06-01, AC-001-06-02). |
| `API-RESIDENCY-648672` | POST | `/residency/launch-gate-checks` | `residency` | 200 | 400, 500, 503 | Evaluate the launch gate. It refreshes the compliance report and reports whether any non-EU resource blocks launch (US-001-05, BR-003, NFR-004). |
| `API-RESIDENCY-026899` | POST | `/residency/provisioning-requests` | `residency` | 201 | 400, 500 | Request provisioning of an infrastructure resource. The request is accepted only if the target region is an approved EU region (BR-001, US-001-01). |

### `API-RESIDENCY-174124` — POST `/residency/backup-validations`

Validate a backup configuration. A non-EU destination fails within 30 seconds and writes no data (US-001-02, BR-001, AC-001-02-02).

**Request:** 
- `backup_name` (string, required): Name or identifier of the backup job or configuration being validated.
- `destination_region` (string, required): Region of the backup destination to validate.
- `replica_regions` (array): Any additional regions the backup is copied to. Each must also be an EU region.
**Response:** 
- `valid` (boolean, required): True only if every backup destination is in an approved EU region.
- `backup_name` (string, required): Echo of the backup name.
- `validated_regions` (array, required): Regions that were checked.
- `validated_at` (string, required): Timestamp of the validation (UTC).

### `API-RESIDENCY-034853` — POST `/residency/ci-gate-checks`

Run the CI residency gate against a deployment's resource regions. The result lets the pipeline block a deploy that is not EU-only (US-001-03, BR-002, NFR-002).

**Request:** 
- `deployment_id` (string, required): Identifier of the deployment or pipeline run being checked.
- `resources` (array, required): Resources in the deployment, each with a name and a region.
**Response:** 
- `gate_result` (string, required): pass or fail. A fail means the deploy must not reach production.
- `deployment_id` (string, required): Echo of the deployment identifier.
- `violations` (array, required): Resources found outside approved EU regions, each with name and region. Empty on pass.
- `checked_at` (string, required): Timestamp of the check (UTC).

### `API-RESIDENCY-913995` — GET `/residency/compliance-report`

Get the current residency compliance report, listing each resource and its region (US-001-04, NFR-004).

**Request:** none
**Response:** 
- `generated_at` (string, required): Timestamp when the report was generated (UTC).
- `total_resources` (integer, required): Number of resources covered by the report.
- `eu_resources` (integer, required): Number of resources verified as being in an approved EU region.
- `compliance_percentage` (number, required): Share of resources verified as EU (NFR-001).
- `resources` (array, required): Per-resource entries, each with name, type, region and an EU-verified flag.

### `API-RESIDENCY-922826` — POST `/residency/drift-scans`

Trigger a residency drift scan across all resources. A non-EU resource is logged and alerts the DevOps team within 15 minutes (US-001-06, BR-004, NFR-003).

**Request:** none
**Response:** 
- `scan_id` (string, required): Identifier of the scan.
- `status` (string, required): Scan status, such as completed or failed.
- `started_at` (string, required): Timestamp when the scan started (UTC).
- `resources_scanned` (integer, required): Number of resources inspected.
- `drift_detected` (boolean, required): True if any resource was found outside an approved EU region.
- `drifted_resources` (array, required): Resources found outside the EU, each with name, type and region. Empty when none.
- `alert_sent` (boolean, required): True if an alert was raised to the DevOps team for this scan.

### `API-RESIDENCY-202775` — GET `/residency/drift-scans/{id}`

Get the result of a drift scan by its identifier (US-001-06, AC-001-06-01, AC-001-06-02).

**Request:** 
- `id` (string, required): Identifier of the drift scan. This is the {id} path parameter.
**Response:** 
- `scan_id` (string, required): Identifier of the scan.
- `status` (string, required): Scan status.
- `started_at` (string, required): Timestamp when the scan started (UTC).
- `resources_scanned` (integer, required): Number of resources inspected.
- `drift_detected` (boolean, required): True if any resource was found outside an approved EU region.
- `drifted_resources` (array, required): Resources found outside the EU, each with name, type and region. Empty when none.
- `alert_sent` (boolean, required): True if an alert was raised to the DevOps team for this scan.

### `API-RESIDENCY-648672` — POST `/residency/launch-gate-checks`

Evaluate the launch gate. It refreshes the compliance report and reports whether any non-EU resource blocks launch (US-001-05, BR-003, NFR-004).

**Request:** 
- `release_id` (string): Identifier of the release being gated, for the record.
**Response:** 
- `gate_result` (string, required): pass or fail. A fail blocks launch.
- `report_generated_at` (string, required): Timestamp of the refreshed compliance report the decision is based on (UTC).
- `compliance_percentage` (number, required): Share of resources verified as EU at the time of the check.
- `blocking_findings` (array, required): Non-EU resources that block launch, each with name and region. Empty on pass.

### `API-RESIDENCY-026899` — POST `/residency/provisioning-requests`

Request provisioning of an infrastructure resource. The request is accepted only if the target region is an approved EU region (BR-001, US-001-01).

**Request:** 
- `resource_type` (string, required): Kind of infrastructure resource to provision, such as database, compute or backup storage.
- `resource_name` (string, required): Name of the resource to provision.
- `region` (string, required): Target region. Must be an approved EU region or the request is refused.
**Response:** 
- `provisioning_id` (string, required): Identifier of the accepted provisioning request.
- `resource_type` (string, required): Echo of the resource type.
- `resource_name` (string, required): Echo of the resource name.
- `region` (string, required): The approved EU region the resource was provisioned in.
- `status` (string, required): Outcome of the request, for example accepted.
- `created_at` (string, required): Timestamp when the request was accepted (UTC).
<!-- /SECTION: backend.residency.api-contract -->
<!-- SECTION: backend.residency.invariants | status: DRAFT-UNVERIFIED -->
## Invariants

- **INV-BE-251.** No resource whose region is non-EU is ever recorded with status `pass`. Source: `BR-001`, `AC-001-03-02`.
- **INV-BE-252.** A resource whose region is missing or unreadable is recorded as `fail`, never as `pass`. Source: `AC-001-03-03`, `ASM-BE-259`.
- **INV-BE-253.** Every resource in the scanned inventory appears in the report. A resource of an unrecognised type appears as `unverified`, and the number of report rows equals `resources_total`. Source: `AC-001-04-02`.
- **INV-BE-254.** A launch-gate evaluation has outcome `pass` if and only if its scan is `completed`, `resources_total > 0`, `resources_fail = 0` and `resources_unverified = 0`. Source: `BR-003`, `AC-001-05-01`, `AC-001-05-02`, `ASM-BE-257`.
- **INV-BE-255.** A rejected provisioning request or a failed backup validation leaves no resource created and no backup artefact written. Source: `AC-001-01-02`, `AC-001-02-02`.
- **INV-BE-256.** A scan in state `completed` or `failed` is never modified. Source: `NFR-004` (report accuracy and auditability), `ASM-BE-258`.
- **INV-BE-257.** Every `fail` finding recorded by a drift scan has exactly one `residency_alerts` row. Source: `AC-001-06-02`, `BR-004`, `ASM-BE-263`.
- **INV-BE-258.** The percent-compliant figure equals `resources_pass / resources_total`, with `unverified` counted as not compliant, and is derived from counts, not from rounded values. Source: `AC-001-04-01`, `US-001-05`, `ASM-BE-265`.
- **INV-BE-259.** No stored value, alert or log line contains a credential. Source: `ASM-BE-260`.
<!-- /SECTION: backend.residency.invariants -->
<!-- SECTION: backend.residency.authorization | status: DRAFT-UNVERIFIED -->
## Authorization

**Mechanism.** The credential, session and role model is undecided (`Q-BE-003`, `backend.auth-model` is `BLOCKED`). The mapping below uses only the personas named in the stories and is a proposal (`ASM-BE-266`), not a defined role set.

| Operation | Proposed caller (persona or system) | Source |
|---|---|---|
| `API-RESIDENCY-026899` | Platform/DevOps Engineer | `US-001-01` |
| `API-RESIDENCY-174124` | Platform/DevOps Engineer; the backup pipeline as a system caller | `US-001-02` |
| `API-RESIDENCY-034853` | Release/CI Pipeline Owner; the deployment pipeline as a system caller | `US-001-03` |
| `API-RESIDENCY-913995` | Compliance/Security Auditor; Engineering Lead; Platform/DevOps Engineer | `US-001-04`, `US-001-05` |
| `API-RESIDENCY-648672` | Engineering Lead | `US-001-05`, `BR-003` |
| `API-RESIDENCY-922826` | Platform/DevOps Engineer | `US-001-06` |
| `API-RESIDENCY-202775` | Platform/DevOps Engineer; Compliance/Security Auditor | `US-001-06` |

**Ownership predicate.** None. Residency records describe shared infrastructure and are not user-scoped, so there is no per-record owner. This follows `ASM-BE-266`.

**Open.** Whether these operations require authentication at all, how system callers authenticate, and the 401/403 behaviour all wait on `Q-BE-003` and `GAP-BE-260`.
<!-- /SECTION: backend.residency.authorization -->
<!-- SECTION: backend.residency.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

| Job | Purpose | Trigger | Idempotency and retries |
|---|---|---|---|
| Periodic drift scan | Run a `drift` scan post-launch (`US-001-06`: "periodic residency scan") | Schedule. The interval is not stated by any input (`GAP-BE-254`). | At most one `drift` scan running at a time (`ASM-BE-262`). A scheduled trigger that finds one running is skipped. |
| Alert delivery sweep | Deliver `pending` and retry `failed` alerts so each is sent within 15 minutes of `detected_at` (`NFR-003`) | Short-interval sweep. The cadence is a design choice that must leave margin inside the 15-minute bound; the figure is not stated by any input. | Delivery is keyed by `residency_alerts.id`, so a retry never creates a second alert (`INV-BE-257`). The retry cap is open (`GAP-BE-255`). |
| Stuck-scan sweep | Move scans stuck in `requested` or `running` to `failed` | Schedule. The timeout is not stated by any input (`GAP-BE-264`). | Safe to repeat; terminal scans are skipped. |

**Idempotency keys.** `API-RESIDENCY-922826` and `API-RESIDENCY-026899` accept an optional client key, backed by the `UNIQUE` column on their tables (`ASM-BE-261`). A repeat with the same key returns the original record. The other POST operations produce independent evaluation records and carry no key.

**Backup pre-check.** The backup pipeline is expected to call `API-RESIDENCY-174124` before each scheduled backup (`AC-001-02-02`). The backup job is owned outside this module (`ASM-BE-254`).

**Not scheduled.** Retention purge of residency records. No retention period is stated (`GAP-BE-256`).
<!-- /SECTION: backend.residency.jobs -->
<!-- SECTION: backend.residency.test-plan | status: DERIVED -->
## Test plan

**Unit**
- Region classification: EU, non-EU, null and unrecognised-type inputs map to `pass`, `fail`, `fail` and `unverified` respectively (`INV-BE-251`, `INV-BE-252`, `INV-BE-253`).
- Percent calculation, including zero total and `unverified` counted as not compliant (`INV-BE-258`, `INV-BE-254`).
- Launch-gate decision table over every combination of total, fail count, unverified count and scan state.
- Alert deduplication: one alert per finding (`INV-BE-257`).
- Scan state-transition guards: terminal scans cannot move (`INV-BE-256`).

**Integration (PostgreSQL 16)**
- Each FR above: `BE-RESIDENCY-FR-001` to `BE-RESIDENCY-FR-017`, using the planned test names in `backend.residency.requirements`.
- Backup validation timing: the failure path completes within 30 seconds of receipt (`BE-RESIDENCY-FR-005`).
- Launch gate triggers a fresh scan before deciding (`BE-RESIDENCY-FR-014`).
- Alert delivered within 15 minutes of detection with an injected delivery failure and retry (`BE-RESIDENCY-FR-016`).
- Idempotency key replay returns the original record without a duplicate row (`ASM-BE-261`).
- Constraint checks: foreign keys reject deletes; check constraints reject invalid status values.
- A fixture scan containing an unrecognised resource type, to prove it is listed and not omitted.

**Contract**
- Every operation in `scope` is present in the published OpenAPI document and the response codes match the rendered records (`NFR-008`).
- No residency operation exposes a credential field, and no example contains a secret (`INV-BE-259`).
- Error responses use the registry once `GAP-BE-011` is closed.
<!-- /SECTION: backend.residency.test-plan -->
<!-- SECTION: backend.residency.gaps | status: VERIFIED -->
## Gaps, contradictions and assumptions

### Gaps

| ID | What is missing | Degrades | Fallback used | Closing artefact |
|---|---|---|---|---|
| `GAP-BE-252` | The approved EU region list | provisioning, backup, CI gate and scan classification | list treated as configuration (`ASM-BE-251`) | The approved region list from the platform owner |
| `GAP-BE-253` | The source of the resource inventory and the catalogue of recognised resource types | scan, report, launch gate | an unknown type is `unverified` (`AC-001-04-02`) | A description of how resources are enumerated and their types |
| `GAP-BE-254` | Drift-scan schedule | `backend.residency.jobs` | none; the schedule is left open | A scan interval from the platform owner |
| `GAP-BE-255` | Alert channel, alert payload and retry cap for the DevOps team | `BE-RESIDENCY-FR-016` | alert delivery is modelled as a pluggable step | The alert destination and retry policy |
| `GAP-BE-256` | Retention of scans, findings, alerts and decision records | `backend.residency.data-model`, `jobs` | no purge job specified | A retention requirement |
| `GAP-BE-257` | Encryption at rest and in transit for residency data | `backend.residency.data-model` | nothing is asserted | A security requirement |
| `GAP-BE-258` | Whether `API-RESIDENCY-026899` itself creates the resource or only decides | `BE-RESIDENCY-FR-001` | decision only (`ASM-BE-254`) | Confirmation from the platform owner |
| `GAP-BE-259` | Whether the Engineering Lead sign-off in `BR-003` must be recorded by this module | `API-RESIDENCY-648672` | automatic result only | A decision on sign-off recording |
| `GAP-BE-260` | How the pipelines and the personas authenticate to these operations | `backend.residency.authorization` | none; waits on `Q-BE-003` | The session-model decision |
| `GAP-BE-261` | Backup retention and which system executes the backup | `US-001-02` | execution is outside this module | The backup design and retention |
| `GAP-BE-264` | Timeout after which a scan is abandoned; also the unknown-id behaviour is assumed rather than specified | stuck-scan sweep, `BE-RESIDENCY-FR-017` | none | A timeout figure and the final error registry |

### Contradiction

**`CON-BE-251` (non-critical).**
- `US-001-06` and `AC-001-06-02` say the alert is sent "immediately".
- `NFR-003` says "alert fired within 15 minutes of drift detection".
- Consequence of the strict reading: the alert path needs a synchronous send. Consequence of the relaxed reading: a 15-minute sweep is acceptable.
- The module reads `NFR-003` as the upper bound and aims to send as early as possible. This is not resolved here. `BE-RESIDENCY-FR-016` is `DRAFT-UNVERIFIED` until a human decides.

### Questions (degradable, default applied)

- **`Q-BE-251`** (degradable). With zero resources in the inventory, should the launch gate pass? Default: it fails (`ASM-BE-257`).
- **`Q-BE-252`** (degradable). If the same resource stays non-EU across scans, should each scan raise a new alert? Default: one alert per finding per scan (`ASM-BE-263`).

### Assumptions

| ID | Assumption | Reasoning and what would confirm it |
|---|---|---|
| `ASM-BE-251` | The approved EU region list is configuration supplied to the module, not hard-coded. | No input contains the list. Confirmed by `GAP-BE-252`. |
| `ASM-BE-252` | Identifiers are `uuid`. | No input states an id type. Confirmed by an identifier decision. |
| `ASM-BE-253` | Timestamps are `timestamptz` in UTC. | No input states timezone handling. Confirmed by a platform decision. |
| `ASM-BE-254` | The module decides, records and reports. Resource creation and the backup job are executed by infrastructure tooling outside it. | Follows `ASM-BE-011` and `BR-001` enforcement text. Confirmed by `GAP-BE-258`. |
| `ASM-BE-255` | A residency rejection returns 422. | The error model lists no status for it; 422 fits a well-formed request refused on a business rule. Confirmed by `GAP-BE-011`. |
| `ASM-BE-256` | The validation audit record is not "data" for `AC-001-02-02`; only backup data is meant. | The criterion concerns the backup pipeline. Confirmed by the author of `US-001-02`. |
| `ASM-BE-257` | An empty inventory, or a failed or missing refresh scan, makes the launch gate fail. | A gate that passes on no evidence defeats `BR-003`. Confirmed by `Q-BE-251`. |
| `ASM-BE-258` | The compliance report is the latest completed scan's findings; the GET returns it with its scan time and does not run a scan. | `NFR-004` ties refresh to the launch-gate check. Confirmed by the report requirement owner. |
| `ASM-BE-259` | A missing or unreadable region is `fail` in every context, not only the CI gate. | `AC-001-03-03` states it for the gate; consistency requires it elsewhere. Confirmed by the requirement owner. |
| `ASM-BE-260` | No credential or connection string is stored, returned or logged. Destinations are identified by reference. | The standing security constraint in this prompt, applied to this module. Confirmed by a security review. |
| `ASM-BE-261` | `API-RESIDENCY-922826` and `API-RESIDENCY-026899` accept an optional idempotency key. | Needed to make retried triggers safe; no input states it. Confirmed by the rendered operation records. |
| `ASM-BE-262` | At most one scan of a given kind runs at a time. | Avoids contradictory concurrent reports. Confirmed by an operations decision. |
| `ASM-BE-263` | One alert per `fail` finding per scan, with no cross-scan suppression. | The simplest reading of `AC-001-06-02`. Confirmed by `Q-BE-252`. |
| `ASM-BE-264` | An unknown scan id returns 404. | By analogy with the project error-model policy for unknown records. Confirmed by `GAP-BE-011`. |
| `ASM-BE-265` | Percent compliant is `resources_pass / resources_total`, with `unverified` counted as not compliant. | `BR-003` requires 100% EU-verified, so an unverified resource cannot count. Confirmed by the requirement owner. |
| `ASM-BE-266` | The persona-to-operation mapping is a proposal, and the records are not user-scoped. | Only story personas exist; no role set is defined. Confirmed by the auth decision (`Q-BE-003`). |
<!-- /SECTION: backend.residency.gaps -->
