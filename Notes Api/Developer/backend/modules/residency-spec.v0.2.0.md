---
id: agent-developer/notes_api/backend/modules/residency-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/residency-SPEC.md
depends_on: [notes_api:US-001-01, notes_api:US-001-02, notes_api:US-001-03, notes_api:US-001-04, notes_api:US-001-05, notes_api:US-001-06]
commit_kind: fork
confidence: agent
run_id: e1e628bb-b234-5a13-b948-b4689dcc8eab
okf_id: notes_api:backend/modules/residency-SPEC.md
---

<!-- SECTION: backend.residency.frontmatter | status: VERIFIED -->
## Frontmatter

```yaml
id: residency
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [GAP-BE-270, GAP-BE-271]
scope: []
```

`scope:` is empty because no operation records, and therefore no minted `API-` ids, were supplied for this module (`GAP-BE-270`). Operations are referred to below by method and path, with `ASM-BE-267` as the basis.
<!-- /SECTION: backend.residency.frontmatter -->
<!-- SECTION: backend.residency.purpose | status: DERIVED -->
## Purpose

**Responsible for.** The `residency` module keeps and evaluates the evidence that notes_api infrastructure is in approved EU regions. Source: module map in `backend.module-map`; BR-001 to BR-004; NFR-001 to NFR-004.

- Deciding whether a provisioning request names an approved EU region (US-001-01).
- Validating that a backup configuration is EU-only before any backup runs (US-001-02).
- Evaluating a deployment against the CI residency gate, failing closed on missing region metadata (US-001-03).
- Producing the compliance report: every resource, its region, its pass/fail or unverified status, and the percent compliant (US-001-04).
- Deriving the launch gate from that report (US-001-05).
- Running the post-launch drift scan and raising an alert to the DevOps team when a non-EU resource is found (US-001-06).

**Operations.** The prior approved BACKEND-SPEC (attached to `feedback.md`, CR-131) lists these operations by method and path. `ASM-BE-267` explains why they are not given ids here.

- `POST /residency/provisioning-requests`
- `POST /residency/backup-validations`
- `POST /residency/ci-gate-checks`
- `GET /residency/compliance-report`
- `GET /residency/launch-gate`
- `POST /residency/drift-scans`
- `GET /residency/drift-scans/latest`

**Not responsible for.**

- Creating, moving or deleting cloud resources. Region constraints are infrastructure-as-code (BR-001, `backend.migration-plan`), and this module records and decides but does not provision (`ASM-BE-275`).
- Writing backups. This module judges a backup configuration only.
- Note, search, login or OpenAPI behaviour. Those belong to `notes`, `search`, `auth` and `contract-docs`.
- Documenting the data-residency note in the published OpenAPI document (`contract-docs`, US-004-06).
- The Engineering Lead's human sign-off process outside the API (BR-003; `GAP-BE-277`).
- Any real-time or push channel to the DevOps team beyond recording that an alert was raised (`GAP-BE-275`).

**Dependencies.** None on other modules (module map). It is not on the request path of notes or search (`ASM-BE-013` of the project spec).
<!-- /SECTION: backend.residency.purpose -->
<!-- SECTION: backend.residency.data-model | status: DRAFT-UNVERIFIED -->
## Data model

PostgreSQL 16 in an EU region (`platform.md`; BR-001). Tables live in a `residency` schema in the same database (`ASM-BE-268`). Ids are UUIDs and timestamps are `timestamptz` in UTC (`ASM-BE-269`). No column holds PII or a secret.

**`residency.approved_regions`**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `region_code` | text | NOT NULL | | PRIMARY KEY |
| `active` | boolean | NOT NULL | `true` | |
| `added_at` | timestamptz | NOT NULL | `now()` | |

The contents are not supplied (`GAP-BE-272`). The primary key serves exact-match lookups.

**`residency.resources`** (the inventory)

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | PRIMARY KEY |
| `resource_ref` | text | NOT NULL | | UNIQUE |
| `resource_type` | text | NOT NULL | | |
| `region` | text | NULL | | NULL means missing or unreadable |
| `verification_status` | text | NOT NULL | `'unverified'` | CHECK in (`verified`, `non_compliant`, `unverified`) |
| `last_verified_at` | timestamptz | NULL | | |
| `created_at` | timestamptz | NOT NULL | `now()` | |

Index on `verification_status` serves the percent-compliant count.

**`residency.provisioning_requests`**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | PRIMARY KEY |
| `resource_type` | text | NOT NULL | | |
| `requested_region` | text | NOT NULL | | |
| `decision` | text | NOT NULL | | CHECK in (`accepted`, `rejected`) |
| `rejection_reason` | text | NULL | | |
| `resource_id` | uuid | NULL | | FK `resources(id)` ON DELETE RESTRICT |
| `decided_at` | timestamptz | NOT NULL | `now()` | |

`CHECK (decision <> 'rejected' OR resource_id IS NULL)` enforces `INV-BE-252`. Index on `decided_at` serves history queries.

**`residency.backup_validations`**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | PRIMARY KEY |
| `backup_target` | text | NOT NULL | | |
| `primary_region` | text | NOT NULL | | |
| `replica_regions` | text[] | NOT NULL | `'{}'` | |
| `valid` | boolean | NOT NULL | | |
| `violating_regions` | text[] | NOT NULL | `'{}'` | CHECK (`valid` = (cardinality(`violating_regions`) = 0)) |
| `duration_ms` | integer | NOT NULL | | CHECK (`duration_ms` >= 0) |
| `validated_at` | timestamptz | NOT NULL | `now()` | |

Index on `validated_at`.

**`residency.ci_gate_checks`**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | PRIMARY KEY |
| `deployment_id` | text | NOT NULL | | |
| `target_regions` | text[] | NOT NULL | | |
| `verdict` | text | NOT NULL | | CHECK in (`pass`, `fail`) |
| `violations` | jsonb | NOT NULL | `'[]'` | CHECK (`verdict` = 'fail' OR `violations` = '[]') |
| `checked_at` | timestamptz | NOT NULL | `now()` | |

Index on `deployment_id`. It is not unique, because a re-check creates a new row (`ASM-BE-277`).

**`residency.compliance_reports`**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | PRIMARY KEY |
| `generated_at` | timestamptz | NOT NULL | `now()` | |
| `total_resources` | integer | NOT NULL | | CHECK (>= 0) |
| `verified_count` | integer | NOT NULL | | CHECK (between 0 and `total_resources`) |
| `percent_compliant` | numeric(5,2) | NOT NULL | | CHECK (between 0 and 100) |

Scale 2 is proposed (`ASM-BE-271`). Index on `generated_at` serves the "latest report" lookup.

**`residency.compliance_report_items`**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `report_id` | uuid | NOT NULL | | FK `compliance_reports(id)` ON DELETE RESTRICT |
| `resource_id` | uuid | NOT NULL | | FK `resources(id)` ON DELETE RESTRICT |
| `region` | text | NULL | | snapshot at generation |
| `status` | text | NOT NULL | | CHECK in (`verified`, `non_compliant`, `unverified`) |

PRIMARY KEY (`report_id`, `resource_id`); the key's leading column serves per-report listing.

**`residency.launch_gate_evaluations`**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | PRIMARY KEY |
| `report_id` | uuid | NOT NULL | | FK `compliance_reports(id)` ON DELETE RESTRICT |
| `status` | text | NOT NULL | | CHECK in (`passed`, `failed`) |
| `evaluated_at` | timestamptz | NOT NULL | `now()` | |

Index on `evaluated_at` serves the latest-evaluation lookup.

**`residency.scan_runs`**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | PRIMARY KEY |
| `trigger` | text | NOT NULL | | CHECK in (`scheduled`, `manual`) |
| `status` | text | NOT NULL | `'running'` | CHECK in (`running`, `completed`, `failed`) |
| `started_at` | timestamptz | NOT NULL | `now()` | |
| `completed_at` | timestamptz | NULL | | |
| `drift_detected` | boolean | NULL | | NULL until completed |
| `alert_status` | text | NULL | | CHECK in (`not_required`, `sent`, `failed`) |
| `failure_reason` | text | NULL | | |

Index on `started_at` serves the latest-scan lookup.

**`residency.scan_findings`**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | PRIMARY KEY |
| `scan_run_id` | uuid | NOT NULL | | FK `scan_runs(id)` ON DELETE RESTRICT |
| `resource_id` | uuid | NOT NULL | | FK `resources(id)` ON DELETE RESTRICT |
| `observed_region` | text | NULL | | |
| `finding_type` | text | NOT NULL | | CHECK in (`non_eu`, `unreadable`, `unrecognized_type`) |

Index on `scan_run_id`.

All `ON DELETE RESTRICT` choices reflect that evidence rows are never deleted by this module; no input defines deletion (`GAP-BE-276`). Encryption at rest is not stated by any input and no claim is made (`GAP-BE-006` of the project spec).
<!-- /SECTION: backend.residency.data-model -->
<!-- SECTION: backend.residency.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

Entities with a status column or a decided outcome:

**Resource `verification_status`**

| From | To | Trigger | Guard |
|---|---|---|---|
| (new) | `unverified` | Inventory record created, or resource type not recognised by the scanner | none (AC-001-04-02) |
| `unverified`, `non_compliant`, `verified` | `verified` | Verification reads a region | region is in `approved_regions` with `active = true` |
| `unverified`, `verified` | `non_compliant` | Verification reads a region | region is not an active approved region |
| `verified`, `non_compliant` | `unverified` | Verification cannot read the region | `region` is NULL |

No state is terminal. Status is re-derived on each verification. An illegal transition cannot occur, because the target is a function of the observed region.

**Provisioning request `decision`**

| From | To | Trigger | Guard |
|---|---|---|---|
| (new) | `accepted` | Request evaluated | requested region is an active approved region (AC-001-01-01) |
| (new) | `rejected` | Request evaluated | requested region is not approved, or the approved list is empty (AC-001-01-02, `ASM-BE-270`) |

Both outcomes are terminal. There is no transition between them.

**Backup validation `valid`.** Decided once at evaluation and terminal. `valid = true` only if the primary and every replica region is an active approved region. Otherwise `false`, within the 30-second deadline (AC-001-02-02, `ASM-BE-273`).

**CI gate check `verdict`.** Decided once and terminal. `pass` only if every target region is read and approved. Any non-EU, missing or unreadable region gives `fail` (AC-001-03-01 to 03).

**Launch gate** (`launch_gate_evaluations.status`, derived per `ASM-BE-272`)

| From | To | Trigger | Guard |
|---|---|---|---|
| (none) | `passed` | Evaluation on a freshly generated report | `percent_compliant` = 100 and no `unverified` item (AC-001-05-01) |
| (none) | `failed` | Evaluation on a freshly generated report | otherwise (AC-001-05-02) |

Each evaluation row is terminal, but the gate is not: the next evaluation can produce a different status. "Blocked until resolved" is met by the next evaluation after the resource is fixed.

**Scan run `status`**

| From | To | Trigger | Guard |
|---|---|---|---|
| `running` | `completed` | All resources read and findings stored | none |
| `running` | `failed` | Scan could not complete | `failure_reason` recorded |

`completed` and `failed` are terminal. On `completed`, `drift_detected` is set, and `alert_status` is set to `not_required` when there are no findings (AC-001-06-01) or to `sent` or `failed` otherwise. A scan failure does not mean "no drift": it is surfaced as failed, never as a pass (`INV-BE-257`).
<!-- /SECTION: backend.residency.state-machine -->
<!-- SECTION: backend.residency.requirements | status: BLOCKED -->
## Requirements

**BLOCKED.** the run did not produce this section for module `residency`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: backend.residency.requirements -->
<!-- SECTION: backend.residency.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

No HTTP operation is defined.
<!-- /SECTION: backend.residency.api-contract -->
<!-- SECTION: backend.residency.invariants | status: DERIVED -->
## Invariants

**INV-BE-251** A resource is `verified` only if its `region` is non-NULL and an active approved region. No other state counts toward `verified_count`. (BR-001, AC-001-04-02.)

**INV-BE-252** A `rejected` provisioning request has `resource_id` NULL; no resource row exists because of it. Enforced by CHECK. (AC-001-01-02.)

**INV-BE-253** A CI gate check has verdict `pass` only if every target region is read and approved. A missing, unreadable or non-EU region gives `fail`. (AC-001-03-01 to 03.)

**INV-BE-254** A backup validation that is not `valid` records `violating_regions` and no artefact reference. This module never causes a backup write. (AC-001-02-02.)

**INV-BE-255** For every report, `percent_compliant` = `verified_count` / `total_resources` × 100, `unverified` resources are in the denominator, and the item count equals `total_resources`. (AC-001-04-01, `ASM-BE-271`.)

**INV-BE-256** A launch gate evaluation is `passed` only if its report has `percent_compliant` = 100 and no `unverified` item. The report is generated at evaluation time. (BR-003, NFR-004, `ASM-BE-272`.)

**INV-BE-257** A scan that did not reach `completed` never reports `drift_detected = false`. A `failed` scan is surfaced, not treated as a pass. (BR-004.)

**INV-BE-258** A `completed` scan with at least one finding has `alert_status` of `sent` or `failed`, never `not_required`. (AC-001-06-02.)

**INV-BE-259** Decision and evidence rows (`provisioning_requests`, `backup_validations`, `ci_gate_checks`, reports, evaluations, completed scans and findings) are append-only and never updated after completion. Resource rows are updated only through verification.

**INV-BE-260** With no active approved region, no outcome is accept, valid, `pass` or `verified` (BE-RESIDENCY-FR-014).
<!-- /SECTION: backend.residency.invariants -->
<!-- SECTION: backend.residency.authorization | status: BLOCKED -->
## Authorization

**BLOCKED.** The role-by-operation matrix and any ownership predicate cannot be written without inventing an authorisation rule. `backend.auth-model` is itself `BLOCKED`, and no input says how callers of these operations are authenticated or which role may invoke which operation (`GAP-BE-271`).

The stories name these intended users, which is not a permission matrix:

- Platform/DevOps Engineer: provisioning, backups, drift alerts (US-001-01, 02, 06).
- Release/CI Pipeline Owner: the CI check (US-001-03).
- Compliance/Security Auditor: the compliance report (US-001-04).
- Engineering Lead: launch gate sign-off (US-001-05, BR-003).

The data is not user-scoped and carries no owner column, so no ownership predicate applies.

**What would unblock it.** A decision on how the pipeline and the named humans authenticate, and which roles may invoke each of the seven operations. Close `GAP-BE-271` and the project-level auth decisions.
<!-- /SECTION: backend.residency.authorization -->
<!-- SECTION: backend.residency.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

| Job | Trigger | Behaviour | Idempotency key | Retry |
|---|---|---|---|---|
| Drift scan | Schedule, plus manual trigger | Reads every inventory resource, records findings, sets `drift_detected`, raises an alert when findings exist (`ASM-BE-274`) | none; each run is a new `scan_runs` row | A `failed` run is not retried silently; the next scheduled run proceeds |
| Report refresh | Each launch gate evaluation and each report request | Generates a fresh `compliance_reports` row so the report is accurate at each launch gate check (NFR-004) | none; each generation is a new row | none |
| CI gate check | Each deployment | Evaluates `target_regions` | `deployment_id`; a repeat creates a new row (`ASM-BE-277`) | The pipeline may re-run; the verdict is recomputed |
| Backup config validation | Before each backup run | Evaluates the configuration within 30 seconds | none | A timeout is `valid = false` (`ASM-BE-273`) |

**Scan cadence** is not stated: "periodic" only (AC-001-06-01). It is a configuration value with a proposed default of 15 minutes (`ASM-BE-278`, `Q-BE-255`). The alert deadline is 15 minutes from detection (NFR-003), and detection happens when a scan observes the resource.

**Not specified by any input:** alert delivery retries (`GAP-BE-278`), and retention sweeps for residency records (`GAP-BE-276`).
<!-- /SECTION: backend.residency.jobs -->
<!-- SECTION: backend.residency.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

**Unit**
- Region comparison: approved, not approved, NULL, empty list (BE-RESIDENCY-FR-014).
- Verification status derivation for a resource.
- Percent computation with `unverified` in the denominator and exact two-decimal rounding.
- Launch gate derivation at 100%, below 100%, and with an `unverified` item.
- Backup deadline: a validation exceeding 30 seconds returns invalid.

**Integration** (PostgreSQL 16)
- Constraint tests: CHECK on `rejected` with `resource_id`, `valid` against `violating_regions`, `verdict` against `violations`.
- Provisioning reject leaves no `resources` row.
- Drift scan with a seeded non-EU resource stores a finding and sets `alert_status`.
- Alert delivery failure sets `alert_status = failed` and keeps the finding.
- Report snapshot has one item per resource at generation time.
- Append-only: completed decision rows cannot be updated.

**Contract** (method and path until ids are minted)
- Each of the seven operations returns only its documented success and error statuses, taken from the published contract.
- The CI gate check fails for each of: a non-EU region, a missing region, an unreadable region.
- No contract test relies on an approved region list that is not supplied (`GAP-BE-272`).

Named tests are in `BE-RESIDENCY-FR-001` to `BE-RESIDENCY-FR-014`.
<!-- /SECTION: backend.residency.test-plan -->
<!-- SECTION: backend.residency.gaps | status: VERIFIED -->
## Gaps

**Gaps**

| ID | Gap | Degrades | Closing artefact |
|---|---|---|---|
| `GAP-BE-270` | No operation records were supplied to this module, so no `API-` ids exist. Operations are named by method and path from the prior approved spec. | `scope:`, `api-contract`, API lines in requirements | Operation records from DevAgent |
| `GAP-BE-271` | Which roles may invoke which residency operation, and how pipelines authenticate, is not stated. | `authorization` (BLOCKED) | Auth-model decision and a role matrix for these operations |
| `GAP-BE-272` | The approved EU region list is not supplied. | `data-model`, FR-001 to FR-007, FR-014 | A requirement or platform input listing the approved regions |
| `GAP-BE-273` | Which resource types make up the inventory, and how a region is read for each, is not stated. | `data-model` (`resources`), FR-008, FR-009 | A platform input naming resource types and region sources |
| `GAP-BE-274` | The "periodic" scan cadence is not stated. | `jobs` | A requirement stating the interval |
| `GAP-BE-275` | The alert channel and recipient mechanism for the DevOps team are not stated. | FR-013 | An alerting or on-call requirement |
| `GAP-BE-276` | Retention and deletion of residency evidence (reports, scans, findings, validations) are not stated. See also `GAP-BE-006`. | `data-model` | A data-retention requirement |
| `GAP-BE-277` | Whether the Engineering Lead's sign-off (BR-003) is a recorded API action or an outside process is not stated. This module derives the gate only. | FR-010, FR-011 | A decision on the sign-off mechanism |
| `GAP-BE-278` | Alert delivery retry and escalation behaviour are not stated. | FR-013, `jobs` | An alerting requirement |

**Contradiction**

- `CON-BE-254` (non-critical). AC-001-06-02 says an alert is sent "immediately". NFR-003 says the alert is fired "within 15 minutes of drift detection". Reading used: send on scan completion, with 15 minutes as the upper bound (`ASM-BE-276`). Choosing "immediately" as a hard requirement would add a latency target that no other input supports.

**Questions** (all degradable; unattended defaults apply)

- `Q-BE-254` (degradable). What are the approved EU regions? Default: none supplied, so every check fails closed (`ASM-BE-270`).
- `Q-BE-255` (degradable). What is the drift scan interval? Default: 15 minutes (`ASM-BE-278`).
- `Q-BE-256` (degradable). How is the DevOps team alerted? Default: the alert is recorded in `alert_status` and logged as an event; transport is left to infrastructure.

**Assumptions**

| ID | Assumption |
|---|---|
| `ASM-BE-267` | The seven operations are those listed in the prior approved BACKEND-SPEC attached to `feedback.md` (CR-131). Their ids are not reused because this run did not mint them. |
| `ASM-BE-268` | Residency tables live in a `residency` schema of the same PostgreSQL 16 database, in an EU region. `platform.md` names PostgreSQL 16 only. |
| `ASM-BE-269` | Ids are UUIDs and timestamps are `timestamptz` in UTC. No input fixes either. |
| `ASM-BE-270` | A region is EU iff it exactly matches an active row of `approved_regions`. An empty list fails closed. |
| `ASM-BE-271` | `unverified` resources count in the denominator and not as compliant. `percent_compliant` is `numeric(5,2)`. AC-001-04-02 only requires them to be flagged. |
| `ASM-BE-272` | The launch gate is derived from a report generated at evaluation time, because NFR-004 requires the report to be refreshed at each gate check. |
| `ASM-BE-273` | The 30-second backup deadline runs from receipt of the validation request to the failure result. A timeout is treated as invalid, not as a pass. |
| `ASM-BE-274` | Each completed scan with one or more findings raises exactly one alert, with no deduplication across scans. |
| `ASM-BE-275` | This module records provisioning decisions only. The real "no resource is created" is enforced by infrastructure-as-code region constraints (BR-001). |
| `ASM-BE-276` | "Immediately" (AC-001-06-02) means at scan completion. NFR-003's 15 minutes bounds the time from detection to the alert. |
| `ASM-BE-277` | A repeated CI check on the same deployment identifier creates a new row. |
| `ASM-BE-278` | The proposed default scan interval is 15 minutes. It is unsourced and is the value `Q-BE-255` asks about. |
<!-- /SECTION: backend.residency.gaps -->
