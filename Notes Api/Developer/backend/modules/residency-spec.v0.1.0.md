---
id: agent-developer/notes_api/backend/modules/residency-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/residency-SPEC.md
depends_on: [notes_api:US-001-01, notes_api:US-001-02, notes_api:US-001-03, notes_api:US-001-04, notes_api:US-001-05, notes_api:US-001-06]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
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
scope: [API-RESIDENCY-026899, API-RESIDENCY-174124, API-RESIDENCY-034853, API-RESIDENCY-913995, API-RESIDENCY-395094, API-RESIDENCY-922826, API-RESIDENCY-726277]
```

`blocked_by` lists `Q-BE-003` (the credential decision, minted in the project spec). It blocks only `backend.residency.authorization`. Every other section is written.
<!-- /SECTION: backend.residency.frontmatter -->
<!-- SECTION: backend.residency.purpose | status: DERIVED -->
## Purpose

**Responsibility.** `residency` keeps and proves EU data residency for `notes_api` infrastructure: the database, hosting compute and backups (BR-001, NFR-001). It does this by:

- Deciding whether a requested resource region or backup destination is allowed (US-001-01, US-001-02).
- Deciding whether a deployment may proceed (US-001-03, BR-002).
- Producing the compliance report with a percent-compliant figure (US-001-04, NFR-004).
- Reporting the launch gate derived from that report (US-001-05, BR-003).
- Running post-launch drift scans and alerting the DevOps team (US-001-06, BR-004, NFR-003).

**Not responsible for:**

- Creating, modifying or deleting cloud infrastructure. `GAP-BE-278` records the open question of what an accepted request triggers; this spec assumes the IaC pipeline does it (`ASM-BE-264`).
- Executing the backup itself, or writing any backup artifact. The module validates a configuration only (US-001-02).
- Note data, search, login codes, or the SES email content. These belong to `notes`, `search` and `auth`. Whether SES appears in the inventory is part of `GAP-BE-279`.
- Per-request access checks on note data, or any user-owned data. The module has no owner column (Q-BE-002 does not affect it).
- Recording an Engineering Lead's sign-off decision. See `GAP-BE-285`.
- The web_app UI, and the scope-guard refusal of out-of-scope endpoints. The latter belongs to `scope-guard`.

**Placement.** The project module map says `residency` is "not on the request path" (`ASM-BE-007`). The supplied operation records are HTTP endpoints. See `CON-BE-253`; this spec follows the endpoint reading (`ASM-BE-263`).
<!-- /SECTION: backend.residency.purpose -->
<!-- SECTION: backend.residency.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**Conventions** (`ASM-BE-269`):

- Store: PostgreSQL 16 (`platform.md`).
- Primary keys are `uuid`.
- Timestamps are `timestamptz`, stored in UTC.
- Enumerations are `text` with `CHECK` constraints.
- A region is an opaque `text` value. No region value is asserted here (`GAP-BE-002`).
- No table holds a credential, secret or PII (core 12). Destination and resource references are identifiers only.
- Encryption at rest and backup treatment are unstated (`GAP-BE-010`).

### `approved_regions`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| region_code | text | NOT NULL | — | PRIMARY KEY; `CHECK (length(region_code) > 0)` |
| active | boolean | NOT NULL | true | — |
| added_at | timestamptz | NOT NULL | now() | — |

- Seeded from the approved EU region list (BR-001, AC-001-01-01). The list is missing (`GAP-BE-002`).
- Index: the primary key serves the exact-match lookup in `ASM-BE-267`.

### `resource_inventory`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| resource_id | uuid | NOT NULL | — | PRIMARY KEY |
| external_ref | text | NOT NULL | — | UNIQUE |
| resource_type | text | NOT NULL | — | — |
| recognised | boolean | NOT NULL | true | — |
| source | text | NOT NULL | — | `CHECK (source IN ('PROVISIONING','DISCOVERED'))` |
| registered_at | timestamptz | NOT NULL | now() | — |

- `recognised = false` marks a resource type the scanner does not know. These resources are reported as `UNVERIFIED` (AC-001-04-02).
- Index: the `external_ref` unique index serves the upsert performed by the drift scan.

### `provisioning_requests`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| request_id | uuid | NOT NULL | — | PRIMARY KEY |
| resource_type | text | NOT NULL | — | — |
| requested_region | text | NULL | — | NULL means missing (`INV-BE-257`) |
| status | text | NOT NULL | — | `CHECK (status IN ('ACCEPTED','REJECTED'))` |
| rejection_reason | text | NULL | — | NOT NULL when `status='REJECTED'` |
| resource_id | uuid | NULL | — | FK → `resource_inventory` `ON DELETE RESTRICT`; NOT NULL only when `ACCEPTED` |
| requested_by | text | NULL | — | `ASM-BE-273` |
| created_at | timestamptz | NOT NULL | now() | — |

- No secondary index. No operation lists requests.

### `backup_validations`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| validation_id | uuid | NOT NULL | — | PRIMARY KEY |
| destination_ref | text | NOT NULL | — | Identifier only. A connection string with a password is never stored. |
| destination_region | text | NULL | — | NULL means missing |
| outcome | text | NOT NULL | — | `CHECK (outcome IN ('PASSED','FAILED'))` |
| failure_code | text | NULL | — | NOT NULL when `FAILED` |
| duration_ms | integer | NOT NULL | — | `CHECK (duration_ms >= 0)` |
| requested_by | text | NULL | — | — |
| created_at | timestamptz | NOT NULL | now() | — |

- `duration_ms` is recorded so the 30-second budget in US-001-02 (AC-001-02-02) is observable. It is not enforced by a constraint.

### `ci_gate_checks` and `ci_gate_check_targets`

`ci_gate_checks`:

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| check_id | uuid | NOT NULL | — | PRIMARY KEY |
| deployment_ref | text | NOT NULL | — | — |
| outcome | text | NOT NULL | — | `CHECK (outcome IN ('PASSED','BLOCKED'))` |
| blocked_reason | text | NULL | — | NOT NULL when `BLOCKED` |
| requested_by | text | NULL | — | — |
| created_at | timestamptz | NOT NULL | now() | — |

`ci_gate_check_targets`:

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| check_id | uuid | NOT NULL | — | FK → `ci_gate_checks` `ON DELETE CASCADE` |
| target_ref | text | NOT NULL | — | — |
| region_code | text | NULL | — | NULL means missing |
| verdict | text | NOT NULL | — | `CHECK (verdict IN ('PASS','FAIL'))` |

- Primary key: `(check_id, target_ref)`.

### `scan_runs`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| scan_id | uuid | NOT NULL | — | PRIMARY KEY |
| trigger | text | NOT NULL | — | `CHECK (trigger IN ('SCHEDULED','MANUAL'))` |
| status | text | NOT NULL | 'RUNNING' | `CHECK (status IN ('RUNNING','COMPLETED_PASS','COMPLETED_DRIFT','FAILED'))` |
| started_at | timestamptz | NOT NULL | now() | — |
| completed_at | timestamptz | NULL | — | NOT NULL when `status <> 'RUNNING'` |
| resources_scanned | integer | NULL | — | `CHECK (resources_scanned >= 0)` |
| non_compliant_count | integer | NULL | — | `CHECK (non_compliant_count >= 0)` |
| unverified_count | integer | NULL | — | `CHECK (unverified_count >= 0)` |
| failure_reason | text | NULL | — | NOT NULL when `FAILED` |

Indexes:

- `(started_at DESC)`: serves "latest scan" (API-RESIDENCY-726277) and "latest completed scan" for the report and gate.
- A partial unique index on `((true)) WHERE status = 'RUNNING'`: enforces `INV-BE-262`.

### `scan_results`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| scan_id | uuid | NOT NULL | — | FK → `scan_runs` `ON DELETE CASCADE` |
| resource_id | uuid | NOT NULL | — | FK → `resource_inventory` `ON DELETE RESTRICT` |
| observed_region | text | NULL | — | NULL means missing or unreadable |
| compliance_status | text | NOT NULL | — | `CHECK (compliance_status IN ('COMPLIANT','NON_COMPLIANT','UNVERIFIED'))` |

- Primary key: `(scan_id, resource_id)`.
- Index `(scan_id, compliance_status)`: serves the report and gate counts, and extraction of findings per scan.

### `drift_alerts`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| alert_id | uuid | NOT NULL | — | PRIMARY KEY |
| scan_id | uuid | NOT NULL | — | FK → `scan_runs` `ON DELETE RESTRICT` |
| resource_id | uuid | NOT NULL | — | FK → `resource_inventory` `ON DELETE RESTRICT` |
| status | text | NOT NULL | 'PENDING' | `CHECK (status IN ('PENDING','SENT','FAILED'))` |
| attempts | integer | NOT NULL | 0 | `CHECK (attempts >= 0)` |
| detected_at | timestamptz | NOT NULL | now() | — |
| sent_at | timestamptz | NULL | — | NOT NULL when `SENT` |
| last_error | text | NULL | — | Must not contain secrets or addresses |

- `UNIQUE (scan_id, resource_id)`: idempotency for `INV-BE-259`.
- Partial index on `(detected_at) WHERE status IN ('PENDING','FAILED')`: serves the dispatch/retry job (see `backend.residency.jobs`).

**Not persisted:** the compliance report and the launch gate. Both are derived on read from the latest completed scan and the inventory (`ASM-BE-275`).

**Retention** of all tables above is unstated (`GAP-BE-282`).
<!-- /SECTION: backend.residency.data-model -->
<!-- SECTION: backend.residency.state-machine | status: DRAFT-UNVERIFIED -->
## State machines

Transitions happen only inside the module. A client request creates a record and never moves one between states. An illegal transition is therefore an internal defect, not a client error. It is not mapped to an error code and is logged and fails the operation (`ASM-BE-268`).

### Provisioning request

| From | To | Trigger | Guard |
|---|---|---|---|
| (RECEIVED, transient, in-transaction) | ACCEPTED | Request evaluated | `requested_region` non-null and active in `approved_regions` (`ASM-BE-267`); an inventory row is registered (`ASM-BE-264`) |
| (RECEIVED, transient) | REJECTED | Request evaluated | Region null, not in `approved_regions`, or evaluation error (`ASM-BE-268`); no inventory row |

Terminal: ACCEPTED, REJECTED.

### Backup validation

| From | To | Trigger | Guard |
|---|---|---|---|
| (RECEIVED, transient) | PASSED | Evaluated | `destination_region` non-null and active in `approved_regions` |
| (RECEIVED, transient) | FAILED | Evaluated | Region null, not approved, or evaluation error |

Terminal: PASSED, FAILED.

### CI gate check

| From | To | Trigger | Guard |
|---|---|---|---|
| (RECEIVED, transient) | PASSED | Evaluated | At least one target (`ASM-BE-272`), and every target `PASS` |
| (RECEIVED, transient) | BLOCKED | Evaluated | Zero targets, or any target `FAIL` (non-EU, null, unmatched), or evaluation error |

Terminal: PASSED, BLOCKED.

### Scan run

| From | To | Trigger | Guard |
|---|---|---|---|
| (none) | RUNNING | Scheduled or manual start | No other scan RUNNING (`INV-BE-262`) |
| RUNNING | COMPLETED_PASS | All inventory resources evaluated | Every result `COMPLIANT` |
| RUNNING | COMPLETED_DRIFT | All inventory resources evaluated | At least one `NON_COMPLIANT` or `UNVERIFIED` |
| RUNNING | FAILED | Evaluation error, or stale-scan reaper | A failed scan does not alter any previous scan's results |

Terminal: COMPLETED_PASS, COMPLETED_DRIFT, FAILED.

A scan whose results contain only `UNVERIFIED` and `COMPLIANT` entries is `COMPLETED_DRIFT`. That choice rests on `ASM-BE-265`.

### Drift alert

| From | To | Trigger | Guard |
|---|---|---|---|
| (none) | PENDING | `NON_COMPLIANT` result written | One per `(scan_id, resource_id)` |
| PENDING | SENT | Dispatch succeeds | — |
| PENDING | FAILED | A dispatch attempt fails | — |
| FAILED | SENT | Retry succeeds | — |

Terminal: SENT. FAILED is non-terminal while retries remain. The retry count and backoff are unstated (`GAP-BE-284`).

### Resource compliance and launch gate (derived, not stored states)

- **Compliance status.** Per scan, each resource is `COMPLIANT`, `NON_COMPLIANT` or `UNVERIFIED`. The rule is `INV-BE-254`.
- **Launch gate.** The gate is derived as `PASSED` or `FAILED` (`INV-BE-258`).
<!-- /SECTION: backend.residency.state-machine -->
<!-- SECTION: backend.residency.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Error codes and statuses for the rejection outcomes below are not set by the inputs (`GAP-BE-280`). `RESIDENCY_VIOLATION` is named in AC-001-02-02 as "residency violation error". Other names are proposals (`ASM-BE-274`).

**BE-RESIDENCY-FR-001** — Accept an EU provisioning request.
- Given a provisioning request whose requested region is active in `approved_regions`,
- When the request is submitted,
- Then it is recorded `ACCEPTED`, an inventory entry is registered for the resource, and its region is within the approved EU region list.
- **Traces to:** US-001-01, AC-001-01-01, BR-001, NFR-001.
- **Verification:** `residency_provisioning_eu_region_accepted` (planned).
- **API:** → API-RESIDENCY-026899.

**BE-RESIDENCY-FR-002** — Reject a non-EU provisioning request.
- Given a provisioning request whose region is not an approved EU region,
- When the request is submitted,
- Then it is recorded `REJECTED` with a residency-violation reason, and no inventory entry is created.
- **Traces to:** US-001-01, AC-001-01-02, BR-001.
- **Verification:** `residency_provisioning_non_eu_rejected_no_resource` (planned).
- **API:** → API-RESIDENCY-026899.

**BE-RESIDENCY-FR-003** — Reject a provisioning request with no region.
- Given a provisioning request with a missing or empty region,
- When it is submitted,
- Then it is rejected as in FR-002.
- **Traces to:** BR-001, derived from the fail-closed stance of AC-001-03-03 (`ASM-BE-267`, `ASM-BE-268`).
- **Verification:** `residency_provisioning_missing_region_rejected` (planned).
- **API:** → API-RESIDENCY-026899.

**BE-RESIDENCY-FR-004** — Pass an EU backup destination.
- Given a backup configuration whose destination region is an approved EU region,
- When it is validated,
- Then the outcome is `PASSED`, so the backup artifact is stored in an EU-region storage destination.
- **Traces to:** US-001-02, AC-001-02-01, BR-001.
- **Verification:** `residency_backup_validation_eu_passed` (planned).
- **API:** → API-RESIDENCY-174124.

**BE-RESIDENCY-FR-005** — Fail a non-EU backup destination fast.
- Given a backup configuration whose destination is outside the EU, or has no region,
- When it is validated,
- Then the outcome is `FAILED` with a `RESIDENCY_VIOLATION` error within 30 seconds of submission, and nothing is written to any backup destination.
- **Traces to:** US-001-02, AC-001-02-02, BR-001.
- **Verification:** `residency_backup_validation_non_eu_fails_within_30s_no_write` (planned).
- **API:** → API-RESIDENCY-174124.

**BE-RESIDENCY-FR-006** — Allow a deployment with all targets in the EU.
- Given a deployment whose targets are all in approved EU regions,
- When the CI residency check runs,
- Then the check is `PASSED` and the deployment may proceed.
- **Traces to:** US-001-03, AC-001-03-01, BR-002, NFR-002.
- **Verification:** `residency_ci_gate_all_eu_passes` (planned).
- **API:** → API-RESIDENCY-034853.

**BE-RESIDENCY-FR-007** — Block a deployment with any non-EU target.
- Given a deployment with at least one target outside the EU,
- When the check runs,
- Then the check is `BLOCKED` and the deployment must not proceed.
- **Traces to:** US-001-03, AC-001-03-02, BR-002, NFR-002.
- **Verification:** `residency_ci_gate_any_non_eu_blocks` (planned).
- **API:** → API-RESIDENCY-034853.

**BE-RESIDENCY-FR-008** — Block on missing region metadata or no targets.
- Given a target whose region is missing or unreadable, or a deployment with zero targets,
- When the check runs,
- Then the check is `BLOCKED`, never passed by default.
- **Traces to:** US-001-03, AC-001-03-03 (missing metadata); `ASM-BE-272` (zero targets, default for `Q-BE-261`).
- **Verification:** `residency_ci_gate_missing_region_blocks`, `residency_ci_gate_zero_targets_blocks` (planned).
- **API:** → API-RESIDENCY-034853.

**BE-RESIDENCY-FR-009** — Report every resource with region and status.
- Given resources exist and a scan has completed,
- When the compliance report is requested,
- Then it lists every inventory resource, its region and a pass/fail status, plus an overall percent-compliant figure.
- **Traces to:** US-001-04, AC-001-04-01, NFR-001, NFR-004.
- **Verification:** `residency_report_lists_all_resources_with_percent` (planned).
- **API:** → API-RESIDENCY-913995.

**BE-RESIDENCY-FR-010** — Flag unrecognised resources as unverified.
- Given a resource of a type the scanner does not recognise, or an inventory resource with no result in the latest scan,
- When the report is requested,
- Then it appears flagged `UNVERIFIED` and is never omitted. It does not count as compliant (`ASM-BE-265`).
- **Traces to:** US-001-04, AC-001-04-02.
- **Verification:** `residency_report_unrecognised_resource_unverified` (planned).
- **API:** → API-RESIDENCY-913995.

**BE-RESIDENCY-FR-011** — Pass the launch gate at full compliance.
- Given the latest completed scan shows 100% of a non-empty inventory `COMPLIANT`,
- When the launch gate is requested,
- Then the status is `PASSED`.
- **Traces to:** US-001-05, AC-001-05-01, BR-003, NFR-004.
- **Verification:** `residency_launch_gate_full_compliance_passes` (planned).
- **API:** → API-RESIDENCY-395094.

**BE-RESIDENCY-FR-012** — Fail the launch gate below full compliance.
- Given the latest completed scan shows less than 100% compliant, or no completed scan exists, or the inventory is empty,
- When the launch gate is requested,
- Then the status is `FAILED` (`ASM-BE-265`, `ASM-BE-266`).
- **Traces to:** US-001-05, AC-001-05-02, BR-003.
- **Verification:** `residency_launch_gate_below_100_fails`, `residency_launch_gate_no_scan_fails` (planned).
- **API:** → API-RESIDENCY-395094.

**BE-RESIDENCY-FR-013** — Pass a clean drift scan without alerting.
- Given a drift scan in which all resources are in EU regions,
- When it completes,
- Then the scan is `COMPLETED_PASS`, a pass is logged, and no alert is created.
- **Traces to:** US-001-06, AC-001-06-01.
- **Verification:** `residency_drift_scan_clean_logs_pass_no_alert` (planned).
- **API:** → API-RESIDENCY-922826.

**BE-RESIDENCY-FR-014** — Record drift and alert the DevOps team.
- Given a drift scan finds a resource in a non-EU region,
- When the finding is recorded,
- Then a `drift_alert` is created with resource details and delivered to the DevOps team within 15 minutes of detection (NFR-003).
- **Traces to:** US-001-06, AC-001-06-02, BR-004, NFR-003.
- **Verification:** `residency_drift_scan_non_eu_records_and_alerts_within_15m` (planned).
- **API:** → API-RESIDENCY-922826.

**BE-RESIDENCY-FR-015** — Reject a concurrent scan.
- Given a scan is `RUNNING`,
- When another scan is requested,
- Then the request is refused and no second `RUNNING` scan exists (`ASM-BE-270`).
- **Traces to:** US-001-06, derived from `INV-BE-262`.
- **Verification:** `residency_drift_scan_concurrent_refused` (planned).
- **API:** → API-RESIDENCY-922826.

**BE-RESIDENCY-FR-016** — Return the latest scan result.
- Given at least one scan exists,
- When the latest result is requested,
- Then the most recent scan by `started_at` is returned, whatever its status.
- If no scan exists, a documented "no scan available" error is returned (`ASM-BE-274`; status open under `GAP-BE-280`).
- **Traces to:** US-001-06.
- **Verification:** `residency_latest_scan_returns_most_recent`, `residency_latest_scan_none_available` (planned).
- **API:** → API-RESIDENCY-726277.

**BE-RESIDENCY-FR-017** — Retry failed alert delivery.
- Given an alert dispatch attempt fails,
- When the retry job runs,
- Then the alert is retried, and the failure and attempt count remain visible on the alert record (`last_error`, `attempts`).
- **Traces to:** NFR-003 and BR-004. The retry mechanism is `ASM-BE-271`; its bounds are `GAP-BE-284`.
- **Verification:** `residency_alert_retry_on_failure` (planned).
- **API:** none.
<!-- /SECTION: backend.residency.requirements -->
<!-- SECTION: backend.residency.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-RESIDENCY-174124` | POST | `/residency/backup-validations` | `residency` | 200 | 400, 422, 500 | Validate a backup configuration is EU-only; a non-EU configuration fails fast and writes no data (US-001-02). |
| `API-RESIDENCY-034853` | POST | `/residency/ci-gate-checks` | `residency` | 200 | 400, 500 | Evaluate a deployment against the CI residency gate, blocking non-EU deploys (US-001-03). |
| `API-RESIDENCY-913995` | GET | `/residency/compliance-report` | `residency` | 200 | 404, 500 | Get the residency compliance report listing each resource, its region, pass/fail status and overall percent compliant (US-001-04). |
| `API-RESIDENCY-922826` | POST | `/residency/drift-scans` | `residency` | 201 | 500, 503 | Run a residency drift scan; non-EU resources are recorded and trigger an alert to the DevOps team (US-001-06). |
| `API-RESIDENCY-726277` | GET | `/residency/drift-scans/latest` | `residency` | 200 | 404, 500 | Get the result of the most recent drift scan (US-001-06). |
| `API-RESIDENCY-395094` | GET | `/residency/launch-gate` | `residency` | 200 | 404, 500 | Get the launch gate status, passing only when the residency report shows full EU compliance (US-001-05). |
| `API-RESIDENCY-026899` | POST | `/residency/provisioning-requests` | `residency` | 201 | 400, 422, 500 | Submit a resource provisioning request; accepted only if the target region is an approved EU region, otherwise rejected (US-001-01). |

### `API-RESIDENCY-174124` — POST `/residency/backup-validations`

Validate a backup configuration is EU-only; a non-EU configuration fails fast and writes no data (US-001-02).

**Request:** 
- `backup_target` (string, required): Backup storage bucket or service identifier.
- `region` (string, required): Region of the backup target.
- `replica_regions` (array): Regions of any backup replicas or copies.
**Response:** 
- `valid` (boolean, required): True when all backup locations are in approved EU regions.
- `regions_checked` (array, required): Regions that were evaluated.

### `API-RESIDENCY-034853` — POST `/residency/ci-gate-checks`

Evaluate a deployment against the CI residency gate, blocking non-EU deploys (US-001-03).

**Request:** 
- `deployment_id` (string, required): Identifier of the CI deployment being evaluated.
- `target_regions` (array, required): Regions the deployment would touch.
**Response:** 
- `deployment_id` (string, required): Identifier of the evaluated deployment.
- `verdict` (string, required): Gate result: pass or fail.
- `violations` (array, required): Non-EU resources or regions causing a failure; empty on pass.

### `API-RESIDENCY-913995` — GET `/residency/compliance-report`

Get the residency compliance report listing each resource, its region, pass/fail status and overall percent compliant (US-001-04).

**Request:** none
**Response:** 
- `resources` (array, required): Each resource with its region and pass/fail status.
- `percent_compliant` (number, required): Overall percentage of infrastructure verified in EU regions.
- `generated_at` (string, required): Timestamp when the report was generated.

### `API-RESIDENCY-922826` — POST `/residency/drift-scans`

Run a residency drift scan; non-EU resources are recorded and trigger an alert to the DevOps team (US-001-06).

**Request:** none
**Response:** 
- `scan_id` (string, required): Identifier of the scan run.
- `scanned_at` (string, required): Timestamp of the scan.
- `drift_detected` (boolean, required): True when any resource was found in a non-EU region.
- `non_eu_resources` (array, required): Details of resources found outside EU regions; empty when none.
- `alert_sent` (boolean, required): Whether an alert was raised for this scan.

### `API-RESIDENCY-726277` — GET `/residency/drift-scans/latest`

Get the result of the most recent drift scan (US-001-06).

**Request:** none
**Response:** 
- `scan_id` (string, required): Identifier of the scan run.
- `scanned_at` (string, required): Timestamp of the scan.
- `drift_detected` (boolean, required): True when any resource was found in a non-EU region.
- `non_eu_resources` (array, required): Resources found outside EU regions; empty when none.

### `API-RESIDENCY-395094` — GET `/residency/launch-gate`

Get the launch gate status, passing only when the residency report shows full EU compliance (US-001-05).

**Request:** none
**Response:** 
- `verdict` (string, required): Launch gate result: pass or fail.
- `percent_compliant` (number, required): Compliance percentage from the current report.
- `blocking_resources` (array, required): Non-compliant resources blocking launch; empty on pass.

### `API-RESIDENCY-026899` — POST `/residency/provisioning-requests`

Submit a resource provisioning request; accepted only if the target region is an approved EU region, otherwise rejected (US-001-01).

**Request:** 
- `resource_type` (string, required): Kind of infrastructure resource to provision (database, compute, backup storage).
- `resource_name` (string, required): Name of the resource to provision.
- `region` (string, required): Target cloud region; must be an approved EU region.
**Response:** 
- `resource_name` (string, required): Name of the accepted resource.
- `region` (string, required): Region the resource was accepted for.
- `status` (string, required): Outcome of the provisioning request.
<!-- /SECTION: backend.residency.api-contract -->
<!-- SECTION: backend.residency.invariants | status: DERIVED -->
## Invariants

- **INV-BE-254.** A result or decision is `COMPLIANT`, `PASSED` or `ACCEPTED` only if the region is non-null and active in `approved_regions` at evaluation time. (BR-001, BR-002)
- **INV-BE-255.** A `REJECTED` provisioning request has `resource_id` NULL and registered no inventory row. (AC-001-01-02)
- **INV-BE-256.** In every outcome, the module writes nothing to any backup destination or artifact store. A failed validation additionally leaves no partial output. (AC-001-02-02)
- **INV-BE-257.** A missing, unreadable or unmatched region never produces a pass, `COMPLIANT` or `PASSED`. (AC-001-03-03)
- **INV-BE-258.** The launch gate is `PASSED` only if a completed scan exists, the inventory is non-empty, and every inventory resource is `COMPLIANT` in it (100%). (BR-003, AC-001-05-01)
- **INV-BE-259.** Each `NON_COMPLIANT` scan result has exactly one `drift_alert` for that `(scan_id, resource_id)`. (BR-004)
- **INV-BE-260.** The report's resource count equals the inventory's. A resource without a result in the latest scan is `UNVERIFIED`, never absent. (AC-001-04-02)
- **INV-BE-261.** Records in a terminal state are never updated, except that `drift_alerts` may advance `FAILED → SENT`.
- **INV-BE-262.** At most one scan has `status = 'RUNNING'`.
- **INV-BE-263.** The module stores no credential, secret or PII in any table or log (core 12).
<!-- /SECTION: backend.residency.invariants -->
<!-- SECTION: backend.residency.authorization | status: BLOCKED -->
## Authorisation

**Blocked.** The role × operation matrix cannot be written. `backend.auth-model` is `BLOCKED` on `Q-BE-001` and `Q-BE-003`, and no input defines a credential, a role model, or a machine identity for CI callers.

**What the stories name** (intent only, not a permission matrix):

| Actor (story) | Operation(s) of interest |
|---|---|
| Platform/DevOps Engineer (US-001-01, US-001-02, US-001-06) | Provisioning, backup validation, drift scans, drift alerts |
| Release/CI Pipeline Owner (US-001-03) | CI gate checks |
| Compliance/Security Auditor (US-001-04) | Compliance report |
| Engineering Lead (US-001-05) | Launch gate |

**Ownership predicate.** None. The module's data is infrastructure state, with no user-owned rows (`backend.residency.data-model`).

**What would unblock it:**

- The answer to `Q-BE-003`: the credential type, lifetime and transport, and whether non-interactive CI callers are supported.
- A role definition mapping the four actors to the seven operations.
- Whether the GET operations (API-RESIDENCY-913995, API-RESIDENCY-395094, API-RESIDENCY-726277) are readable by anyone authenticated.
<!-- /SECTION: backend.residency.authorization -->
<!-- SECTION: backend.residency.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

| Job | Behaviour | Idempotency key | Gap |
|---|---|---|---|
| Periodic drift scan | Starts a `SCHEDULED` scan: discovers or upserts inventory, evaluates every resource, writes `scan_results`, sets scan status, creates alerts. Respects `INV-BE-262`; if a scan is already RUNNING the tick is skipped. (US-001-06, AC-001-06-01) | `scan_id` per run. Alert key `(scan_id, resource_id)`. | Interval and discovery source: `GAP-BE-011` (project spec) and `GAP-BE-279` |
| Alert dispatch and retry | Picks `PENDING` and `FAILED` alerts (partial index) and sends to the DevOps channel. Updates `attempts`, `sent_at` and `last_error`. Must respect the 15-minute target. (NFR-003) | `alert_id` | Channel: `GAP-BE-011`. Retry count and backoff: `GAP-BE-284` |
| Stale-scan reaper | Sets a `RUNNING` scan to `FAILED` after a timeout, so a crashed run cannot block all future scans (`INV-BE-262`). | `scan_id` | Timeout value unsupplied: `GAP-BE-284` |
| Retention sweep | Not specified. | — | `GAP-BE-282` |

**Request idempotency.** Whether the POST operations carry a client idempotency key, and the rate-limit class, are unstated (`GAP-BE-283`). Each POST creates a new decision record.
<!-- /SECTION: backend.residency.jobs -->
<!-- SECTION: backend.residency.test-plan | status: DERIVED -->
## Test plan

**Unit** (planned):

- Region matching: exact string against active `approved_regions`; null, empty and unknown values fail (`INV-BE-254`, `INV-BE-257`).
- Percent-compliant computation, including empty inventory and `UNVERIFIED` handling (`ASM-BE-265`).
- Gate derivation (`INV-BE-258`).
- Scan state guards, and the alert uniqueness key.
- Fail-closed behaviour when an evaluation throws (`ASM-BE-268`).

**Integration** (planned, PostgreSQL 16 per `platform.md`):

- The `FR-001` to `FR-017` tests named above, against a real database.
- Constraint tests: partial unique index on `RUNNING` scans, `CHECK` constraints, and FK `RESTRICT` and `CASCADE` behaviour.
- A timing test for the 30-second backup failure budget (`residency_backup_validation_non_eu_fails_within_30s_no_write`).
- An alert-latency test against a stubbed channel for the 15-minute target (NFR-003).
- A log inspection test: no secrets or addresses in output (`INV-BE-263`).

**Contract** (planned):

- Every `API-RESIDENCY-*` operation in the module's records is present in the published OpenAPI document with request, response and 4xx/5xx schemas (BR-012, NFR-008).
- Documented outcomes for rejection, block and "no scan available" match actual behaviour (`GAP-BE-280`).
- The residency data-handling note is present (BR-013).
<!-- /SECTION: backend.residency.test-plan -->
<!-- SECTION: backend.residency.gaps | status: DERIVED -->
## Gaps, contradictions, assumptions and questions

### Contradictions (non-critical)

- **CON-BE-253.** The project module map and `ASM-BE-007` say `residency` is operational tooling "not on the request path". The module's operation records are seven HTTP endpoints. Reading A (endpoints served by the `notes_api` process) is followed here. Reading B (a separate tool) would change where the endpoints are hosted and how they are authorised. Neither reading changes a data shape, state machine, authorisation rule or money/PII path.
- **CON-BE-254.** US-001-06 is "Should Have", while NFR-003 (alert within 15 minutes of drift detection) is "Must Have". This spec follows the stricter reading (the alert behaviour is required). Reading "Should" would allow the alerting to ship after launch.

### Questions (all degradable; defaults taken silently, unattended)

- **Q-BE-258** (degradable): Does an `UNVERIFIED` resource count against the percent-compliant figure? Default: yes (`ASM-BE-265`).
- **Q-BE-259** (degradable): What is the launch gate when no completed scan exists? Default: `FAILED` (`ASM-BE-266`).
- **Q-BE-260** (degradable): Should persistent drift re-alert on every scan? Default: one alert per `(scan, resource)`, i.e. one on each scan that finds it (`ASM-BE-271`).
- **Q-BE-261** (degradable): Is a deployment with zero targets blocked? Default: yes (`ASM-BE-272`).

### Assumptions

- **ASM-BE-263.** The residency operations are served by the same Express and PostgreSQL service (`platform.md`). This resolves `CON-BE-253`.
- **ASM-BE-264.** An `ACCEPTED` provisioning request registers a pending inventory entry. The actual infrastructure is created by the IaC pipeline, outside this module. Confirm with `GAP-BE-278`.
- **ASM-BE-265.** `UNVERIFIED` counts as not EU-verified. Percent compliant = `COMPLIANT` count ÷ all inventory resources. An empty inventory yields no percent and a `FAILED` gate. (NFR-001 requires 100% EU-verified.)
- **ASM-BE-266.** The report and gate read the latest completed scan. No completed scan means the gate is `FAILED`. A GET never triggers a scan. See `GAP-BE-281`.
- **ASM-BE-267.** Region comparison is exact, case-sensitive string equality against active `approved_regions`.
- **ASM-BE-268.** Any evaluation error yields a non-pass outcome (fail closed). It is recorded and does not alter earlier scan results.
- **ASM-BE-269.** Keys are `uuid`, timestamps are `timestamptz` (UTC), and enumerations are `text` with `CHECK` constraints.
- **ASM-BE-270.** A second scan requested while one is `RUNNING` is refused, not queued.
- **ASM-BE-271.** One alert per `(scan, resource)`. Failed dispatch is retried by a job with bounds still to be set.
- **ASM-BE-272.** A CI gate check with zero targets is `BLOCKED`.
- **ASM-BE-273.** `requested_by` is a nullable identifier, populated once `Q-BE-003` is decided. No credential is stored.
- **ASM-BE-274.** The error code names `SCAN_IN_PROGRESS` and `NO_SCAN_AVAILABLE` are proposals. `RESIDENCY_VIOLATION` follows AC-001-02-02.
- **ASM-BE-275.** The compliance report and launch gate are derived on read and are not persisted.

### Gaps (each names its closing artefact)

- **GAP-BE-278.** What an `ACCEPTED` provisioning request triggers in infrastructure is unstated. Closing artefact: an IaC or platform input.
- **GAP-BE-279.** The scanner's inventory source of truth, how it reads a resource's region, and the list of recognised resource types are unstated. Whether SES is in the inventory is also open. Closing artefact: a platform input.
- **GAP-BE-280.** The HTTP statuses and bodies for provisioning rejection, backup failure, CI block, scan-in-progress and no-scan-available are not supplied. Closing artefact: acceptance criteria or an error-model decision.
- **GAP-BE-281.** NFR-004 requires the report to be "refreshed" at each gate check, with no age bound, and does not say whether a gate check triggers a scan. Closing artefact: an NFR with a maximum data age.
- **GAP-BE-282.** The retention of provisioning, validation, check, scan and alert records is unstated. NFR-019 covers only login records. Closing artefact: a retention NFR.
- **GAP-BE-283.** Idempotency, rate-limit class and client retry behaviour for the POST operations are unstated, though CI callers may retry. Closing artefact: an NFR.
- **GAP-BE-284.** The alert retry count and backoff, the alert repeat policy for persistent drift, and the stale-scan timeout are unstated. Closing artefact: an operations requirement.
- **GAP-BE-285.** BR-003 names "launch gate sign-off by Engineering Lead". Whether sign-off is recorded in the system, with an approver, or the gate is only computed, is unstated. Closing artefact: a story or acceptance criterion.

### Project-level gaps and assumptions applied here

- `GAP-BE-002`: the approved EU region list.
- `GAP-BE-010`: encryption at rest and backup schedule.
- `GAP-BE-011`: IaC tool, CI system, report format, scan interval and alert channel.
- `GAP-BE-018`: observability requirements.
- `ASM-BE-007`: placement of `residency`.
- `Q-BE-003`: blocks `backend.residency.authorization`.
<!-- /SECTION: backend.residency.gaps -->
