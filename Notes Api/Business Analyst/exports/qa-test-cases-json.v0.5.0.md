---
id: agent-ba/notes_api/exports/qa_test_cases.json
type: export
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-ba/notes_api/exports/qa_test_cases.json
depends_on: []
commit_kind: fork
confidence: agent
run_id: e1e628bb-b234-5a13-b948-b4689dcc8eab
okf_id: notes_api:QA-TEST-CASES
---

[
  {
    "testId": "TC-001-01",
    "title": "the DB and hosting resources are created",
    "story": "US-001-01",
    "epic": "EP-001",
    "preconditions": "an infrastructure provisioning request for notes_api",
    "steps": "the DB and hosting resources are created",
    "expectedResult": "both resources report a region within the approved EU region list",
    "module": "eu-data-residency-infrastructure",
    "acceptanceCriteria": "Given an infrastructure provisioning request for notes_api, when the DB and hosting resources are created, then both resources report a region within the approved EU region list; Given a provisioning request specifying a non-EU region, when the request is submitted, then the request is rejected and no resource is created",
    "criteria": [
      {
        "id": "AC-001-01-01",
        "text": "Given an infrastructure provisioning request for notes_api, when the DB and hosting resources are created, then both resources report a region within the approved EU region list"
      },
      {
        "id": "AC-001-01-02",
        "text": "Given a provisioning request specifying a non-EU region, when the request is submitted, then the request is rejected and no resource is created"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-001-02",
    "title": "[error] the request is submitted",
    "story": "US-001-01",
    "epic": "EP-001",
    "preconditions": "a provisioning request specifying a non-EU region",
    "steps": "the request is submitted",
    "expectedResult": "the request is rejected and no resource is created",
    "module": "eu-data-residency-infrastructure",
    "acceptanceCriteria": "Given an infrastructure provisioning request for notes_api, when the DB and hosting resources are created, then both resources report a region within the approved EU region list; Given a provisioning request specifying a non-EU region, when the request is submitted, then the request is rejected and no resource is created",
    "criteria": [
      {
        "id": "AC-001-01-01",
        "text": "Given an infrastructure provisioning request for notes_api, when the DB and hosting resources are created, then both resources report a region within the approved EU region list"
      },
      {
        "id": "AC-001-01-02",
        "text": "Given a provisioning request specifying a non-EU region, when the request is submitted, then the request is rejected and no resource is created"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-001-18",
    "title": "the backup completes",
    "story": "US-001-02",
    "epic": "EP-001",
    "preconditions": "a scheduled backup job runs for notes_api",
    "steps": "the backup completes",
    "expectedResult": "the backup artifact is stored in an EU-region storage bucket/service",
    "module": "eu-data-residency-infrastructure",
    "acceptanceCriteria": "Given a scheduled backup job runs for notes_api, when the backup completes, then the backup artifact is stored in an EU-region storage bucket/service; Given a backup destination configuration pointing outside the EU, when the backup pipeline validates the config before running, then the job fails within 30 seconds with a residency violation error and does not write data",
    "criteria": [
      {
        "id": "AC-001-02-01",
        "text": "Given a scheduled backup job runs for notes_api, when the backup completes, then the backup artifact is stored in an EU-region storage bucket/service"
      },
      {
        "id": "AC-001-02-02",
        "text": "Given a backup destination configuration pointing outside the EU, when the backup pipeline validates the config before running, then the job fails within 30 seconds with a residency violation error and does not write data"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-001-19",
    "title": "[error] the backup pipeline validates the config before running",
    "story": "US-001-02",
    "epic": "EP-001",
    "preconditions": "a backup destination configuration pointing outside the EU",
    "steps": "the backup pipeline validates the config before running",
    "expectedResult": "the job fails within 30 seconds with a residency violation error and does not write data",
    "module": "eu-data-residency-infrastructure",
    "acceptanceCriteria": "Given a scheduled backup job runs for notes_api, when the backup completes, then the backup artifact is stored in an EU-region storage bucket/service; Given a backup destination configuration pointing outside the EU, when the backup pipeline validates the config before running, then the job fails within 30 seconds with a residency violation error and does not write data",
    "criteria": [
      {
        "id": "AC-001-02-01",
        "text": "Given a scheduled backup job runs for notes_api, when the backup completes, then the backup artifact is stored in an EU-region storage bucket/service"
      },
      {
        "id": "AC-001-02-02",
        "text": "Given a backup destination configuration pointing outside the EU, when the backup pipeline validates the config before running, then the job fails within 30 seconds with a residency violation error and does not write data"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-001-05",
    "title": "the residency check runs in the pipeline",
    "story": "US-001-03",
    "epic": "EP-001",
    "preconditions": "a deployment is triggered with all target resources in EU regions",
    "steps": "the residency check runs in the pipeline",
    "expectedResult": "the check passes and the deployment proceeds",
    "module": "eu-data-residency-infrastructure",
    "acceptanceCriteria": "Given a deployment is triggered with all target resources in EU regions, when the residency check runs in the pipeline, then the check passes and the deployment proceeds; Given a deployment is triggered with at least one target resource outside the EU, when the residency check runs, then the pipeline fails the build and blocks the deployment; Given a resource's region metadata is missing or unreadable, when the residency check attempts to evaluate it, then the check treats it as a failure and blocks deployment rather than passing by default",
    "criteria": [
      {
        "id": "AC-001-03-01",
        "text": "Given a deployment is triggered with all target resources in EU regions, when the residency check runs in the pipeline, then the check passes and the deployment proceeds"
      },
      {
        "id": "AC-001-03-02",
        "text": "Given a deployment is triggered with at least one target resource outside the EU, when the residency check runs, then the pipeline fails the build and blocks the deployment"
      },
      {
        "id": "AC-001-03-03",
        "text": "Given a resource's region metadata is missing or unreadable, when the residency check attempts to evaluate it, then the check treats it as a failure and blocks deployment rather than passing by default"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-001-06",
    "title": "[error] the residency check runs",
    "story": "US-001-03",
    "epic": "EP-001",
    "preconditions": "a deployment is triggered with at least one target resource outside the EU",
    "steps": "the residency check runs",
    "expectedResult": "the pipeline fails the build and blocks the deployment",
    "module": "eu-data-residency-infrastructure",
    "acceptanceCriteria": "Given a deployment is triggered with all target resources in EU regions, when the residency check runs in the pipeline, then the check passes and the deployment proceeds; Given a deployment is triggered with at least one target resource outside the EU, when the residency check runs, then the pipeline fails the build and blocks the deployment; Given a resource's region metadata is missing or unreadable, when the residency check attempts to evaluate it, then the check treats it as a failure and blocks deployment rather than passing by default",
    "criteria": [
      {
        "id": "AC-001-03-01",
        "text": "Given a deployment is triggered with all target resources in EU regions, when the residency check runs in the pipeline, then the check passes and the deployment proceeds"
      },
      {
        "id": "AC-001-03-02",
        "text": "Given a deployment is triggered with at least one target resource outside the EU, when the residency check runs, then the pipeline fails the build and blocks the deployment"
      },
      {
        "id": "AC-001-03-03",
        "text": "Given a resource's region metadata is missing or unreadable, when the residency check attempts to evaluate it, then the check treats it as a failure and blocks deployment rather than passing by default"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-001-07",
    "title": "[edge] the residency check attempts to evaluate it",
    "story": "US-001-03",
    "epic": "EP-001",
    "preconditions": "a resource's region metadata is missing or unreadable",
    "steps": "the residency check attempts to evaluate it",
    "expectedResult": "the check treats it as a failure and blocks deployment rather than passing by default",
    "module": "eu-data-residency-infrastructure",
    "acceptanceCriteria": "Given a deployment is triggered with all target resources in EU regions, when the residency check runs in the pipeline, then the check passes and the deployment proceeds; Given a deployment is triggered with at least one target resource outside the EU, when the residency check runs, then the pipeline fails the build and blocks the deployment; Given a resource's region metadata is missing or unreadable, when the residency check attempts to evaluate it, then the check treats it as a failure and blocks deployment rather than passing by default",
    "criteria": [
      {
        "id": "AC-001-03-01",
        "text": "Given a deployment is triggered with all target resources in EU regions, when the residency check runs in the pipeline, then the check passes and the deployment proceeds"
      },
      {
        "id": "AC-001-03-02",
        "text": "Given a deployment is triggered with at least one target resource outside the EU, when the residency check runs, then the pipeline fails the build and blocks the deployment"
      },
      {
        "id": "AC-001-03-03",
        "text": "Given a resource's region metadata is missing or unreadable, when the residency check attempts to evaluate it, then the check treats it as a failure and blocks deployment rather than passing by default"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-001-08",
    "title": "the compliance report is generated",
    "story": "US-001-04",
    "epic": "EP-001",
    "preconditions": "all notes_api resources exist and have been scanned",
    "steps": "the compliance report is generated",
    "expectedResult": "the report lists each resource, its region, and a pass/fail residency status with an overall % compliant metric",
    "module": "eu-data-residency-infrastructure",
    "acceptanceCriteria": "Given all notes_api resources exist and have been scanned, when the compliance report is generated, then the report lists each resource, its region, and a pass/fail residency status with an overall % compliant metric; Given a new resource type is added that the scanner doesn't yet recognize, when the report is generated, then the resource appears flagged as 'unverified' rather than silently omitted",
    "criteria": [
      {
        "id": "AC-001-04-01",
        "text": "Given all notes_api resources exist and have been scanned, when the compliance report is generated, then the report lists each resource, its region, and a pass/fail residency status with an overall % compliant metric"
      },
      {
        "id": "AC-001-04-02",
        "text": "Given a new resource type is added that the scanner doesn't yet recognize, when the report is generated, then the resource appears flagged as 'unverified' rather than silently omitted"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-001-09",
    "title": "[edge] the report is generated",
    "story": "US-001-04",
    "epic": "EP-001",
    "preconditions": "a new resource type is added that the scanner doesn't yet recognize",
    "steps": "the report is generated",
    "expectedResult": "the resource appears flagged as 'unverified' rather than silently omitted",
    "module": "eu-data-residency-infrastructure",
    "acceptanceCriteria": "Given all notes_api resources exist and have been scanned, when the compliance report is generated, then the report lists each resource, its region, and a pass/fail residency status with an overall % compliant metric; Given a new resource type is added that the scanner doesn't yet recognize, when the report is generated, then the resource appears flagged as 'unverified' rather than silently omitted",
    "criteria": [
      {
        "id": "AC-001-04-01",
        "text": "Given all notes_api resources exist and have been scanned, when the compliance report is generated, then the report lists each resource, its region, and a pass/fail residency status with an overall % compliant metric"
      },
      {
        "id": "AC-001-04-02",
        "text": "Given a new resource type is added that the scanner doesn't yet recognize, when the report is generated, then the resource appears flagged as 'unverified' rather than silently omitted"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-001-10",
    "title": "the launch checklist is reviewed",
    "story": "US-001-05",
    "epic": "EP-001",
    "preconditions": "the residency compliance report shows 100% EU-verified resources",
    "steps": "the launch checklist is reviewed",
    "expectedResult": "the launch gate is marked passed and release is authorized",
    "module": "eu-data-residency-infrastructure",
    "acceptanceCriteria": "Given the residency compliance report shows 100% EU-verified resources, when the launch checklist is reviewed, then the launch gate is marked passed and release is authorized; Given the residency compliance report shows less than 100% EU-verified resources, when the launch checklist is reviewed, then the launch gate is marked failed and release is blocked until resolved",
    "criteria": [
      {
        "id": "AC-001-05-01",
        "text": "Given the residency compliance report shows 100% EU-verified resources, when the launch checklist is reviewed, then the launch gate is marked passed and release is authorized"
      },
      {
        "id": "AC-001-05-02",
        "text": "Given the residency compliance report shows less than 100% EU-verified resources, when the launch checklist is reviewed, then the launch gate is marked failed and release is blocked until resolved"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-001-11",
    "title": "[error] the launch checklist is reviewed",
    "story": "US-001-05",
    "epic": "EP-001",
    "preconditions": "the residency compliance report shows less than 100% EU-verified resources",
    "steps": "the launch checklist is reviewed",
    "expectedResult": "the launch gate is marked failed and release is blocked until resolved",
    "module": "eu-data-residency-infrastructure",
    "acceptanceCriteria": "Given the residency compliance report shows 100% EU-verified resources, when the launch checklist is reviewed, then the launch gate is marked passed and release is authorized; Given the residency compliance report shows less than 100% EU-verified resources, when the launch checklist is reviewed, then the launch gate is marked failed and release is blocked until resolved",
    "criteria": [
      {
        "id": "AC-001-05-01",
        "text": "Given the residency compliance report shows 100% EU-verified resources, when the launch checklist is reviewed, then the launch gate is marked passed and release is authorized"
      },
      {
        "id": "AC-001-05-02",
        "text": "Given the residency compliance report shows less than 100% EU-verified resources, when the launch checklist is reviewed, then the launch gate is marked failed and release is blocked until resolved"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-001-12",
    "title": "all resources remain in EU regions",
    "story": "US-001-06",
    "epic": "EP-001",
    "preconditions": "a periodic residency scan runs post-launch",
    "steps": "all resources remain in EU regions",
    "expectedResult": "no alert is raised and the scan logs a pass",
    "module": "eu-data-residency-infrastructure",
    "acceptanceCriteria": "Given a periodic residency scan runs post-launch, when all resources remain in EU regions, then no alert is raised and the scan logs a pass; Given a periodic residency scan runs post-launch, when a resource is found in a non-EU region (e.g., via misconfigured auto-scaling), then an alert is sent to the DevOps team immediately with resource details",
    "criteria": [
      {
        "id": "AC-001-06-01",
        "text": "Given a periodic residency scan runs post-launch, when all resources remain in EU regions, then no alert is raised and the scan logs a pass"
      },
      {
        "id": "AC-001-06-02",
        "text": "Given a periodic residency scan runs post-launch, when a resource is found in a non-EU region (e.g., via misconfigured auto-scaling), then an alert is sent to the DevOps team immediately with resource details"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-001-13",
    "title": "[edge] a resource is found in a non-EU region (e.g., via misconfigured auto-scal",
    "story": "US-001-06",
    "epic": "EP-001",
    "preconditions": "a periodic residency scan runs post-launch",
    "steps": "a resource is found in a non-EU region (e.g., via misconfigured auto-scaling)",
    "expectedResult": "an alert is sent to the DevOps team immediately with resource details",
    "module": "eu-data-residency-infrastructure",
    "acceptanceCriteria": "Given a periodic residency scan runs post-launch, when all resources remain in EU regions, then no alert is raised and the scan logs a pass; Given a periodic residency scan runs post-launch, when a resource is found in a non-EU region (e.g., via misconfigured auto-scaling), then an alert is sent to the DevOps team immediately with resource details",
    "criteria": [
      {
        "id": "AC-001-06-01",
        "text": "Given a periodic residency scan runs post-launch, when all resources remain in EU regions, then no alert is raised and the scan logs a pass"
      },
      {
        "id": "AC-001-06-02",
        "text": "Given a periodic residency scan runs post-launch, when a resource is found in a non-EU region (e.g., via misconfigured auto-scaling), then an alert is sent to the DevOps team immediately with resource details"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-13",
    "title": "POST to the notes endpoint",
    "story": "US-002-01",
    "epic": "EP-002",
    "preconditions": "a valid title and body payload",
    "steps": "POST to the notes endpoint",
    "expectedResult": "API returns 201 with the created note including a unique ID, timestamps, and pinned=false by default",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given a valid title and body payload, when POST to the notes endpoint, then API returns 201 with the created note including a unique ID, timestamps, and pinned=false by default; Given a valid payload with pinned=true, when POST to the notes endpoint, then API returns 201 and the stored note has pinned=true; Given a payload missing required title, when POST to the notes endpoint, then API returns 400 with a validation error and no note is persisted",
    "criteria": [
      {
        "id": "AC-002-01-01",
        "text": "Given a valid title and body payload, when POST to the notes endpoint, then API returns 201 with the created note including a unique ID, timestamps, and pinned=false by default"
      },
      {
        "id": "AC-002-01-02",
        "text": "Given a valid payload with pinned=true, when POST to the notes endpoint, then API returns 201 and the stored note has pinned=true"
      },
      {
        "id": "AC-002-01-03",
        "text": "Given a payload missing required title, when POST to the notes endpoint, then API returns 400 with a validation error and no note is persisted"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-14",
    "title": "POST to the notes endpoint",
    "story": "US-002-01",
    "epic": "EP-002",
    "preconditions": "a valid payload with pinned=true",
    "steps": "POST to the notes endpoint",
    "expectedResult": "API returns 201 and the stored note has pinned=true",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given a valid title and body payload, when POST to the notes endpoint, then API returns 201 with the created note including a unique ID, timestamps, and pinned=false by default; Given a valid payload with pinned=true, when POST to the notes endpoint, then API returns 201 and the stored note has pinned=true; Given a payload missing required title, when POST to the notes endpoint, then API returns 400 with a validation error and no note is persisted",
    "criteria": [
      {
        "id": "AC-002-01-01",
        "text": "Given a valid title and body payload, when POST to the notes endpoint, then API returns 201 with the created note including a unique ID, timestamps, and pinned=false by default"
      },
      {
        "id": "AC-002-01-02",
        "text": "Given a valid payload with pinned=true, when POST to the notes endpoint, then API returns 201 and the stored note has pinned=true"
      },
      {
        "id": "AC-002-01-03",
        "text": "Given a payload missing required title, when POST to the notes endpoint, then API returns 400 with a validation error and no note is persisted"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-15",
    "title": "POST to the notes endpoint",
    "story": "US-002-01",
    "epic": "EP-002",
    "preconditions": "a payload missing required title",
    "steps": "POST to the notes endpoint",
    "expectedResult": "API returns 400 with a validation error and no note is persisted",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given a valid title and body payload, when POST to the notes endpoint, then API returns 201 with the created note including a unique ID, timestamps, and pinned=false by default; Given a valid payload with pinned=true, when POST to the notes endpoint, then API returns 201 and the stored note has pinned=true; Given a payload missing required title, when POST to the notes endpoint, then API returns 400 with a validation error and no note is persisted",
    "criteria": [
      {
        "id": "AC-002-01-01",
        "text": "Given a valid title and body payload, when POST to the notes endpoint, then API returns 201 with the created note including a unique ID, timestamps, and pinned=false by default"
      },
      {
        "id": "AC-002-01-02",
        "text": "Given a valid payload with pinned=true, when POST to the notes endpoint, then API returns 201 and the stored note has pinned=true"
      },
      {
        "id": "AC-002-01-03",
        "text": "Given a payload missing required title, when POST to the notes endpoint, then API returns 400 with a validation error and no note is persisted"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-16",
    "title": "PUT/PATCH to the note endpoint",
    "story": "US-002-02",
    "epic": "EP-002",
    "preconditions": "an existing note ID and valid updated fields",
    "steps": "PUT/PATCH to the note endpoint",
    "expectedResult": "API returns 200 with the updated note and an updated timestamp",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given an existing note ID and valid updated fields, when PUT/PATCH to the note endpoint, then API returns 200 with the updated note and an updated timestamp; Given a PATCH containing only the pinned field, when it is submitted, then API returns 200, pinned is updated, and title, body and tags are unchanged; Given a non-existent note ID, when PUT/PATCH to that note endpoint, then API returns 404 and no data is modified",
    "criteria": [
      {
        "id": "AC-002-02-01",
        "text": "Given an existing note ID and valid updated fields, when PUT/PATCH to the note endpoint, then API returns 200 with the updated note and an updated timestamp"
      },
      {
        "id": "AC-002-02-02",
        "text": "Given a PATCH containing only the pinned field, when it is submitted, then API returns 200, pinned is updated, and title, body and tags are unchanged"
      },
      {
        "id": "AC-002-02-03",
        "text": "Given a non-existent note ID, when PUT/PATCH to that note endpoint, then API returns 404 and no data is modified"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-17",
    "title": "it is submitted",
    "story": "US-002-02",
    "epic": "EP-002",
    "preconditions": "a PATCH containing only the pinned field",
    "steps": "it is submitted",
    "expectedResult": "API returns 200, pinned is updated, and title, body and tags are unchanged",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given an existing note ID and valid updated fields, when PUT/PATCH to the note endpoint, then API returns 200 with the updated note and an updated timestamp; Given a PATCH containing only the pinned field, when it is submitted, then API returns 200, pinned is updated, and title, body and tags are unchanged; Given a non-existent note ID, when PUT/PATCH to that note endpoint, then API returns 404 and no data is modified",
    "criteria": [
      {
        "id": "AC-002-02-01",
        "text": "Given an existing note ID and valid updated fields, when PUT/PATCH to the note endpoint, then API returns 200 with the updated note and an updated timestamp"
      },
      {
        "id": "AC-002-02-02",
        "text": "Given a PATCH containing only the pinned field, when it is submitted, then API returns 200, pinned is updated, and title, body and tags are unchanged"
      },
      {
        "id": "AC-002-02-03",
        "text": "Given a non-existent note ID, when PUT/PATCH to that note endpoint, then API returns 404 and no data is modified"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-18",
    "title": "PUT/PATCH to that note endpoint",
    "story": "US-002-02",
    "epic": "EP-002",
    "preconditions": "a non-existent note ID",
    "steps": "PUT/PATCH to that note endpoint",
    "expectedResult": "API returns 404 and no data is modified",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given an existing note ID and valid updated fields, when PUT/PATCH to the note endpoint, then API returns 200 with the updated note and an updated timestamp; Given a PATCH containing only the pinned field, when it is submitted, then API returns 200, pinned is updated, and title, body and tags are unchanged; Given a non-existent note ID, when PUT/PATCH to that note endpoint, then API returns 404 and no data is modified",
    "criteria": [
      {
        "id": "AC-002-02-01",
        "text": "Given an existing note ID and valid updated fields, when PUT/PATCH to the note endpoint, then API returns 200 with the updated note and an updated timestamp"
      },
      {
        "id": "AC-002-02-02",
        "text": "Given a PATCH containing only the pinned field, when it is submitted, then API returns 200, pinned is updated, and title, body and tags are unchanged"
      },
      {
        "id": "AC-002-02-03",
        "text": "Given a non-existent note ID, when PUT/PATCH to that note endpoint, then API returns 404 and no data is modified"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-05",
    "title": "a tag is added via the API",
    "story": "US-002-03",
    "epic": "EP-002",
    "preconditions": "an existing note and a tag name",
    "steps": "a tag is added via the API",
    "expectedResult": "API returns 200 and the note's tag list includes the new tag",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given an existing note and a tag name, when a tag is added via the API, then API returns 200 and the note's tag list includes the new tag; Given a tag that already exists on the note, when the same tag is added again, then API returns 200 without creating a duplicate tag entry",
    "criteria": [
      {
        "id": "AC-002-03-01",
        "text": "Given an existing note and a tag name, when a tag is added via the API, then API returns 200 and the note's tag list includes the new tag"
      },
      {
        "id": "AC-002-03-02",
        "text": "Given a tag that already exists on the note, when the same tag is added again, then API returns 200 without creating a duplicate tag entry"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-06",
    "title": "[edge] the same tag is added again",
    "story": "US-002-03",
    "epic": "EP-002",
    "preconditions": "a tag that already exists on the note",
    "steps": "the same tag is added again",
    "expectedResult": "API returns 200 without creating a duplicate tag entry",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given an existing note and a tag name, when a tag is added via the API, then API returns 200 and the note's tag list includes the new tag; Given a tag that already exists on the note, when the same tag is added again, then API returns 200 without creating a duplicate tag entry",
    "criteria": [
      {
        "id": "AC-002-03-01",
        "text": "Given an existing note and a tag name, when a tag is added via the API, then API returns 200 and the note's tag list includes the new tag"
      },
      {
        "id": "AC-002-03-02",
        "text": "Given a tag that already exists on the note, when the same tag is added again, then API returns 200 without creating a duplicate tag entry"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-07",
    "title": "the archive endpoint is called",
    "story": "US-002-04",
    "epic": "EP-002",
    "preconditions": "an active, existing note",
    "steps": "the archive endpoint is called",
    "expectedResult": "API returns 200, marks the note archived, and its data remains intact",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given an active, existing note, when the archive endpoint is called, then API returns 200, marks the note archived, and its data remains intact; Given an already-archived note, when the archive endpoint is called again, then API returns a conflict/error response indicating it's already archived",
    "criteria": [
      {
        "id": "AC-002-04-01",
        "text": "Given an active, existing note, when the archive endpoint is called, then API returns 200, marks the note archived, and its data remains intact"
      },
      {
        "id": "AC-002-04-02",
        "text": "Given an already-archived note, when the archive endpoint is called again, then API returns a conflict/error response indicating it's already archived"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-08",
    "title": "[error] the archive endpoint is called again",
    "story": "US-002-04",
    "epic": "EP-002",
    "preconditions": "an already-archived note",
    "steps": "the archive endpoint is called again",
    "expectedResult": "API returns a conflict/error response indicating it's already archived",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given an active, existing note, when the archive endpoint is called, then API returns 200, marks the note archived, and its data remains intact; Given an already-archived note, when the archive endpoint is called again, then API returns a conflict/error response indicating it's already archived",
    "criteria": [
      {
        "id": "AC-002-04-01",
        "text": "Given an active, existing note, when the archive endpoint is called, then API returns 200, marks the note archived, and its data remains intact"
      },
      {
        "id": "AC-002-04-02",
        "text": "Given an already-archived note, when the archive endpoint is called again, then API returns a conflict/error response indicating it's already archived"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-09",
    "title": "the unarchive endpoint is called",
    "story": "US-002-05",
    "epic": "EP-002",
    "preconditions": "an archived note",
    "steps": "the unarchive endpoint is called",
    "expectedResult": "API returns 200 and the note's status changes to active with all original data preserved",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given an archived note, when the unarchive endpoint is called, then API returns 200 and the note's status changes to active with all original data preserved; Given a note ID that does not exist, when the unarchive endpoint is called, then API returns 404",
    "criteria": [
      {
        "id": "AC-002-05-01",
        "text": "Given an archived note, when the unarchive endpoint is called, then API returns 200 and the note's status changes to active with all original data preserved"
      },
      {
        "id": "AC-002-05-02",
        "text": "Given a note ID that does not exist, when the unarchive endpoint is called, then API returns 404"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-10",
    "title": "[error] the unarchive endpoint is called",
    "story": "US-002-05",
    "epic": "EP-002",
    "preconditions": "a note ID that does not exist",
    "steps": "the unarchive endpoint is called",
    "expectedResult": "API returns 404",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given an archived note, when the unarchive endpoint is called, then API returns 200 and the note's status changes to active with all original data preserved; Given a note ID that does not exist, when the unarchive endpoint is called, then API returns 404",
    "criteria": [
      {
        "id": "AC-002-05-01",
        "text": "Given an archived note, when the unarchive endpoint is called, then API returns 200 and the note's status changes to active with all original data preserved"
      },
      {
        "id": "AC-002-05-02",
        "text": "Given a note ID that does not exist, when the unarchive endpoint is called, then API returns 404"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-19",
    "title": "GET is called on the note endpoint",
    "story": "US-002-06",
    "epic": "EP-002",
    "preconditions": "an existing note ID",
    "steps": "GET is called on the note endpoint",
    "expectedResult": "API returns 200 with full note details including tags, archive status and the pinned flag",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given an existing note ID, when GET is called on the note endpoint, then API returns 200 with full note details including tags, archive status and the pinned flag; Given an ID for a deleted or never-existing note, when GET is called on that note endpoint, then API returns 404 with no partial data exposed",
    "criteria": [
      {
        "id": "AC-002-06-01",
        "text": "Given an existing note ID, when GET is called on the note endpoint, then API returns 200 with full note details including tags, archive status and the pinned flag"
      },
      {
        "id": "AC-002-06-02",
        "text": "Given an ID for a deleted or never-existing note, when GET is called on that note endpoint, then API returns 404 with no partial data exposed"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-002-20",
    "title": "GET is called on that note endpoint",
    "story": "US-002-06",
    "epic": "EP-002",
    "preconditions": "an ID for a deleted or never-existing note",
    "steps": "GET is called on that note endpoint",
    "expectedResult": "API returns 404 with no partial data exposed",
    "module": "note-lifecycle-api--create--edit--tag--a",
    "acceptanceCriteria": "Given an existing note ID, when GET is called on the note endpoint, then API returns 200 with full note details including tags, archive status and the pinned flag; Given an ID for a deleted or never-existing note, when GET is called on that note endpoint, then API returns 404 with no partial data exposed",
    "criteria": [
      {
        "id": "AC-002-06-01",
        "text": "Given an existing note ID, when GET is called on the note endpoint, then API returns 200 with full note details including tags, archive status and the pinned flag"
      },
      {
        "id": "AC-002-06-02",
        "text": "Given an ID for a deleted or never-existing note, when GET is called on that note endpoint, then API returns 404 with no partial data exposed"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-19",
    "title": "GET /notes/search?q=keyword is called",
    "story": "US-003-01",
    "epic": "EP-003",
    "preconditions": "notes exist matching the keyword",
    "steps": "GET /notes/search?q=keyword is called",
    "expectedResult": "matching notes are returned with pinned notes first, then ordered by relevance, with 200 status",
    "module": "note-search",
    "acceptanceCriteria": "Given notes exist matching the keyword, when GET /notes/search?q=keyword is called, then matching notes are returned with pinned notes first, then ordered by relevance, with 200 status; Given no notes match the keyword, when the search is executed, then an empty array is returned with 200 status, not an error; Given the q query parameter is missing or empty, when the search request is made, then API returns 400 with a validation error message",
    "criteria": [
      {
        "id": "AC-003-01-01",
        "text": "Given notes exist matching the keyword, when GET /notes/search?q=keyword is called, then matching notes are returned with pinned notes first, then ordered by relevance, with 200 status"
      },
      {
        "id": "AC-003-01-02",
        "text": "Given no notes match the keyword, when the search is executed, then an empty array is returned with 200 status, not an error"
      },
      {
        "id": "AC-003-01-03",
        "text": "Given the q query parameter is missing or empty, when the search request is made, then API returns 400 with a validation error message"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-20",
    "title": "the search is executed",
    "story": "US-003-01",
    "epic": "EP-003",
    "preconditions": "no notes match the keyword",
    "steps": "the search is executed",
    "expectedResult": "an empty array is returned with 200 status, not an error",
    "module": "note-search",
    "acceptanceCriteria": "Given notes exist matching the keyword, when GET /notes/search?q=keyword is called, then matching notes are returned with pinned notes first, then ordered by relevance, with 200 status; Given no notes match the keyword, when the search is executed, then an empty array is returned with 200 status, not an error; Given the q query parameter is missing or empty, when the search request is made, then API returns 400 with a validation error message",
    "criteria": [
      {
        "id": "AC-003-01-01",
        "text": "Given notes exist matching the keyword, when GET /notes/search?q=keyword is called, then matching notes are returned with pinned notes first, then ordered by relevance, with 200 status"
      },
      {
        "id": "AC-003-01-02",
        "text": "Given no notes match the keyword, when the search is executed, then an empty array is returned with 200 status, not an error"
      },
      {
        "id": "AC-003-01-03",
        "text": "Given the q query parameter is missing or empty, when the search request is made, then API returns 400 with a validation error message"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-21",
    "title": "the search request is made",
    "story": "US-003-01",
    "epic": "EP-003",
    "preconditions": "the q query parameter is missing or empty",
    "steps": "the search request is made",
    "expectedResult": "API returns 400 with a validation error message",
    "module": "note-search",
    "acceptanceCriteria": "Given notes exist matching the keyword, when GET /notes/search?q=keyword is called, then matching notes are returned with pinned notes first, then ordered by relevance, with 200 status; Given no notes match the keyword, when the search is executed, then an empty array is returned with 200 status, not an error; Given the q query parameter is missing or empty, when the search request is made, then API returns 400 with a validation error message",
    "criteria": [
      {
        "id": "AC-003-01-01",
        "text": "Given notes exist matching the keyword, when GET /notes/search?q=keyword is called, then matching notes are returned with pinned notes first, then ordered by relevance, with 200 status"
      },
      {
        "id": "AC-003-01-02",
        "text": "Given no notes match the keyword, when the search is executed, then an empty array is returned with 200 status, not an error"
      },
      {
        "id": "AC-003-01-03",
        "text": "Given the q query parameter is missing or empty, when the search request is made, then API returns 400 with a validation error message"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-04",
    "title": "GET /notes/search?tags=project-x is called",
    "story": "US-003-02",
    "epic": "EP-003",
    "preconditions": "notes tagged 'project-x' exist",
    "steps": "GET /notes/search?tags=project-x is called",
    "expectedResult": "only notes with that tag are returned",
    "module": "note-search",
    "acceptanceCriteria": "Given notes tagged 'project-x' exist, when GET /notes/search?tags=project-x is called, then only notes with that tag are returned; Given a tag with no matching notes, when the search is executed, then an empty result set is returned with 200 status; Given the tags parameter contains invalid characters, when the search request is made, then API returns 400 with an error detail",
    "criteria": [
      {
        "id": "AC-003-02-01",
        "text": "Given notes tagged 'project-x' exist, when GET /notes/search?tags=project-x is called, then only notes with that tag are returned"
      },
      {
        "id": "AC-003-02-02",
        "text": "Given a tag with no matching notes, when the search is executed, then an empty result set is returned with 200 status"
      },
      {
        "id": "AC-003-02-03",
        "text": "Given the tags parameter contains invalid characters, when the search request is made, then API returns 400 with an error detail"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-05",
    "title": "[edge] the search is executed",
    "story": "US-003-02",
    "epic": "EP-003",
    "preconditions": "a tag with no matching notes",
    "steps": "the search is executed",
    "expectedResult": "an empty result set is returned with 200 status",
    "module": "note-search",
    "acceptanceCriteria": "Given notes tagged 'project-x' exist, when GET /notes/search?tags=project-x is called, then only notes with that tag are returned; Given a tag with no matching notes, when the search is executed, then an empty result set is returned with 200 status; Given the tags parameter contains invalid characters, when the search request is made, then API returns 400 with an error detail",
    "criteria": [
      {
        "id": "AC-003-02-01",
        "text": "Given notes tagged 'project-x' exist, when GET /notes/search?tags=project-x is called, then only notes with that tag are returned"
      },
      {
        "id": "AC-003-02-02",
        "text": "Given a tag with no matching notes, when the search is executed, then an empty result set is returned with 200 status"
      },
      {
        "id": "AC-003-02-03",
        "text": "Given the tags parameter contains invalid characters, when the search request is made, then API returns 400 with an error detail"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-06",
    "title": "[error] the search request is made",
    "story": "US-003-02",
    "epic": "EP-003",
    "preconditions": "the tags parameter contains invalid characters",
    "steps": "the search request is made",
    "expectedResult": "API returns 400 with an error detail",
    "module": "note-search",
    "acceptanceCriteria": "Given notes tagged 'project-x' exist, when GET /notes/search?tags=project-x is called, then only notes with that tag are returned; Given a tag with no matching notes, when the search is executed, then an empty result set is returned with 200 status; Given the tags parameter contains invalid characters, when the search request is made, then API returns 400 with an error detail",
    "criteria": [
      {
        "id": "AC-003-02-01",
        "text": "Given notes tagged 'project-x' exist, when GET /notes/search?tags=project-x is called, then only notes with that tag are returned"
      },
      {
        "id": "AC-003-02-02",
        "text": "Given a tag with no matching notes, when the search is executed, then an empty result set is returned with 200 status"
      },
      {
        "id": "AC-003-02-03",
        "text": "Given the tags parameter contains invalid characters, when the search request is made, then API returns 400 with an error detail"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-28",
    "title": "the request is executed",
    "story": "US-003-03",
    "epic": "EP-003",
    "preconditions": "a search request under 50 requests per second load",
    "steps": "the request is executed",
    "expectedResult": "p95 response latency is under 300ms",
    "module": "note-search",
    "acceptanceCriteria": "Given a search request under 50 requests per second load, when the request is executed, then p95 response latency is under 300ms; Given a large dataset (e.g., 100k notes), when a search is executed, then the latency SLA is still met via indexed lookup; Given the search index is degraded or unavailable, when a search is requested, then API returns 503 with a retry-after header instead of hanging",
    "criteria": [
      {
        "id": "AC-003-03-01",
        "text": "Given a search request under 50 requests per second load, when the request is executed, then p95 response latency is under 300ms"
      },
      {
        "id": "AC-003-03-02",
        "text": "Given a large dataset (e.g., 100k notes), when a search is executed, then the latency SLA is still met via indexed lookup"
      },
      {
        "id": "AC-003-03-03",
        "text": "Given the search index is degraded or unavailable, when a search is requested, then API returns 503 with a retry-after header instead of hanging"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-29",
    "title": "[edge] a search is executed",
    "story": "US-003-03",
    "epic": "EP-003",
    "preconditions": "a large dataset (e.g., 100k notes)",
    "steps": "a search is executed",
    "expectedResult": "the latency SLA is still met via indexed lookup",
    "module": "note-search",
    "acceptanceCriteria": "Given a search request under 50 requests per second load, when the request is executed, then p95 response latency is under 300ms; Given a large dataset (e.g., 100k notes), when a search is executed, then the latency SLA is still met via indexed lookup; Given the search index is degraded or unavailable, when a search is requested, then API returns 503 with a retry-after header instead of hanging",
    "criteria": [
      {
        "id": "AC-003-03-01",
        "text": "Given a search request under 50 requests per second load, when the request is executed, then p95 response latency is under 300ms"
      },
      {
        "id": "AC-003-03-02",
        "text": "Given a large dataset (e.g., 100k notes), when a search is executed, then the latency SLA is still met via indexed lookup"
      },
      {
        "id": "AC-003-03-03",
        "text": "Given the search index is degraded or unavailable, when a search is requested, then API returns 503 with a retry-after header instead of hanging"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-30",
    "title": "[error] a search is requested",
    "story": "US-003-03",
    "epic": "EP-003",
    "preconditions": "the search index is degraded or unavailable",
    "steps": "a search is requested",
    "expectedResult": "API returns 503 with a retry-after header instead of hanging",
    "module": "note-search",
    "acceptanceCriteria": "Given a search request under 50 requests per second load, when the request is executed, then p95 response latency is under 300ms; Given a large dataset (e.g., 100k notes), when a search is executed, then the latency SLA is still met via indexed lookup; Given the search index is degraded or unavailable, when a search is requested, then API returns 503 with a retry-after header instead of hanging",
    "criteria": [
      {
        "id": "AC-003-03-01",
        "text": "Given a search request under 50 requests per second load, when the request is executed, then p95 response latency is under 300ms"
      },
      {
        "id": "AC-003-03-02",
        "text": "Given a large dataset (e.g., 100k notes), when a search is executed, then the latency SLA is still met via indexed lookup"
      },
      {
        "id": "AC-003-03-03",
        "text": "Given the search index is degraded or unavailable, when a search is requested, then API returns 503 with a retry-after header instead of hanging"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-10",
    "title": "the search is executed",
    "story": "US-003-04",
    "epic": "EP-003",
    "preconditions": "q=budget and tags=finance are both supplied",
    "steps": "the search is executed",
    "expectedResult": "only notes matching both criteria are returned",
    "module": "note-search",
    "acceptanceCriteria": "Given q=budget and tags=finance are both supplied, when the search is executed, then only notes matching both criteria are returned; Given the keyword matches but the tag filter excludes all notes, when the search is executed, then an empty result set is returned; Given a malformed filter combination is supplied, when the search request is made, then API returns 400 with error details",
    "criteria": [
      {
        "id": "AC-003-04-01",
        "text": "Given q=budget and tags=finance are both supplied, when the search is executed, then only notes matching both criteria are returned"
      },
      {
        "id": "AC-003-04-02",
        "text": "Given the keyword matches but the tag filter excludes all notes, when the search is executed, then an empty result set is returned"
      },
      {
        "id": "AC-003-04-03",
        "text": "Given a malformed filter combination is supplied, when the search request is made, then API returns 400 with error details"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-11",
    "title": "[edge] the search is executed",
    "story": "US-003-04",
    "epic": "EP-003",
    "preconditions": "the keyword matches but the tag filter excludes all notes",
    "steps": "the search is executed",
    "expectedResult": "an empty result set is returned",
    "module": "note-search",
    "acceptanceCriteria": "Given q=budget and tags=finance are both supplied, when the search is executed, then only notes matching both criteria are returned; Given the keyword matches but the tag filter excludes all notes, when the search is executed, then an empty result set is returned; Given a malformed filter combination is supplied, when the search request is made, then API returns 400 with error details",
    "criteria": [
      {
        "id": "AC-003-04-01",
        "text": "Given q=budget and tags=finance are both supplied, when the search is executed, then only notes matching both criteria are returned"
      },
      {
        "id": "AC-003-04-02",
        "text": "Given the keyword matches but the tag filter excludes all notes, when the search is executed, then an empty result set is returned"
      },
      {
        "id": "AC-003-04-03",
        "text": "Given a malformed filter combination is supplied, when the search request is made, then API returns 400 with error details"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-12",
    "title": "[error] the search request is made",
    "story": "US-003-04",
    "epic": "EP-003",
    "preconditions": "a malformed filter combination is supplied",
    "steps": "the search request is made",
    "expectedResult": "API returns 400 with error details",
    "module": "note-search",
    "acceptanceCriteria": "Given q=budget and tags=finance are both supplied, when the search is executed, then only notes matching both criteria are returned; Given the keyword matches but the tag filter excludes all notes, when the search is executed, then an empty result set is returned; Given a malformed filter combination is supplied, when the search request is made, then API returns 400 with error details",
    "criteria": [
      {
        "id": "AC-003-04-01",
        "text": "Given q=budget and tags=finance are both supplied, when the search is executed, then only notes matching both criteria are returned"
      },
      {
        "id": "AC-003-04-02",
        "text": "Given the keyword matches but the tag filter excludes all notes, when the search is executed, then an empty result set is returned"
      },
      {
        "id": "AC-003-04-03",
        "text": "Given a malformed filter combination is supplied, when the search request is made, then API returns 400 with error details"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-13",
    "title": "page and limit params are supplied",
    "story": "US-003-05",
    "epic": "EP-003",
    "preconditions": "more results exist than the page size",
    "steps": "page and limit params are supplied",
    "expectedResult": "the correct subset is returned along with a total count",
    "module": "note-search",
    "acceptanceCriteria": "Given more results exist than the page size, when page and limit params are supplied, then the correct subset is returned along with a total count; Given a page number beyond available results, when the search is executed, then an empty array is returned with 200 status; Given negative or non-numeric page/limit values, when the search request is made, then API returns 400 with a validation error",
    "criteria": [
      {
        "id": "AC-003-05-01",
        "text": "Given more results exist than the page size, when page and limit params are supplied, then the correct subset is returned along with a total count"
      },
      {
        "id": "AC-003-05-02",
        "text": "Given a page number beyond available results, when the search is executed, then an empty array is returned with 200 status"
      },
      {
        "id": "AC-003-05-03",
        "text": "Given negative or non-numeric page/limit values, when the search request is made, then API returns 400 with a validation error"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-14",
    "title": "[edge] the search is executed",
    "story": "US-003-05",
    "epic": "EP-003",
    "preconditions": "a page number beyond available results",
    "steps": "the search is executed",
    "expectedResult": "an empty array is returned with 200 status",
    "module": "note-search",
    "acceptanceCriteria": "Given more results exist than the page size, when page and limit params are supplied, then the correct subset is returned along with a total count; Given a page number beyond available results, when the search is executed, then an empty array is returned with 200 status; Given negative or non-numeric page/limit values, when the search request is made, then API returns 400 with a validation error",
    "criteria": [
      {
        "id": "AC-003-05-01",
        "text": "Given more results exist than the page size, when page and limit params are supplied, then the correct subset is returned along with a total count"
      },
      {
        "id": "AC-003-05-02",
        "text": "Given a page number beyond available results, when the search is executed, then an empty array is returned with 200 status"
      },
      {
        "id": "AC-003-05-03",
        "text": "Given negative or non-numeric page/limit values, when the search request is made, then API returns 400 with a validation error"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-15",
    "title": "[error] the search request is made",
    "story": "US-003-05",
    "epic": "EP-003",
    "preconditions": "negative or non-numeric page/limit values",
    "steps": "the search request is made",
    "expectedResult": "API returns 400 with a validation error",
    "module": "note-search",
    "acceptanceCriteria": "Given more results exist than the page size, when page and limit params are supplied, then the correct subset is returned along with a total count; Given a page number beyond available results, when the search is executed, then an empty array is returned with 200 status; Given negative or non-numeric page/limit values, when the search request is made, then API returns 400 with a validation error",
    "criteria": [
      {
        "id": "AC-003-05-01",
        "text": "Given more results exist than the page size, when page and limit params are supplied, then the correct subset is returned along with a total count"
      },
      {
        "id": "AC-003-05-02",
        "text": "Given a page number beyond available results, when the search is executed, then an empty array is returned with 200 status"
      },
      {
        "id": "AC-003-05-03",
        "text": "Given negative or non-numeric page/limit values, when the search request is made, then API returns 400 with a validation error"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-16",
    "title": "a default search is executed",
    "story": "US-003-06",
    "epic": "EP-003",
    "preconditions": "both archived and active notes exist",
    "steps": "a default search is executed",
    "expectedResult": "only active notes are returned",
    "module": "note-search",
    "acceptanceCriteria": "Given both archived and active notes exist, when a default search is executed, then only active notes are returned; Given include_archived=true is passed, when the search is executed, then archived notes are included in results; Given an invalid include_archived value is supplied, when the search request is made, then API returns 400 with an error message",
    "criteria": [
      {
        "id": "AC-003-06-01",
        "text": "Given both archived and active notes exist, when a default search is executed, then only active notes are returned"
      },
      {
        "id": "AC-003-06-02",
        "text": "Given include_archived=true is passed, when the search is executed, then archived notes are included in results"
      },
      {
        "id": "AC-003-06-03",
        "text": "Given an invalid include_archived value is supplied, when the search request is made, then API returns 400 with an error message"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-17",
    "title": "[edge] the search is executed",
    "story": "US-003-06",
    "epic": "EP-003",
    "preconditions": "include_archived=true is passed",
    "steps": "the search is executed",
    "expectedResult": "archived notes are included in results",
    "module": "note-search",
    "acceptanceCriteria": "Given both archived and active notes exist, when a default search is executed, then only active notes are returned; Given include_archived=true is passed, when the search is executed, then archived notes are included in results; Given an invalid include_archived value is supplied, when the search request is made, then API returns 400 with an error message",
    "criteria": [
      {
        "id": "AC-003-06-01",
        "text": "Given both archived and active notes exist, when a default search is executed, then only active notes are returned"
      },
      {
        "id": "AC-003-06-02",
        "text": "Given include_archived=true is passed, when the search is executed, then archived notes are included in results"
      },
      {
        "id": "AC-003-06-03",
        "text": "Given an invalid include_archived value is supplied, when the search request is made, then API returns 400 with an error message"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-003-18",
    "title": "[error] the search request is made",
    "story": "US-003-06",
    "epic": "EP-003",
    "preconditions": "an invalid include_archived value is supplied",
    "steps": "the search request is made",
    "expectedResult": "API returns 400 with an error message",
    "module": "note-search",
    "acceptanceCriteria": "Given both archived and active notes exist, when a default search is executed, then only active notes are returned; Given include_archived=true is passed, when the search is executed, then archived notes are included in results; Given an invalid include_archived value is supplied, when the search request is made, then API returns 400 with an error message",
    "criteria": [
      {
        "id": "AC-003-06-01",
        "text": "Given both archived and active notes exist, when a default search is executed, then only active notes are returned"
      },
      {
        "id": "AC-003-06-02",
        "text": "Given include_archived=true is passed, when the search is executed, then archived notes are included in results"
      },
      {
        "id": "AC-003-06-03",
        "text": "Given an invalid include_archived value is supplied, when the search request is made, then API returns 400 with an error message"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-23",
    "title": "client requests the OpenAPI document",
    "story": "US-004-01",
    "epic": "EP-004",
    "preconditions": "notes_api is deployed",
    "steps": "client requests the OpenAPI document",
    "expectedResult": "a valid OpenAPI 3.x JSON/YAML document is returned listing all CRUD, tagging, search, archive, and email one-time-code login (send and verify) endpoints",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given notes_api is deployed, when client requests the OpenAPI document, then a valid OpenAPI 3.x JSON/YAML document is returned listing all CRUD, tagging, search, archive, and email one-time-code login (send and verify) endpoints; Given a new endpoint is added to notes_api, when the spec is regenerated/published, then the new endpoint appears in the OpenAPI document before or at the same time it becomes callable; Given the OpenAPI endpoint is requested with an unsupported Accept header, when client requests the spec, then API returns a 406 or a default-format fallback with a clear content-type",
    "criteria": [
      {
        "id": "AC-004-01-01",
        "text": "Given notes_api is deployed, when client requests the OpenAPI document, then a valid OpenAPI 3.x JSON/YAML document is returned listing all CRUD, tagging, search, archive, and email one-time-code login (send and verify) endpoints"
      },
      {
        "id": "AC-004-01-02",
        "text": "Given a new endpoint is added to notes_api, when the spec is regenerated/published, then the new endpoint appears in the OpenAPI document before or at the same time it becomes callable"
      },
      {
        "id": "AC-004-01-03",
        "text": "Given the OpenAPI endpoint is requested with an unsupported Accept header, when client requests the spec, then API returns a 406 or a default-format fallback with a clear content-type"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-24",
    "title": "the spec is regenerated/published",
    "story": "US-004-01",
    "epic": "EP-004",
    "preconditions": "a new endpoint is added to notes_api",
    "steps": "the spec is regenerated/published",
    "expectedResult": "the new endpoint appears in the OpenAPI document before or at the same time it becomes callable",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given notes_api is deployed, when client requests the OpenAPI document, then a valid OpenAPI 3.x JSON/YAML document is returned listing all CRUD, tagging, search, archive, and email one-time-code login (send and verify) endpoints; Given a new endpoint is added to notes_api, when the spec is regenerated/published, then the new endpoint appears in the OpenAPI document before or at the same time it becomes callable; Given the OpenAPI endpoint is requested with an unsupported Accept header, when client requests the spec, then API returns a 406 or a default-format fallback with a clear content-type",
    "criteria": [
      {
        "id": "AC-004-01-01",
        "text": "Given notes_api is deployed, when client requests the OpenAPI document, then a valid OpenAPI 3.x JSON/YAML document is returned listing all CRUD, tagging, search, archive, and email one-time-code login (send and verify) endpoints"
      },
      {
        "id": "AC-004-01-02",
        "text": "Given a new endpoint is added to notes_api, when the spec is regenerated/published, then the new endpoint appears in the OpenAPI document before or at the same time it becomes callable"
      },
      {
        "id": "AC-004-01-03",
        "text": "Given the OpenAPI endpoint is requested with an unsupported Accept header, when client requests the spec, then API returns a 406 or a default-format fallback with a clear content-type"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-25",
    "title": "client requests the spec",
    "story": "US-004-01",
    "epic": "EP-004",
    "preconditions": "the OpenAPI endpoint is requested with an unsupported Accept header",
    "steps": "client requests the spec",
    "expectedResult": "API returns a 406 or a default-format fallback with a clear content-type",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given notes_api is deployed, when client requests the OpenAPI document, then a valid OpenAPI 3.x JSON/YAML document is returned listing all CRUD, tagging, search, archive, and email one-time-code login (send and verify) endpoints; Given a new endpoint is added to notes_api, when the spec is regenerated/published, then the new endpoint appears in the OpenAPI document before or at the same time it becomes callable; Given the OpenAPI endpoint is requested with an unsupported Accept header, when client requests the spec, then API returns a 406 or a default-format fallback with a clear content-type",
    "criteria": [
      {
        "id": "AC-004-01-01",
        "text": "Given notes_api is deployed, when client requests the OpenAPI document, then a valid OpenAPI 3.x JSON/YAML document is returned listing all CRUD, tagging, search, archive, and email one-time-code login (send and verify) endpoints"
      },
      {
        "id": "AC-004-01-02",
        "text": "Given a new endpoint is added to notes_api, when the spec is regenerated/published, then the new endpoint appears in the OpenAPI document before or at the same time it becomes callable"
      },
      {
        "id": "AC-004-01-03",
        "text": "Given the OpenAPI endpoint is requested with an unsupported Accept header, when client requests the spec, then API returns a 406 or a default-format fallback with a clear content-type"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-04",
    "title": "the schema is inspected in the spec",
    "story": "US-004-02",
    "epic": "EP-004",
    "preconditions": "an endpoint accepts a note payload",
    "steps": "the schema is inspected in the spec",
    "expectedResult": "all required fields, types, and constraints (e.g., title length, tag format) are documented",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given an endpoint accepts a note payload, when the schema is inspected in the spec, then all required fields, types, and constraints (e.g., title length, tag format) are documented; Given an endpoint supports optional query parameters (e.g., search filters), when the schema is reviewed, then optional parameters are marked optional with default values documented; Given a documented field type is changed in code, when the spec is not updated to match, then a spec-validation/CI check fails, flagging the mismatch",
    "criteria": [
      {
        "id": "AC-004-02-01",
        "text": "Given an endpoint accepts a note payload, when the schema is inspected in the spec, then all required fields, types, and constraints (e.g., title length, tag format) are documented"
      },
      {
        "id": "AC-004-02-02",
        "text": "Given an endpoint supports optional query parameters (e.g., search filters), when the schema is reviewed, then optional parameters are marked optional with default values documented"
      },
      {
        "id": "AC-004-02-03",
        "text": "Given a documented field type is changed in code, when the spec is not updated to match, then a spec-validation/CI check fails, flagging the mismatch"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-05",
    "title": "[edge] the schema is reviewed",
    "story": "US-004-02",
    "epic": "EP-004",
    "preconditions": "an endpoint supports optional query parameters (e.g., search filters)",
    "steps": "the schema is reviewed",
    "expectedResult": "optional parameters are marked optional with default values documented",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given an endpoint accepts a note payload, when the schema is inspected in the spec, then all required fields, types, and constraints (e.g., title length, tag format) are documented; Given an endpoint supports optional query parameters (e.g., search filters), when the schema is reviewed, then optional parameters are marked optional with default values documented; Given a documented field type is changed in code, when the spec is not updated to match, then a spec-validation/CI check fails, flagging the mismatch",
    "criteria": [
      {
        "id": "AC-004-02-01",
        "text": "Given an endpoint accepts a note payload, when the schema is inspected in the spec, then all required fields, types, and constraints (e.g., title length, tag format) are documented"
      },
      {
        "id": "AC-004-02-02",
        "text": "Given an endpoint supports optional query parameters (e.g., search filters), when the schema is reviewed, then optional parameters are marked optional with default values documented"
      },
      {
        "id": "AC-004-02-03",
        "text": "Given a documented field type is changed in code, when the spec is not updated to match, then a spec-validation/CI check fails, flagging the mismatch"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-06",
    "title": "[error] the spec is not updated to match",
    "story": "US-004-02",
    "epic": "EP-004",
    "preconditions": "a documented field type is changed in code",
    "steps": "the spec is not updated to match",
    "expectedResult": "a spec-validation/CI check fails, flagging the mismatch",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given an endpoint accepts a note payload, when the schema is inspected in the spec, then all required fields, types, and constraints (e.g., title length, tag format) are documented; Given an endpoint supports optional query parameters (e.g., search filters), when the schema is reviewed, then optional parameters are marked optional with default values documented; Given a documented field type is changed in code, when the spec is not updated to match, then a spec-validation/CI check fails, flagging the mismatch",
    "criteria": [
      {
        "id": "AC-004-02-01",
        "text": "Given an endpoint accepts a note payload, when the schema is inspected in the spec, then all required fields, types, and constraints (e.g., title length, tag format) are documented"
      },
      {
        "id": "AC-004-02-02",
        "text": "Given an endpoint supports optional query parameters (e.g., search filters), when the schema is reviewed, then optional parameters are marked optional with default values documented"
      },
      {
        "id": "AC-004-02-03",
        "text": "Given a documented field type is changed in code, when the spec is not updated to match, then a spec-validation/CI check fails, flagging the mismatch"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-128",
    "title": "the spec is reviewed",
    "story": "US-004-03",
    "epic": "EP-004",
    "preconditions": "an endpoint can return validation errors",
    "steps": "the spec is reviewed",
    "expectedResult": "a 400 response schema with error structure/fields is documented",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given an endpoint can return validation errors, when the spec is reviewed, then a 400 response schema with error structure/fields is documented; Given an endpoint requires authentication/authorization or is an email one-time-code login endpoint, when the spec is reviewed, then 401/403 responses are documented with expected body shape, and the send/verify login endpoints document a TBD (asked: Q-BA-627430) response for more than 3 requests within 10 minutes and a locked-account response after 5 wrong codes, locked for 15 minutes; Given a documented field type is changed in code, when the 404 response is checked against the spec, then documented 404 response matches the actual API behavior",
    "criteria": [
      {
        "id": "AC-004-03-01",
        "text": "Given an endpoint can return validation errors, when the spec is reviewed, then a 400 response schema with error structure/fields is documented"
      },
      {
        "id": "AC-004-03-02",
        "text": "Given an endpoint requires authentication/authorization or is an email one-time-code login endpoint, when the spec is reviewed, then 401/403 responses are documented with expected body shape, and the send/verify login endpoints document a TBD (asked: Q-BA-627430) response for more than 3 requests within 10 minutes and a locked-account response after 5 wrong codes, locked for 15 minutes"
      },
      {
        "id": "AC-004-03-03",
        "text": "Given a documented field type is changed in code, when the 404 response is checked against the spec, then documented 404 response matches the actual API behavior"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-129",
    "title": "the spec is reviewed",
    "story": "US-004-03",
    "epic": "EP-004",
    "preconditions": "an endpoint requires authentication/authorization or is an email one-time-code login endpoint",
    "steps": "the spec is reviewed",
    "expectedResult": "401/403 responses are documented with expected body shape, and the send/verify login endpoints document a TBD (asked: Q-BA-627430) response for more than 3 requests within 10 minutes and a locked-account response after 5 wrong codes, locked for 15 minutes",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given an endpoint can return validation errors, when the spec is reviewed, then a 400 response schema with error structure/fields is documented; Given an endpoint requires authentication/authorization or is an email one-time-code login endpoint, when the spec is reviewed, then 401/403 responses are documented with expected body shape, and the send/verify login endpoints document a TBD (asked: Q-BA-627430) response for more than 3 requests within 10 minutes and a locked-account response after 5 wrong codes, locked for 15 minutes; Given a documented field type is changed in code, when the 404 response is checked against the spec, then documented 404 response matches the actual API behavior",
    "criteria": [
      {
        "id": "AC-004-03-01",
        "text": "Given an endpoint can return validation errors, when the spec is reviewed, then a 400 response schema with error structure/fields is documented"
      },
      {
        "id": "AC-004-03-02",
        "text": "Given an endpoint requires authentication/authorization or is an email one-time-code login endpoint, when the spec is reviewed, then 401/403 responses are documented with expected body shape, and the send/verify login endpoints document a TBD (asked: Q-BA-627430) response for more than 3 requests within 10 minutes and a locked-account response after 5 wrong codes, locked for 15 minutes"
      },
      {
        "id": "AC-004-03-03",
        "text": "Given a documented field type is changed in code, when the 404 response is checked against the spec, then documented 404 response matches the actual API behavior"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-130",
    "title": "the 404 response is checked against the spec",
    "story": "US-004-03",
    "epic": "EP-004",
    "preconditions": "a documented field type is changed in code",
    "steps": "the 404 response is checked against the spec",
    "expectedResult": "documented 404 response matches the actual API behavior",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given an endpoint can return validation errors, when the spec is reviewed, then a 400 response schema with error structure/fields is documented; Given an endpoint requires authentication/authorization or is an email one-time-code login endpoint, when the spec is reviewed, then 401/403 responses are documented with expected body shape, and the send/verify login endpoints document a TBD (asked: Q-BA-627430) response for more than 3 requests within 10 minutes and a locked-account response after 5 wrong codes, locked for 15 minutes; Given a documented field type is changed in code, when the 404 response is checked against the spec, then documented 404 response matches the actual API behavior",
    "criteria": [
      {
        "id": "AC-004-03-01",
        "text": "Given an endpoint can return validation errors, when the spec is reviewed, then a 400 response schema with error structure/fields is documented"
      },
      {
        "id": "AC-004-03-02",
        "text": "Given an endpoint requires authentication/authorization or is an email one-time-code login endpoint, when the spec is reviewed, then 401/403 responses are documented with expected body shape, and the send/verify login endpoints document a TBD (asked: Q-BA-627430) response for more than 3 requests within 10 minutes and a locked-account response after 5 wrong codes, locked for 15 minutes"
      },
      {
        "id": "AC-004-03-03",
        "text": "Given a documented field type is changed in code, when the 404 response is checked against the spec, then documented 404 response matches the actual API behavior"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-10",
    "title": "the coverage check runs",
    "story": "US-004-04",
    "epic": "EP-004",
    "preconditions": "all implemented endpoints are documented",
    "steps": "the coverage check runs",
    "expectedResult": "it reports 100% coverage",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given all implemented endpoints are documented, when the coverage check runs, then it reports 100% coverage; Given one endpoint is implemented but missing from the spec, when the coverage check runs, then it reports less than 100% and names the missing endpoint; Given the spec file is malformed, when the coverage check runs, then it fails with a clear parsing error rather than reporting false coverage",
    "criteria": [
      {
        "id": "AC-004-04-01",
        "text": "Given all implemented endpoints are documented, when the coverage check runs, then it reports 100% coverage"
      },
      {
        "id": "AC-004-04-02",
        "text": "Given one endpoint is implemented but missing from the spec, when the coverage check runs, then it reports less than 100% and names the missing endpoint"
      },
      {
        "id": "AC-004-04-03",
        "text": "Given the spec file is malformed, when the coverage check runs, then it fails with a clear parsing error rather than reporting false coverage"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-11",
    "title": "[edge] the coverage check runs",
    "story": "US-004-04",
    "epic": "EP-004",
    "preconditions": "one endpoint is implemented but missing from the spec",
    "steps": "the coverage check runs",
    "expectedResult": "it reports less than 100% and names the missing endpoint",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given all implemented endpoints are documented, when the coverage check runs, then it reports 100% coverage; Given one endpoint is implemented but missing from the spec, when the coverage check runs, then it reports less than 100% and names the missing endpoint; Given the spec file is malformed, when the coverage check runs, then it fails with a clear parsing error rather than reporting false coverage",
    "criteria": [
      {
        "id": "AC-004-04-01",
        "text": "Given all implemented endpoints are documented, when the coverage check runs, then it reports 100% coverage"
      },
      {
        "id": "AC-004-04-02",
        "text": "Given one endpoint is implemented but missing from the spec, when the coverage check runs, then it reports less than 100% and names the missing endpoint"
      },
      {
        "id": "AC-004-04-03",
        "text": "Given the spec file is malformed, when the coverage check runs, then it fails with a clear parsing error rather than reporting false coverage"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-12",
    "title": "[error] the coverage check runs",
    "story": "US-004-04",
    "epic": "EP-004",
    "preconditions": "the spec file is malformed",
    "steps": "the coverage check runs",
    "expectedResult": "it fails with a clear parsing error rather than reporting false coverage",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given all implemented endpoints are documented, when the coverage check runs, then it reports 100% coverage; Given one endpoint is implemented but missing from the spec, when the coverage check runs, then it reports less than 100% and names the missing endpoint; Given the spec file is malformed, when the coverage check runs, then it fails with a clear parsing error rather than reporting false coverage",
    "criteria": [
      {
        "id": "AC-004-04-01",
        "text": "Given all implemented endpoints are documented, when the coverage check runs, then it reports 100% coverage"
      },
      {
        "id": "AC-004-04-02",
        "text": "Given one endpoint is implemented but missing from the spec, when the coverage check runs, then it reports less than 100% and names the missing endpoint"
      },
      {
        "id": "AC-004-04-03",
        "text": "Given the spec file is malformed, when the coverage check runs, then it fails with a clear parsing error rather than reporting false coverage"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-13",
    "title": "a developer views the spec",
    "story": "US-004-05",
    "epic": "EP-004",
    "preconditions": "the search endpoint is documented",
    "steps": "a developer views the spec",
    "expectedResult": "an example request with query params and an example response body are shown",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given the search endpoint is documented, when a developer views the spec, then an example request with query params and an example response body are shown; Given search returns zero results, when the example responses are reviewed, then an example empty-result response is included",
    "criteria": [
      {
        "id": "AC-004-05-01",
        "text": "Given the search endpoint is documented, when a developer views the spec, then an example request with query params and an example response body are shown"
      },
      {
        "id": "AC-004-05-02",
        "text": "Given search returns zero results, when the example responses are reviewed, then an example empty-result response is included"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-14",
    "title": "[edge] the example responses are reviewed",
    "story": "US-004-05",
    "epic": "EP-004",
    "preconditions": "search returns zero results",
    "steps": "the example responses are reviewed",
    "expectedResult": "an example empty-result response is included",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given the search endpoint is documented, when a developer views the spec, then an example request with query params and an example response body are shown; Given search returns zero results, when the example responses are reviewed, then an example empty-result response is included",
    "criteria": [
      {
        "id": "AC-004-05-01",
        "text": "Given the search endpoint is documented, when a developer views the spec, then an example request with query params and an example response body are shown"
      },
      {
        "id": "AC-004-05-02",
        "text": "Given search returns zero results, when the example responses are reviewed, then an example empty-result response is included"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-15",
    "title": "an operations engineer reviews it",
    "story": "US-004-06",
    "epic": "EP-004",
    "preconditions": "the API documentation includes a data-handling note",
    "steps": "an operations engineer reviews it",
    "expectedResult": "it states that all note data is stored and served within the EU",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given the API documentation includes a data-handling note, when an operations engineer reviews it, then it states that all note data is stored and served within the EU; Given archive endpoints behave differently from active-note endpoints regarding storage location, when documentation is reviewed, then any difference is explicitly called out",
    "criteria": [
      {
        "id": "AC-004-06-01",
        "text": "Given the API documentation includes a data-handling note, when an operations engineer reviews it, then it states that all note data is stored and served within the EU"
      },
      {
        "id": "AC-004-06-02",
        "text": "Given archive endpoints behave differently from active-note endpoints regarding storage location, when documentation is reviewed, then any difference is explicitly called out"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-004-16",
    "title": "[edge] documentation is reviewed",
    "story": "US-004-06",
    "epic": "EP-004",
    "preconditions": "archive endpoints behave differently from active-note endpoints regarding storage location",
    "steps": "documentation is reviewed",
    "expectedResult": "any difference is explicitly called out",
    "module": "api-documentation---integration-readines",
    "acceptanceCriteria": "Given the API documentation includes a data-handling note, when an operations engineer reviews it, then it states that all note data is stored and served within the EU; Given archive endpoints behave differently from active-note endpoints regarding storage location, when documentation is reviewed, then any difference is explicitly called out",
    "criteria": [
      {
        "id": "AC-004-06-01",
        "text": "Given the API documentation includes a data-handling note, when an operations engineer reviews it, then it states that all note data is stored and served within the EU"
      },
      {
        "id": "AC-004-06-02",
        "text": "Given archive endpoints behave differently from active-note endpoints regarding storage location, when documentation is reviewed, then any difference is explicitly called out"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-005-01",
    "title": "a reader views the 'Scope' section",
    "story": "US-005-01",
    "epic": "EP-005",
    "preconditions": "the published API documentation",
    "steps": "a reader views the 'Scope' section",
    "expectedResult": "mobile apps, attachments, and real-time collaboration are listed as explicitly excluded",
    "module": "scope-guardrails--no-mobile--attachments",
    "acceptanceCriteria": "Given the published API documentation, when a reader views the 'Scope' section, then mobile apps, attachments, and real-time collaboration are listed as explicitly excluded; Given a documentation update is proposed, when a new out-of-scope item is added or changed, then the change requires review/approval before merge; Given the docs build pipeline, when the Scope section is missing from a build, then the documentation build fails with a lint/validation error",
    "criteria": [
      {
        "id": "AC-005-01-01",
        "text": "Given the published API documentation, when a reader views the 'Scope' section, then mobile apps, attachments, and real-time collaboration are listed as explicitly excluded"
      },
      {
        "id": "AC-005-01-02",
        "text": "Given a documentation update is proposed, when a new out-of-scope item is added or changed, then the change requires review/approval before merge"
      },
      {
        "id": "AC-005-01-03",
        "text": "Given the docs build pipeline, when the Scope section is missing from a build, then the documentation build fails with a lint/validation error"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-005-02",
    "title": "[edge] a new out-of-scope item is added or changed",
    "story": "US-005-01",
    "epic": "EP-005",
    "preconditions": "a documentation update is proposed",
    "steps": "a new out-of-scope item is added or changed",
    "expectedResult": "the change requires review/approval before merge",
    "module": "scope-guardrails--no-mobile--attachments",
    "acceptanceCriteria": "Given the published API documentation, when a reader views the 'Scope' section, then mobile apps, attachments, and real-time collaboration are listed as explicitly excluded; Given a documentation update is proposed, when a new out-of-scope item is added or changed, then the change requires review/approval before merge; Given the docs build pipeline, when the Scope section is missing from a build, then the documentation build fails with a lint/validation error",
    "criteria": [
      {
        "id": "AC-005-01-01",
        "text": "Given the published API documentation, when a reader views the 'Scope' section, then mobile apps, attachments, and real-time collaboration are listed as explicitly excluded"
      },
      {
        "id": "AC-005-01-02",
        "text": "Given a documentation update is proposed, when a new out-of-scope item is added or changed, then the change requires review/approval before merge"
      },
      {
        "id": "AC-005-01-03",
        "text": "Given the docs build pipeline, when the Scope section is missing from a build, then the documentation build fails with a lint/validation error"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-005-03",
    "title": "[error] the Scope section is missing from a build",
    "story": "US-005-01",
    "epic": "EP-005",
    "preconditions": "the docs build pipeline",
    "steps": "the Scope section is missing from a build",
    "expectedResult": "the documentation build fails with a lint/validation error",
    "module": "scope-guardrails--no-mobile--attachments",
    "acceptanceCriteria": "Given the published API documentation, when a reader views the 'Scope' section, then mobile apps, attachments, and real-time collaboration are listed as explicitly excluded; Given a documentation update is proposed, when a new out-of-scope item is added or changed, then the change requires review/approval before merge; Given the docs build pipeline, when the Scope section is missing from a build, then the documentation build fails with a lint/validation error",
    "criteria": [
      {
        "id": "AC-005-01-01",
        "text": "Given the published API documentation, when a reader views the 'Scope' section, then mobile apps, attachments, and real-time collaboration are listed as explicitly excluded"
      },
      {
        "id": "AC-005-01-02",
        "text": "Given a documentation update is proposed, when a new out-of-scope item is added or changed, then the change requires review/approval before merge"
      },
      {
        "id": "AC-005-01-03",
        "text": "Given the docs build pipeline, when the Scope section is missing from a build, then the documentation build fails with a lint/validation error"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-005-18",
    "title": "a request is sent to any attachment path (e.g. POST /notes/{id}/attachments)",
    "story": "US-005-02",
    "epic": "EP-005",
    "preconditions": "the deployed API route registry",
    "steps": "a request is sent to any attachment path (e.g. POST /notes/{id}/attachments)",
    "expectedResult": "the API returns 404 Not Found",
    "module": "scope-guardrails--no-mobile--attachments",
    "acceptanceCriteria": "Given the deployed API route registry, when a request is sent to any attachment path (e.g. POST /notes/{id}/attachments), then the API returns 404 Not Found; Given a note create/update endpoint, when a multipart/form-data request with a file payload is submitted, then the API returns 415 and persists no file content",
    "criteria": [
      {
        "id": "AC-005-02-01",
        "text": "Given the deployed API route registry, when a request is sent to any attachment path (e.g. POST /notes/{id}/attachments), then the API returns 404 Not Found"
      },
      {
        "id": "AC-005-02-02",
        "text": "Given a note create/update endpoint, when a multipart/form-data request with a file payload is submitted, then the API returns 415 and persists no file content"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-005-19",
    "title": "[error] a multipart/form-data request with a file payload is submitted",
    "story": "US-005-02",
    "epic": "EP-005",
    "preconditions": "a note create/update endpoint",
    "steps": "a multipart/form-data request with a file payload is submitted",
    "expectedResult": "the API returns 415 and persists no file content",
    "module": "scope-guardrails--no-mobile--attachments",
    "acceptanceCriteria": "Given the deployed API route registry, when a request is sent to any attachment path (e.g. POST /notes/{id}/attachments), then the API returns 404 Not Found; Given a note create/update endpoint, when a multipart/form-data request with a file payload is submitted, then the API returns 415 and persists no file content",
    "criteria": [
      {
        "id": "AC-005-02-01",
        "text": "Given the deployed API route registry, when a request is sent to any attachment path (e.g. POST /notes/{id}/attachments), then the API returns 404 Not Found"
      },
      {
        "id": "AC-005-02-02",
        "text": "Given a note create/update endpoint, when a multipart/form-data request with a file payload is submitted, then the API returns 415 and persists no file content"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-005-06",
    "title": "a client attempts a WebSocket upgrade or SSE connection to any notes_api endpoin",
    "story": "US-005-03",
    "epic": "EP-005",
    "preconditions": "the running API server",
    "steps": "a client attempts a WebSocket upgrade or SSE connection to any notes_api endpoint",
    "expectedResult": "the connection is rejected (no protocol upgrade, standard error status returned)",
    "module": "scope-guardrails--no-mobile--attachments",
    "acceptanceCriteria": "Given the running API server, when a client attempts a WebSocket upgrade or SSE connection to any notes_api endpoint, then the connection is rejected (no protocol upgrade, standard error status returned); Given two clients submit PATCH requests to the same note concurrently, when both requests are processed, then the API applies a standard last-write/conflict response with no live broadcast or merge logic",
    "criteria": [
      {
        "id": "AC-005-03-01",
        "text": "Given the running API server, when a client attempts a WebSocket upgrade or SSE connection to any notes_api endpoint, then the connection is rejected (no protocol upgrade, standard error status returned)"
      },
      {
        "id": "AC-005-03-02",
        "text": "Given two clients submit PATCH requests to the same note concurrently, when both requests are processed, then the API applies a standard last-write/conflict response with no live broadcast or merge logic"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-005-07",
    "title": "[edge] both requests are processed",
    "story": "US-005-03",
    "epic": "EP-005",
    "preconditions": "two clients submit PATCH requests to the same note concurrently",
    "steps": "both requests are processed",
    "expectedResult": "the API applies a standard last-write/conflict response with no live broadcast or merge logic",
    "module": "scope-guardrails--no-mobile--attachments",
    "acceptanceCriteria": "Given the running API server, when a client attempts a WebSocket upgrade or SSE connection to any notes_api endpoint, then the connection is rejected (no protocol upgrade, standard error status returned); Given two clients submit PATCH requests to the same note concurrently, when both requests are processed, then the API applies a standard last-write/conflict response with no live broadcast or merge logic",
    "criteria": [
      {
        "id": "AC-005-03-01",
        "text": "Given the running API server, when a client attempts a WebSocket upgrade or SSE connection to any notes_api endpoint, then the connection is rejected (no protocol upgrade, standard error status returned)"
      },
      {
        "id": "AC-005-03-02",
        "text": "Given two clients submit PATCH requests to the same note concurrently, when both requests are processed, then the API applies a standard last-write/conflict response with no live broadcast or merge logic"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-005-08",
    "title": "the request is made",
    "story": "US-005-04",
    "epic": "EP-005",
    "preconditions": "an integrator calls an out-of-scope endpoint",
    "steps": "the request is made",
    "expectedResult": "the response body includes a message referencing the scope exclusion rather than a bare 404",
    "module": "scope-guardrails--no-mobile--attachments",
    "acceptanceCriteria": "Given an integrator calls an out-of-scope endpoint, when the request is made, then the response body includes a message referencing the scope exclusion rather than a bare 404; Given an integrator sends a multipart file upload to a notes endpoint, when the request is validated, then the response returns a structured error code (e.g. OUT_OF_SCOPE) with a human-readable reason",
    "criteria": [
      {
        "id": "AC-005-04-01",
        "text": "Given an integrator calls an out-of-scope endpoint, when the request is made, then the response body includes a message referencing the scope exclusion rather than a bare 404"
      },
      {
        "id": "AC-005-04-02",
        "text": "Given an integrator sends a multipart file upload to a notes endpoint, when the request is validated, then the response returns a structured error code (e.g. OUT_OF_SCOPE) with a human-readable reason"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-005-09",
    "title": "[error] the request is validated",
    "story": "US-005-04",
    "epic": "EP-005",
    "preconditions": "an integrator sends a multipart file upload to a notes endpoint",
    "steps": "the request is validated",
    "expectedResult": "the response returns a structured error code (e.g. OUT_OF_SCOPE) with a human-readable reason",
    "module": "scope-guardrails--no-mobile--attachments",
    "acceptanceCriteria": "Given an integrator calls an out-of-scope endpoint, when the request is made, then the response body includes a message referencing the scope exclusion rather than a bare 404; Given an integrator sends a multipart file upload to a notes endpoint, when the request is validated, then the response returns a structured error code (e.g. OUT_OF_SCOPE) with a human-readable reason",
    "criteria": [
      {
        "id": "AC-005-04-01",
        "text": "Given an integrator calls an out-of-scope endpoint, when the request is made, then the response body includes a message referencing the scope exclusion rather than a bare 404"
      },
      {
        "id": "AC-005-04-02",
        "text": "Given an integrator sends a multipart file upload to a notes endpoint, when the request is validated, then the response returns a structured error code (e.g. OUT_OF_SCOPE) with a human-readable reason"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-005-10",
    "title": "the scope-guardrail test suite executes against the OpenAPI spec",
    "story": "US-005-05",
    "epic": "EP-005",
    "preconditions": "the CI pipeline runs",
    "steps": "the scope-guardrail test suite executes against the OpenAPI spec",
    "expectedResult": "it passes only when zero endpoints match excluded categories (mobile, attachments, real-time)",
    "module": "scope-guardrails--no-mobile--attachments",
    "acceptanceCriteria": "Given the CI pipeline runs, when the scope-guardrail test suite executes against the OpenAPI spec, then it passes only when zero endpoints match excluded categories (mobile, attachments, real-time); Given a developer adds a new endpoint matching an excluded pattern (e.g. /attachments or /ws), when CI runs on the pull request, then the guardrail test fails and blocks the merge",
    "criteria": [
      {
        "id": "AC-005-05-01",
        "text": "Given the CI pipeline runs, when the scope-guardrail test suite executes against the OpenAPI spec, then it passes only when zero endpoints match excluded categories (mobile, attachments, real-time)"
      },
      {
        "id": "AC-005-05-02",
        "text": "Given a developer adds a new endpoint matching an excluded pattern (e.g. /attachments or /ws), when CI runs on the pull request, then the guardrail test fails and blocks the merge"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-005-11",
    "title": "[edge] CI runs on the pull request",
    "story": "US-005-05",
    "epic": "EP-005",
    "preconditions": "a developer adds a new endpoint matching an excluded pattern (e.g. /attachments or /ws)",
    "steps": "CI runs on the pull request",
    "expectedResult": "the guardrail test fails and blocks the merge",
    "module": "scope-guardrails--no-mobile--attachments",
    "acceptanceCriteria": "Given the CI pipeline runs, when the scope-guardrail test suite executes against the OpenAPI spec, then it passes only when zero endpoints match excluded categories (mobile, attachments, real-time); Given a developer adds a new endpoint matching an excluded pattern (e.g. /attachments or /ws), when CI runs on the pull request, then the guardrail test fails and blocks the merge",
    "criteria": [
      {
        "id": "AC-005-05-01",
        "text": "Given the CI pipeline runs, when the scope-guardrail test suite executes against the OpenAPI spec, then it passes only when zero endpoints match excluded categories (mobile, attachments, real-time)"
      },
      {
        "id": "AC-005-05-02",
        "text": "Given a developer adds a new endpoint matching an excluded pattern (e.g. /attachments or /ws), when CI runs on the pull request, then the guardrail test fails and blocks the merge"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-005-12",
    "title": "the PM logs the request",
    "story": "US-005-06",
    "epic": "EP-005",
    "preconditions": "a stakeholder submits a request for mobile app support",
    "steps": "the PM logs the request",
    "expectedResult": "it is recorded with status 'rejected - out of scope' and linked to the EP-005 guardrail documentation",
    "module": "scope-guardrails--no-mobile--attachments",
    "acceptanceCriteria": "Given a stakeholder submits a request for mobile app support, when the PM logs the request, then it is recorded with status 'rejected - out of scope' and linked to the EP-005 guardrail documentation; Given a request is ambiguous (e.g. 'notes with images' could imply attachments), when the PM triages it, then the request is classified against the documented exclusion criteria before a decision is logged",
    "criteria": [
      {
        "id": "AC-005-06-01",
        "text": "Given a stakeholder submits a request for mobile app support, when the PM logs the request, then it is recorded with status 'rejected - out of scope' and linked to the EP-005 guardrail documentation"
      },
      {
        "id": "AC-005-06-02",
        "text": "Given a request is ambiguous (e.g. 'notes with images' could imply attachments), when the PM triages it, then the request is classified against the documented exclusion criteria before a decision is logged"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-005-13",
    "title": "[edge] the PM triages it",
    "story": "US-005-06",
    "epic": "EP-005",
    "preconditions": "a request is ambiguous (e.g. 'notes with images' could imply attachments)",
    "steps": "the PM triages it",
    "expectedResult": "the request is classified against the documented exclusion criteria before a decision is logged",
    "module": "scope-guardrails--no-mobile--attachments",
    "acceptanceCriteria": "Given a stakeholder submits a request for mobile app support, when the PM logs the request, then it is recorded with status 'rejected - out of scope' and linked to the EP-005 guardrail documentation; Given a request is ambiguous (e.g. 'notes with images' could imply attachments), when the PM triages it, then the request is classified against the documented exclusion criteria before a decision is logged",
    "criteria": [
      {
        "id": "AC-005-06-01",
        "text": "Given a stakeholder submits a request for mobile app support, when the PM logs the request, then it is recorded with status 'rejected - out of scope' and linked to the EP-005 guardrail documentation"
      },
      {
        "id": "AC-005-06-02",
        "text": "Given a request is ambiguous (e.g. 'notes with images' could imply attachments), when the PM triages it, then the request is classified against the documented exclusion criteria before a decision is logged"
      }
    ],
    "created_at": "2026-10-11T13:26:50.721177+00:00"
  },
  {
    "testId": "TC-006-01",
    "title": "I open the web app notes list page",
    "story": "US-006-01",
    "epic": "EP-006",
    "preconditions": "notes exist in the system",
    "steps": "I open the web app notes list page",
    "expectedResult": "I see each note's title and an archived/active indicator",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given notes exist in the system, when I open the web app notes list page, then I see each note's title and an archived/active indicator; Given no notes exist, when I open the notes list page, then I see an empty-state message rather than an error; Given the API request for the notes list fails, when the list page loads, then an error message is shown instead of a blank/broken page",
    "criteria": [
      {
        "id": "AC-006-01-01",
        "text": "Given notes exist in the system, when I open the web app notes list page, then I see each note's title and an archived/active indicator"
      },
      {
        "id": "AC-006-01-02",
        "text": "Given no notes exist, when I open the notes list page, then I see an empty-state message rather than an error"
      },
      {
        "id": "AC-006-01-03",
        "text": "Given the API request for the notes list fails, when the list page loads, then an error message is shown instead of a blank/broken page"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-02",
    "title": "I open the notes list page",
    "story": "US-006-01",
    "epic": "EP-006",
    "preconditions": "no notes exist",
    "steps": "I open the notes list page",
    "expectedResult": "I see an empty-state message rather than an error",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given notes exist in the system, when I open the web app notes list page, then I see each note's title and an archived/active indicator; Given no notes exist, when I open the notes list page, then I see an empty-state message rather than an error; Given the API request for the notes list fails, when the list page loads, then an error message is shown instead of a blank/broken page",
    "criteria": [
      {
        "id": "AC-006-01-01",
        "text": "Given notes exist in the system, when I open the web app notes list page, then I see each note's title and an archived/active indicator"
      },
      {
        "id": "AC-006-01-02",
        "text": "Given no notes exist, when I open the notes list page, then I see an empty-state message rather than an error"
      },
      {
        "id": "AC-006-01-03",
        "text": "Given the API request for the notes list fails, when the list page loads, then an error message is shown instead of a blank/broken page"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-03",
    "title": "the list page loads",
    "story": "US-006-01",
    "epic": "EP-006",
    "preconditions": "the API request for the notes list fails",
    "steps": "the list page loads",
    "expectedResult": "an error message is shown instead of a blank/broken page",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given notes exist in the system, when I open the web app notes list page, then I see each note's title and an archived/active indicator; Given no notes exist, when I open the notes list page, then I see an empty-state message rather than an error; Given the API request for the notes list fails, when the list page loads, then an error message is shown instead of a blank/broken page",
    "criteria": [
      {
        "id": "AC-006-01-01",
        "text": "Given notes exist in the system, when I open the web app notes list page, then I see each note's title and an archived/active indicator"
      },
      {
        "id": "AC-006-01-02",
        "text": "Given no notes exist, when I open the notes list page, then I see an empty-state message rather than an error"
      },
      {
        "id": "AC-006-01-03",
        "text": "Given the API request for the notes list fails, when the list page loads, then an error message is shown instead of a blank/broken page"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-04",
    "title": "I save",
    "story": "US-006-02",
    "epic": "EP-006",
    "preconditions": "I open the editor and enter a title and body",
    "steps": "I save",
    "expectedResult": "the note is created via the API and appears in the notes list",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given I open the editor and enter a title and body, when I save, then the note is created via the API and appears in the notes list; Given I open an existing note in the editor and change its body, when I save, then the note is updated via the API and the editor shows the updated content; Given I submit the editor with an empty title, when I try to save, then the editor shows a validation error and no API call is made",
    "criteria": [
      {
        "id": "AC-006-02-01",
        "text": "Given I open the editor and enter a title and body, when I save, then the note is created via the API and appears in the notes list"
      },
      {
        "id": "AC-006-02-02",
        "text": "Given I open an existing note in the editor and change its body, when I save, then the note is updated via the API and the editor shows the updated content"
      },
      {
        "id": "AC-006-02-03",
        "text": "Given I submit the editor with an empty title, when I try to save, then the editor shows a validation error and no API call is made"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-05",
    "title": "I save",
    "story": "US-006-02",
    "epic": "EP-006",
    "preconditions": "I open an existing note in the editor and change its body",
    "steps": "I save",
    "expectedResult": "the note is updated via the API and the editor shows the updated content",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given I open the editor and enter a title and body, when I save, then the note is created via the API and appears in the notes list; Given I open an existing note in the editor and change its body, when I save, then the note is updated via the API and the editor shows the updated content; Given I submit the editor with an empty title, when I try to save, then the editor shows a validation error and no API call is made",
    "criteria": [
      {
        "id": "AC-006-02-01",
        "text": "Given I open the editor and enter a title and body, when I save, then the note is created via the API and appears in the notes list"
      },
      {
        "id": "AC-006-02-02",
        "text": "Given I open an existing note in the editor and change its body, when I save, then the note is updated via the API and the editor shows the updated content"
      },
      {
        "id": "AC-006-02-03",
        "text": "Given I submit the editor with an empty title, when I try to save, then the editor shows a validation error and no API call is made"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-06",
    "title": "I try to save",
    "story": "US-006-02",
    "epic": "EP-006",
    "preconditions": "I submit the editor with an empty title",
    "steps": "I try to save",
    "expectedResult": "the editor shows a validation error and no API call is made",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given I open the editor and enter a title and body, when I save, then the note is created via the API and appears in the notes list; Given I open an existing note in the editor and change its body, when I save, then the note is updated via the API and the editor shows the updated content; Given I submit the editor with an empty title, when I try to save, then the editor shows a validation error and no API call is made",
    "criteria": [
      {
        "id": "AC-006-02-01",
        "text": "Given I open the editor and enter a title and body, when I save, then the note is created via the API and appears in the notes list"
      },
      {
        "id": "AC-006-02-02",
        "text": "Given I open an existing note in the editor and change its body, when I save, then the note is updated via the API and the editor shows the updated content"
      },
      {
        "id": "AC-006-02-03",
        "text": "Given I submit the editor with an empty title, when I try to save, then the editor shows a validation error and no API call is made"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-07",
    "title": "I submit the search",
    "story": "US-006-03",
    "epic": "EP-006",
    "preconditions": "I enter a keyword into the search box",
    "steps": "I submit the search",
    "expectedResult": "the web app calls the search endpoint and displays matching notes",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given I enter a keyword into the search box, when I submit the search, then the web app calls the search endpoint and displays matching notes; Given I filter by a tag in the search box, when I submit the search, then only notes with that tag are displayed; Given my search returns no results, when the search completes, then the web app shows a 'no results' message rather than an empty blank area",
    "criteria": [
      {
        "id": "AC-006-03-01",
        "text": "Given I enter a keyword into the search box, when I submit the search, then the web app calls the search endpoint and displays matching notes"
      },
      {
        "id": "AC-006-03-02",
        "text": "Given I filter by a tag in the search box, when I submit the search, then only notes with that tag are displayed"
      },
      {
        "id": "AC-006-03-03",
        "text": "Given my search returns no results, when the search completes, then the web app shows a 'no results' message rather than an empty blank area"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-08",
    "title": "I submit the search",
    "story": "US-006-03",
    "epic": "EP-006",
    "preconditions": "I filter by a tag in the search box",
    "steps": "I submit the search",
    "expectedResult": "only notes with that tag are displayed",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given I enter a keyword into the search box, when I submit the search, then the web app calls the search endpoint and displays matching notes; Given I filter by a tag in the search box, when I submit the search, then only notes with that tag are displayed; Given my search returns no results, when the search completes, then the web app shows a 'no results' message rather than an empty blank area",
    "criteria": [
      {
        "id": "AC-006-03-01",
        "text": "Given I enter a keyword into the search box, when I submit the search, then the web app calls the search endpoint and displays matching notes"
      },
      {
        "id": "AC-006-03-02",
        "text": "Given I filter by a tag in the search box, when I submit the search, then only notes with that tag are displayed"
      },
      {
        "id": "AC-006-03-03",
        "text": "Given my search returns no results, when the search completes, then the web app shows a 'no results' message rather than an empty blank area"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-09",
    "title": "the search completes",
    "story": "US-006-03",
    "epic": "EP-006",
    "preconditions": "my search returns no results",
    "steps": "the search completes",
    "expectedResult": "the web app shows a 'no results' message rather than an empty blank area",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given I enter a keyword into the search box, when I submit the search, then the web app calls the search endpoint and displays matching notes; Given I filter by a tag in the search box, when I submit the search, then only notes with that tag are displayed; Given my search returns no results, when the search completes, then the web app shows a 'no results' message rather than an empty blank area",
    "criteria": [
      {
        "id": "AC-006-03-01",
        "text": "Given I enter a keyword into the search box, when I submit the search, then the web app calls the search endpoint and displays matching notes"
      },
      {
        "id": "AC-006-03-02",
        "text": "Given I filter by a tag in the search box, when I submit the search, then only notes with that tag are displayed"
      },
      {
        "id": "AC-006-03-03",
        "text": "Given my search returns no results, when the search completes, then the web app shows a 'no results' message rather than an empty blank area"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-10",
    "title": "I add a tag and save",
    "story": "US-006-04",
    "epic": "EP-006",
    "preconditions": "I am editing a note",
    "steps": "I add a tag and save",
    "expectedResult": "the tag appears on the note in both the editor and the notes list",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given I am editing a note, when I add a tag and save, then the tag appears on the note in both the editor and the notes list; Given I remove a tag from a note in the editor, when I save, then the tag no longer appears on that note; Given I try to add a tag that already exists on the note, when I save, then no duplicate tag is created and the UI reflects a single instance",
    "criteria": [
      {
        "id": "AC-006-04-01",
        "text": "Given I am editing a note, when I add a tag and save, then the tag appears on the note in both the editor and the notes list"
      },
      {
        "id": "AC-006-04-02",
        "text": "Given I remove a tag from a note in the editor, when I save, then the tag no longer appears on that note"
      },
      {
        "id": "AC-006-04-03",
        "text": "Given I try to add a tag that already exists on the note, when I save, then no duplicate tag is created and the UI reflects a single instance"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-11",
    "title": "I save",
    "story": "US-006-04",
    "epic": "EP-006",
    "preconditions": "I remove a tag from a note in the editor",
    "steps": "I save",
    "expectedResult": "the tag no longer appears on that note",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given I am editing a note, when I add a tag and save, then the tag appears on the note in both the editor and the notes list; Given I remove a tag from a note in the editor, when I save, then the tag no longer appears on that note; Given I try to add a tag that already exists on the note, when I save, then no duplicate tag is created and the UI reflects a single instance",
    "criteria": [
      {
        "id": "AC-006-04-01",
        "text": "Given I am editing a note, when I add a tag and save, then the tag appears on the note in both the editor and the notes list"
      },
      {
        "id": "AC-006-04-02",
        "text": "Given I remove a tag from a note in the editor, when I save, then the tag no longer appears on that note"
      },
      {
        "id": "AC-006-04-03",
        "text": "Given I try to add a tag that already exists on the note, when I save, then no duplicate tag is created and the UI reflects a single instance"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-12",
    "title": "I save",
    "story": "US-006-04",
    "epic": "EP-006",
    "preconditions": "I try to add a tag that already exists on the note",
    "steps": "I save",
    "expectedResult": "no duplicate tag is created and the UI reflects a single instance",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given I am editing a note, when I add a tag and save, then the tag appears on the note in both the editor and the notes list; Given I remove a tag from a note in the editor, when I save, then the tag no longer appears on that note; Given I try to add a tag that already exists on the note, when I save, then no duplicate tag is created and the UI reflects a single instance",
    "criteria": [
      {
        "id": "AC-006-04-01",
        "text": "Given I am editing a note, when I add a tag and save, then the tag appears on the note in both the editor and the notes list"
      },
      {
        "id": "AC-006-04-02",
        "text": "Given I remove a tag from a note in the editor, when I save, then the tag no longer appears on that note"
      },
      {
        "id": "AC-006-04-03",
        "text": "Given I try to add a tag that already exists on the note, when I save, then no duplicate tag is created and the UI reflects a single instance"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-13",
    "title": "I click archive",
    "story": "US-006-05",
    "epic": "EP-006",
    "preconditions": "an active note is open",
    "steps": "I click archive",
    "expectedResult": "the note is marked archived via the API and removed from the default notes list view",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given an active note is open, when I click archive, then the note is marked archived via the API and removed from the default notes list view; Given an archived note is viewed in an archived-notes view, when I click unarchive, then the note becomes active again and reappears in the default list; Given an archive or unarchive API call fails, when the action is attempted, then the web app shows an error and the note's displayed status remains unchanged",
    "criteria": [
      {
        "id": "AC-006-05-01",
        "text": "Given an active note is open, when I click archive, then the note is marked archived via the API and removed from the default notes list view"
      },
      {
        "id": "AC-006-05-02",
        "text": "Given an archived note is viewed in an archived-notes view, when I click unarchive, then the note becomes active again and reappears in the default list"
      },
      {
        "id": "AC-006-05-03",
        "text": "Given an archive or unarchive API call fails, when the action is attempted, then the web app shows an error and the note's displayed status remains unchanged"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-14",
    "title": "I click unarchive",
    "story": "US-006-05",
    "epic": "EP-006",
    "preconditions": "an archived note is viewed in an archived-notes view",
    "steps": "I click unarchive",
    "expectedResult": "the note becomes active again and reappears in the default list",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given an active note is open, when I click archive, then the note is marked archived via the API and removed from the default notes list view; Given an archived note is viewed in an archived-notes view, when I click unarchive, then the note becomes active again and reappears in the default list; Given an archive or unarchive API call fails, when the action is attempted, then the web app shows an error and the note's displayed status remains unchanged",
    "criteria": [
      {
        "id": "AC-006-05-01",
        "text": "Given an active note is open, when I click archive, then the note is marked archived via the API and removed from the default notes list view"
      },
      {
        "id": "AC-006-05-02",
        "text": "Given an archived note is viewed in an archived-notes view, when I click unarchive, then the note becomes active again and reappears in the default list"
      },
      {
        "id": "AC-006-05-03",
        "text": "Given an archive or unarchive API call fails, when the action is attempted, then the web app shows an error and the note's displayed status remains unchanged"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-15",
    "title": "the action is attempted",
    "story": "US-006-05",
    "epic": "EP-006",
    "preconditions": "an archive or unarchive API call fails",
    "steps": "the action is attempted",
    "expectedResult": "the web app shows an error and the note's displayed status remains unchanged",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given an active note is open, when I click archive, then the note is marked archived via the API and removed from the default notes list view; Given an archived note is viewed in an archived-notes view, when I click unarchive, then the note becomes active again and reappears in the default list; Given an archive or unarchive API call fails, when the action is attempted, then the web app shows an error and the note's displayed status remains unchanged",
    "criteria": [
      {
        "id": "AC-006-05-01",
        "text": "Given an active note is open, when I click archive, then the note is marked archived via the API and removed from the default notes list view"
      },
      {
        "id": "AC-006-05-02",
        "text": "Given an archived note is viewed in an archived-notes view, when I click unarchive, then the note becomes active again and reappears in the default list"
      },
      {
        "id": "AC-006-05-03",
        "text": "Given an archive or unarchive API call fails, when the action is attempted, then the web app shows an error and the note's displayed status remains unchanged"
      }
    ],
    "created_at": "2026-10-13T00:45:28.221168+00:00"
  },
  {
    "testId": "TC-006-70",
    "title": "I request a new code more than 3 requests within 10 minutes",
    "story": "US-006-06",
    "epic": "EP-006",
    "preconditions": "I enter my email on the sign-in page",
    "steps": "I request a new code more than 3 requests within 10 minutes",
    "expectedResult": "the app shows a rate-limit message and sends no further code",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given I enter my email on the sign-in page, when I request a new code more than 3 requests within 10 minutes, then the app shows a rate-limit message and sends no further code; Given I submit a one-time code more than 10 minutes after it was issued, when I try to sign in, then sign-in is rejected with an expired-code message; Given I have entered 5 wrong codes, when I try again, then my account is locked for 15 minutes and the app shows a locked-account message",
    "criteria": [
      {
        "id": "AC-006-06-01",
        "text": "Given I enter my email on the sign-in page, when I request a new code more than 3 requests within 10 minutes, then the app shows a rate-limit message and sends no further code"
      },
      {
        "id": "AC-006-06-02",
        "text": "Given I submit a one-time code more than 10 minutes after it was issued, when I try to sign in, then sign-in is rejected with an expired-code message"
      },
      {
        "id": "AC-006-06-03",
        "text": "Given I have entered 5 wrong codes, when I try again, then my account is locked for 15 minutes and the app shows a locked-account message"
      }
    ],
    "created_at": "2026-10-27T21:33:04.043885+00:00"
  },
  {
    "testId": "TC-006-71",
    "title": "I try to sign in",
    "story": "US-006-06",
    "epic": "EP-006",
    "preconditions": "I submit a one-time code more than 10 minutes after it was issued",
    "steps": "I try to sign in",
    "expectedResult": "sign-in is rejected with an expired-code message",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given I enter my email on the sign-in page, when I request a new code more than 3 requests within 10 minutes, then the app shows a rate-limit message and sends no further code; Given I submit a one-time code more than 10 minutes after it was issued, when I try to sign in, then sign-in is rejected with an expired-code message; Given I have entered 5 wrong codes, when I try again, then my account is locked for 15 minutes and the app shows a locked-account message",
    "criteria": [
      {
        "id": "AC-006-06-01",
        "text": "Given I enter my email on the sign-in page, when I request a new code more than 3 requests within 10 minutes, then the app shows a rate-limit message and sends no further code"
      },
      {
        "id": "AC-006-06-02",
        "text": "Given I submit a one-time code more than 10 minutes after it was issued, when I try to sign in, then sign-in is rejected with an expired-code message"
      },
      {
        "id": "AC-006-06-03",
        "text": "Given I have entered 5 wrong codes, when I try again, then my account is locked for 15 minutes and the app shows a locked-account message"
      }
    ],
    "created_at": "2026-10-27T21:33:04.043885+00:00"
  },
  {
    "testId": "TC-006-72",
    "title": "I try again",
    "story": "US-006-06",
    "epic": "EP-006",
    "preconditions": "I have entered 5 wrong codes",
    "steps": "I try again",
    "expectedResult": "my account is locked for 15 minutes and the app shows a locked-account message",
    "module": "web-application-front-end--notes-list--e",
    "acceptanceCriteria": "Given I enter my email on the sign-in page, when I request a new code more than 3 requests within 10 minutes, then the app shows a rate-limit message and sends no further code; Given I submit a one-time code more than 10 minutes after it was issued, when I try to sign in, then sign-in is rejected with an expired-code message; Given I have entered 5 wrong codes, when I try again, then my account is locked for 15 minutes and the app shows a locked-account message",
    "criteria": [
      {
        "id": "AC-006-06-01",
        "text": "Given I enter my email on the sign-in page, when I request a new code more than 3 requests within 10 minutes, then the app shows a rate-limit message and sends no further code"
      },
      {
        "id": "AC-006-06-02",
        "text": "Given I submit a one-time code more than 10 minutes after it was issued, when I try to sign in, then sign-in is rejected with an expired-code message"
      },
      {
        "id": "AC-006-06-03",
        "text": "Given I have entered 5 wrong codes, when I try again, then my account is locked for 15 minutes and the app shows a locked-account message"
      }
    ],
    "created_at": "2026-10-27T21:33:04.043885+00:00"
  }
]
