---
id: agent-developer/notes_api/backend/modules/residency-compliance-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/residency-compliance-SPEC.md
depends_on: [notes_api:US-001-01, notes_api:US-001-02, notes_api:US-001-03, notes_api:US-001-04, notes_api:US-001-05, notes_api:US-001-06]
commit_kind: append
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: notes_api:backend/modules/residency-compliance-SPEC.md
---

<!-- SECTION: backend.residency-compliance.frontmatter | status: DERIVED -->
```yaml
id: residency-compliance
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [CON-BE-151, Q-BE-151, Q-BE-152]
scope: [API-RESIDENCYCOMPLIANCE-405210, API-RESIDENCYCOMPLIANCE-466056, API-RESIDENCYCOMPLIANCE-381444, API-RESIDENCYCOMPLIANCE-894798, API-RESIDENCYCOMPLIANCE-395094, API-RESIDENCYCOMPLIANCE-047792]
```
<!-- /SECTION: backend.residency-compliance.frontmatter -->
<!-- SECTION: backend.residency-compliance.purpose | status: DRAFT-UNVERIFIED -->
## 1. Purpose

**Responsibility.** The `residency-compliance` module is the service-side evidence and enforcement point for EU data residency of `notes_api` infrastructure (database, compute, backup storage). [requirements.md BR-001; stories.md EP-001; backend.module-map] It owns:

1. **Region validation.** Checking a resource's declared region against the approved EU region list, for provisioning requests, backup destinations and the CI/CD residency gate (`API-RESIDENCYCOMPLIANCE-405210`). [US-001-01, US-001-02, US-001-03; BR-001, BR-002]
2. **Scanning.** Recording the region and compliance status of each provisioned resource (`API-RESIDENCYCOMPLIANCE-466056`). [US-001-04, US-001-06]
3. **Inventory.** Listing resources with region, status and last scan time (`API-RESIDENCYCOMPLIANCE-381444`). A resource type the scanner does not recognise is shown as `unverified`, never omitted. [AC-001-04-02]
4. **Compliance report.** Per-resource region and pass/fail status plus an overall % compliant metric (`API-RESIDENCYCOMPLIANCE-894798`). [AC-001-04-01; NFR-004]
5. **Launch gate evaluation.** A read-only computation from the current report (`API-RESIDENCYCOMPLIANCE-395094`). [US-001-05; BR-003]
6. **Drift alerts.** Raising and listing alerts when a running resource is found outside the approved regions (`API-RESIDENCYCOMPLIANCE-047792`). [US-001-06; BR-004; NFR-003]
7. **Data ownership.** INFRA_RESOURCE and DRIFT_ALERT, and the scan-run record this module needs to operate (`ASM-BE-557`). [backend.domain-model]

**Not responsible for.**

- **Provisioning and backup execution.** Creating the database, compute or backups is done by infrastructure-as-code outside this service. [BR-001 Enforcement: "Infrastructure-as-code region constraints"] This module reports a validation result. It creates and rejects no resource itself.
- **Pipeline wiring.** Failing the build or blocking a deploy is done by the pipeline acting on the validation result. Pipeline failure is not an HTTP response. [backend.error-model; AC-001-03-02]
- **Release authorisation.** Whether a passed gate authorises release or Engineering Lead sign-off is required is `CON-BE-151` and `Q-BE-152`. No sign-off operation is defined here.
- **Alert delivery channel and recipients.** Open in `GAP-BE-308`. This module records the alert and exposes it; delivery is not specified.
- **Note data, login data, the global error envelope, the OpenAPI document and its Scope section.** These belong to `notes`, `auth`, `backend.error-model` and `api-docs`. `residency-compliance` has no outbound module dependencies. [backend.module-map]
- **Backup scheduling, frequency and retention.** Not stated by any input (`GAP-BE-307`).
- **Email transport region.** `Q-BE-005` and `CON-BE-006`. It is a residency question for the login flow, owned by `auth`.

**Operations owned.** `API-RESIDENCYCOMPLIANCE-405210`, `API-RESIDENCYCOMPLIANCE-466056`, `API-RESIDENCYCOMPLIANCE-381444`, `API-RESIDENCYCOMPLIANCE-894798`, `API-RESIDENCYCOMPLIANCE-395094`, `API-RESIDENCYCOMPLIANCE-047792`, referenced by id only.

**Identifier note.** This module mints from its reserved blocks: `ASM-BE-551` onward, `CON-BE-451` onward, `GAP-BE-767` onward, `INV-BE-160` onward. It mints no `Q-`. Other ids are cited from the project spec.

**Inbound handoffs.** Three handoffs were delivered to this run (HANDOFF-WEBAPP-163212, HANDOFF-WEBAPP-200601, HANDOFF-WEBAPP-743710). Their content was not supplied (`GAP-BE-602`), so none can be answered here.
<!-- /SECTION: backend.residency-compliance.purpose -->
<!-- SECTION: backend.residency-compliance.data-model | status: DRAFT-UNVERIFIED -->
## 2. Data model

PostgreSQL 16 is the platform [platform.md]. No input supplies column types, nullability or constraints, so everything below is a proposal (`ASM-BE-551`). Entity names follow `backend.domain-model`. Whether these tables live in the `notes_api` database at all is `GAP-BE-305`.

**approved_regions**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `region` | text | NOT NULL | none | primary key; exact-match comparison (`ASM-BE-561`) |
| `added_at` | timestamptz | NOT NULL | none | |

The contents are the approved EU region list. That list is not supplied (`Q-BE-151`, AC-001-01-01). No row is written in this spec.

**infra_resources** (INFRA_RESOURCE)

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `resource_id` | uuid | NOT NULL | none | primary key |
| `external_ref` | text | NOT NULL | none | unique; the resource's identifier in the hosting platform |
| `resource_type` | text | NOT NULL | none | free text, with no enumerated check, because an unrecognised type must still be stored and flagged [AC-001-04-02]; the domain model names database, compute and backup storage |
| `region` | text | NULL | none | null when metadata is missing or unreadable (`ASM-BE-554`) |
| `compliance_status` | text | NOT NULL | `'unverified'` | `CHECK (compliance_status IN ('pass','fail','unverified'))` [backend.domain-model] |
| `last_scanned_at` | timestamptz | NULL | none | null until the first scan |
| `created_at` | timestamptz | NOT NULL | none | |

Table check: `CHECK (compliance_status <> 'pass' OR region IS NOT NULL)`. An unknown or unreadable region can never be recorded as pass (`INV-BE-167`).

Indexes:
- unique btree `(external_ref)` serves upsert of scan results.
- btree `(compliance_status)` serves the pass count behind the report and the gate.

**residency_scans** (new, `ASM-BE-557`)

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `scan_id` | uuid | NOT NULL | none | primary key |
| `trigger` | text | NOT NULL | none | `CHECK (trigger IN ('api','schedule'))` |
| `status` | text | NOT NULL | `'running'` | `CHECK (status IN ('running','completed','failed'))` |
| `started_at` | timestamptz | NOT NULL | none | |
| `finished_at` | timestamptz | NULL | none | `CHECK (finished_at IS NULL OR finished_at >= started_at)` |
| `resources_seen` | integer | NULL | none | `CHECK (resources_seen IS NULL OR resources_seen >= 0)`; set at completion |

Index: btree `(started_at DESC)` serves the latest-scan lookup.

**drift_alerts** (DRIFT_ALERT)

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `alert_id` | uuid | NOT NULL | none | primary key |
| `resource_id` | uuid | NOT NULL | none | foreign key to `infra_resources`, `ON DELETE RESTRICT` (alerts are compliance evidence) |
| `scan_id` | uuid | NOT NULL | none | foreign key to `residency_scans`, `ON DELETE RESTRICT` |
| `detected_region` | text | NOT NULL | none | |
| `detected_at` | timestamptz | NOT NULL | none | when the scan found the resource outside the approved regions |
| `raised_at` | timestamptz | NOT NULL | none | `CHECK (raised_at >= detected_at)` |
| `cleared_at` | timestamptz | NULL | none | set when a later scan finds the resource in an approved region (`ASM-BE-555`) |

Indexes:
- unique partial btree `(resource_id, detected_region) WHERE cleared_at IS NULL` enforces one open alert per resource and region (`ASM-BE-555`).
- btree `(raised_at DESC)` serves the alert listing.

**Time.** Every instant is `timestamptz`.

**PII and secrets.** These tables hold no personal data. Credentials the scanner needs to read the hosting platform are never stored in these tables or in this spec (`GAP-BE-767`).

**Encryption at rest.** No input states it, so none is claimed (`GAP-BE-304`).

**Retention.** Not stated for scan results, alerts or reports (`GAP-BE-305`). None is claimed. Hard deletion of `infra_resources` rows is also unstated (`GAP-BE-774`).
<!-- /SECTION: backend.residency-compliance.data-model -->
<!-- SECTION: backend.residency-compliance.state-machine | status: DRAFT-UNVERIFIED -->
## 3. State machine

### 3.1 Resource compliance status (`infra_resources.compliance_status`)

States: `unverified` (initial), `pass`, `fail`. None is terminal, because every scan re-evaluates every resource. [backend.domain-model]

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| none | `unverified` | resource first inventoried | | row created |
| any | `pass` | scan evaluates the resource | resource type recognised and `region` is in `approved_regions` | `last_scanned_at` set; any open alert for the resource is cleared (`ASM-BE-555`) |
| any | `fail` | scan evaluates the resource | resource type recognised and `region` is present and not in `approved_regions` | `last_scanned_at` set; a drift alert is raised unless one is already open for this resource and region (`ASM-BE-555`) |
| any | `unverified` | scan evaluates the resource | type not recognised, or `region` missing or unreadable (`ASM-BE-554`) | `last_scanned_at` set; `region` kept as read, null if unreadable |

An `unverified` resource is not EU-verified for the report or the gate (`ASM-BE-552`). An unrecognised type is flagged, never silently omitted. [AC-001-04-02] A resource whose region cannot be read is never treated as a pass. [AC-001-03-03; `INV-BE-007`]

### 3.2 Scan run (`residency_scans.status`)

Derived, not specified by any input (`ASM-BE-557`). States: `running` (initial), `completed` (terminal), `failed` (terminal).

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| none | `running` | scan trigger (API or schedule) | | row created |
| `running` | `completed` | all resources evaluated | | `finished_at` and `resources_seen` set |
| `running` | `failed` | the scanner cannot read the hosting platform | | `finished_at` set; previous resource results untouched; a failed scan is never read as a pass |

Whether a second trigger is accepted while a scan is `running` is `GAP-BE-775`.

### 3.3 Drift alert

An alert is open while `cleared_at` is null and cleared when it is set (`ASM-BE-555`). No input defines an acknowledgement or closure workflow (`GAP-BE-771`), so none is specified. There is no terminal state beyond cleared.

### 3.4 Launch gate (derived, not stored)

States: `PASSED` and `FAILED`, computed from the current report on each request. [AC-001-05-01, AC-001-05-02]

| Condition | Gate |
|---|---|
| at least one resource, and every resource has status `pass` (100% EU-verified) | `PASSED` |
| fewer than 100% `pass`, including any `unverified` resource | `FAILED` |
| no resources inventoried (`ASM-BE-559`) | `FAILED` |

`PASSED` does not assert that release is authorised (`ASM-BE-560`; `CON-BE-151`, `Q-BE-152`).
<!-- /SECTION: backend.residency-compliance.state-machine -->
<!-- SECTION: backend.residency-compliance.requirements | status: DRAFT-UNVERIFIED -->
## 4. Requirements

### BE-RESIDENCY-COMPLIANCE-FR-001 Validate an EU region
**Given** a validation request naming a resource and a region that is in `approved_regions`,
**When** `API-RESIDENCYCOMPLIANCE-405210` is called,
**Then** the result states that the check passed, so the deployment or provisioning may proceed. Nothing is written (`ASM-BE-556`).
- **Traces to.** US-001-01, US-001-03; AC-001-01-01, AC-001-03-01; BR-001, BR-002.
- **Verification.** `test-residency-validate-eu-pass` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-405210`.
- **Status.** Blocked on the approved region list (`Q-BE-151`). Success status and body shape are `GAP-BE-769`.

### BE-RESIDENCY-COMPLIANCE-FR-002 Report a non-EU region as a violation
**Given** a validation request whose region is not in `approved_regions`,
**When** `API-RESIDENCYCOMPLIANCE-405210` is called,
**Then** the result states a residency violation in the response body, naming the offending resource and region. The pipeline fails the build on it. No resource is created by this module.
- **Traces to.** US-001-01, US-001-03; AC-001-01-02, AC-001-03-02; BR-001, BR-002; NFR-002.
- **Verification.** `test-residency-validate-non-eu-violation` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-405210`. A violation is reported in the body, not as a pipeline status. [operation record]

### BE-RESIDENCY-COMPLIANCE-FR-003 Missing or unreadable region is a violation
**Given** a validation request where the region is absent, empty or cannot be read,
**When** `API-RESIDENCYCOMPLIANCE-405210` is called,
**Then** the result states a violation, never a pass by default.
- **Traces to.** US-001-03; AC-001-03-03; `INV-BE-007`.
- **Verification.** `test-residency-validate-missing-region-violation` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-405210`.

### BE-RESIDENCY-COMPLIANCE-FR-004 Validate a backup destination within 30 seconds
**Given** a backup destination configuration pointing outside the approved regions,
**When** the pipeline calls `API-RESIDENCYCOMPLIANCE-405210` before the backup runs,
**Then** the result states a residency violation within 30 seconds, so the pipeline can fail the job before any data is written. A destination in an approved region passes.
- **Traces to.** US-001-02; AC-001-02-01, AC-001-02-02; BR-001.
- **Verification.** `test-residency-validate-backup-destination-30s` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-405210`.
- **Status.** The 30 seconds is the end-to-end budget of the job failing, as written in the AC. Whether this call alone must complete inside it is `GAP-BE-776`.

### BE-RESIDENCY-COMPLIANCE-FR-005 Reject a malformed validation request
**Given** a validation request missing the resource or region field, or with a field of the wrong type,
**When** `API-RESIDENCYCOMPLIANCE-405210` is called,
**Then** a 400 validation error is returned in the global envelope, distinct from a violation result.
- **Traces to.** Derived from the status policy in `backend.error-model` (400 for validation failure) [US-004-03]; request shape is `GAP-BE-035`.
- **Verification.** `test-residency-validate-bad-request-400` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-405210`.
- **Distinction.** Missing region metadata inside an otherwise well-formed request is FR-003, a violation. A request that lacks the fields altogether is this requirement.

### BE-RESIDENCY-COMPLIANCE-FR-006 Scan provisioned infrastructure
**Given** a scan trigger,
**When** `API-RESIDENCYCOMPLIANCE-466056` is called,
**Then** a `residency_scans` row is created, every inventoried resource is evaluated per state machine 3.1, `region`, `compliance_status` and `last_scanned_at` are recorded, and the scan finishes `completed` or `failed`.
- **Traces to.** US-001-04, US-001-06; AC-001-04-01, AC-001-06-01; NFR-001.
- **Verification.** `test-residency-scan-records-status` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-466056`.
- **Status.** How resources are discovered is `GAP-BE-767`. Sync or async response and success status are `GAP-BE-769`.

### BE-RESIDENCY-COMPLIANCE-FR-007 Flag an unrecognised resource type
**Given** a resource whose type the scanner does not recognise,
**When** a scan completes and the inventory or report is read,
**Then** the resource appears with status `unverified` and is counted as not EU-verified.
- **Traces to.** US-001-04; AC-001-04-02.
- **Verification.** `test-residency-unrecognised-type-unverified` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-466056`, `API-RESIDENCYCOMPLIANCE-381444`, `API-RESIDENCYCOMPLIANCE-894798`.

### BE-RESIDENCY-COMPLIANCE-FR-008 List inventoried resources
**Given** inventoried resources,
**When** `API-RESIDENCYCOMPLIANCE-381444` is called,
**Then** each resource is listed with its region, compliance status and last scan time. A never-scanned resource shows `unverified` and no scan time.
- **Traces to.** US-001-04; AC-001-04-01, AC-001-04-02.
- **Verification.** `test-residency-list-resources` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-381444`.
- **Status.** Pagination, sorting and filtering are not supplied (`GAP-BE-500` covers page sizes only for search); the collection is unpaginated by default (`GAP-BE-770`).

### BE-RESIDENCY-COMPLIANCE-FR-009 Compliance report
**Given** the current inventory,
**When** `API-RESIDENCYCOMPLIANCE-894798` is called,
**Then** the response lists each resource, its region and a pass/fail residency status, with an overall % compliant metric. The metric is `pass` resources divided by all resources, so `unverified` resources count against it (`ASM-BE-552`).
- **Traces to.** US-001-04; AC-001-04-01; NFR-001, NFR-004.
- **Verification.** `test-residency-report-percent-compliant` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-894798`.
- **Status.** Report fields beyond those named and format are `GAP-BE-770`.

### BE-RESIDENCY-COMPLIANCE-FR-010 Report is refreshed per request
**Given** any request for the report,
**When** `API-RESIDENCYCOMPLIANCE-894798` is called,
**Then** the report is recomputed from the current stored results at that request, not served from a cached earlier report, and states the time it was generated and each resource's last scan time (`ASM-BE-553`).
- **Traces to.** NFR-004 ("refreshed and 100% accurate at each launch gate check").
- **Verification.** `test-residency-report-recomputed-per-request` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-894798`.
- **Status.** Whether a request must also trigger a fresh scan, and how old a result may be, is `GAP-BE-773`.

### BE-RESIDENCY-COMPLIANCE-FR-011 Launch gate passes
**Given** at least one resource and a report with 100% EU-verified resources,
**When** `API-RESIDENCYCOMPLIANCE-395094` is called,
**Then** the gate is reported `PASSED`.
- **Traces to.** US-001-05; AC-001-05-01; BR-003.
- **Verification.** `test-residency-gate-pass` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-395094`.
- **Status.** The consequence of `PASSED` for release is blocked on `CON-BE-151` and `Q-BE-152`.

### BE-RESIDENCY-COMPLIANCE-FR-012 Launch gate fails
**Given** a report with less than 100% EU-verified resources, including any `unverified` resource, or an empty inventory,
**When** `API-RESIDENCYCOMPLIANCE-395094` is called,
**Then** the gate is reported `FAILED`, with the resources that are not EU-verified named, until the report reaches 100%.
- **Traces to.** US-001-05; AC-001-05-02; BR-003; `ASM-BE-552`, `ASM-BE-559`.
- **Verification.** `test-residency-gate-fail-below-100`, `test-residency-gate-fail-empty-inventory` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-395094`.

### BE-RESIDENCY-COMPLIANCE-FR-013 Gate is read-only
**Given** any call to `API-RESIDENCYCOMPLIANCE-395094`,
**When** it is evaluated,
**Then** no row is written, and no sign-off or override is possible through this module.
- **Traces to.** Operation record for `API-RESIDENCYCOMPLIANCE-395094`; `CON-BE-151`.
- **Verification.** `test-residency-gate-no-writes` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-395094`.

### BE-RESIDENCY-COMPLIANCE-FR-014 Raise a drift alert
**Given** a scan finds a running resource in a region not in `approved_regions`,
**When** the scan evaluates it,
**Then** a drift alert is raised carrying the resource details (resource, type, detected region, time), no later than 15 minutes after detection, and the resource is set to `fail`.
- **Traces to.** US-001-06; AC-001-06-02; BR-004; NFR-003.
- **Verification.** `test-residency-drift-alert-raised` (planned, injectable clock).
- **API.** `API-RESIDENCYCOMPLIANCE-466056`, `API-RESIDENCYCOMPLIANCE-047792`.
- **Status.** The AC says "immediately" and NFR-003 says within 15 minutes: `CON-BE-451`. Delivery to the DevOps team is `GAP-BE-308`.

### BE-RESIDENCY-COMPLIANCE-FR-015 No alert on a clean scan
**Given** a periodic scan in which every resource remains in an approved region,
**When** the scan completes,
**Then** no alert is raised and the scan logs a pass.
- **Traces to.** US-001-06; AC-001-06-01.
- **Verification.** `test-residency-scan-clean-no-alert` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-466056`.

### BE-RESIDENCY-COMPLIANCE-FR-016 No duplicate alerts
**Given** an open alert for a resource and region,
**When** a later scan finds the same resource still in that region,
**Then** no second alert is created.
- **Traces to.** `ASM-BE-555` (not stated by any input).
- **Verification.** `test-residency-drift-alert-dedup` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-466056`.

### BE-RESIDENCY-COMPLIANCE-FR-017 List drift alerts
**Given** raised drift alerts,
**When** `API-RESIDENCYCOMPLIANCE-047792` is called,
**Then** the alerts are listed with resource, detected region, detection time and raised time.
- **Traces to.** US-001-06; AC-001-06-02; NFR-003.
- **Verification.** `test-residency-list-drift-alerts` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-047792`.

### BE-RESIDENCY-COMPLIANCE-FR-018 A failed scan is never a pass
**Given** a scan that cannot read the hosting platform,
**When** it ends `failed`,
**Then** earlier results are not overwritten and the failure is visible. It is not logged as a pass.
- **Traces to.** Derived from AC-001-03-03 (fail rather than pass by default) and AC-001-06-01 (a pass is logged only for a scan that runs).
- **Verification.** `test-residency-scan-failure-not-pass` (planned).
- **API.** `API-RESIDENCYCOMPLIANCE-466056`.
<!-- /SECTION: backend.residency-compliance.requirements -->
<!-- SECTION: backend.residency-compliance.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-RESIDENCYCOMPLIANCE-047792` | GET | `/residency/drift-alerts` | `residency-compliance` | 200 | 400, 401, 403 | List drift alerts raised when a resource is detected outside approved EU regions, to be raised within 15 minutes of detection (NFR-003). Alert channel and recipients are open (GAP-BE-308). Proposed path (GAP-BE-303). |
| `API-RESIDENCYCOMPLIANCE-395094` | GET | `/residency/launch-gate` | `residency-compliance` | 200 | 401, 403, 503 | Evaluate the launch gate from the current compliance report. Read-only. Whether a pass authorises release or Engineering Lead sign-off is required is unresolved (CON-BE-151, Q-BE-152), so no sign-off operation is defined. Proposed path (GAP-BE-303). |
| `API-RESIDENCYCOMPLIANCE-894798` | GET | `/residency/report` | `residency-compliance` | 200 | 401, 403, 503 | Return the residency compliance report covering all infrastructure components, refreshed at each request so it is accurate at launch gate checks (NFR-004). Proposed path (GAP-BE-303); storage of report data is open (GAP-BE-305). |
| `API-RESIDENCYCOMPLIANCE-381444` | GET | `/residency/resources` | `residency-compliance` | 200 | 400, 401, 403 | List inventoried infrastructure resources with region, compliance status and last scan time. Resource types the scanner does not recognise appear with status unverified (AC-001-04-02). Proposed path (GAP-BE-303). |
| `API-RESIDENCYCOMPLIANCE-466056` | POST | `/residency/scans` | `residency-compliance` | 202 | 400, 401, 403, 404, 409, 503 | Trigger a residency scan of provisioned infrastructure, recording region and compliance status per resource. Proposed path (GAP-BE-303). Drift alerts are raised for any resource found outside approved regions. |
| `API-RESIDENCYCOMPLIANCE-405210` | POST | `/residency/validations` | `residency-compliance` | 200 | 400, 401, 403, 503 | Validate a resource's declared region against the approved EU region list (CI/CD residency gate and provisioning check). Proposed path (GAP-BE-303); approved region list pending (Q-BE-151). A violation is reported in the body, as pipeline failure handling is outside HTTP. |

### `API-RESIDENCYCOMPLIANCE-047792` — GET `/residency/drift-alerts`

List drift alerts raised when a resource is detected outside approved EU regions, to be raised within 15 minutes of detection (NFR-003). Alert channel and recipients are open (GAP-BE-308). Proposed path (GAP-BE-303).

**Request:** 
- `resource_id` (string): Query filter by resource.
- `raised_after` (string): Query filter: only alerts raised after this timestamp.
- `page` (integer): Page number.
- `page_size` (integer): Items per page.
**Response:** 
- `items` (array, required): Drift alerts, each with id, resource_id, detected_region and raised_at.
- `total` (integer, required): Total number of matching alerts.

### `API-RESIDENCYCOMPLIANCE-395094` — GET `/residency/launch-gate`

Evaluate the launch gate from the current compliance report. Read-only. Whether a pass authorises release or Engineering Lead sign-off is required is unresolved (CON-BE-151, Q-BE-152), so no sign-off operation is defined. Proposed path (GAP-BE-303).

**Request:** none
**Response:** 
- `gate_passed` (boolean, required): True only when every component is compliant.
- `evaluated_at` (string, required): Timestamp of the evaluation against a freshly refreshed report.
- `blocking_resources` (array, required): Resources that are failing or unverified and prevent the gate passing; empty when passed.

### `API-RESIDENCYCOMPLIANCE-894798` — GET `/residency/report`

Return the residency compliance report covering all infrastructure components, refreshed at each request so it is accurate at launch gate checks (NFR-004). Proposed path (GAP-BE-303); storage of report data is open (GAP-BE-305).

**Request:** none
**Response:** 
- `generated_at` (string, required): Timestamp at which the report was generated.
- `total_components` (integer, required): Number of infrastructure components assessed.
- `verified_eu_components` (integer, required): Number of components verified as being in approved EU regions.
- `non_compliant_components` (integer, required): Number of components in the fail state.
- `unverified_components` (integer, required): Number of components flagged unverified, including unrecognised types.
- `components` (array, required): Per-component entries with resource_id, resource_type, region, compliance_status and last_scanned_at.

### `API-RESIDENCYCOMPLIANCE-381444` — GET `/residency/resources`

List inventoried infrastructure resources with region, compliance status and last scan time. Resource types the scanner does not recognise appear with status unverified (AC-001-04-02). Proposed path (GAP-BE-303).

**Request:** 
- `compliance_status` (string): Query filter: pass, fail or unverified.
- `resource_type` (string): Query filter: database, compute or backup storage.
- `page` (integer): Page number; default and maximum page size are not stated (GAP-BE-500).
- `page_size` (integer): Items per page.
**Response:** 
- `items` (array, required): Resources, each with resource_id, resource_type, region, compliance_status and last_scanned_at.
- `total` (integer, required): Total number of matching resources.

### `API-RESIDENCYCOMPLIANCE-466056` — POST `/residency/scans`

Trigger a residency scan of provisioned infrastructure, recording region and compliance status per resource. Proposed path (GAP-BE-303). Drift alerts are raised for any resource found outside approved regions.

**Request:** 
- `resource_ids` (array): Optional subset of resource identifiers to scan; all resources are scanned when omitted.
**Response:** 
- `scan_started_at` (string, required): Timestamp at which the scan was accepted.
- `resources_queued` (integer, required): Number of resources included in the scan.

### `API-RESIDENCYCOMPLIANCE-405210` — POST `/residency/validations`

Validate a resource's declared region against the approved EU region list (CI/CD residency gate and provisioning check). Proposed path (GAP-BE-303); approved region list pending (Q-BE-151). A violation is reported in the body, as pipeline failure handling is outside HTTP.

**Request:** 
- `resource_id` (string, required): Identifier of the infrastructure component being checked.
- `resource_type` (string, required): Component type: database, compute or backup storage. Unrecognised types are flagged unverified, not rejected.
- `region` (string): Declared region of the resource. If missing or unreadable the check fails and deployment is blocked (AC-001-03-03).
**Response:** 
- `resource_id` (string, required): Identifier of the checked resource.
- `region` (string): Region as read for the resource; absent when metadata is missing or unreadable.
- `compliance_status` (string, required): Result: pass, fail or unverified.
- `deployment_blocked` (boolean, required): True when the check fails, including missing or unreadable region metadata.
- `violation_reason` (string): Residency violation reason present when status is fail.
<!-- /SECTION: backend.residency-compliance.api-contract -->
<!-- SECTION: backend.residency-compliance.invariants | status: DRAFT-UNVERIFIED -->
## 6. Invariants

These hold after every operation. `INV-BE-007` (a residency check with missing or unreadable region metadata fails and blocks deployment) is defined in the project spec and applies here by reference.

- `INV-BE-160`. A validation never reports a pass for a region that is missing, unreadable or not in `approved_regions`. [AC-001-01-02, AC-001-03-02, AC-001-03-03]
- `INV-BE-161`. A validation request writes nothing (`ASM-BE-556`).
- `INV-BE-162`. A resource has status `pass` only if its last-scanned region is in `approved_regions`.
- `INV-BE-163`. Every inventoried resource appears in the inventory and in the report. A resource of an unrecognised type appears as `unverified` and is never omitted. [AC-001-04-02]
- `INV-BE-164`. The % compliant metric equals `pass` resources divided by all inventoried resources (`ASM-BE-552`). The gate is `PASSED` only when that is 100% and at least one resource exists (`ASM-BE-559`). [AC-001-05-01, AC-001-05-02]
- `INV-BE-165`. For every resource with status `fail` and a detected region, an alert exists that is open (`cleared_at` null) for that resource and region. No resource with status `pass` has an open alert. (`ASM-BE-555`)
- `INV-BE-166`. At most one open alert exists per resource and region. `raised_at` is never earlier than `detected_at`. A clean scan raises no alert. [AC-001-06-01]
- `INV-BE-167`. A row with `compliance_status` `pass` has a non-null `region` (enforced by table check).
- `INV-BE-168`. The gate and report endpoints change no state (`API-RESIDENCYCOMPLIANCE-395094`, `API-RESIDENCYCOMPLIANCE-894798`, `API-RESIDENCYCOMPLIANCE-381444` and `API-RESIDENCYCOMPLIANCE-047792` are reads).
- `INV-BE-169`. A scan with status `failed` changes no resource status and no alert.
<!-- /SECTION: backend.residency-compliance.invariants -->
<!-- SECTION: backend.residency-compliance.authorization | status: BLOCKED -->
## 7. Authorisation

**Blocked.** The permission matrix (role × operation) cannot be written.

- **Roles.** No input defines API caller roles. The existing authentication method is unnamed (`GAP-BE-085`), and the project authorisation model is itself `BLOCKED` (`backend.auth-model`).
- **Intent the stories state.** These are the actors named by the stories, not API roles. Each wants these operations:
  - Platform/DevOps Engineer: scans and drift alerts. [US-001-01, US-001-02, US-001-06]
  - Release/CI Pipeline Owner: the validation check. [US-001-03]
  - Compliance/Security Auditor: the report and inventory. [US-001-04]
  - Engineering Lead: the gate. [US-001-05]
- **Machine callers.** The pipeline calls `API-RESIDENCYCOMPLIANCE-405210` without a person. How a machine caller is identified is not stated (`GAP-BE-772`).
- **Ownership predicate.** None applies. Resources are infrastructure, not user-owned. Nothing in this module is scoped to a user.
- **Default.** A role has no permission on an operation until an input grants it (`ASM-BE-302`).
- **Sign-off.** No operation grants release sign-off (`CON-BE-151`, `Q-BE-152`).

**Unblocked by:**
- a statement of the existing authentication method and its roles (`GAP-BE-085`);
- a decision on which roles may call each of the six operations and how the pipeline is identified (`GAP-BE-772`);
- the resolution of `CON-BE-151` and `Q-BE-152` for the gate.
<!-- /SECTION: backend.residency-compliance.authorization -->
<!-- SECTION: backend.residency-compliance.jobs | status: DRAFT-UNVERIFIED -->
## 8. Jobs

### 8.1 Periodic residency scan

- **Purpose.** After launch, detect any running resource that has drifted to or is found in a non-EU region, for example through misconfigured auto-scaling. [US-001-06; AC-001-06-02; BR-004]
- **Schedule.** The period is not stated (`Q-BE-153`). The period is no longer than 15 minutes, because the alert must be fired within 15 minutes of detection (`ASM-BE-153`, NFR-003).
- **Action.** The same scan as `API-RESIDENCYCOMPLIANCE-466056`, with `trigger = 'schedule'`.
- **Output.** A clean scan logs a pass and raises nothing. [AC-001-06-01] A finding raises an alert per BE-RESIDENCY-COMPLIANCE-FR-014. A failed scan is recorded and never read as a pass (FR-018).
- **Overlap.** A schedule tick while a scan is `running` is skipped (`ASM-BE-562`). The same rule for the API trigger is `GAP-BE-775`.
- **Retry.** No retry policy is stated. A failed scan waits for the next tick (`GAP-BE-775`).

### 8.2 Alert clearing

Clearing is part of the scan, not a separate job (`ASM-BE-555`). A scan that finds a previously alerting resource in an approved region sets `cleared_at`.

### 8.3 Idempotency

- Resource upsert is keyed on `external_ref`. Re-running a scan over unchanged resources produces the same resource rows and no new alert (FR-016).
- Each scan run has its own `scan_id`. Scan runs are not deduplicated: two triggers produce two runs (overlap rule above).
- The validation operation is stateless and idempotent by construction (`ASM-BE-556`).

### 8.4 Retention

No retention job is specified. NFR-019 covers login records only, and retention of scan results, alerts and reports is not stated (`GAP-BE-305`).
<!-- /SECTION: backend.residency-compliance.jobs -->
<!-- SECTION: backend.residency-compliance.test-plan | status: DRAFT-UNVERIFIED -->
## 9. Test plan

All names are planned. Tests that depend on the region list use a fixture list, because the real list is `Q-BE-151`. The injectable clock is required for every time-based test.

### Unit
- `test-residency-region-match-exact` — comparison against `approved_regions` (`ASM-BE-561`).
- `test-residency-status-transitions` — table 3.1, every row including the unrecognised-type and missing-region rows.
- `test-residency-percent-compliant-math` — `pass` divided by all, with `unverified` in the denominator (`ASM-BE-552`).
- `test-residency-gate-evaluation` — 100%, below 100%, empty inventory (`ASM-BE-559`).
- `test-residency-alert-dedup-key` — one open alert per resource and region.
- `test-residency-percent-nan-guard` — empty inventory gives a defined result, not a division error.

### Integration
- `test-residency-validate-eu-pass`, `test-residency-validate-non-eu-violation`, `test-residency-validate-missing-region-violation`, `test-residency-validate-bad-request-400`.
- `test-residency-validate-backup-destination-30s`.
- `test-residency-scan-records-status`, `test-residency-unrecognised-type-unverified`, `test-residency-scan-failure-not-pass`.
- `test-residency-list-resources`, `test-residency-report-percent-compliant`, `test-residency-report-recomputed-per-request`.
- `test-residency-gate-pass`, `test-residency-gate-fail-below-100`, `test-residency-gate-fail-empty-inventory`, `test-residency-gate-no-writes`.
- `test-residency-drift-alert-raised`, `test-residency-scan-clean-no-alert`, `test-residency-drift-alert-dedup`, `test-residency-list-drift-alerts`.
- `test-residency-alert-cleared-on-return-to-eu` (`ASM-BE-555`).
- `test-residency-scheduled-scan-skips-overlap` (`ASM-BE-562`).
- `test-residency-db-check-pass-requires-region` — the table check from the data model.

### Contract
- Every operation in scope appears in the published OpenAPI document with request and response schemas and its 4xx and 5xx errors. [BR-012; NFR-008] Run by `api-docs`; it needs the success statuses (`GAP-BE-769`).
- Error responses use the global envelope. [backend.error-model]
- A violation result and a 400 validation error are distinguishable in the documented schemas (FR-005).
- The data-handling note in the OpenAPI document states EU residency of note data. [BR-013; US-004-06] The note is owned by `api-docs`; this module supplies the region list source.
<!-- /SECTION: backend.residency-compliance.test-plan -->
<!-- SECTION: backend.residency-compliance.gaps | status: DRAFT-UNVERIFIED -->
## 10. Gaps, contradictions and assumptions

Open items defined in the project spec are cited, not redefined: `Q-BE-151`, `Q-BE-152`, `Q-BE-153`, `CON-BE-151`, `GAP-BE-035`, `GAP-BE-085`, `GAP-BE-304`, `GAP-BE-305`, `GAP-BE-307`, `GAP-BE-308`, `GAP-BE-500`, `GAP-BE-602`, `ASM-BE-153`, `ASM-BE-302`. Items below are new to this module.

### Contradictions

- `CON-BE-451` (non-critical). AC-001-06-02 says an alert is sent "immediately" when a resource is found in a non-EU region. NFR-003 says the alert is fired "within 15 minutes of drift detection". Reading used: the alert is raised in the same scan run that detects the drift, and in any case within 15 minutes of detection. Consequence of the AC reading: no latency budget can be tested beyond "same run". Consequence of the NFR reading: a 15-minute budget is the only testable bound.

### Gaps

| ID | Missing | Degrades | Closing artefact |
|---|---|---|---|
| `GAP-BE-767` | How the scanner discovers resources, which resource types it recognises, and how it is authorised to read the hosting platform. No input names a mechanism. No credential is written here. | FR-006, FR-007, scan job | a statement of the inventory source and the recognised-type list |
| `GAP-BE-768` | The format of region identifiers and whether the list is maintained in configuration or data. `Q-BE-151` supplies the values. | FR-001 to FR-004, `approved_regions` | the approved region list with its source |
| `GAP-BE-769` | Success status and body of the six operations, and whether a scan is synchronous or returns a run to poll. | api-contract, contract tests | a decision on statuses and the scan response |
| `GAP-BE-770` | Report and inventory shape beyond the fields the ACs name: format, pagination, sorting, filtering, history of earlier reports kept for audit. | FR-008, FR-009 | a report specification |
| `GAP-BE-771` | Alert lifecycle: acknowledgement, who closes an alert, repeat notification while a resource stays out of region. | state machine 3.3, FR-016 | an alert handling rule |
| `GAP-BE-772` | How a machine caller such as the CI pipeline is identified, and which roles may call each operation. | authorization | an authentication decision (`GAP-BE-085`) |
| `GAP-BE-773` | Maximum age of a scan result for the report and the gate, and whether a gate or report request triggers a fresh scan. NFR-004 requires accuracy at each gate check. | FR-010, FR-011, FR-012 | a freshness rule |
| `GAP-BE-774` | What happens when a resource is decommissioned: removal from inventory, deletion of its alerts. | data-model | a decommissioning rule |
| `GAP-BE-775` | Behaviour of an API scan trigger while a scan is running, and the retry rule for a failed scan. | jobs 8.1 | an operations decision |
| `GAP-BE-776` | Which part of the 30-second budget this service owns in AC-001-02-02, and whether validations are logged for audit. | FR-004 | a latency allocation and an audit decision |

No latency or throughput target is supplied for the scan, report or gate operations, and none is invented (`GAP-BE-301` covers login only).

### Assumptions

- `ASM-BE-551`. Column types: uuid keys, text strings, timestamptz instants, integer counts. PostgreSQL 16 is the platform and no types are supplied. Confirmed by a platform decision.
- `ASM-BE-552`. An `unverified` resource is not EU-verified. The % compliant metric is `pass` divided by all resources. AC-001-04-02 flags `unverified`, and AC-001-05-02 blocks at less than 100% EU-verified. Confirmed by a product-owner decision.
- `ASM-BE-553`. "Refreshed at each request" means recomputed from the latest stored scan results, with each row's last scan time shown, not that a request triggers a scan. NFR-004 does not say. Confirmed by a decision on `GAP-BE-773`.
- `ASM-BE-554`. A scan whose resource has a missing or unreadable region records status `unverified` with a null region; the validation operation reports a violation for the same case. AC-001-03-03 requires a failure for validation and says nothing about inventory status. Confirmed by a product-owner decision.
- `ASM-BE-555`. One open alert exists per resource and region. A later scan that finds the resource in an approved region sets `cleared_at`. A repeat scan with the resource still out of region raises no second alert. No input defines the alert lifecycle (`GAP-BE-771`). Confirmed by an alert handling rule.
- `ASM-BE-556`. The validation operation is stateless and writes nothing. The operation record describes a check, and no input asks for validation history. Confirmed by a decision on `GAP-BE-776`.
- `ASM-BE-557`. A `residency_scans` record is needed to run, order and audit scans. It extends the project domain model, which lists only INFRA_RESOURCE and DRIFT_ALERT. Confirmed by a design review.
- `ASM-BE-559`. An empty inventory fails the gate. 100% of nothing cannot be shown as EU-verified. Confirmed by a product-owner decision.
- `ASM-BE-560`. The gate endpoint returns the computed outcome only. It does not assert release authorisation, because `CON-BE-151` is open. Confirmed by the resolution of `CON-BE-151`.
- `ASM-BE-561`. Region comparison is exact and case-sensitive against `approved_regions`, with no normalisation. Confirmed by `GAP-BE-768`.
- `ASM-BE-562`. A schedule tick while a scan is `running` is skipped. Confirmed by a decision on `GAP-BE-775`.
<!-- /SECTION: backend.residency-compliance.gaps -->
