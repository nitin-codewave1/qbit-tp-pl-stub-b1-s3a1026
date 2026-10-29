---
id: agent-developer/notes_api/backend/modules/residency-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/residency-SPEC.md
depends_on: [notes_api:US-001-01, notes_api:US-001-02, notes_api:US-001-03, notes_api:US-001-04, notes_api:US-001-05, notes_api:US-001-06]
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
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
scope: [API-RESIDENCY-892950, API-RESIDENCY-174124, API-RESIDENCY-685134, API-RESIDENCY-913995, API-RESIDENCY-648672, API-RESIDENCY-922826]
```

`blocked_by` lists `Q-BE-003` (defined in `backend.auth-model`) because it blocks only the enforcement part of `backend.residency.authorization`. Every other section is written.
<!-- /SECTION: backend.residency.frontmatter -->
<!-- SECTION: backend.residency.purpose | status: DERIVED -->
## Purpose

**Responsibility.** The `residency` module decides, and records, whether notes_api infrastructure sits in approved EU regions. The project spec's module map defines its boundary as: region-constrained provisioning, EU-only backup validation, CI residency gate, compliance report, launch gate, drift scan and alert.

| Capability | Operation | Source |
|---|---|---|
| Accept or reject a resource provisioning request by target region | `API-RESIDENCY-892950` | US-001-01, BR-001 |
| Accept or reject a backup destination; a non-EU destination writes no data | `API-RESIDENCY-174124` | US-001-02, BR-001 |
| Pass or fail a build's residency gate | `API-RESIDENCY-685134` | US-001-03, BR-002, NFR-002 |
| Report each infrastructure component with its region, status and an overall compliance measure | `API-RESIDENCY-913995` | US-001-04, NFR-001, NFR-004 |
| Refresh the report and return the launch gate verdict | `API-RESIDENCY-648672` | US-001-05, BR-003, NFR-004 |
| Scan for non-EU resources after launch and alert the DevOps team | `API-RESIDENCY-922826` | US-001-06, BR-004, NFR-003 |

**Not responsible for:**
- **Provisioning, backing up or deploying.** The module returns verdicts. Infrastructure-as-code and the pipeline act on them (BR-001 and BR-002 name those as the enforcement points; `ASM-BE-264`).
- **The human launch sign-off.** BR-003 assigns sign-off to the Engineering Lead. The module supplies the verdict only (`ASM-BE-266`).
- **The approved EU region list.** It is supplied configuration, not decided here (`ASM-BE-262`, `GAP-BE-011`).
- **Residency of the one-time-code emails.** NFR-017 belongs to the `auth` module.
- **Where note data is described to integrators.** That is `contract-docs`, under BR-013.
- **Classifying stakeholder requests.** That is BR-017 (`GAP-BE-009`).
- **The alerting and scheduling tools.** No input names them (`GAP-BE-275`).
- **Request-path behaviour.** `residency` is not on the request path (`ASM-BE-011`).
<!-- /SECTION: backend.residency.purpose -->
<!-- SECTION: backend.residency.data-model | status: DRAFT-UNVERIFIED -->
## Data model

No input defines tables for this module. `ASM-BE-011` calls residency artefacts infrastructure records. The persistence below is a proposal that rests on `ASM-BE-261` (PostgreSQL 16 per `platform.md`, same database as notes) and on `ASM-BE-267`, `ASM-BE-268` and `ASM-BE-269`. If these records live outside the application database, this section is rewritten.

**Approved region list.** It is configuration, not a table (`ASM-BE-262`). Evaluation compares a region code to the list by exact match. Each observation stores the outcome, so a later list change does not rewrite history.

### `residency_resource` (inventory)

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `resource_id` | uuid | NOT NULL | none; assigned by the application | PK |
| `external_ref` | text | NOT NULL | none | UNIQUE; identifier of the component in its hosting platform |
| `resource_type` | text | NOT NULL | none | component class (database, compute, backup storage per BR-001) |
| `region_code` | text | NULL | NULL | NULL means missing or unreadable metadata (AC-001-03-03) |
| `verification_status` | text | NOT NULL | `'UNVERIFIED'` | CHECK in (`EU_VERIFIED`, `NON_EU`, `UNVERIFIED`) |
| `first_seen_at` | timestamptz | NOT NULL | `now()` | |
| `last_scanned_at` | timestamptz | NULL | NULL | |
| `updated_at` | timestamptz | NOT NULL | `now()` | |

Indexes:
- The UNIQUE index on `external_ref` serves upsert by identity during a scan.
- An index on `verification_status` serves the non-compliant and unverified counts used by the report, launch gate and drift query.

### `residency_scan_run`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `scan_run_id` | uuid | NOT NULL | none | PK |
| `trigger_kind` | text | NOT NULL | none | CHECK in (`SCHEDULED`, `ON_DEMAND`, `LAUNCH_GATE`) |
| `status` | text | NOT NULL | `'RUNNING'` | CHECK in (`RUNNING`, `COMPLETED_CLEAN`, `COMPLETED_DRIFT`, `FAILED`) |
| `started_at` | timestamptz | NOT NULL | `now()` | |
| `finished_at` | timestamptz | NULL | NULL | CHECK: NOT NULL when `status` <> `RUNNING` |
| `resources_scanned` | integer | NULL | NULL | CHECK >= 0 |
| `non_eu_count` | integer | NULL | NULL | CHECK >= 0 |
| `unverified_count` | integer | NULL | NULL | CHECK >= 0 |
| `failure_reason` | text | NULL | NULL | set only when `FAILED` |

Index: `(started_at DESC)` serves "latest completed scan".

### `residency_compliance_report` (snapshot)

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `report_id` | uuid | NOT NULL | none | PK |
| `scan_run_id` | uuid | NOT NULL | none | FK → `residency_scan_run`, ON DELETE RESTRICT |
| `generated_at` | timestamptz | NOT NULL | `now()` | |
| `total_resources` | integer | NOT NULL | none | CHECK >= 0 |
| `eu_verified_count` | integer | NOT NULL | none | CHECK between 0 and `total_resources` |
| `non_eu_count` | integer | NOT NULL | none | CHECK >= 0 |
| `unverified_count` | integer | NOT NULL | none | CHECK >= 0 |

`total_resources = eu_verified_count + non_eu_count + unverified_count`. Compliance percent is derived as `eu_verified_count ÷ total_resources` and is not stored, so no rounding rule is invented (`ASM-BE-265`, `GAP-BE-279`). Index: `(generated_at DESC)` serves "current report".

### `residency_compliance_report_line`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `report_id` | uuid | NOT NULL | none | FK → `residency_compliance_report`, ON DELETE RESTRICT |
| `resource_id` | uuid | NOT NULL | none | FK → `residency_resource`, ON DELETE RESTRICT |
| `resource_type` | text | NOT NULL | none | |
| `region_code` | text | NULL | NULL | |
| `outcome` | text | NOT NULL | none | CHECK in (`EU_VERIFIED`, `NON_EU`, `UNVERIFIED`) |

PK is `(report_id, resource_id)`. This guarantees one line per inventoried resource, so none is silently omitted (AC-001-04-02). The PK serves report rendering.

### `residency_gate_result` (CI gate and launch gate only)

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `gate_result_id` | uuid | NOT NULL | none | PK |
| `gate_kind` | text | NOT NULL | none | CHECK in (`CI_GATE`, `LAUNCH_GATE`) |
| `verdict` | text | NOT NULL | none | CHECK in (`PASSED`, `FAILED`) |
| `subject_ref` | text | NULL | NULL | build or deployment reference for `CI_GATE`; its form is unstated (`GAP-BE-276`) |
| `report_id` | uuid | NULL | NULL | FK → `residency_compliance_report`, ON DELETE RESTRICT; set for `LAUNCH_GATE` |
| `evaluated_at` | timestamptz | NOT NULL | `now()` | |

Index: `(gate_kind, evaluated_at DESC)` serves the latest gate verdict.

### `residency_gate_result_item`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `gate_result_id` | uuid | NOT NULL | none | FK → `residency_gate_result`, ON DELETE RESTRICT |
| `resource_ref` | text | NOT NULL | none | resource named in the evaluated deployment |
| `region_code` | text | NULL | NULL | |
| `outcome` | text | NOT NULL | none | CHECK in (`EU_VERIFIED`, `NON_EU`, `UNVERIFIED`) |

PK is `(gate_result_id, resource_ref)`. It serves "which resource blocked this build".

### `residency_drift_alert`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `alert_id` | uuid | NOT NULL | none | PK |
| `scan_run_id` | uuid | NOT NULL | none | FK → `residency_scan_run`, ON DELETE RESTRICT |
| `resource_id` | uuid | NOT NULL | none | FK → `residency_resource`, ON DELETE RESTRICT |
| `detected_region_code` | text | NULL | NULL | |
| `detected_at` | timestamptz | NOT NULL | `now()` | |
| `delivery_status` | text | NOT NULL | `'PENDING'` | CHECK in (`PENDING`, `SENT`, `SEND_FAILED`) |
| `sent_at` | timestamptz | NULL | NULL | |

Constraints and indexes:
- UNIQUE `(scan_run_id, resource_id)` gives one alert per resource per scan (`ASM-BE-270`).
- A partial index on `delivery_status` where `delivery_status <> 'SENT'` serves the delivery retry sweep.
- An index on `(resource_id, detected_at DESC)` serves per-resource alert history.

**ON DELETE.** Every foreign key is RESTRICT. Residency records are audit evidence and are never deleted by this module (`ASM-BE-267`). Retention is unstated (`GAP-BE-278`).

**PII and secrets.** No PII column exists. No credential column exists (`INV-BE-257`). At-rest treatment is unstated; see `GAP-BE-006` in the project spec.

**Timestamps.** All timestamps are `timestamptz` and are stored in UTC (`ASM-BE-268`).
<!-- /SECTION: backend.residency.data-model -->
<!-- SECTION: backend.residency.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

Four entities carry a status. The state sets are derived from the stories; unlisted transitions are illegal.

### Resource `verification_status`

| From | To | Trigger | Guard |
|---|---|---|---|
| (new) | `UNVERIFIED` | resource first seen | initial state |
| any | `EU_VERIFIED` | scan observation | `region_code` is present and in the approved list |
| any | `NON_EU` | scan observation | `region_code` is present and not in the approved list |
| any | `UNVERIFIED` | scan observation | region missing or unreadable, or resource type not recognised (AC-001-03-03, AC-001-04-02, `ASM-BE-263`) |

- The status is a function of the latest observation, so every transition among the three states is legal.
- No state is terminal, because resources are rescanned for as long as they exist (BR-004).
- `EU_VERIFIED` → `NON_EU` is a drift (AC-001-06-02).
- A value outside the set is rejected by the CHECK constraint. It is an internal error, because no client can request a transition.

### Scan run `status`

| From | To | Trigger | Guard |
|---|---|---|---|
| (new) | `RUNNING` | scan begins | initial state |
| `RUNNING` | `COMPLETED_CLEAN` | scan finishes | `non_eu_count` = 0 (AC-001-06-01) |
| `RUNNING` | `COMPLETED_DRIFT` | scan finishes | `non_eu_count` > 0 (AC-001-06-02) |
| `RUNNING` | `FAILED` | scan cannot complete | `failure_reason` set |

- Terminal states: `COMPLETED_CLEAN`, `COMPLETED_DRIFT`, `FAILED`.
- A terminal run never transitions again (`ASM-BE-267`).
- Unverified resources are counted in `unverified_count` and do not by themselves select `COMPLETED_DRIFT` (`ASM-BE-273`).

### Drift alert `delivery_status`

| From | To | Trigger | Guard |
|---|---|---|---|
| (new) | `PENDING` | non-EU resource detected | one per scan and resource |
| `PENDING` | `SENT` | handed to the alerting channel | |
| `PENDING` | `SEND_FAILED` | hand-off fails | |
| `SEND_FAILED` | `PENDING` | retry sweep | retry limit unstated (`GAP-BE-275`) |

Terminal state: `SENT`. `SEND_FAILED` is not terminal, so a failed alert stays visible and is never silently dropped (`INV-BE-256`).

### Gate result `verdict`

A gate result is written once with `PASSED` or `FAILED` and never changes. Both are terminal.

**Illegal transitions.** A transition not listed above is a defect, not a client error. It is refused by the constraint or by the code. It surfaces as an internal error (the 500 documented for these operations), with machine-readable code unassigned (`GAP-BE-273`).
<!-- /SECTION: backend.residency.state-machine -->
<!-- SECTION: backend.residency.requirements | status: BLOCKED -->
## Requirements

**BLOCKED.** the run did not produce this section for module `residency`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: backend.residency.requirements -->
<!-- SECTION: backend.residency.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-RESIDENCY-174124` | POST | `/residency/backup-validations` | `residency` | 200 | 400, 500 | Validate that a backup destination is EU-only; reject non-EU destinations without writing data (US-001-02). |
| `API-RESIDENCY-685134` | POST | `/residency/ci-gate-evaluations` | `residency` | 200 | 400, 500 | Evaluate the CI residency gate for a build, failing it when any resource is non-EU (US-001-03). |
| `API-RESIDENCY-913995` | GET | `/residency/compliance-report` | `residency` | 200 | 500 | Return the current EU data-residency compliance report (US-001-04). |
| `API-RESIDENCY-922826` | POST | `/residency/drift-scans` | `residency` | 200 | 500 | Run a drift scan for non-EU resources and alert the DevOps team on detection (US-001-06). |
| `API-RESIDENCY-648672` | POST | `/residency/launch-gate-checks` | `residency` | 200 | 500 | Run the launch gate check, refreshing the compliance report and blocking launch on non-EU resources (US-001-05). |
| `API-RESIDENCY-892950` | POST | `/residency/provisioning-checks` | `residency` | 200 | 400, 500 | Validate that a resource provisioning request targets an approved EU region (US-001-01). |

### `API-RESIDENCY-174124` — POST `/residency/backup-validations`

Validate that a backup destination is EU-only; reject non-EU destinations without writing data (US-001-02).

**Request:** 
- `destination_region` (string, required): Destination region or location of the backup. A non-EU destination fails validation within 30 seconds and writes no data (AC-001-02-01, AC-001-02-02).
- `backup_id` (string): Identifier of the backup configuration or job being validated.
**Response:** 
- `valid` (boolean, required): True when the backup destination is EU-only.
- `destination_region` (string, required): The destination region that was evaluated.

### `API-RESIDENCY-685134` — POST `/residency/ci-gate-evaluations`

Evaluate the CI residency gate for a build, failing it when any resource is non-EU (US-001-03).

**Request:** 
- `resources` (array, required): Deployment targets (resource identifiers with regions) in the build under evaluation, so non-EU deploys can be blocked from production (BR-002, NFR-002).
- `build_id` (string): Identifier of the CI build being gated.
**Response:** 
- `result` (string, required): Gate outcome: pass or fail.
- `violations` (array, required): Resources found outside approved EU regions; empty on pass.

### `API-RESIDENCY-913995` — GET `/residency/compliance-report`

Return the current EU data-residency compliance report (US-001-04).

**Request:** none
**Response:** 
- `components` (array, required): Per-component residency status of the infrastructure inventory (NFR-004).
- `eu_verified_percent` (number, required): Share of infrastructure components EU-verified (NFR-001).
- `generated_at` (string, required): Timestamp at which the report was last refreshed.

### `API-RESIDENCY-922826` — POST `/residency/drift-scans`

Run a drift scan for non-EU resources and alert the DevOps team on detection (US-001-06).

**Request:** none
**Response:** 
- `result` (string, required): Scan outcome: clean or drift-detected.
- `drifted_resources` (array, required): Resources found in a non-EU region, with resource details; empty when clean (BR-004, AC-001-06-02).
- `alert_sent` (boolean, required): True when an alert was sent to the DevOps team (NFR-003).
- `scanned_at` (string, required): Timestamp of the scan.

### `API-RESIDENCY-648672` — POST `/residency/launch-gate-checks`

Run the launch gate check, refreshing the compliance report and blocking launch on non-EU resources (US-001-05).

**Request:** 
- `release_id` (string): Identifier of the release or launch being checked.
**Response:** 
- `result` (string, required): Launch gate outcome: pass or fail.
- `violations` (array, required): Non-compliant resources blocking launch; empty on pass.
- `report_generated_at` (string, required): Timestamp of the compliance report refreshed by this check (NFR-004).

### `API-RESIDENCY-892950` — POST `/residency/provisioning-checks`

Validate that a resource provisioning request targets an approved EU region (US-001-01).

**Request:** 
- `region` (string, required): Target region for the resource being provisioned. Must be an approved EU region (BR-001, US-001-01). The approved region list is open (GAP-BE-011).
- `resource_type` (string, required): Kind of infrastructure resource being provisioned (database, compute, backup storage).
**Response:** 
- `allowed` (boolean, required): True when the region is an approved EU region and provisioning may proceed.
- `region` (string, required): The region that was evaluated.
- `resource_type` (string, required): The resource type that was evaluated.
<!-- /SECTION: backend.residency.api-contract -->
<!-- SECTION: backend.residency.invariants | status: DRAFT-UNVERIFIED -->
## Invariants

| Id | Must be true after every operation | Source |
|---|---|---|
| `INV-BE-251` | A rejected provisioning check or backup validation leaves no created resource and no written backup data attributable to this module. | BR-001, AC-001-01-02, AC-001-02-02 |
| `INV-BE-252` | A resource whose region is missing, unreadable or not in the approved list, or whose type is unrecognised, is never `EU_VERIFIED`. | AC-001-03-03, AC-001-04-02, `ASM-BE-263` |
| `INV-BE-253` | Every inventoried resource has exactly one line in each report. `total_resources` equals `eu_verified_count + non_eu_count + unverified_count`. | AC-001-04-01, AC-001-04-02 |
| `INV-BE-254` | A launch gate result is `PASSED` only if `eu_verified_count = total_resources` on the report refreshed in that same check. Empty-inventory handling follows `Q-BE-261`. | BR-003, US-001-05, NFR-004 |
| `INV-BE-255` | A CI gate result is `PASSED` only if every evaluated resource is `EU_VERIFIED`. No input passes by default. | BR-002, AC-001-03-03 |
| `INV-BE-256` | Every non-EU resource detected by a scan has exactly one alert row for that scan, and an alert whose delivery failed remains retrievable. | BR-004, AC-001-06-02 |
| `INV-BE-257` | No credential, key, token or connection string supplied in a request is persisted or logged. Only region and location identifiers are kept. | core §12, `ASM-BE-271` |
| `INV-BE-258` | A terminal scan run, gate result, report and report line is never modified. | `ASM-BE-267`, NFR-004 |
<!-- /SECTION: backend.residency.invariants -->
<!-- SECTION: backend.residency.authorization | status: DRAFT-UNVERIFIED -->
## Authorization

**Enforcement is blocked.** `backend.auth-model` is `BLOCKED` on `Q-BE-001`, `Q-BE-002` and `Q-BE-003`. The session model, and whether and how these operations are authenticated, are undecided. This section therefore asserts no enforceable role, permission or status code. The table records only the intended callers the stories name.

| Operation | Intended caller (persona in the story) | Source |
|---|---|---|
| `API-RESIDENCY-892950` | Platform/DevOps Engineer, and the provisioning tooling acting for them | US-001-01 |
| `API-RESIDENCY-174124` | Platform/DevOps Engineer, via the backup pipeline | US-001-02 |
| `API-RESIDENCY-685134` | Release/CI Pipeline Owner, via the deployment pipeline | US-001-03 |
| `API-RESIDENCY-913995` | Compliance/Security Auditor | US-001-04 |
| `API-RESIDENCY-648672` | Engineering Lead | US-001-05, BR-003 |
| `API-RESIDENCY-922826` | Platform/DevOps Engineer, and the scheduler | US-001-06 |

**Ownership predicate.** None. These records describe shared infrastructure, not data owned by a user (`ASM-BE-261`, `ASM-BE-011`).

**Machine callers.** The pipeline and the scheduler are not human users, and no input says how they authenticate (`GAP-BE-274`).

**Not decided here:** role names, a permission matrix, 401 and 403 behaviour for these operations.
<!-- /SECTION: backend.residency.authorization -->
<!-- SECTION: backend.residency.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

| Job | What it does | Trigger | Idempotency and retries |
|---|---|---|---|
| Scheduled drift scan | Runs the logic of `API-RESIDENCY-922826` with `trigger_kind = SCHEDULED`. It rescans the inventory, updates resource statuses, writes a scan run and one alert per non-EU resource (US-001-06, BR-004). | Scheduler. Cadence and scheduler are unstated (`GAP-BE-011`, `GAP-BE-275`). | No idempotency key. Each run is a new `residency_scan_run`. A duplicate alert within one run is blocked by UNIQUE `(scan_run_id, resource_id)`. |
| Alert delivery | The first delivery attempt happens inside the scan, so NFR-003's 15 minutes is not spent waiting for a sweep (`ASM-BE-270`). | Scan completion | Failures become `SEND_FAILED`. |
| Alert retry sweep | Re-attempts alerts that are `PENDING` or `SEND_FAILED`. | Periodic. Interval and retry limit are unstated (`GAP-BE-275`). | Retrying an alert never creates a second alert row. |
| Launch-gate refresh | Runs a scan with `trigger_kind = LAUNCH_GATE`, builds a new report snapshot, then computes the verdict (FR-012). | Call to `API-RESIDENCY-648672` | Not scheduled. Each call creates a new scan run, report and gate result. |

**Request idempotency.** The validation operations `API-RESIDENCY-892950` and `API-RESIDENCY-174124` have no side effects, so repeating a call is safe. Repeated calls to `API-RESIDENCY-685134`, `API-RESIDENCY-648672` and `API-RESIDENCY-922826` each write a new independent record (`ASM-BE-274`). No input names an idempotency key.

**Retention sweep.** None is specified (`GAP-BE-278`).
<!-- /SECTION: backend.residency.jobs -->
<!-- SECTION: backend.residency.test-plan | status: DERIVED -->
## Test plan

**Unit**
- Region classification gives `EU_VERIFIED`, `NON_EU` or `UNVERIFIED` for: an approved region, a non-approved region, a missing region and an unrecognised type (FR-001, FR-002, FR-007, FR-009).
- Counting preserves `total = eu + non_eu + unverified` (`INV-BE-253`).
- The launch verdict is `PASSED` only at full compliance (FR-010, FR-011, `INV-BE-254`).
- The CI verdict fails on any non-`EU_VERIFIED` item (FR-005 to FR-007, `INV-BE-255`).
- The scan-run state guards select `COMPLETED_CLEAN`, `COMPLETED_DRIFT` or `FAILED` correctly.
- The alert state machine permits only the listed transitions (FR-015).

**Integration** (PostgreSQL 16)
- CHECK, UNIQUE and RESTRICT constraints hold. This includes the `finished_at` rule and one alert per scan and resource.
- A scan updates resource statuses, writes a report with one line per resource, and raises one alert per non-EU resource (FR-008, FR-014).
- A launch check refreshes the report in the same call (FR-012).
- A rejected backup validation writes nothing and responds within 30 seconds (FR-004).
- A non-EU detection produces an alert within 15 minutes of detection (FR-014). The same test records the elapsed time against "immediately" for `CON-BE-003`.
- A failed alert hand-off is recorded and retried (FR-015).
- A request containing a credential-shaped field leaves no credential in any table or log (`INV-BE-257`).

**Contract** (against each operation's published record)
- `API-RESIDENCY-892950`: accepted and rejected outcomes (FR-001, FR-002).
- `API-RESIDENCY-174124`: accepted and rejected outcomes (FR-003, FR-004).
- `API-RESIDENCY-685134`: pass, fail and missing-metadata outcomes (FR-005 to FR-007).
- `API-RESIDENCY-913995`: lists every resource, with unverified resources flagged (FR-008, FR-009).
- `API-RESIDENCY-648672`: passed and failed verdicts (FR-010 to FR-012).
- `API-RESIDENCY-922826`: clean and drift outcomes (FR-013, FR-014).
- Every documented error status in each operation's record has a case. Status and error-code expectations stay open until `GAP-BE-273` is closed.
<!-- /SECTION: backend.residency.test-plan -->
<!-- SECTION: backend.residency.gaps | status: VERIFIED -->
## Gaps

**Contradiction checks run.** Story ↔ design, story ↔ requirement, requirement ↔ requirement and design ↔ design were all checked. The design brief (`design-brief.md`) repeats the stories' acceptance criteria without conflict. The only contradiction found is the already-recorded `CON-BE-003`: AC-001-06-02 says an alert is sent "immediately", while NFR-003 says within 15 minutes. NFR-003 is used as the measurable bound. No new `CON-` is minted. The apparent tension between BR-003 (Engineering Lead sign-off) and AC-001-05-01 ("gate is marked passed") is resolved by `ASM-BE-266`, not by a `CON-`.

**Already defined in the project spec and relied on here:** `GAP-BE-011` (approved EU region list, scan cadence, resource inventory source), `GAP-BE-006` (encryption and retention unstated), `GAP-BE-009`, `ASM-BE-011`, `ASM-BE-006`, `CON-BE-003`, `Q-BE-003`.

### Gaps

| Id | What is missing | Degrades | What was done instead | Closing artefact |
|---|---|---|---|---|
| `GAP-BE-273` | Whether a non-compliant verdict is returned as an error status or as a success response carrying a failed verdict, and the machine-readable error codes for each operation. | requirements, test-plan | Outcomes are stated without transport codes. `RESIDENCY_VIOLATION` is used as the proposed code (`ASM-BE-006`). | An error-model decision and the operation records |
| `GAP-BE-274` | Authentication and permissions for these operations, including pipeline and scheduler callers. | authorization | Intended callers listed only. | Decision on `Q-BE-003` plus a role list |
| `GAP-BE-275` | The alerting channel for "the DevOps team", alert content format, retry interval and limit, the scheduler. | jobs, state-machine | A hand-off to an unnamed channel is assumed (`ASM-BE-270`). | An operations input naming channel, retry policy and scheduler |
| `GAP-BE-276` | Request payload content: how a deployment's target resources, a backup destination and a provisioning request are described, and the form of the build reference. | data-model, requirements | Region and location identifiers are the only fields relied on. | The operation records' field lists |
| `GAP-BE-277` | The list of resource types the scanner recognises, and how a region is read from each. | requirements, data-model | Anything not recognised is `UNVERIFIED` (`ASM-BE-263`). | A scanner scope input |
| `GAP-BE-278` | Retention and at-rest treatment of residency records. | data-model, jobs | Records are kept and never deleted by this module. | A data-lifecycle statement |
| `GAP-BE-279` | Report precision and export form for audit sign-off, including percent rounding. | requirements | Counts are exposed and percent is derived without rounding (`ASM-BE-265`). | An audit-report requirement |
| `GAP-BE-280` | What happens to the inventory row of a resource that is decommissioned and no longer discoverable. | data-model, state-machine | The row stays with its last status. | An inventory lifecycle decision |
| `GAP-BE-281` | The project spec's rendered `backend.api-surface` carries records for the provisioning check and the CI gate under different ids and paths from those supplied to this module. | api-contract | Only the ids supplied for this module are used. | A re-render of the project spec's `api-surface` from the current operation records |

### Assumptions

| Id | Assumption | Confirmation |
|---|---|---|
| `ASM-BE-261` | Residency records live in the notes PostgreSQL 16 database (`platform.md`). Names, types and keys in `data-model` are design proposals. | Platform or architecture statement |
| `ASM-BE-262` | The approved EU region list is configuration supplied to the module, matched by exact region code. Its contents are `GAP-BE-011`. | The list |
| `ASM-BE-263` | A missing, unreadable or unrecognised region, or an unrecognised resource type, is never compliant in any operation (extends AC-001-03-03 and AC-001-04-02). | Product owner |
| `ASM-BE-264` | The module returns verdicts and records. IaC and the pipeline perform provisioning, backups and deploy blocking. | Platform statement |
| `ASM-BE-265` | Compliance % is `EU_VERIFIED ÷ all inventoried resources`, with `UNVERIFIED` resources in the denominator. | Product owner |
| `ASM-BE-266` | The launch-gate operation yields a verdict, and the Engineering Lead's sign-off (BR-003) happens outside this module. "Marked passed" in AC-001-05-01 means the verdict is recorded. | Engineering Lead |
| `ASM-BE-267` | Terminal scan runs, gate results, reports and report lines are append-only, as audit evidence for NFR-004. | Compliance owner |
| `ASM-BE-268` | Timestamps are `timestamptz`, stored in UTC. | Platform statement |
| `ASM-BE-269` | Surrogate keys are uuids assigned by the application. | Platform statement |
| `ASM-BE-270` | One alert per scan and resource. The first delivery is attempted inside the scan, and `SENT` means handed to the alerting channel. | Operations input |
| `ASM-BE-271` | Backup destinations are validated on the destination's region and location only, and credentials are never accepted into persistence. | Security decision |
| `ASM-BE-272` | The 30 seconds in AC-001-02-02 is the ceiling for this module's rejection response. | Product owner |
| `ASM-BE-273` | Unverified resources raise no drift alert. They are counted in `unverified_count` and make the launch gate fail. | Product owner |
| `ASM-BE-274` | Repeated calls to the gate and scan operations are independent and each creates its own records. | API owner |

### Questions

| Id | Class | Question | Default if unanswered |
|---|---|---|---|
| `Q-BE-261` | degradable | With zero resources in the inventory, should the launch gate pass? | It fails, because nothing is verified and BR-003 requires a report showing 100% EU-verified infrastructure. Marked `DRAFT-UNVERIFIED`. |
| `Q-BE-262` | degradable | If the same resource stays non-EU across successive scans, should each scan raise a new alert? | Yes, one per scan (`ASM-BE-270`). |
| `Q-BE-263` | degradable | Should an `UNVERIFIED` resource raise a drift alert? | No (`ASM-BE-273`). |
<!-- /SECTION: backend.residency.gaps -->
