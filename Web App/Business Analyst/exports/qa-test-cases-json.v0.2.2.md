---
id: agent-ba/web_app/exports/qa_test_cases.json
type: export
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.2
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-ba/web_app/exports/qa_test_cases.json
depends_on: []
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: web_app:QA-TEST-CASES
---

[
  {
    "testId": "TC-001-01",
    "title": "POST /notes is called",
    "story": "US-001-01",
    "epic": "EP-001",
    "preconditions": "a valid title and body payload",
    "steps": "POST /notes is called",
    "expectedResult": "API returns 201 with the created note including its id",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given a valid title and body payload, when POST /notes is called, then API returns 201 with the created note including its id; Given a payload missing required title field, when POST /notes is called, then API returns 400 with a validation error and no note is created; Given a body field at the maximum allowed length, when POST /notes is called, then API returns 201 and stores the full body without truncation",
    "criteria": [
      {
        "id": "AC-001-01-01",
        "text": "Given a valid title and body payload, when POST /notes is called, then API returns 201 with the created note including its id"
      },
      {
        "id": "AC-001-01-02",
        "text": "Given a payload missing required title field, when POST /notes is called, then API returns 400 with a validation error and no note is created"
      },
      {
        "id": "AC-001-01-03",
        "text": "Given a body field at the maximum allowed length, when POST /notes is called, then API returns 201 and stores the full body without truncation"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-02",
    "title": "[error] POST /notes is called",
    "story": "US-001-01",
    "epic": "EP-001",
    "preconditions": "a payload missing required title field",
    "steps": "POST /notes is called",
    "expectedResult": "API returns 400 with a validation error and no note is created",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given a valid title and body payload, when POST /notes is called, then API returns 201 with the created note including its id; Given a payload missing required title field, when POST /notes is called, then API returns 400 with a validation error and no note is created; Given a body field at the maximum allowed length, when POST /notes is called, then API returns 201 and stores the full body without truncation",
    "criteria": [
      {
        "id": "AC-001-01-01",
        "text": "Given a valid title and body payload, when POST /notes is called, then API returns 201 with the created note including its id"
      },
      {
        "id": "AC-001-01-02",
        "text": "Given a payload missing required title field, when POST /notes is called, then API returns 400 with a validation error and no note is created"
      },
      {
        "id": "AC-001-01-03",
        "text": "Given a body field at the maximum allowed length, when POST /notes is called, then API returns 201 and stores the full body without truncation"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-03",
    "title": "[edge] POST /notes is called",
    "story": "US-001-01",
    "epic": "EP-001",
    "preconditions": "a body field at the maximum allowed length",
    "steps": "POST /notes is called",
    "expectedResult": "API returns 201 and stores the full body without truncation",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given a valid title and body payload, when POST /notes is called, then API returns 201 with the created note including its id; Given a payload missing required title field, when POST /notes is called, then API returns 400 with a validation error and no note is created; Given a body field at the maximum allowed length, when POST /notes is called, then API returns 201 and stores the full body without truncation",
    "criteria": [
      {
        "id": "AC-001-01-01",
        "text": "Given a valid title and body payload, when POST /notes is called, then API returns 201 with the created note including its id"
      },
      {
        "id": "AC-001-01-02",
        "text": "Given a payload missing required title field, when POST /notes is called, then API returns 400 with a validation error and no note is created"
      },
      {
        "id": "AC-001-01-03",
        "text": "Given a body field at the maximum allowed length, when POST /notes is called, then API returns 201 and stores the full body without truncation"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-04",
    "title": "PUT/PATCH /notes/{id} is called",
    "story": "US-001-02",
    "epic": "EP-001",
    "preconditions": "an existing note id and updated fields",
    "steps": "PUT/PATCH /notes/{id} is called",
    "expectedResult": "API returns 200 with the updated note reflecting changes",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given an existing note id and updated fields, when PUT/PATCH /notes/{id} is called, then API returns 200 with the updated note reflecting changes; Given a note id that does not exist, when PUT/PATCH /notes/{id} is called, then API returns 404 and no data is altered; Given a concurrent edit request on the same note, when two update requests are sent in quick succession, then the last accepted write wins and no fields are partially corrupted",
    "criteria": [
      {
        "id": "AC-001-02-01",
        "text": "Given an existing note id and updated fields, when PUT/PATCH /notes/{id} is called, then API returns 200 with the updated note reflecting changes"
      },
      {
        "id": "AC-001-02-02",
        "text": "Given a note id that does not exist, when PUT/PATCH /notes/{id} is called, then API returns 404 and no data is altered"
      },
      {
        "id": "AC-001-02-03",
        "text": "Given a concurrent edit request on the same note, when two update requests are sent in quick succession, then the last accepted write wins and no fields are partially corrupted"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-05",
    "title": "[error] PUT/PATCH /notes/{id} is called",
    "story": "US-001-02",
    "epic": "EP-001",
    "preconditions": "a note id that does not exist",
    "steps": "PUT/PATCH /notes/{id} is called",
    "expectedResult": "API returns 404 and no data is altered",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given an existing note id and updated fields, when PUT/PATCH /notes/{id} is called, then API returns 200 with the updated note reflecting changes; Given a note id that does not exist, when PUT/PATCH /notes/{id} is called, then API returns 404 and no data is altered; Given a concurrent edit request on the same note, when two update requests are sent in quick succession, then the last accepted write wins and no fields are partially corrupted",
    "criteria": [
      {
        "id": "AC-001-02-01",
        "text": "Given an existing note id and updated fields, when PUT/PATCH /notes/{id} is called, then API returns 200 with the updated note reflecting changes"
      },
      {
        "id": "AC-001-02-02",
        "text": "Given a note id that does not exist, when PUT/PATCH /notes/{id} is called, then API returns 404 and no data is altered"
      },
      {
        "id": "AC-001-02-03",
        "text": "Given a concurrent edit request on the same note, when two update requests are sent in quick succession, then the last accepted write wins and no fields are partially corrupted"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-06",
    "title": "[edge] two update requests are sent in quick succession",
    "story": "US-001-02",
    "epic": "EP-001",
    "preconditions": "a concurrent edit request on the same note",
    "steps": "two update requests are sent in quick succession",
    "expectedResult": "the last accepted write wins and no fields are partially corrupted",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given an existing note id and updated fields, when PUT/PATCH /notes/{id} is called, then API returns 200 with the updated note reflecting changes; Given a note id that does not exist, when PUT/PATCH /notes/{id} is called, then API returns 404 and no data is altered; Given a concurrent edit request on the same note, when two update requests are sent in quick succession, then the last accepted write wins and no fields are partially corrupted",
    "criteria": [
      {
        "id": "AC-001-02-01",
        "text": "Given an existing note id and updated fields, when PUT/PATCH /notes/{id} is called, then API returns 200 with the updated note reflecting changes"
      },
      {
        "id": "AC-001-02-02",
        "text": "Given a note id that does not exist, when PUT/PATCH /notes/{id} is called, then API returns 404 and no data is altered"
      },
      {
        "id": "AC-001-02-03",
        "text": "Given a concurrent edit request on the same note, when two update requests are sent in quick succession, then the last accepted write wins and no fields are partially corrupted"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-07",
    "title": "GET /notes/{id} is called",
    "story": "US-001-03",
    "epic": "EP-001",
    "preconditions": "a note exists with a given id",
    "steps": "GET /notes/{id} is called",
    "expectedResult": "API returns 200 with the full note content",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given a note exists with a given id, when GET /notes/{id} is called, then API returns 200 with the full note content; Given a note id that does not exist or was deleted, when GET /notes/{id} is called, then API returns 404",
    "criteria": [
      {
        "id": "AC-001-03-01",
        "text": "Given a note exists with a given id, when GET /notes/{id} is called, then API returns 200 with the full note content"
      },
      {
        "id": "AC-001-03-02",
        "text": "Given a note id that does not exist or was deleted, when GET /notes/{id} is called, then API returns 404"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-08",
    "title": "[error] GET /notes/{id} is called",
    "story": "US-001-03",
    "epic": "EP-001",
    "preconditions": "a note id that does not exist or was deleted",
    "steps": "GET /notes/{id} is called",
    "expectedResult": "API returns 404",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given a note exists with a given id, when GET /notes/{id} is called, then API returns 200 with the full note content; Given a note id that does not exist or was deleted, when GET /notes/{id} is called, then API returns 404",
    "criteria": [
      {
        "id": "AC-001-03-01",
        "text": "Given a note exists with a given id, when GET /notes/{id} is called, then API returns 200 with the full note content"
      },
      {
        "id": "AC-001-03-02",
        "text": "Given a note id that does not exist or was deleted, when GET /notes/{id} is called, then API returns 404"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-09",
    "title": "GET /notes is called",
    "story": "US-001-04",
    "epic": "EP-001",
    "preconditions": "multiple notes exist",
    "steps": "GET /notes is called",
    "expectedResult": "API returns 200 with a list of all notes",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given multiple notes exist, when GET /notes is called, then API returns 200 with a list of all notes; Given no notes exist yet, when GET /notes is called, then API returns 200 with an empty list, not an error",
    "criteria": [
      {
        "id": "AC-001-04-01",
        "text": "Given multiple notes exist, when GET /notes is called, then API returns 200 with a list of all notes"
      },
      {
        "id": "AC-001-04-02",
        "text": "Given no notes exist yet, when GET /notes is called, then API returns 200 with an empty list, not an error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-10",
    "title": "[edge] GET /notes is called",
    "story": "US-001-04",
    "epic": "EP-001",
    "preconditions": "no notes exist yet",
    "steps": "GET /notes is called",
    "expectedResult": "API returns 200 with an empty list, not an error",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given multiple notes exist, when GET /notes is called, then API returns 200 with a list of all notes; Given no notes exist yet, when GET /notes is called, then API returns 200 with an empty list, not an error",
    "criteria": [
      {
        "id": "AC-001-04-01",
        "text": "Given multiple notes exist, when GET /notes is called, then API returns 200 with a list of all notes"
      },
      {
        "id": "AC-001-04-02",
        "text": "Given no notes exist yet, when GET /notes is called, then API returns 200 with an empty list, not an error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-11",
    "title": "[error] POST or PATCH /notes is called",
    "story": "US-001-05",
    "epic": "EP-001",
    "preconditions": "a request with an invalid data type for a field",
    "steps": "POST or PATCH /notes is called",
    "expectedResult": "API returns 400 with a message naming the invalid field",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given a request with an invalid data type for a field, when POST or PATCH /notes is called, then API returns 400 with a message naming the invalid field; Given an empty JSON body, when POST /notes is called, then API returns 400 identifying all missing required fields",
    "criteria": [
      {
        "id": "AC-001-05-01",
        "text": "Given a request with an invalid data type for a field, when POST or PATCH /notes is called, then API returns 400 with a message naming the invalid field"
      },
      {
        "id": "AC-001-05-02",
        "text": "Given an empty JSON body, when POST /notes is called, then API returns 400 identifying all missing required fields"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-12",
    "title": "[edge] POST /notes is called",
    "story": "US-001-05",
    "epic": "EP-001",
    "preconditions": "an empty JSON body",
    "steps": "POST /notes is called",
    "expectedResult": "API returns 400 identifying all missing required fields",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given a request with an invalid data type for a field, when POST or PATCH /notes is called, then API returns 400 with a message naming the invalid field; Given an empty JSON body, when POST /notes is called, then API returns 400 identifying all missing required fields",
    "criteria": [
      {
        "id": "AC-001-05-01",
        "text": "Given a request with an invalid data type for a field, when POST or PATCH /notes is called, then API returns 400 with a message naming the invalid field"
      },
      {
        "id": "AC-001-05-02",
        "text": "Given an empty JSON body, when POST /notes is called, then API returns 400 identifying all missing required fields"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-13",
    "title": "POST /notes succeeds",
    "story": "US-001-06",
    "epic": "EP-001",
    "preconditions": "a note is created",
    "steps": "POST /notes succeeds",
    "expectedResult": "the response includes a created_at timestamp set by the server",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given a note is created, when POST /notes succeeds, then the response includes a created_at timestamp set by the server; Given a note is edited, when PATCH /notes/{id} succeeds, then the response includes an updated_at timestamp reflecting the edit time; Given a client sends its own created_at or updated_at value, when POST or PATCH is called, then API ignores client-supplied timestamps and uses server time",
    "criteria": [
      {
        "id": "AC-001-06-01",
        "text": "Given a note is created, when POST /notes succeeds, then the response includes a created_at timestamp set by the server"
      },
      {
        "id": "AC-001-06-02",
        "text": "Given a note is edited, when PATCH /notes/{id} succeeds, then the response includes an updated_at timestamp reflecting the edit time"
      },
      {
        "id": "AC-001-06-03",
        "text": "Given a client sends its own created_at or updated_at value, when POST or PATCH is called, then API ignores client-supplied timestamps and uses server time"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-14",
    "title": "PATCH /notes/{id} succeeds",
    "story": "US-001-06",
    "epic": "EP-001",
    "preconditions": "a note is edited",
    "steps": "PATCH /notes/{id} succeeds",
    "expectedResult": "the response includes an updated_at timestamp reflecting the edit time",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given a note is created, when POST /notes succeeds, then the response includes a created_at timestamp set by the server; Given a note is edited, when PATCH /notes/{id} succeeds, then the response includes an updated_at timestamp reflecting the edit time; Given a client sends its own created_at or updated_at value, when POST or PATCH is called, then API ignores client-supplied timestamps and uses server time",
    "criteria": [
      {
        "id": "AC-001-06-01",
        "text": "Given a note is created, when POST /notes succeeds, then the response includes a created_at timestamp set by the server"
      },
      {
        "id": "AC-001-06-02",
        "text": "Given a note is edited, when PATCH /notes/{id} succeeds, then the response includes an updated_at timestamp reflecting the edit time"
      },
      {
        "id": "AC-001-06-03",
        "text": "Given a client sends its own created_at or updated_at value, when POST or PATCH is called, then API ignores client-supplied timestamps and uses server time"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-001-15",
    "title": "[edge] POST or PATCH is called",
    "story": "US-001-06",
    "epic": "EP-001",
    "preconditions": "a client sends its own created_at or updated_at value",
    "steps": "POST or PATCH is called",
    "expectedResult": "API ignores client-supplied timestamps and uses server time",
    "module": "core-note-crud-api",
    "acceptanceCriteria": "Given a note is created, when POST /notes succeeds, then the response includes a created_at timestamp set by the server; Given a note is edited, when PATCH /notes/{id} succeeds, then the response includes an updated_at timestamp reflecting the edit time; Given a client sends its own created_at or updated_at value, when POST or PATCH is called, then API ignores client-supplied timestamps and uses server time",
    "criteria": [
      {
        "id": "AC-001-06-01",
        "text": "Given a note is created, when POST /notes succeeds, then the response includes a created_at timestamp set by the server"
      },
      {
        "id": "AC-001-06-02",
        "text": "Given a note is edited, when PATCH /notes/{id} succeeds, then the response includes an updated_at timestamp reflecting the edit time"
      },
      {
        "id": "AC-001-06-03",
        "text": "Given a client sends its own created_at or updated_at value, when POST or PATCH is called, then API ignores client-supplied timestamps and uses server time"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-01",
    "title": "client sends a request to add tags to the note",
    "story": "US-002-01",
    "epic": "EP-002",
    "preconditions": "an existing note and valid tag strings",
    "steps": "client sends a request to add tags to the note",
    "expectedResult": "API returns 200 with the note's updated tag list",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given an existing note and valid tag strings, when client sends a request to add tags to the note, then API returns 200 with the note's updated tag list; Given an existing note with no tags, when client submits a duplicate of an existing tag, then API deduplicates and returns the tag list without duplicates; Given a note id that does not exist, when client sends a tag-add request to that id, then API returns 404 with an error message",
    "criteria": [
      {
        "id": "AC-002-01-01",
        "text": "Given an existing note and valid tag strings, when client sends a request to add tags to the note, then API returns 200 with the note's updated tag list"
      },
      {
        "id": "AC-002-01-02",
        "text": "Given an existing note with no tags, when client submits a duplicate of an existing tag, then API deduplicates and returns the tag list without duplicates"
      },
      {
        "id": "AC-002-01-03",
        "text": "Given a note id that does not exist, when client sends a tag-add request to that id, then API returns 404 with an error message"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-02",
    "title": "[edge] client submits a duplicate of an existing tag",
    "story": "US-002-01",
    "epic": "EP-002",
    "preconditions": "an existing note with no tags",
    "steps": "client submits a duplicate of an existing tag",
    "expectedResult": "API deduplicates and returns the tag list without duplicates",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given an existing note and valid tag strings, when client sends a request to add tags to the note, then API returns 200 with the note's updated tag list; Given an existing note with no tags, when client submits a duplicate of an existing tag, then API deduplicates and returns the tag list without duplicates; Given a note id that does not exist, when client sends a tag-add request to that id, then API returns 404 with an error message",
    "criteria": [
      {
        "id": "AC-002-01-01",
        "text": "Given an existing note and valid tag strings, when client sends a request to add tags to the note, then API returns 200 with the note's updated tag list"
      },
      {
        "id": "AC-002-01-02",
        "text": "Given an existing note with no tags, when client submits a duplicate of an existing tag, then API deduplicates and returns the tag list without duplicates"
      },
      {
        "id": "AC-002-01-03",
        "text": "Given a note id that does not exist, when client sends a tag-add request to that id, then API returns 404 with an error message"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-03",
    "title": "[error] client sends a tag-add request to that id",
    "story": "US-002-01",
    "epic": "EP-002",
    "preconditions": "a note id that does not exist",
    "steps": "client sends a tag-add request to that id",
    "expectedResult": "API returns 404 with an error message",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given an existing note and valid tag strings, when client sends a request to add tags to the note, then API returns 200 with the note's updated tag list; Given an existing note with no tags, when client submits a duplicate of an existing tag, then API deduplicates and returns the tag list without duplicates; Given a note id that does not exist, when client sends a tag-add request to that id, then API returns 404 with an error message",
    "criteria": [
      {
        "id": "AC-002-01-01",
        "text": "Given an existing note and valid tag strings, when client sends a request to add tags to the note, then API returns 200 with the note's updated tag list"
      },
      {
        "id": "AC-002-01-02",
        "text": "Given an existing note with no tags, when client submits a duplicate of an existing tag, then API deduplicates and returns the tag list without duplicates"
      },
      {
        "id": "AC-002-01-03",
        "text": "Given a note id that does not exist, when client sends a tag-add request to that id, then API returns 404 with an error message"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-19",
    "title": "client sends a request to remove that tag",
    "story": "US-002-02",
    "epic": "EP-002",
    "preconditions": "a note with an existing tag",
    "steps": "client sends a request to remove that tag",
    "expectedResult": "API returns 200 and the tag is no longer in the note's tag list",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given a note with an existing tag, when client sends a request to remove that tag, then API returns 200 and the tag is no longer in the note's tag list; Given a note that does not have the specified tag, when client requests removal of that tag, then API returns 415 indicating tag not found on note",
    "criteria": [
      {
        "id": "AC-002-02-01",
        "text": "Given a note with an existing tag, when client sends a request to remove that tag, then API returns 200 and the tag is no longer in the note's tag list"
      },
      {
        "id": "AC-002-02-02",
        "text": "Given a note that does not have the specified tag, when client requests removal of that tag, then API returns 415 indicating tag not found on note"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-20",
    "title": "[error] client requests removal of that tag",
    "story": "US-002-02",
    "epic": "EP-002",
    "preconditions": "a note that does not have the specified tag",
    "steps": "client requests removal of that tag",
    "expectedResult": "API returns 415 indicating tag not found on note",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given a note with an existing tag, when client sends a request to remove that tag, then API returns 200 and the tag is no longer in the note's tag list; Given a note that does not have the specified tag, when client requests removal of that tag, then API returns 415 indicating tag not found on note",
    "criteria": [
      {
        "id": "AC-002-02-01",
        "text": "Given a note with an existing tag, when client sends a request to remove that tag, then API returns 200 and the tag is no longer in the note's tag list"
      },
      {
        "id": "AC-002-02-02",
        "text": "Given a note that does not have the specified tag, when client requests removal of that tag, then API returns 415 indicating tag not found on note"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-06",
    "title": "client calls search endpoint with that term",
    "story": "US-002-03",
    "epic": "EP-002",
    "preconditions": "notes exist whose title or body contains the query term",
    "steps": "client calls search endpoint with that term",
    "expectedResult": "API returns 200 with matching notes ranked by relevance within 300ms median",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given notes exist whose title or body contains the query term, when client calls search endpoint with that term, then API returns 200 with matching notes ranked by relevance within 300ms median; Given a query term that matches no notes, when client calls search endpoint, then API returns 200 with an empty result list, not an error; Given a search request with an empty or missing query parameter, when client calls the search endpoint, then API returns 400 with a validation error message",
    "criteria": [
      {
        "id": "AC-002-03-01",
        "text": "Given notes exist whose title or body contains the query term, when client calls search endpoint with that term, then API returns 200 with matching notes ranked by relevance within 300ms median"
      },
      {
        "id": "AC-002-03-02",
        "text": "Given a query term that matches no notes, when client calls search endpoint, then API returns 200 with an empty result list, not an error"
      },
      {
        "id": "AC-002-03-03",
        "text": "Given a search request with an empty or missing query parameter, when client calls the search endpoint, then API returns 400 with a validation error message"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-07",
    "title": "[edge] client calls search endpoint",
    "story": "US-002-03",
    "epic": "EP-002",
    "preconditions": "a query term that matches no notes",
    "steps": "client calls search endpoint",
    "expectedResult": "API returns 200 with an empty result list, not an error",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given notes exist whose title or body contains the query term, when client calls search endpoint with that term, then API returns 200 with matching notes ranked by relevance within 300ms median; Given a query term that matches no notes, when client calls search endpoint, then API returns 200 with an empty result list, not an error; Given a search request with an empty or missing query parameter, when client calls the search endpoint, then API returns 400 with a validation error message",
    "criteria": [
      {
        "id": "AC-002-03-01",
        "text": "Given notes exist whose title or body contains the query term, when client calls search endpoint with that term, then API returns 200 with matching notes ranked by relevance within 300ms median"
      },
      {
        "id": "AC-002-03-02",
        "text": "Given a query term that matches no notes, when client calls search endpoint, then API returns 200 with an empty result list, not an error"
      },
      {
        "id": "AC-002-03-03",
        "text": "Given a search request with an empty or missing query parameter, when client calls the search endpoint, then API returns 400 with a validation error message"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-08",
    "title": "[error] client calls the search endpoint",
    "story": "US-002-03",
    "epic": "EP-002",
    "preconditions": "a search request with an empty or missing query parameter",
    "steps": "client calls the search endpoint",
    "expectedResult": "API returns 400 with a validation error message",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given notes exist whose title or body contains the query term, when client calls search endpoint with that term, then API returns 200 with matching notes ranked by relevance within 300ms median; Given a query term that matches no notes, when client calls search endpoint, then API returns 200 with an empty result list, not an error; Given a search request with an empty or missing query parameter, when client calls the search endpoint, then API returns 400 with a validation error message",
    "criteria": [
      {
        "id": "AC-002-03-01",
        "text": "Given notes exist whose title or body contains the query term, when client calls search endpoint with that term, then API returns 200 with matching notes ranked by relevance within 300ms median"
      },
      {
        "id": "AC-002-03-02",
        "text": "Given a query term that matches no notes, when client calls search endpoint, then API returns 200 with an empty result list, not an error"
      },
      {
        "id": "AC-002-03-03",
        "text": "Given a search request with an empty or missing query parameter, when client calls the search endpoint, then API returns 400 with a validation error message"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-09",
    "title": "client calls search endpoint with that tag as a filter parameter",
    "story": "US-002-04",
    "epic": "EP-002",
    "preconditions": "notes tagged with a specific tag exist",
    "steps": "client calls search endpoint with that tag as a filter parameter",
    "expectedResult": "API returns 200 with only notes containing that tag",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given notes tagged with a specific tag exist, when client calls search endpoint with that tag as a filter parameter, then API returns 200 with only notes containing that tag; Given notes exist matching both a keyword and a tag filter, when client calls search with both parameters combined, then API returns 200 with notes satisfying both conditions; Given a filter request referencing a tag that does not exist in the system, when client calls search endpoint with that tag, then API returns 200 with an empty result list",
    "criteria": [
      {
        "id": "AC-002-04-01",
        "text": "Given notes tagged with a specific tag exist, when client calls search endpoint with that tag as a filter parameter, then API returns 200 with only notes containing that tag"
      },
      {
        "id": "AC-002-04-02",
        "text": "Given notes exist matching both a keyword and a tag filter, when client calls search with both parameters combined, then API returns 200 with notes satisfying both conditions"
      },
      {
        "id": "AC-002-04-03",
        "text": "Given a filter request referencing a tag that does not exist in the system, when client calls search endpoint with that tag, then API returns 200 with an empty result list"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-10",
    "title": "client calls search with both parameters combined",
    "story": "US-002-04",
    "epic": "EP-002",
    "preconditions": "notes exist matching both a keyword and a tag filter",
    "steps": "client calls search with both parameters combined",
    "expectedResult": "API returns 200 with notes satisfying both conditions",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given notes tagged with a specific tag exist, when client calls search endpoint with that tag as a filter parameter, then API returns 200 with only notes containing that tag; Given notes exist matching both a keyword and a tag filter, when client calls search with both parameters combined, then API returns 200 with notes satisfying both conditions; Given a filter request referencing a tag that does not exist in the system, when client calls search endpoint with that tag, then API returns 200 with an empty result list",
    "criteria": [
      {
        "id": "AC-002-04-01",
        "text": "Given notes tagged with a specific tag exist, when client calls search endpoint with that tag as a filter parameter, then API returns 200 with only notes containing that tag"
      },
      {
        "id": "AC-002-04-02",
        "text": "Given notes exist matching both a keyword and a tag filter, when client calls search with both parameters combined, then API returns 200 with notes satisfying both conditions"
      },
      {
        "id": "AC-002-04-03",
        "text": "Given a filter request referencing a tag that does not exist in the system, when client calls search endpoint with that tag, then API returns 200 with an empty result list"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-11",
    "title": "[error] client calls search endpoint with that tag",
    "story": "US-002-04",
    "epic": "EP-002",
    "preconditions": "a filter request referencing a tag that does not exist in the system",
    "steps": "client calls search endpoint with that tag",
    "expectedResult": "API returns 200 with an empty result list",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given notes tagged with a specific tag exist, when client calls search endpoint with that tag as a filter parameter, then API returns 200 with only notes containing that tag; Given notes exist matching both a keyword and a tag filter, when client calls search with both parameters combined, then API returns 200 with notes satisfying both conditions; Given a filter request referencing a tag that does not exist in the system, when client calls search endpoint with that tag, then API returns 200 with an empty result list",
    "criteria": [
      {
        "id": "AC-002-04-01",
        "text": "Given notes tagged with a specific tag exist, when client calls search endpoint with that tag as a filter parameter, then API returns 200 with only notes containing that tag"
      },
      {
        "id": "AC-002-04-02",
        "text": "Given notes exist matching both a keyword and a tag filter, when client calls search with both parameters combined, then API returns 200 with notes satisfying both conditions"
      },
      {
        "id": "AC-002-04-03",
        "text": "Given a filter request referencing a tag that does not exist in the system, when client calls search endpoint with that tag, then API returns 200 with an empty result list"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-12",
    "title": "client calls the list-tags endpoint",
    "story": "US-002-05",
    "epic": "EP-002",
    "preconditions": "notes with various tags exist in the system",
    "steps": "client calls the list-tags endpoint",
    "expectedResult": "API returns 200 with a deduplicated list of all tags in use",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given notes with various tags exist in the system, when client calls the list-tags endpoint, then API returns 200 with a deduplicated list of all tags in use; Given no notes have any tags yet, when client calls the list-tags endpoint, then API returns 200 with an empty list",
    "criteria": [
      {
        "id": "AC-002-05-01",
        "text": "Given notes with various tags exist in the system, when client calls the list-tags endpoint, then API returns 200 with a deduplicated list of all tags in use"
      },
      {
        "id": "AC-002-05-02",
        "text": "Given no notes have any tags yet, when client calls the list-tags endpoint, then API returns 200 with an empty list"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-13",
    "title": "[edge] client calls the list-tags endpoint",
    "story": "US-002-05",
    "epic": "EP-002",
    "preconditions": "no notes have any tags yet",
    "steps": "client calls the list-tags endpoint",
    "expectedResult": "API returns 200 with an empty list",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given notes with various tags exist in the system, when client calls the list-tags endpoint, then API returns 200 with a deduplicated list of all tags in use; Given no notes have any tags yet, when client calls the list-tags endpoint, then API returns 200 with an empty list",
    "criteria": [
      {
        "id": "AC-002-05-01",
        "text": "Given notes with various tags exist in the system, when client calls the list-tags endpoint, then API returns 200 with a deduplicated list of all tags in use"
      },
      {
        "id": "AC-002-05-02",
        "text": "Given no notes have any tags yet, when client calls the list-tags endpoint, then API returns 200 with an empty list"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-14",
    "title": "client requests a page via page/limit parameters",
    "story": "US-002-06",
    "epic": "EP-002",
    "preconditions": "a search query matches more results than the page size",
    "steps": "client requests a page via page/limit parameters",
    "expectedResult": "API returns 200 with the correct page of results and total count",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given a search query matches more results than the page size, when client requests a page via page/limit parameters, then API returns 200 with the correct page of results and total count; Given a requested page number beyond available results, when client calls search with that page number, then API returns 200 with an empty result list and correct total count; Given a negative or non-numeric page or limit parameter, when client calls search endpoint, then API returns 400 with a validation error",
    "criteria": [
      {
        "id": "AC-002-06-01",
        "text": "Given a search query matches more results than the page size, when client requests a page via page/limit parameters, then API returns 200 with the correct page of results and total count"
      },
      {
        "id": "AC-002-06-02",
        "text": "Given a requested page number beyond available results, when client calls search with that page number, then API returns 200 with an empty result list and correct total count"
      },
      {
        "id": "AC-002-06-03",
        "text": "Given a negative or non-numeric page or limit parameter, when client calls search endpoint, then API returns 400 with a validation error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-15",
    "title": "[edge] client calls search with that page number",
    "story": "US-002-06",
    "epic": "EP-002",
    "preconditions": "a requested page number beyond available results",
    "steps": "client calls search with that page number",
    "expectedResult": "API returns 200 with an empty result list and correct total count",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given a search query matches more results than the page size, when client requests a page via page/limit parameters, then API returns 200 with the correct page of results and total count; Given a requested page number beyond available results, when client calls search with that page number, then API returns 200 with an empty result list and correct total count; Given a negative or non-numeric page or limit parameter, when client calls search endpoint, then API returns 400 with a validation error",
    "criteria": [
      {
        "id": "AC-002-06-01",
        "text": "Given a search query matches more results than the page size, when client requests a page via page/limit parameters, then API returns 200 with the correct page of results and total count"
      },
      {
        "id": "AC-002-06-02",
        "text": "Given a requested page number beyond available results, when client calls search with that page number, then API returns 200 with an empty result list and correct total count"
      },
      {
        "id": "AC-002-06-03",
        "text": "Given a negative or non-numeric page or limit parameter, when client calls search endpoint, then API returns 400 with a validation error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-002-16",
    "title": "[error] client calls search endpoint",
    "story": "US-002-06",
    "epic": "EP-002",
    "preconditions": "a negative or non-numeric page or limit parameter",
    "steps": "client calls search endpoint",
    "expectedResult": "API returns 400 with a validation error",
    "module": "tagging---search-api",
    "acceptanceCriteria": "Given a search query matches more results than the page size, when client requests a page via page/limit parameters, then API returns 200 with the correct page of results and total count; Given a requested page number beyond available results, when client calls search with that page number, then API returns 200 with an empty result list and correct total count; Given a negative or non-numeric page or limit parameter, when client calls search endpoint, then API returns 400 with a validation error",
    "criteria": [
      {
        "id": "AC-002-06-01",
        "text": "Given a search query matches more results than the page size, when client requests a page via page/limit parameters, then API returns 200 with the correct page of results and total count"
      },
      {
        "id": "AC-002-06-02",
        "text": "Given a requested page number beyond available results, when client calls search with that page number, then API returns 200 with an empty result list and correct total count"
      },
      {
        "id": "AC-002-06-03",
        "text": "Given a negative or non-numeric page or limit parameter, when client calls search endpoint, then API returns 400 with a validation error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-01",
    "title": "client calls archive endpoint for that note",
    "story": "US-003-01",
    "epic": "EP-003",
    "preconditions": "an active note with a valid id exists",
    "steps": "client calls archive endpoint for that note",
    "expectedResult": "API returns 200 and note status is set to archived, content unchanged",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given an active note with a valid id exists, when client calls archive endpoint for that note, then API returns 200 and note status is set to archived, content unchanged; Given note id does not exist, when client calls archive endpoint, then API returns 404 with error body; Given note is already archived, when client calls archive endpoint again, then API returns 200 idempotently without error or state change",
    "criteria": [
      {
        "id": "AC-003-01-01",
        "text": "Given an active note with a valid id exists, when client calls archive endpoint for that note, then API returns 200 and note status is set to archived, content unchanged"
      },
      {
        "id": "AC-003-01-02",
        "text": "Given note id does not exist, when client calls archive endpoint, then API returns 404 with error body"
      },
      {
        "id": "AC-003-01-03",
        "text": "Given note is already archived, when client calls archive endpoint again, then API returns 200 idempotently without error or state change"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-02",
    "title": "[error] client calls archive endpoint",
    "story": "US-003-01",
    "epic": "EP-003",
    "preconditions": "note id does not exist",
    "steps": "client calls archive endpoint",
    "expectedResult": "API returns 404 with error body",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given an active note with a valid id exists, when client calls archive endpoint for that note, then API returns 200 and note status is set to archived, content unchanged; Given note id does not exist, when client calls archive endpoint, then API returns 404 with error body; Given note is already archived, when client calls archive endpoint again, then API returns 200 idempotently without error or state change",
    "criteria": [
      {
        "id": "AC-003-01-01",
        "text": "Given an active note with a valid id exists, when client calls archive endpoint for that note, then API returns 200 and note status is set to archived, content unchanged"
      },
      {
        "id": "AC-003-01-02",
        "text": "Given note id does not exist, when client calls archive endpoint, then API returns 404 with error body"
      },
      {
        "id": "AC-003-01-03",
        "text": "Given note is already archived, when client calls archive endpoint again, then API returns 200 idempotently without error or state change"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-03",
    "title": "[edge] client calls archive endpoint again",
    "story": "US-003-01",
    "epic": "EP-003",
    "preconditions": "note is already archived",
    "steps": "client calls archive endpoint again",
    "expectedResult": "API returns 200 idempotently without error or state change",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given an active note with a valid id exists, when client calls archive endpoint for that note, then API returns 200 and note status is set to archived, content unchanged; Given note id does not exist, when client calls archive endpoint, then API returns 404 with error body; Given note is already archived, when client calls archive endpoint again, then API returns 200 idempotently without error or state change",
    "criteria": [
      {
        "id": "AC-003-01-01",
        "text": "Given an active note with a valid id exists, when client calls archive endpoint for that note, then API returns 200 and note status is set to archived, content unchanged"
      },
      {
        "id": "AC-003-01-02",
        "text": "Given note id does not exist, when client calls archive endpoint, then API returns 404 with error body"
      },
      {
        "id": "AC-003-01-03",
        "text": "Given note is already archived, when client calls archive endpoint again, then API returns 200 idempotently without error or state change"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-04",
    "title": "client calls unarchive endpoint for that note",
    "story": "US-003-02",
    "epic": "EP-003",
    "preconditions": "an archived note with a valid id exists",
    "steps": "client calls unarchive endpoint for that note",
    "expectedResult": "API returns 200 and note status is set to active, content unchanged",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given an archived note with a valid id exists, when client calls unarchive endpoint for that note, then API returns 200 and note status is set to active, content unchanged; Given note id does not exist, when client calls unarchive endpoint, then API returns 404 with error body; Given note is already active, when client calls unarchive endpoint, then API returns 200 idempotently without error or state change",
    "criteria": [
      {
        "id": "AC-003-02-01",
        "text": "Given an archived note with a valid id exists, when client calls unarchive endpoint for that note, then API returns 200 and note status is set to active, content unchanged"
      },
      {
        "id": "AC-003-02-02",
        "text": "Given note id does not exist, when client calls unarchive endpoint, then API returns 404 with error body"
      },
      {
        "id": "AC-003-02-03",
        "text": "Given note is already active, when client calls unarchive endpoint, then API returns 200 idempotently without error or state change"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-05",
    "title": "[error] client calls unarchive endpoint",
    "story": "US-003-02",
    "epic": "EP-003",
    "preconditions": "note id does not exist",
    "steps": "client calls unarchive endpoint",
    "expectedResult": "API returns 404 with error body",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given an archived note with a valid id exists, when client calls unarchive endpoint for that note, then API returns 200 and note status is set to active, content unchanged; Given note id does not exist, when client calls unarchive endpoint, then API returns 404 with error body; Given note is already active, when client calls unarchive endpoint, then API returns 200 idempotently without error or state change",
    "criteria": [
      {
        "id": "AC-003-02-01",
        "text": "Given an archived note with a valid id exists, when client calls unarchive endpoint for that note, then API returns 200 and note status is set to active, content unchanged"
      },
      {
        "id": "AC-003-02-02",
        "text": "Given note id does not exist, when client calls unarchive endpoint, then API returns 404 with error body"
      },
      {
        "id": "AC-003-02-03",
        "text": "Given note is already active, when client calls unarchive endpoint, then API returns 200 idempotently without error or state change"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-06",
    "title": "[edge] client calls unarchive endpoint",
    "story": "US-003-02",
    "epic": "EP-003",
    "preconditions": "note is already active",
    "steps": "client calls unarchive endpoint",
    "expectedResult": "API returns 200 idempotently without error or state change",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given an archived note with a valid id exists, when client calls unarchive endpoint for that note, then API returns 200 and note status is set to active, content unchanged; Given note id does not exist, when client calls unarchive endpoint, then API returns 404 with error body; Given note is already active, when client calls unarchive endpoint, then API returns 200 idempotently without error or state change",
    "criteria": [
      {
        "id": "AC-003-02-01",
        "text": "Given an archived note with a valid id exists, when client calls unarchive endpoint for that note, then API returns 200 and note status is set to active, content unchanged"
      },
      {
        "id": "AC-003-02-02",
        "text": "Given note id does not exist, when client calls unarchive endpoint, then API returns 404 with error body"
      },
      {
        "id": "AC-003-02-03",
        "text": "Given note is already active, when client calls unarchive endpoint, then API returns 200 idempotently without error or state change"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-07",
    "title": "client requests the list filtered by archived status",
    "story": "US-003-03",
    "epic": "EP-003",
    "preconditions": "some notes are archived and some are active",
    "steps": "client requests the list filtered by archived status",
    "expectedResult": "API returns only archived notes with 200",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given some notes are archived and some are active, when client requests the list filtered by archived status, then API returns only archived notes with 200; Given no notes are currently archived, when client requests the archived list, then API returns 200 with an empty list, not an error",
    "criteria": [
      {
        "id": "AC-003-03-01",
        "text": "Given some notes are archived and some are active, when client requests the list filtered by archived status, then API returns only archived notes with 200"
      },
      {
        "id": "AC-003-03-02",
        "text": "Given no notes are currently archived, when client requests the archived list, then API returns 200 with an empty list, not an error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-08",
    "title": "[edge] client requests the archived list",
    "story": "US-003-03",
    "epic": "EP-003",
    "preconditions": "no notes are currently archived",
    "steps": "client requests the archived list",
    "expectedResult": "API returns 200 with an empty list, not an error",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given some notes are archived and some are active, when client requests the list filtered by archived status, then API returns only archived notes with 200; Given no notes are currently archived, when client requests the archived list, then API returns 200 with an empty list, not an error",
    "criteria": [
      {
        "id": "AC-003-03-01",
        "text": "Given some notes are archived and some are active, when client requests the list filtered by archived status, then API returns only archived notes with 200"
      },
      {
        "id": "AC-003-03-02",
        "text": "Given no notes are currently archived, when client requests the archived list, then API returns 200 with an empty list, not an error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-09",
    "title": "client requests the default or active-filtered list",
    "story": "US-003-04",
    "epic": "EP-003",
    "preconditions": "some notes are archived and some are active",
    "steps": "client requests the default or active-filtered list",
    "expectedResult": "API returns only active notes with 200",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given some notes are archived and some are active, when client requests the default or active-filtered list, then API returns only active notes with 200; Given all notes are archived, when client requests the active list, then API returns 200 with an empty list, not an error",
    "criteria": [
      {
        "id": "AC-003-04-01",
        "text": "Given some notes are archived and some are active, when client requests the default or active-filtered list, then API returns only active notes with 200"
      },
      {
        "id": "AC-003-04-02",
        "text": "Given all notes are archived, when client requests the active list, then API returns 200 with an empty list, not an error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-10",
    "title": "[edge] client requests the active list",
    "story": "US-003-04",
    "epic": "EP-003",
    "preconditions": "all notes are archived",
    "steps": "client requests the active list",
    "expectedResult": "API returns 200 with an empty list, not an error",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given some notes are archived and some are active, when client requests the default or active-filtered list, then API returns only active notes with 200; Given all notes are archived, when client requests the active list, then API returns 200 with an empty list, not an error",
    "criteria": [
      {
        "id": "AC-003-04-01",
        "text": "Given some notes are archived and some are active, when client requests the default or active-filtered list, then API returns only active notes with 200"
      },
      {
        "id": "AC-003-04-02",
        "text": "Given all notes are archived, when client requests the active list, then API returns 200 with an empty list, not an error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-11",
    "title": "client sends an edit or tag update request for it",
    "story": "US-003-05",
    "epic": "EP-003",
    "preconditions": "an archived note exists",
    "steps": "client sends an edit or tag update request for it",
    "expectedResult": "API applies the update and returns 200, archive status unchanged",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given an archived note exists, when client sends an edit or tag update request for it, then API applies the update and returns 200, archive status unchanged; Given an archived note is updated, when update completes, then note remains archived and no data fields are lost",
    "criteria": [
      {
        "id": "AC-003-05-01",
        "text": "Given an archived note exists, when client sends an edit or tag update request for it, then API applies the update and returns 200, archive status unchanged"
      },
      {
        "id": "AC-003-05-02",
        "text": "Given an archived note is updated, when update completes, then note remains archived and no data fields are lost"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-12",
    "title": "[edge] update completes",
    "story": "US-003-05",
    "epic": "EP-003",
    "preconditions": "an archived note is updated",
    "steps": "update completes",
    "expectedResult": "note remains archived and no data fields are lost",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given an archived note exists, when client sends an edit or tag update request for it, then API applies the update and returns 200, archive status unchanged; Given an archived note is updated, when update completes, then note remains archived and no data fields are lost",
    "criteria": [
      {
        "id": "AC-003-05-01",
        "text": "Given an archived note exists, when client sends an edit or tag update request for it, then API applies the update and returns 200, archive status unchanged"
      },
      {
        "id": "AC-003-05-02",
        "text": "Given an archived note is updated, when update completes, then note remains archived and no data fields are lost"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-13",
    "title": "[error] client calls archive endpoint",
    "story": "US-003-06",
    "epic": "EP-003",
    "preconditions": "an archive request is sent with a malformed note id (non-UUID or invalid format)",
    "steps": "client calls archive endpoint",
    "expectedResult": "API returns 400 with a descriptive error body",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given an archive request is sent with a malformed note id (non-UUID or invalid format), when client calls archive endpoint, then API returns 400 with a descriptive error body; Given an unarchive request is sent with an empty id field, when client calls unarchive endpoint, then API returns 400 without server error",
    "criteria": [
      {
        "id": "AC-003-06-01",
        "text": "Given an archive request is sent with a malformed note id (non-UUID or invalid format), when client calls archive endpoint, then API returns 400 with a descriptive error body"
      },
      {
        "id": "AC-003-06-02",
        "text": "Given an unarchive request is sent with an empty id field, when client calls unarchive endpoint, then API returns 400 without server error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-003-14",
    "title": "[edge] client calls unarchive endpoint",
    "story": "US-003-06",
    "epic": "EP-003",
    "preconditions": "an unarchive request is sent with an empty id field",
    "steps": "client calls unarchive endpoint",
    "expectedResult": "API returns 400 without server error",
    "module": "archive---restore-lifecycle",
    "acceptanceCriteria": "Given an archive request is sent with a malformed note id (non-UUID or invalid format), when client calls archive endpoint, then API returns 400 with a descriptive error body; Given an unarchive request is sent with an empty id field, when client calls unarchive endpoint, then API returns 400 without server error",
    "criteria": [
      {
        "id": "AC-003-06-01",
        "text": "Given an archive request is sent with a malformed note id (non-UUID or invalid format), when client calls archive endpoint, then API returns 400 with a descriptive error body"
      },
      {
        "id": "AC-003-06-02",
        "text": "Given an unarchive request is sent with an empty id field, when client calls unarchive endpoint, then API returns 400 without server error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-004-01",
    "title": "infrastructure is created",
    "story": "US-004-01",
    "epic": "EP-004",
    "preconditions": "a new deployment environment is being provisioned",
    "steps": "infrastructure is created",
    "expectedResult": "database and compute resources are located in an approved EU region",
    "module": "eu-data-residency-compliance",
    "acceptanceCriteria": "Given a new deployment environment is being provisioned, when infrastructure is created, then database and compute resources are located in an approved EU region; Given an attempt to provision a resource in a non-EU region, when the provisioning request is submitted, then the request is rejected and logged",
    "criteria": [
      {
        "id": "AC-004-01-01",
        "text": "Given a new deployment environment is being provisioned, when infrastructure is created, then database and compute resources are located in an approved EU region"
      },
      {
        "id": "AC-004-01-02",
        "text": "Given an attempt to provision a resource in a non-EU region, when the provisioning request is submitted, then the request is rejected and logged"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-004-02",
    "title": "[error] the provisioning request is submitted",
    "story": "US-004-01",
    "epic": "EP-004",
    "preconditions": "an attempt to provision a resource in a non-EU region",
    "steps": "the provisioning request is submitted",
    "expectedResult": "the request is rejected and logged",
    "module": "eu-data-residency-compliance",
    "acceptanceCriteria": "Given a new deployment environment is being provisioned, when infrastructure is created, then database and compute resources are located in an approved EU region; Given an attempt to provision a resource in a non-EU region, when the provisioning request is submitted, then the request is rejected and logged",
    "criteria": [
      {
        "id": "AC-004-01-01",
        "text": "Given a new deployment environment is being provisioned, when infrastructure is created, then database and compute resources are located in an approved EU region"
      },
      {
        "id": "AC-004-01-02",
        "text": "Given an attempt to provision a resource in a non-EU region, when the provisioning request is submitted, then the request is rejected and logged"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-004-03",
    "title": "the auditor requests the residency report",
    "story": "US-004-02",
    "epic": "EP-004",
    "preconditions": "notes_api infrastructure is deployed",
    "steps": "the auditor requests the residency report",
    "expectedResult": "the report lists each data store/service with its region and confirms all are EU",
    "module": "eu-data-residency-compliance",
    "acceptanceCriteria": "Given notes_api infrastructure is deployed, when the auditor requests the residency report, then the report lists each data store/service with its region and confirms all are EU; Given a service's region is undetermined, when the report is generated, then that service is flagged as unverified rather than assumed compliant",
    "criteria": [
      {
        "id": "AC-004-02-01",
        "text": "Given notes_api infrastructure is deployed, when the auditor requests the residency report, then the report lists each data store/service with its region and confirms all are EU"
      },
      {
        "id": "AC-004-02-02",
        "text": "Given a service's region is undetermined, when the report is generated, then that service is flagged as unverified rather than assumed compliant"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-004-04",
    "title": "[edge] the report is generated",
    "story": "US-004-02",
    "epic": "EP-004",
    "preconditions": "a service's region is undetermined",
    "steps": "the report is generated",
    "expectedResult": "that service is flagged as unverified rather than assumed compliant",
    "module": "eu-data-residency-compliance",
    "acceptanceCriteria": "Given notes_api infrastructure is deployed, when the auditor requests the residency report, then the report lists each data store/service with its region and confirms all are EU; Given a service's region is undetermined, when the report is generated, then that service is flagged as unverified rather than assumed compliant",
    "criteria": [
      {
        "id": "AC-004-02-01",
        "text": "Given notes_api infrastructure is deployed, when the auditor requests the residency report, then the report lists each data store/service with its region and confirms all are EU"
      },
      {
        "id": "AC-004-02-02",
        "text": "Given a service's region is undetermined, when the report is generated, then that service is flagged as unverified rather than assumed compliant"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-004-05",
    "title": "the record is persisted and backed up",
    "story": "US-004-03",
    "epic": "EP-004",
    "preconditions": "a note is created or edited via the API",
    "steps": "the record is persisted and backed up",
    "expectedResult": "both primary storage and backups reside in EU regions",
    "module": "eu-data-residency-compliance",
    "acceptanceCriteria": "Given a note is created or edited via the API, when the record is persisted and backed up, then both primary storage and backups reside in EU regions; Given a logging or backup destination configured outside the EU, when the system attempts to write to it, then the write is blocked and an alert is raised",
    "criteria": [
      {
        "id": "AC-004-03-01",
        "text": "Given a note is created or edited via the API, when the record is persisted and backed up, then both primary storage and backups reside in EU regions"
      },
      {
        "id": "AC-004-03-02",
        "text": "Given a logging or backup destination configured outside the EU, when the system attempts to write to it, then the write is blocked and an alert is raised"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-004-06",
    "title": "[error] the system attempts to write to it",
    "story": "US-004-03",
    "epic": "EP-004",
    "preconditions": "a logging or backup destination configured outside the EU",
    "steps": "the system attempts to write to it",
    "expectedResult": "the write is blocked and an alert is raised",
    "module": "eu-data-residency-compliance",
    "acceptanceCriteria": "Given a note is created or edited via the API, when the record is persisted and backed up, then both primary storage and backups reside in EU regions; Given a logging or backup destination configured outside the EU, when the system attempts to write to it, then the write is blocked and an alert is raised",
    "criteria": [
      {
        "id": "AC-004-03-01",
        "text": "Given a note is created or edited via the API, when the record is persisted and backed up, then both primary storage and backups reside in EU regions"
      },
      {
        "id": "AC-004-03-02",
        "text": "Given a logging or backup destination configured outside the EU, when the system attempts to write to it, then the write is blocked and an alert is raised"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-004-07",
    "title": "the auditor reviews the service's data processing location",
    "story": "US-004-04",
    "epic": "EP-004",
    "preconditions": "notes_api depends on a third-party service",
    "steps": "the auditor reviews the service's data processing location",
    "expectedResult": "the location is documented as EU-resident",
    "module": "eu-data-residency-compliance",
    "acceptanceCriteria": "Given notes_api depends on a third-party service, when the auditor reviews the service's data processing location, then the location is documented as EU-resident; Given a third-party service's data residency cannot be confirmed, when the audit is run, then the dependency is flagged for remediation before launch",
    "criteria": [
      {
        "id": "AC-004-04-01",
        "text": "Given notes_api depends on a third-party service, when the auditor reviews the service's data processing location, then the location is documented as EU-resident"
      },
      {
        "id": "AC-004-04-02",
        "text": "Given a third-party service's data residency cannot be confirmed, when the audit is run, then the dependency is flagged for remediation before launch"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-004-08",
    "title": "[edge] the audit is run",
    "story": "US-004-04",
    "epic": "EP-004",
    "preconditions": "a third-party service's data residency cannot be confirmed",
    "steps": "the audit is run",
    "expectedResult": "the dependency is flagged for remediation before launch",
    "module": "eu-data-residency-compliance",
    "acceptanceCriteria": "Given notes_api depends on a third-party service, when the auditor reviews the service's data processing location, then the location is documented as EU-resident; Given a third-party service's data residency cannot be confirmed, when the audit is run, then the dependency is flagged for remediation before launch",
    "criteria": [
      {
        "id": "AC-004-04-01",
        "text": "Given notes_api depends on a third-party service, when the auditor reviews the service's data processing location, then the location is documented as EU-resident"
      },
      {
        "id": "AC-004-04-02",
        "text": "Given a third-party service's data residency cannot be confirmed, when the audit is run, then the dependency is flagged for remediation before launch"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-004-09",
    "title": "the pre-launch check is run",
    "story": "US-004-05",
    "epic": "EP-004",
    "preconditions": "all environments are configured",
    "steps": "the pre-launch check is run",
    "expectedResult": "it reports pass/fail per environment with region details",
    "module": "eu-data-residency-compliance",
    "acceptanceCriteria": "Given all environments are configured, when the pre-launch check is run, then it reports pass/fail per environment with region details; Given a non-EU resource is detected in any environment, when the check runs, then launch is blocked until the resource is corrected",
    "criteria": [
      {
        "id": "AC-004-05-01",
        "text": "Given all environments are configured, when the pre-launch check is run, then it reports pass/fail per environment with region details"
      },
      {
        "id": "AC-004-05-02",
        "text": "Given a non-EU resource is detected in any environment, when the check runs, then launch is blocked until the resource is corrected"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-004-10",
    "title": "[error] the check runs",
    "story": "US-004-05",
    "epic": "EP-004",
    "preconditions": "a non-EU resource is detected in any environment",
    "steps": "the check runs",
    "expectedResult": "launch is blocked until the resource is corrected",
    "module": "eu-data-residency-compliance",
    "acceptanceCriteria": "Given all environments are configured, when the pre-launch check is run, then it reports pass/fail per environment with region details; Given a non-EU resource is detected in any environment, when the check runs, then launch is blocked until the resource is corrected",
    "criteria": [
      {
        "id": "AC-004-05-01",
        "text": "Given all environments are configured, when the pre-launch check is run, then it reports pass/fail per environment with region details"
      },
      {
        "id": "AC-004-05-02",
        "text": "Given a non-EU resource is detected in any environment, when the check runs, then launch is blocked until the resource is corrected"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-004-11",
    "title": "the auditor views the audit history",
    "story": "US-004-06",
    "epic": "EP-004",
    "preconditions": "multiple residency audits have been run",
    "steps": "the auditor views the audit history",
    "expectedResult": "each past audit's date, scope, and result are listed",
    "module": "eu-data-residency-compliance",
    "acceptanceCriteria": "Given multiple residency audits have been run, when the auditor views the audit history, then each past audit's date, scope, and result are listed; Given no audits have been run yet, when the auditor views the audit history, then an empty state is shown rather than an error",
    "criteria": [
      {
        "id": "AC-004-06-01",
        "text": "Given multiple residency audits have been run, when the auditor views the audit history, then each past audit's date, scope, and result are listed"
      },
      {
        "id": "AC-004-06-02",
        "text": "Given no audits have been run yet, when the auditor views the audit history, then an empty state is shown rather than an error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-004-12",
    "title": "[edge] the auditor views the audit history",
    "story": "US-004-06",
    "epic": "EP-004",
    "preconditions": "no audits have been run yet",
    "steps": "the auditor views the audit history",
    "expectedResult": "an empty state is shown rather than an error",
    "module": "eu-data-residency-compliance",
    "acceptanceCriteria": "Given multiple residency audits have been run, when the auditor views the audit history, then each past audit's date, scope, and result are listed; Given no audits have been run yet, when the auditor views the audit history, then an empty state is shown rather than an error",
    "criteria": [
      {
        "id": "AC-004-06-01",
        "text": "Given multiple residency audits have been run, when the auditor views the audit history, then each past audit's date, scope, and result are listed"
      },
      {
        "id": "AC-004-06-02",
        "text": "Given no audits have been run yet, when the auditor views the audit history, then an empty state is shown rather than an error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-005-01",
    "title": "all routes are listed",
    "story": "US-005-01",
    "epic": "EP-005",
    "preconditions": "the API spec is generated",
    "steps": "all routes are listed",
    "expectedResult": "only routes for create, edit, tag, search, and archive note exist",
    "module": "scope-guardrails---minimal-footprint",
    "acceptanceCriteria": "Given the API spec is generated, when all routes are listed, then only routes for create, edit, tag, search, and archive note exist; Given a developer proposes a new route outside the 5 capabilities, when the route is added to the codebase, then the spec-compliance check fails the build",
    "criteria": [
      {
        "id": "AC-005-01-01",
        "text": "Given the API spec is generated, when all routes are listed, then only routes for create, edit, tag, search, and archive note exist"
      },
      {
        "id": "AC-005-01-02",
        "text": "Given a developer proposes a new route outside the 5 capabilities, when the route is added to the codebase, then the spec-compliance check fails the build"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-005-02",
    "title": "[error] the route is added to the codebase",
    "story": "US-005-01",
    "epic": "EP-005",
    "preconditions": "a developer proposes a new route outside the 5 capabilities",
    "steps": "the route is added to the codebase",
    "expectedResult": "the spec-compliance check fails the build",
    "module": "scope-guardrails---minimal-footprint",
    "acceptanceCriteria": "Given the API spec is generated, when all routes are listed, then only routes for create, edit, tag, search, and archive note exist; Given a developer proposes a new route outside the 5 capabilities, when the route is added to the codebase, then the spec-compliance check fails the build",
    "criteria": [
      {
        "id": "AC-005-01-01",
        "text": "Given the API spec is generated, when all routes are listed, then only routes for create, edit, tag, search, and archive note exist"
      },
      {
        "id": "AC-005-01-02",
        "text": "Given a developer proposes a new route outside the 5 capabilities, when the route is added to the codebase, then the spec-compliance check fails the build"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-005-03",
    "title": "the request is submitted",
    "story": "US-005-02",
    "epic": "EP-005",
    "preconditions": "a note is created with only title, body, and tags",
    "steps": "the request is submitted",
    "expectedResult": "the note is saved successfully",
    "module": "scope-guardrails---minimal-footprint",
    "acceptanceCriteria": "Given a note is created with only title, body, and tags, when the request is submitted, then the note is saved successfully; Given a request includes a multipart/form-data file field or binary attachment, when the request hits create or edit endpoint, then the API returns 400 with an 'attachments not supported' error",
    "criteria": [
      {
        "id": "AC-005-02-01",
        "text": "Given a note is created with only title, body, and tags, when the request is submitted, then the note is saved successfully"
      },
      {
        "id": "AC-005-02-02",
        "text": "Given a request includes a multipart/form-data file field or binary attachment, when the request hits create or edit endpoint, then the API returns 400 with an 'attachments not supported' error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-005-04",
    "title": "[error] the request hits create or edit endpoint",
    "story": "US-005-02",
    "epic": "EP-005",
    "preconditions": "a request includes a multipart/form-data file field or binary attachment",
    "steps": "the request hits create or edit endpoint",
    "expectedResult": "the API returns 400 with an 'attachments not supported' error",
    "module": "scope-guardrails---minimal-footprint",
    "acceptanceCriteria": "Given a note is created with only title, body, and tags, when the request is submitted, then the note is saved successfully; Given a request includes a multipart/form-data file field or binary attachment, when the request hits create or edit endpoint, then the API returns 400 with an 'attachments not supported' error",
    "criteria": [
      {
        "id": "AC-005-02-01",
        "text": "Given a note is created with only title, body, and tags, when the request is submitted, then the note is saved successfully"
      },
      {
        "id": "AC-005-02-02",
        "text": "Given a request includes a multipart/form-data file field or binary attachment, when the request hits create or edit endpoint, then the API returns 400 with an 'attachments not supported' error"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-005-05",
    "title": "checked for protocol types",
    "story": "US-005-03",
    "epic": "EP-005",
    "preconditions": "the API's route registry is inspected",
    "steps": "checked for protocol types",
    "expectedResult": "only standard HTTP request/response endpoints are present",
    "module": "scope-guardrails---minimal-footprint",
    "acceptanceCriteria": "Given the API's route registry is inspected, when checked for protocol types, then only standard HTTP request/response endpoints are present; Given a pull request adds a WebSocket or SSE route, when CI scope-guardrail check runs, then the check fails and blocks merge",
    "criteria": [
      {
        "id": "AC-005-03-01",
        "text": "Given the API's route registry is inspected, when checked for protocol types, then only standard HTTP request/response endpoints are present"
      },
      {
        "id": "AC-005-03-02",
        "text": "Given a pull request adds a WebSocket or SSE route, when CI scope-guardrail check runs, then the check fails and blocks merge"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-005-06",
    "title": "[error] CI scope-guardrail check runs",
    "story": "US-005-03",
    "epic": "EP-005",
    "preconditions": "a pull request adds a WebSocket or SSE route",
    "steps": "CI scope-guardrail check runs",
    "expectedResult": "the check fails and blocks merge",
    "module": "scope-guardrails---minimal-footprint",
    "acceptanceCriteria": "Given the API's route registry is inspected, when checked for protocol types, then only standard HTTP request/response endpoints are present; Given a pull request adds a WebSocket or SSE route, when CI scope-guardrail check runs, then the check fails and blocks merge",
    "criteria": [
      {
        "id": "AC-005-03-01",
        "text": "Given the API's route registry is inspected, when checked for protocol types, then only standard HTTP request/response endpoints are present"
      },
      {
        "id": "AC-005-03-02",
        "text": "Given a pull request adds a WebSocket or SSE route, when CI scope-guardrail check runs, then the check fails and blocks merge"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-005-07",
    "title": "the scope-compliance suite runs",
    "story": "US-005-04",
    "epic": "EP-005",
    "preconditions": "a build is produced",
    "steps": "the scope-compliance suite runs",
    "expectedResult": "it reports exactly 5 capability groups with zero extras",
    "module": "scope-guardrails---minimal-footprint",
    "acceptanceCriteria": "Given a build is produced, when the scope-compliance suite runs, then it reports exactly 5 capability groups with zero extras; Given an endpoint exists that technically maps to 'search' but accepts attachment-filter parameters, when the suite inspects its parameters, then it flags the parameter as out-of-scope",
    "criteria": [
      {
        "id": "AC-005-04-01",
        "text": "Given a build is produced, when the scope-compliance suite runs, then it reports exactly 5 capability groups with zero extras"
      },
      {
        "id": "AC-005-04-02",
        "text": "Given an endpoint exists that technically maps to 'search' but accepts attachment-filter parameters, when the suite inspects its parameters, then it flags the parameter as out-of-scope"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-005-08",
    "title": "[edge] the suite inspects its parameters",
    "story": "US-005-04",
    "epic": "EP-005",
    "preconditions": "an endpoint exists that technically maps to 'search' but accepts attachment-filter parameters",
    "steps": "the suite inspects its parameters",
    "expectedResult": "it flags the parameter as out-of-scope",
    "module": "scope-guardrails---minimal-footprint",
    "acceptanceCriteria": "Given a build is produced, when the scope-compliance suite runs, then it reports exactly 5 capability groups with zero extras; Given an endpoint exists that technically maps to 'search' but accepts attachment-filter parameters, when the suite inspects its parameters, then it flags the parameter as out-of-scope",
    "criteria": [
      {
        "id": "AC-005-04-01",
        "text": "Given a build is produced, when the scope-compliance suite runs, then it reports exactly 5 capability groups with zero extras"
      },
      {
        "id": "AC-005-04-02",
        "text": "Given an endpoint exists that technically maps to 'search' but accepts attachment-filter parameters, when the suite inspects its parameters, then it flags the parameter as out-of-scope"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-005-09",
    "title": "a reader views the scope section",
    "story": "US-005-05",
    "epic": "EP-005",
    "preconditions": "the published API documentation",
    "steps": "a reader views the scope section",
    "expectedResult": "attachments, mobile endpoints, and real-time features are explicitly listed as out of scope",
    "module": "scope-guardrails---minimal-footprint",
    "acceptanceCriteria": "Given the published API documentation, when a reader views the scope section, then attachments, mobile endpoints, and real-time features are explicitly listed as out of scope; Given a future contributor proposes an attachment endpoint, when they check the documented out-of-scope list first, then they see the feature is explicitly excluded before writing code",
    "criteria": [
      {
        "id": "AC-005-05-01",
        "text": "Given the published API documentation, when a reader views the scope section, then attachments, mobile endpoints, and real-time features are explicitly listed as out of scope"
      },
      {
        "id": "AC-005-05-02",
        "text": "Given a future contributor proposes an attachment endpoint, when they check the documented out-of-scope list first, then they see the feature is explicitly excluded before writing code"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-005-10",
    "title": "[edge] they check the documented out-of-scope list first",
    "story": "US-005-05",
    "epic": "EP-005",
    "preconditions": "a future contributor proposes an attachment endpoint",
    "steps": "they check the documented out-of-scope list first",
    "expectedResult": "they see the feature is explicitly excluded before writing code",
    "module": "scope-guardrails---minimal-footprint",
    "acceptanceCriteria": "Given the published API documentation, when a reader views the scope section, then attachments, mobile endpoints, and real-time features are explicitly listed as out of scope; Given a future contributor proposes an attachment endpoint, when they check the documented out-of-scope list first, then they see the feature is explicitly excluded before writing code",
    "criteria": [
      {
        "id": "AC-005-05-01",
        "text": "Given the published API documentation, when a reader views the scope section, then attachments, mobile endpoints, and real-time features are explicitly listed as out of scope"
      },
      {
        "id": "AC-005-05-02",
        "text": "Given a future contributor proposes an attachment endpoint, when they check the documented out-of-scope list first, then they see the feature is explicitly excluded before writing code"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-005-11",
    "title": "checked for mobile-only parameters",
    "story": "US-005-06",
    "epic": "EP-005",
    "preconditions": "the API endpoints are reviewed",
    "steps": "checked for mobile-only parameters",
    "expectedResult": "none are present",
    "module": "scope-guardrails---minimal-footprint",
    "acceptanceCriteria": "Given the API endpoints are reviewed, when checked for mobile-only parameters, then none are present; Given a request includes a device push-token field, when submitted to any endpoint, then the API returns 400 indicating the field is unsupported",
    "criteria": [
      {
        "id": "AC-005-06-01",
        "text": "Given the API endpoints are reviewed, when checked for mobile-only parameters, then none are present"
      },
      {
        "id": "AC-005-06-02",
        "text": "Given a request includes a device push-token field, when submitted to any endpoint, then the API returns 400 indicating the field is unsupported"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-005-12",
    "title": "[error] submitted to any endpoint",
    "story": "US-005-06",
    "epic": "EP-005",
    "preconditions": "a request includes a device push-token field",
    "steps": "submitted to any endpoint",
    "expectedResult": "the API returns 400 indicating the field is unsupported",
    "module": "scope-guardrails---minimal-footprint",
    "acceptanceCriteria": "Given the API endpoints are reviewed, when checked for mobile-only parameters, then none are present; Given a request includes a device push-token field, when submitted to any endpoint, then the API returns 400 indicating the field is unsupported",
    "criteria": [
      {
        "id": "AC-005-06-01",
        "text": "Given the API endpoints are reviewed, when checked for mobile-only parameters, then none are present"
      },
      {
        "id": "AC-005-06-02",
        "text": "Given a request includes a device push-token field, when submitted to any endpoint, then the API returns 400 indicating the field is unsupported"
      }
    ],
    "created_at": "2026-10-11T13:48:45.824640+00:00"
  },
  {
    "testId": "TC-006-29",
    "title": "the notes list page loads",
    "story": "US-006-01",
    "epic": "EP-006",
    "preconditions": "notes exist in notes_api",
    "steps": "the notes list page loads",
    "expectedResult": "the web app calls GET /notes, renders each note's title and status, renders pinned notes first with a pin icon, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with Newest first selected by default, and shows a live result count above the list equal to the number of notes listed",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given notes exist in notes_api, when the notes list page loads, then the web app calls GET /notes, renders each note's title and status, renders pinned notes first with a pin icon, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with Newest first selected by default, and shows a live result count above the list equal to the number of notes listed; Given no notes exist yet, when the notes list page loads, then the web app shows an empty-state message and a result count of 0 instead of an error; Given notes_api returns an error, when the notes list page loads, then the web app shows a user-facing error state instead of a blank screen",
    "criteria": [
      {
        "id": "AC-006-01-01",
        "text": "Given notes exist in notes_api, when the notes list page loads, then the web app calls GET /notes, renders each note's title and status, renders pinned notes first with a pin icon, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with Newest first selected by default, and shows a live result count above the list equal to the number of notes listed"
      },
      {
        "id": "AC-006-01-02",
        "text": "Given no notes exist yet, when the notes list page loads, then the web app shows an empty-state message and a result count of 0 instead of an error"
      },
      {
        "id": "AC-006-01-03",
        "text": "Given notes_api returns an error, when the notes list page loads, then the web app shows a user-facing error state instead of a blank screen"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-30",
    "title": "the notes list page loads",
    "story": "US-006-01",
    "epic": "EP-006",
    "preconditions": "no notes exist yet",
    "steps": "the notes list page loads",
    "expectedResult": "the web app shows an empty-state message and a result count of 0 instead of an error",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given notes exist in notes_api, when the notes list page loads, then the web app calls GET /notes, renders each note's title and status, renders pinned notes first with a pin icon, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with Newest first selected by default, and shows a live result count above the list equal to the number of notes listed; Given no notes exist yet, when the notes list page loads, then the web app shows an empty-state message and a result count of 0 instead of an error; Given notes_api returns an error, when the notes list page loads, then the web app shows a user-facing error state instead of a blank screen",
    "criteria": [
      {
        "id": "AC-006-01-01",
        "text": "Given notes exist in notes_api, when the notes list page loads, then the web app calls GET /notes, renders each note's title and status, renders pinned notes first with a pin icon, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with Newest first selected by default, and shows a live result count above the list equal to the number of notes listed"
      },
      {
        "id": "AC-006-01-02",
        "text": "Given no notes exist yet, when the notes list page loads, then the web app shows an empty-state message and a result count of 0 instead of an error"
      },
      {
        "id": "AC-006-01-03",
        "text": "Given notes_api returns an error, when the notes list page loads, then the web app shows a user-facing error state instead of a blank screen"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-31",
    "title": "the notes list page loads",
    "story": "US-006-01",
    "epic": "EP-006",
    "preconditions": "notes_api returns an error",
    "steps": "the notes list page loads",
    "expectedResult": "the web app shows a user-facing error state instead of a blank screen",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given notes exist in notes_api, when the notes list page loads, then the web app calls GET /notes, renders each note's title and status, renders pinned notes first with a pin icon, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with Newest first selected by default, and shows a live result count above the list equal to the number of notes listed; Given no notes exist yet, when the notes list page loads, then the web app shows an empty-state message and a result count of 0 instead of an error; Given notes_api returns an error, when the notes list page loads, then the web app shows a user-facing error state instead of a blank screen",
    "criteria": [
      {
        "id": "AC-006-01-01",
        "text": "Given notes exist in notes_api, when the notes list page loads, then the web app calls GET /notes, renders each note's title and status, renders pinned notes first with a pin icon, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with Newest first selected by default, and shows a live result count above the list equal to the number of notes listed"
      },
      {
        "id": "AC-006-01-02",
        "text": "Given no notes exist yet, when the notes list page loads, then the web app shows an empty-state message and a result count of 0 instead of an error"
      },
      {
        "id": "AC-006-01-03",
        "text": "Given notes_api returns an error, when the notes list page loads, then the web app shows a user-facing error state instead of a blank screen"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-04",
    "title": "the user submits",
    "story": "US-006-02",
    "epic": "EP-006",
    "preconditions": "a title and body are entered in the editor",
    "steps": "the user submits",
    "expectedResult": "the web app calls POST /notes and navigates to the saved note",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given a title and body are entered in the editor, when the user submits, then the web app calls POST /notes and navigates to the saved note; Given the title field is left empty, when the user submits, then the web app shows the API's validation error inline without creating a note; Given the create request succeeds, when the response is received, then the note's server-set created_at is reflected in the UI",
    "criteria": [
      {
        "id": "AC-006-02-01",
        "text": "Given a title and body are entered in the editor, when the user submits, then the web app calls POST /notes and navigates to the saved note"
      },
      {
        "id": "AC-006-02-02",
        "text": "Given the title field is left empty, when the user submits, then the web app shows the API's validation error inline without creating a note"
      },
      {
        "id": "AC-006-02-03",
        "text": "Given the create request succeeds, when the response is received, then the note's server-set created_at is reflected in the UI"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-05",
    "title": "the user submits",
    "story": "US-006-02",
    "epic": "EP-006",
    "preconditions": "the title field is left empty",
    "steps": "the user submits",
    "expectedResult": "the web app shows the API's validation error inline without creating a note",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given a title and body are entered in the editor, when the user submits, then the web app calls POST /notes and navigates to the saved note; Given the title field is left empty, when the user submits, then the web app shows the API's validation error inline without creating a note; Given the create request succeeds, when the response is received, then the note's server-set created_at is reflected in the UI",
    "criteria": [
      {
        "id": "AC-006-02-01",
        "text": "Given a title and body are entered in the editor, when the user submits, then the web app calls POST /notes and navigates to the saved note"
      },
      {
        "id": "AC-006-02-02",
        "text": "Given the title field is left empty, when the user submits, then the web app shows the API's validation error inline without creating a note"
      },
      {
        "id": "AC-006-02-03",
        "text": "Given the create request succeeds, when the response is received, then the note's server-set created_at is reflected in the UI"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-06",
    "title": "the response is received",
    "story": "US-006-02",
    "epic": "EP-006",
    "preconditions": "the create request succeeds",
    "steps": "the response is received",
    "expectedResult": "the note's server-set created_at is reflected in the UI",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given a title and body are entered in the editor, when the user submits, then the web app calls POST /notes and navigates to the saved note; Given the title field is left empty, when the user submits, then the web app shows the API's validation error inline without creating a note; Given the create request succeeds, when the response is received, then the note's server-set created_at is reflected in the UI",
    "criteria": [
      {
        "id": "AC-006-02-01",
        "text": "Given a title and body are entered in the editor, when the user submits, then the web app calls POST /notes and navigates to the saved note"
      },
      {
        "id": "AC-006-02-02",
        "text": "Given the title field is left empty, when the user submits, then the web app shows the API's validation error inline without creating a note"
      },
      {
        "id": "AC-006-02-03",
        "text": "Given the create request succeeds, when the response is received, then the note's server-set created_at is reflected in the UI"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-07",
    "title": "the user changes the title or body and saves",
    "story": "US-006-03",
    "epic": "EP-006",
    "preconditions": "an existing note is opened in the editor",
    "steps": "the user changes the title or body and saves",
    "expectedResult": "the web app calls PUT/PATCH /notes/{id} and shows the updated content",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given an existing note is opened in the editor, when the user changes the title or body and saves, then the web app calls PUT/PATCH /notes/{id} and shows the updated content; Given the save request returns 404, when the response is received, then the web app shows a 'note not found' message and does not apply local changes; Given a save request returns a field-level validation error, when the response is received, then the web app highlights the invalid field",
    "criteria": [
      {
        "id": "AC-006-03-01",
        "text": "Given an existing note is opened in the editor, when the user changes the title or body and saves, then the web app calls PUT/PATCH /notes/{id} and shows the updated content"
      },
      {
        "id": "AC-006-03-02",
        "text": "Given the save request returns 404, when the response is received, then the web app shows a 'note not found' message and does not apply local changes"
      },
      {
        "id": "AC-006-03-03",
        "text": "Given a save request returns a field-level validation error, when the response is received, then the web app highlights the invalid field"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-08",
    "title": "the response is received",
    "story": "US-006-03",
    "epic": "EP-006",
    "preconditions": "the save request returns 404",
    "steps": "the response is received",
    "expectedResult": "the web app shows a 'note not found' message and does not apply local changes",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given an existing note is opened in the editor, when the user changes the title or body and saves, then the web app calls PUT/PATCH /notes/{id} and shows the updated content; Given the save request returns 404, when the response is received, then the web app shows a 'note not found' message and does not apply local changes; Given a save request returns a field-level validation error, when the response is received, then the web app highlights the invalid field",
    "criteria": [
      {
        "id": "AC-006-03-01",
        "text": "Given an existing note is opened in the editor, when the user changes the title or body and saves, then the web app calls PUT/PATCH /notes/{id} and shows the updated content"
      },
      {
        "id": "AC-006-03-02",
        "text": "Given the save request returns 404, when the response is received, then the web app shows a 'note not found' message and does not apply local changes"
      },
      {
        "id": "AC-006-03-03",
        "text": "Given a save request returns a field-level validation error, when the response is received, then the web app highlights the invalid field"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-09",
    "title": "the response is received",
    "story": "US-006-03",
    "epic": "EP-006",
    "preconditions": "a save request returns a field-level validation error",
    "steps": "the response is received",
    "expectedResult": "the web app highlights the invalid field",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given an existing note is opened in the editor, when the user changes the title or body and saves, then the web app calls PUT/PATCH /notes/{id} and shows the updated content; Given the save request returns 404, when the response is received, then the web app shows a 'note not found' message and does not apply local changes; Given a save request returns a field-level validation error, when the response is received, then the web app highlights the invalid field",
    "criteria": [
      {
        "id": "AC-006-03-01",
        "text": "Given an existing note is opened in the editor, when the user changes the title or body and saves, then the web app calls PUT/PATCH /notes/{id} and shows the updated content"
      },
      {
        "id": "AC-006-03-02",
        "text": "Given the save request returns 404, when the response is received, then the web app shows a 'note not found' message and does not apply local changes"
      },
      {
        "id": "AC-006-03-03",
        "text": "Given a save request returns a field-level validation error, when the response is received, then the web app highlights the invalid field"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-26",
    "title": "they submit",
    "story": "US-006-04",
    "epic": "EP-006",
    "preconditions": "the user enters a keyword in the search box",
    "steps": "they submit",
    "expectedResult": "the web app calls the search endpoint, renders matching notes in the currently selected sort order (Newest first, Oldest first, or Title A-Z) without changing the sort selection, and updates the live result count above the list to the number of matches",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given the user enters a keyword in the search box, when they submit, then the web app calls the search endpoint, renders matching notes in the currently selected sort order (Newest first, Oldest first, or Title A-Z) without changing the sort selection, and updates the live result count above the list to the number of matches; Given the search returns no matches, when results load, then the web app shows an empty-results message and a result count of 0 instead of an error; Given the user selects a tag filter, when they apply it, then the web app includes the tag filter in the search request, renders only matching notes in the selected sort order, and updates the live result count to match",
    "criteria": [
      {
        "id": "AC-006-04-01",
        "text": "Given the user enters a keyword in the search box, when they submit, then the web app calls the search endpoint, renders matching notes in the currently selected sort order (Newest first, Oldest first, or Title A-Z) without changing the sort selection, and updates the live result count above the list to the number of matches"
      },
      {
        "id": "AC-006-04-02",
        "text": "Given the search returns no matches, when results load, then the web app shows an empty-results message and a result count of 0 instead of an error"
      },
      {
        "id": "AC-006-04-03",
        "text": "Given the user selects a tag filter, when they apply it, then the web app includes the tag filter in the search request, renders only matching notes in the selected sort order, and updates the live result count to match"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-27",
    "title": "results load",
    "story": "US-006-04",
    "epic": "EP-006",
    "preconditions": "the search returns no matches",
    "steps": "results load",
    "expectedResult": "the web app shows an empty-results message and a result count of 0 instead of an error",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given the user enters a keyword in the search box, when they submit, then the web app calls the search endpoint, renders matching notes in the currently selected sort order (Newest first, Oldest first, or Title A-Z) without changing the sort selection, and updates the live result count above the list to the number of matches; Given the search returns no matches, when results load, then the web app shows an empty-results message and a result count of 0 instead of an error; Given the user selects a tag filter, when they apply it, then the web app includes the tag filter in the search request, renders only matching notes in the selected sort order, and updates the live result count to match",
    "criteria": [
      {
        "id": "AC-006-04-01",
        "text": "Given the user enters a keyword in the search box, when they submit, then the web app calls the search endpoint, renders matching notes in the currently selected sort order (Newest first, Oldest first, or Title A-Z) without changing the sort selection, and updates the live result count above the list to the number of matches"
      },
      {
        "id": "AC-006-04-02",
        "text": "Given the search returns no matches, when results load, then the web app shows an empty-results message and a result count of 0 instead of an error"
      },
      {
        "id": "AC-006-04-03",
        "text": "Given the user selects a tag filter, when they apply it, then the web app includes the tag filter in the search request, renders only matching notes in the selected sort order, and updates the live result count to match"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-28",
    "title": "they apply it",
    "story": "US-006-04",
    "epic": "EP-006",
    "preconditions": "the user selects a tag filter",
    "steps": "they apply it",
    "expectedResult": "the web app includes the tag filter in the search request, renders only matching notes in the selected sort order, and updates the live result count to match",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given the user enters a keyword in the search box, when they submit, then the web app calls the search endpoint, renders matching notes in the currently selected sort order (Newest first, Oldest first, or Title A-Z) without changing the sort selection, and updates the live result count above the list to the number of matches; Given the search returns no matches, when results load, then the web app shows an empty-results message and a result count of 0 instead of an error; Given the user selects a tag filter, when they apply it, then the web app includes the tag filter in the search request, renders only matching notes in the selected sort order, and updates the live result count to match",
    "criteria": [
      {
        "id": "AC-006-04-01",
        "text": "Given the user enters a keyword in the search box, when they submit, then the web app calls the search endpoint, renders matching notes in the currently selected sort order (Newest first, Oldest first, or Title A-Z) without changing the sort selection, and updates the live result count above the list to the number of matches"
      },
      {
        "id": "AC-006-04-02",
        "text": "Given the search returns no matches, when results load, then the web app shows an empty-results message and a result count of 0 instead of an error"
      },
      {
        "id": "AC-006-04-03",
        "text": "Given the user selects a tag filter, when they apply it, then the web app includes the tag filter in the search request, renders only matching notes in the selected sort order, and updates the live result count to match"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-41",
    "title": "they submit the email and then enter a valid 6 digits-digit code on the code-ent",
    "story": "US-006-05",
    "epic": "EP-006",
    "preconditions": "the user is on the login screen and enters their email address",
    "steps": "they submit the email and then enter a valid 6 digits-digit code on the code-entry screen",
    "expectedResult": "the web app calls the notes_api request-code endpoint, shows the code-entry screen, calls the verify-code endpoint with the entered code and signs the user in",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given the user is on the login screen and enters their email address, when they submit the email and then enter a valid 6 digits-digit code on the code-entry screen, then the web app calls the notes_api request-code endpoint, shows the code-entry screen, calls the verify-code endpoint with the entered code and signs the user in; Given the user has entered a wrong code 5 attempts times, when the verify-code endpoint reports the account is locked, then the web app shows a lockout error stating the account is locked for 15 minutes and disables code submission; Given the code was issued more than 10 minutes ago, or the resend limit of 3 resends per 10 minutes has been reached, when the user submits the code or asks for a new code, then the web app shows an expired-code error with an option to request a new code, or a resend-limit error when no more resends are allowed",
    "criteria": [
      {
        "id": "AC-006-05-01",
        "text": "Given the user is on the login screen and enters their email address, when they submit the email and then enter a valid 6 digits-digit code on the code-entry screen, then the web app calls the notes_api request-code endpoint, shows the code-entry screen, calls the verify-code endpoint with the entered code and signs the user in"
      },
      {
        "id": "AC-006-05-02",
        "text": "Given the user has entered a wrong code 5 attempts times, when the verify-code endpoint reports the account is locked, then the web app shows a lockout error stating the account is locked for 15 minutes and disables code submission"
      },
      {
        "id": "AC-006-05-03",
        "text": "Given the code was issued more than 10 minutes ago, or the resend limit of 3 resends per 10 minutes has been reached, when the user submits the code or asks for a new code, then the web app shows an expired-code error with an option to request a new code, or a resend-limit error when no more resends are allowed"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-42",
    "title": "[error] the verify-code endpoint reports the account is locked",
    "story": "US-006-05",
    "epic": "EP-006",
    "preconditions": "the user has entered a wrong code 5 attempts times",
    "steps": "the verify-code endpoint reports the account is locked",
    "expectedResult": "the web app shows a lockout error stating the account is locked for 15 minutes and disables code submission",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given the user is on the login screen and enters their email address, when they submit the email and then enter a valid 6 digits-digit code on the code-entry screen, then the web app calls the notes_api request-code endpoint, shows the code-entry screen, calls the verify-code endpoint with the entered code and signs the user in; Given the user has entered a wrong code 5 attempts times, when the verify-code endpoint reports the account is locked, then the web app shows a lockout error stating the account is locked for 15 minutes and disables code submission; Given the code was issued more than 10 minutes ago, or the resend limit of 3 resends per 10 minutes has been reached, when the user submits the code or asks for a new code, then the web app shows an expired-code error with an option to request a new code, or a resend-limit error when no more resends are allowed",
    "criteria": [
      {
        "id": "AC-006-05-01",
        "text": "Given the user is on the login screen and enters their email address, when they submit the email and then enter a valid 6 digits-digit code on the code-entry screen, then the web app calls the notes_api request-code endpoint, shows the code-entry screen, calls the verify-code endpoint with the entered code and signs the user in"
      },
      {
        "id": "AC-006-05-02",
        "text": "Given the user has entered a wrong code 5 attempts times, when the verify-code endpoint reports the account is locked, then the web app shows a lockout error stating the account is locked for 15 minutes and disables code submission"
      },
      {
        "id": "AC-006-05-03",
        "text": "Given the code was issued more than 10 minutes ago, or the resend limit of 3 resends per 10 minutes has been reached, when the user submits the code or asks for a new code, then the web app shows an expired-code error with an option to request a new code, or a resend-limit error when no more resends are allowed"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-43",
    "title": "[error] the user submits the code or asks for a new code",
    "story": "US-006-05",
    "epic": "EP-006",
    "preconditions": "the code was issued more than 10 minutes ago, or the resend limit of 3 resends per 10 minutes has been reached",
    "steps": "the user submits the code or asks for a new code",
    "expectedResult": "the web app shows an expired-code error with an option to request a new code, or a resend-limit error when no more resends are allowed",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given the user is on the login screen and enters their email address, when they submit the email and then enter a valid 6 digits-digit code on the code-entry screen, then the web app calls the notes_api request-code endpoint, shows the code-entry screen, calls the verify-code endpoint with the entered code and signs the user in; Given the user has entered a wrong code 5 attempts times, when the verify-code endpoint reports the account is locked, then the web app shows a lockout error stating the account is locked for 15 minutes and disables code submission; Given the code was issued more than 10 minutes ago, or the resend limit of 3 resends per 10 minutes has been reached, when the user submits the code or asks for a new code, then the web app shows an expired-code error with an option to request a new code, or a resend-limit error when no more resends are allowed",
    "criteria": [
      {
        "id": "AC-006-05-01",
        "text": "Given the user is on the login screen and enters their email address, when they submit the email and then enter a valid 6 digits-digit code on the code-entry screen, then the web app calls the notes_api request-code endpoint, shows the code-entry screen, calls the verify-code endpoint with the entered code and signs the user in"
      },
      {
        "id": "AC-006-05-02",
        "text": "Given the user has entered a wrong code 5 attempts times, when the verify-code endpoint reports the account is locked, then the web app shows a lockout error stating the account is locked for 15 minutes and disables code submission"
      },
      {
        "id": "AC-006-05-03",
        "text": "Given the code was issued more than 10 minutes ago, or the resend limit of 3 resends per 10 minutes has been reached, when the user submits the code or asks for a new code, then the web app shows an expired-code error with an option to request a new code, or a resend-limit error when no more resends are allowed"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-32",
    "title": "the user clicks archive",
    "story": "US-006-06",
    "epic": "EP-006",
    "preconditions": "an active note is open",
    "steps": "the user clicks archive",
    "expectedResult": "the web app calls the archive endpoint and the note disappears from the default active list",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given an active note is open, when the user clicks archive, then the web app calls the archive endpoint and the note disappears from the default active list; Given an archived note is viewed, when the user clicks restore, then the web app calls the unarchive endpoint and the note reappears in the active list; Given a notes row in the list, when the user clicks its pin toggle, then the web app calls the API to set the note's pinned flag and the row moves to the top with a pin icon (or returns to its sort position when unpinned)",
    "criteria": [
      {
        "id": "AC-006-06-01",
        "text": "Given an active note is open, when the user clicks archive, then the web app calls the archive endpoint and the note disappears from the default active list"
      },
      {
        "id": "AC-006-06-02",
        "text": "Given an archived note is viewed, when the user clicks restore, then the web app calls the unarchive endpoint and the note reappears in the active list"
      },
      {
        "id": "AC-006-06-03",
        "text": "Given a notes row in the list, when the user clicks its pin toggle, then the web app calls the API to set the note's pinned flag and the row moves to the top with a pin icon (or returns to its sort position when unpinned)"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-33",
    "title": "the user clicks restore",
    "story": "US-006-06",
    "epic": "EP-006",
    "preconditions": "an archived note is viewed",
    "steps": "the user clicks restore",
    "expectedResult": "the web app calls the unarchive endpoint and the note reappears in the active list",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given an active note is open, when the user clicks archive, then the web app calls the archive endpoint and the note disappears from the default active list; Given an archived note is viewed, when the user clicks restore, then the web app calls the unarchive endpoint and the note reappears in the active list; Given a notes row in the list, when the user clicks its pin toggle, then the web app calls the API to set the note's pinned flag and the row moves to the top with a pin icon (or returns to its sort position when unpinned)",
    "criteria": [
      {
        "id": "AC-006-06-01",
        "text": "Given an active note is open, when the user clicks archive, then the web app calls the archive endpoint and the note disappears from the default active list"
      },
      {
        "id": "AC-006-06-02",
        "text": "Given an archived note is viewed, when the user clicks restore, then the web app calls the unarchive endpoint and the note reappears in the active list"
      },
      {
        "id": "AC-006-06-03",
        "text": "Given a notes row in the list, when the user clicks its pin toggle, then the web app calls the API to set the note's pinned flag and the row moves to the top with a pin icon (or returns to its sort position when unpinned)"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  },
  {
    "testId": "TC-006-34",
    "title": "the user clicks its pin toggle",
    "story": "US-006-06",
    "epic": "EP-006",
    "preconditions": "a notes row in the list",
    "steps": "the user clicks its pin toggle",
    "expectedResult": "the web app calls the API to set the note's pinned flag and the row moves to the top with a pin icon (or returns to its sort position when unpinned)",
    "module": "web-frontend-for-notes",
    "acceptanceCriteria": "Given an active note is open, when the user clicks archive, then the web app calls the archive endpoint and the note disappears from the default active list; Given an archived note is viewed, when the user clicks restore, then the web app calls the unarchive endpoint and the note reappears in the active list; Given a notes row in the list, when the user clicks its pin toggle, then the web app calls the API to set the note's pinned flag and the row moves to the top with a pin icon (or returns to its sort position when unpinned)",
    "criteria": [
      {
        "id": "AC-006-06-01",
        "text": "Given an active note is open, when the user clicks archive, then the web app calls the archive endpoint and the note disappears from the default active list"
      },
      {
        "id": "AC-006-06-02",
        "text": "Given an archived note is viewed, when the user clicks restore, then the web app calls the unarchive endpoint and the note reappears in the active list"
      },
      {
        "id": "AC-006-06-03",
        "text": "Given a notes row in the list, when the user clicks its pin toggle, then the web app calls the API to set the note's pinned flag and the row moves to the top with a pin icon (or returns to its sort position when unpinned)"
      }
    ],
    "created_at": "2026-10-12T23:58:55.596639+00:00"
  }
]
