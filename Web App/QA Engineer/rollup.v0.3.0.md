---
id: agent-qa/web_app/rollup.md
type: spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-qa/web_app/rollup.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: ed3b268d-0f39-5431-9196-9f249ebe81aa
---

# agent-qa

[
  {
    "testId": "TC-001-01",
    "title": "POST /notes is called",
    "module": "create a new note via the API with title and body text",
    "coveredBy": "TC-002-01",
    "steps": [
      { "stepNo": 1, "instruction": "Send POST /notes with a valid title and body payload", "action": "navigate", "selector": "/notes", "value": "POST {title, body}" },
      { "stepNo": 2, "instruction": "Verify response is 201 with the created note including its id", "action": "assert_text", "selector": "body", "value": "201 created note with id" }
    ],
    "expectedResult": "API returns 201 with the created note including its id"
  },
  {
    "testId": "TC-001-02",
    "title": "[error] POST /notes is called",
    "module": "create a new note via the API with title and body text",
    "coveredBy": "TC-002-02",
    "steps": [
      { "stepNo": 1, "instruction": "Send POST /notes with a payload missing the required title field", "action": "navigate", "selector": "/notes", "value": "POST {body}" },
      { "stepNo": 2, "instruction": "Verify 400 validation error and that no note was created", "action": "assert_text", "selector": "body", "value": "400 validation error" }
    ],
    "expectedResult": "API returns 400 with a validation error and no note is created"
  },
  {
    "testId": "TC-001-03",
    "title": "[edge] POST /notes is called",
    "module": "create a new note via the API with title and body text",
    "steps": [
      { "stepNo": 1, "instruction": "Send POST /notes with a valid title and a body at the maximum allowed length", "action": "navigate", "selector": "/notes", "value": "POST {title, body: max-length string}" },
      { "stepNo": 2, "instruction": "Verify response is 201", "action": "assert_text", "selector": "body", "value": "201" },
      { "stepNo": 3, "instruction": "Verify stored body equals the full max-length string without truncation", "action": "assert_text", "selector": "body", "value": "body length unchanged" }
    ],
    "expectedResult": "API returns 201 and stores the full body without truncation"
  },
  {
    "testId": "TC-001-04",
    "title": "PUT/PATCH /notes/{id} is called",
    "module": "edit an existing note's title or body via the API",
    "coveredBy": "TC-002-03",
    "steps": [
      { "stepNo": 1, "instruction": "Send PATCH /notes/{id} with updated title and body for an existing note", "action": "navigate", "selector": "/notes/{id}", "value": "PATCH {title, body}" },
      { "stepNo": 2, "instruction": "Verify response is 200 with the updated note", "action": "assert_text", "selector": "body", "value": "200 updated note" }
    ],
    "expectedResult": "API returns 200 with the updated note reflecting changes"
  },
  {
    "testId": "TC-001-05",
    "title": "[error] PUT/PATCH /notes/{id} is called",
    "module": "edit an existing note's title or body via the API",
    "coveredBy": "TC-002-04",
    "steps": [
      { "stepNo": 1, "instruction": "Send PATCH /notes/{id} using a non-existent note id", "action": "navigate", "selector": "/notes/{nonexistent-id}", "value": "PATCH {title}" },
      { "stepNo": 2, "instruction": "Verify 404 and confirm no data was altered", "action": "assert_text", "selector": "body", "value": "404" }
    ],
    "expectedResult": "API returns 404 and no data is altered"
  },
  {
    "testId": "TC-001-06",
    "title": "[edge] two update requests are sent in quick succession",
    "module": "edit an existing note's title or body via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Send PATCH /notes/{id} with payload A", "action": "navigate", "selector": "/notes/{id}", "value": "PATCH {title: A}" },
      { "stepNo": 2, "instruction": "Send PATCH /notes/{id} with payload B immediately after", "action": "navigate", "selector": "/notes/{id}", "value": "PATCH {body: B}" },
      { "stepNo": 3, "instruction": "Fetch the note and verify the last accepted write is reflected with no partially corrupted fields", "action": "assert_text", "selector": "body", "value": "last write wins, fields intact" }
    ],
    "expectedResult": "the last accepted write wins and no fields are partially corrupted"
  },
  {
    "testId": "TC-001-07",
    "title": "GET /notes/{id} is called",
    "module": "retrieve a single note by its id",
    "coveredBy": "TC-002-11",
    "steps": [
      { "stepNo": 1, "instruction": "Send GET /notes/{id} for an existing note", "action": "navigate", "selector": "/notes/{id}", "value": "GET" },
      { "stepNo": 2, "instruction": "Verify response is 200 with full note content", "action": "assert_text", "selector": "body", "value": "200 full note content" }
    ],
    "expectedResult": "API returns 200 with the full note content"
  },
  {
    "testId": "TC-001-08",
    "title": "[error] GET /notes/{id} is called",
    "module": "retrieve a single note by its id",
    "coveredBy": "TC-002-12",
    "steps": [
      { "stepNo": 1, "instruction": "Send GET /notes/{id} for an id that does not exist or was deleted", "action": "navigate", "selector": "/notes/{deleted-id}", "value": "GET" },
      { "stepNo": 2, "instruction": "Verify 404 response", "action": "assert_text", "selector": "body", "value": "404" }
    ],
    "expectedResult": "API returns 404"
  },
  {
    "testId": "TC-001-09",
    "title": "GET /notes is called",
    "module": "list all existing notes via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Ensure multiple notes exist", "action": "navigate", "selector": "/notes", "value": "POST multiple notes" },
      { "stepNo": 2, "instruction": "Send GET /notes", "action": "navigate", "selector": "/notes", "value": "GET" },
      { "stepNo": 3, "instruction": "Verify response is 200 with a list of all notes", "action": "assert_text", "selector": "body", "value": "200 list of all notes" }
    ],
    "expectedResult": "API returns 200 with a list of all notes"
  },
  {
    "testId": "TC-001-10",
    "title": "[edge] GET /notes is called",
    "module": "list all existing notes via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Precondition: no notes exist", "action": "navigate", "selector": "/notes", "value": "precondition: empty dataset" },
      { "stepNo": 2, "instruction": "Send GET /notes", "action": "navigate", "selector": "/notes", "value": "GET" },
      { "stepNo": 3, "instruction": "Verify 200 with an empty list, not an error", "action": "assert_text", "selector": "body", "value": "200 []" }
    ],
    "expectedResult": "API returns 200 with an empty list, not an error"
  },
  {
    "testId": "TC-001-11",
    "title": "[error] POST or PATCH /notes is called",
    "module": "receive clear field-level validation errors when note data is malformed",
    "steps": [
      { "stepNo": 1, "instruction": "Send POST /notes with an invalid data type for a field (title as a number)", "action": "navigate", "selector": "/notes", "value": "POST {title: 123, body}" },
      { "stepNo": 2, "instruction": "Verify 400 response names the invalid field", "action": "assert_text", "selector": "body", "value": "400 names field title" }
    ],
    "expectedResult": "API returns 400 with a message naming the invalid field"
  },
  {
    "testId": "TC-001-12",
    "title": "[edge] POST /notes is called",
    "module": "receive clear field-level validation errors when note data is malformed",
    "steps": [
      { "stepNo": 1, "instruction": "Send POST /notes with an empty JSON body", "action": "navigate", "selector": "/notes", "value": "POST {}" },
      { "stepNo": 2, "instruction": "Verify 400 identifies all missing required fields", "action": "assert_text", "selector": "body", "value": "400 lists all missing required fields" }
    ],
    "expectedResult": "API returns 400 identifying all missing required fields"
  },
  {
    "testId": "TC-001-13",
    "title": "POST /notes succeeds",
    "module": "have each note's creation and last-modified timestamps tracked automatically",
    "steps": [
      { "stepNo": 1, "instruction": "Send POST /notes with a valid title and body", "action": "navigate", "selector": "/notes", "value": "POST {title, body}" },
      { "stepNo": 2, "instruction": "Verify response includes a server-set created_at timestamp", "action": "assert_text", "selector": "body", "value": "created_at present" }
    ],
    "expectedResult": "the response includes a created_at timestamp set by the server"
  },
  {
    "testId": "TC-001-14",
    "title": "PATCH /notes/{id} succeeds",
    "module": "have each note's creation and last-modified timestamps tracked automatically",
    "steps": [
      { "stepNo": 1, "instruction": "Send PATCH /notes/{id} to edit an existing note", "action": "navigate", "selector": "/notes/{id}", "value": "PATCH {title}" },
      { "stepNo": 2, "instruction": "Verify response includes an updated_at timestamp reflecting the edit", "action": "assert_text", "selector": "body", "value": "updated_at present" }
    ],
    "expectedResult": "the response includes an updated_at timestamp reflecting the edit time"
  },
  {
    "testId": "TC-001-15",
    "title": "[edge] POST or PATCH is called",
    "module": "have each note's creation and last-modified timestamps tracked automatically",
    "steps": [
      { "stepNo": 1, "instruction": "Send POST /notes with a client-supplied created_at value", "action": "navigate", "selector": "/notes", "value": "POST {title, body, created_at: client-value}" },
      { "stepNo": 2, "instruction": "Verify created_at is server time and the client value is ignored", "action": "assert_text", "selector": "body", "value": "server-set created_at" },
      { "stepNo": 3, "instruction": "Send PATCH /notes/{id} with a client-supplied updated_at value and verify it is ignored", "action": "navigate", "selector": "/notes/{id}", "value": "PATCH {updated_at: client-value}" },
      { "stepNo": 4, "instruction": "Verify updated_at is server time", "action": "assert_text", "selector": "body", "value": "server-set updated_at" }
    ],
    "expectedResult": "API ignores client-supplied timestamps and uses server time"
  },
  {
    "testId": "TC-002-01",
    "title": "client sends a request to add tags to the note",
    "module": "add one or more tags to a note via the API",
    "coveredBy": "TC-002-05",
    "steps": [
      { "stepNo": 1, "instruction": "Send request adding valid tag strings to an existing note", "action": "navigate", "selector": "/notes/{id}/tags", "value": "POST {tags: ['work','idea']}" },
      { "stepNo": 2, "instruction": "Verify 200 with the updated tag list", "action": "assert_text", "selector": "body", "value": "200 updated tag list" }
    ],
    "expectedResult": "API returns 200 with the note's updated tag list"
  },
  {
    "testId": "TC-002-02",
    "title": "[edge] client submits a duplicate of an existing tag",
    "module": "add one or more tags to a note via the API",
    "coveredBy": "TC-002-06",
    "steps": [
      { "stepNo": 1, "instruction": "Add tag 'work' to a note with no tags, then submit 'work' again", "action": "navigate", "selector": "/notes/{id}/tags", "value": "POST {tags: ['work']} twice" },
      { "stepNo": 2, "instruction": "Verify returned tag list has no duplicates", "action": "assert_text", "selector": "body", "value": "deduplicated tag list" }
    ],
    "expectedResult": "API deduplicates and returns the tag list without duplicates"
  },
  {
    "testId": "TC-002-03",
    "title": "[error] client sends a tag-add request to that id",
    "module": "add one or more tags to a note via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Send tag-add request to a note id that does not exist", "action": "navigate", "selector": "/notes/{nonexistent-id}/tags", "value": "POST {tags: ['work']}" },
      { "stepNo": 2, "instruction": "Verify 404 with an error message", "action": "assert_text", "selector": "body", "value": "404 error message" }
    ],
    "expectedResult": "API returns 404 with an error message"
  },
  {
    "testId": "TC-002-04",
    "title": "client sends a request to remove that tag",
    "module": "remove a tag from a note via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Send request removing an existing tag from a note", "action": "navigate", "selector": "/notes/{id}/tags/work", "value": "DELETE" },
      { "stepNo": 2, "instruction": "Verify 200 and the tag is absent from the note's tag list", "action": "assert_text", "selector": "body", "value": "200 tag absent" }
    ],
    "expectedResult": "API returns 200 and the tag is no longer in the note's tag list"
  },
  {
    "testId": "TC-002-05",
    "title": "[error] client requests removal of that tag",
    "module": "remove a tag from a note via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Request removal of a tag the note does not have", "action": "navigate", "selector": "/notes/{id}/tags/absent-tag", "value": "DELETE" },
      { "stepNo": 2, "instruction": "Verify 404 or 400 indicating tag not found on note", "action": "assert_text", "selector": "body", "value": "404 or 400 tag not found" }
    ],
    "expectedResult": "API returns 404 or 400 indicating tag not found on note"
  },
  {
    "testId": "TC-002-06",
    "title": "client calls search endpoint with that term",
    "module": "query the search endpoint with free-text terms",
    "coveredBy": "TC-003-01",
    "steps": [
      { "stepNo": 1, "instruction": "Send GET search with a term that matches existing notes", "action": "navigate", "selector": "/notes/search", "value": "GET ?q=term" },
      { "stepNo": 2, "instruction": "Verify 200 with matching notes ranked by relevance", "action": "assert_text", "selector": "body", "value": "200 matching notes ranked" }
    ],
    "expectedResult": "API returns 200 with matching notes ranked by relevance within 300ms median"
  },
  {
    "testId": "TC-002-07",
    "title": "[edge] client calls search endpoint",
    "module": "query the search endpoint with free-text terms",
    "steps": [
      { "stepNo": 1, "instruction": "Send GET search with a term that matches no notes", "action": "navigate", "selector": "/notes/search", "value": "GET ?q=no-match-term" },
      { "stepNo": 2, "instruction": "Verify 200 with an empty result list, not an error", "action": "assert_text", "selector": "body", "value": "200 []" }
    ],
    "expectedResult": "API returns 200 with an empty result list, not an error"
  },
  {
    "testId": "TC-002-08",
    "title": "[error] client calls the search endpoint",
    "module": "query the search endpoint with free-text terms",
    "steps": [
      { "stepNo": 1, "instruction": "Send GET search with an empty query parameter", "action": "navigate", "selector": "/notes/search", "value": "GET ?q=" },
      { "stepNo": 2, "instruction": "Verify 400 with a validation error message", "action": "assert_text", "selector": "body", "value": "400 validation error" }
    ],
    "expectedResult": "API returns 400 with a validation error message"
  },
  {
    "testId": "TC-002-09",
    "title": "client calls search endpoint with that tag as a filter parameter",
    "module": "filter search results by one or more tags",
    "coveredBy": "TC-003-04",
    "steps": [
      { "stepNo": 1, "instruction": "Send GET search filtered by a tag that exists on notes", "action": "navigate", "selector": "/notes/search", "value": "GET ?tags=project-x" },
      { "stepNo": 2, "instruction": "Verify only notes containing that tag are returned", "action": "assert_text", "selector": "body", "value": "200 only tagged notes" }
    ],
    "expectedResult": "API returns 200 with only notes containing that tag"
  },
  {
    "testId": "TC-002-10",
    "title": "client calls search with both parameters combined",
    "module": "filter search results by one or more tags",
    "steps": [
      { "stepNo": 1, "instruction": "Send GET search with both a keyword and a tag filter", "action": "navigate", "selector": "/notes/search", "value": "GET ?q=keyword&tags=project-x" },
      { "stepNo": 2, "instruction": "Verify returned notes satisfy both conditions", "action": "assert_text", "selector": "body", "value": "200 notes matching both" }
    ],
    "expectedResult": "API returns 200 with notes satisfying both conditions"
  },
  {
    "testId": "TC-002-11",
    "title": "[error] client calls search endpoint with that tag",
    "module": "filter search results by one or more tags",
    "steps": [
      { "stepNo": 1, "instruction": "Send GET search filtered by a tag that does not exist", "action": "navigate", "selector": "/notes/search", "value": "GET ?tags=nonexistent-tag" },
      { "stepNo": 2, "instruction": "Verify 200 with an empty result list", "action": "assert_text", "selector": "body", "value": "200 []" }
    ],
    "expectedResult": "API returns 200 with an empty result list"
  },
  {
    "testId": "TC-002-12",
    "title": "client calls the list-tags endpoint",
    "module": "list all distinct tags currently in use",
    "steps": [
      { "stepNo": 1, "instruction": "Ensure notes with various tags exist", "action": "navigate", "selector": "/notes", "value": "POST notes with tags" },
      { "stepNo": 2, "instruction": "Send GET to the list-tags endpoint", "action": "navigate", "selector": "/tags", "value": "GET" },
      { "stepNo": 3, "instruction": "Verify 200 with a deduplicated list of all tags in use", "action": "assert_text", "selector": "body", "value": "200 deduplicated tag list" }
    ],
    "expectedResult": "API returns 200 with a deduplicated list of all tags in use"
  },
  {
    "testId": "TC-002-13",
    "title": "[edge] client calls the list-tags endpoint",
    "module": "list all distinct tags currently in use",
    "steps": [
      { "stepNo": 1, "instruction": "Precondition: no notes have any tags", "action": "navigate", "selector": "/tags", "value": "precondition: no tags" },
      { "stepNo": 2, "instruction": "Send GET to the list-tags endpoint", "action": "navigate", "selector": "/tags", "value": "GET" },
      { "stepNo": 3, "instruction": "Verify 200 with an empty list", "action": "assert_text", "selector": "body", "value": "200 []" }
    ],
    "expectedResult": "API returns 200 with an empty list"
  },
  {
    "testId": "TC-002-14",
    "title": "client requests a page via page/limit parameters",
    "module": "receive paginated search results",
    "coveredBy": "TC-003-13",
    "steps": [
      { "stepNo": 1, "instruction": "Send GET search with page and limit where results exceed page size", "action": "navigate", "selector": "/notes/search", "value": "GET ?q=term&page=1&limit=10" },
      { "stepNo": 2, "instruction": "Verify the correct page of results and total count are returned", "action": "assert_text", "selector": "body", "value": "200 page and total count" }
    ],
    "expectedResult": "API returns 200 with the correct page of results and total count"
  },
  {
    "testId": "TC-002-15",
    "title": "[edge] client calls search with that page number",
    "module": "receive paginated search results",
    "steps": [
      { "stepNo": 1, "instruction": "Send GET search with a page number beyond available results", "action": "navigate", "selector": "/notes/search", "value": "GET ?q=term&page=999&limit=10" },
      { "stepNo": 2, "instruction": "Verify 200 with an empty result list and correct total count", "action": "assert_text", "selector": "body", "value": "200 [] with total count" }
    ],
    "expectedResult": "API returns 200 with an empty result list and correct total count"
  },
  {
    "testId": "TC-002-16",
    "title": "[error] client calls search endpoint",
    "module": "receive paginated search results",
    "steps": [
      { "stepNo": 1, "instruction": "Send GET search with a negative or non-numeric page or limit", "action": "navigate", "selector": "/notes/search", "value": "GET ?q=term&page=-1" },
      { "stepNo": 2, "instruction": "Verify 400 with a validation error", "action": "assert_text", "selector": "body", "value": "400 validation error" }
    ],
    "expectedResult": "API returns 400 with a validation error"
  },
  {
    "testId": "TC-003-01",
    "title": "client calls archive endpoint for that note",
    "module": "archive a note via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Send archive request for an active note with a valid id", "action": "navigate", "selector": "/notes/{id}/archive", "value": "POST" },
      { "stepNo": 2, "instruction": "Verify 200 and note status is archived with content unchanged", "action": "assert_text", "selector": "body", "value": "200 status archived, content unchanged" }
    ],
    "expectedResult": "API returns 200 and note status is set to archived, content unchanged"
  },
  {
    "testId": "TC-003-02",
    "title": "[error] client calls archive endpoint",
    "module": "archive a note via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Send archive request for a note id that does not exist", "action": "navigate", "selector": "/notes/{nonexistent-id}/archive", "value": "POST" },
      { "stepNo": 2, "instruction": "Verify 404 with error body", "action": "assert_text", "selector": "body", "value": "404 error body" }
    ],
    "expectedResult": "API returns 404 with error body"
  },
  {
    "testId": "TC-003-03",
    "title": "[edge] client calls archive endpoint again",
    "module": "archive a note via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Send archive request for a note that is already archived", "action": "navigate", "selector": "/notes/{id}/archive", "value": "POST" },
      { "stepNo": 2, "instruction": "Verify 200 with no error and no state change", "action": "assert_text", "selector": "body", "value": "200 idempotent" }
    ],
    "expectedResult": "API returns 200 idempotently without error or state change"
  },
  {
    "testId": "TC-003-04",
    "title": "client calls unarchive endpoint for that note",
    "module": "restore (unarchive) a previously archived note via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Send unarchive request for an archived note with a valid id", "action": "navigate", "selector": "/notes/{id}/unarchive", "value": "POST" },
      { "stepNo": 2, "instruction": "Verify 200 and note status is active with content unchanged", "action": "assert_text", "selector": "body", "value": "200 status active, content unchanged" }
    ],
    "expectedResult": "API returns 200 and note status is set to active, content unchanged"
  },
  {
    "testId": "TC-003-05",
    "title": "[error] client calls unarchive endpoint",
    "module": "restore (unarchive) a previously archived note via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Send unarchive request for a note id that does not exist", "action": "navigate", "selector": "/notes/{nonexistent-id}/unarchive", "value": "POST" },
      { "stepNo": 2, "instruction": "Verify 404 with error body", "action": "assert_text", "selector": "body", "value": "404 error body" }
    ],
    "expectedResult": "API returns 404 with error body"
  },
  {
    "testId": "TC-003-06",
    "title": "[edge] client calls unarchive endpoint",
    "module": "restore (unarchive) a previously archived note via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Send unarchive request for a note that is already active", "action": "navigate", "selector": "/notes/{id}/unarchive", "value": "POST" },
      { "stepNo": 2, "instruction": "Verify 200 with no error and no state change", "action": "assert_text", "selector": "body", "value": "200 idempotent" }
    ],
    "expectedResult": "API returns 200 idempotently without error or state change"
  },
  {
    "testId": "TC-003-07",
    "title": "client requests the list filtered by archived status",
    "module": "list only archived notes via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Ensure some notes are archived and some are active", "action": "navigate", "selector": "/notes", "value": "precondition: mixed status" },
      { "stepNo": 2, "instruction": "Send GET /notes filtered by archived status", "action": "navigate", "selector": "/notes?status=archived", "value": "GET" },
      { "stepNo": 3, "instruction": "Verify 200 and only archived notes are returned", "action": "assert_text", "selector": "body", "value": "200 only archived" }
    ],
    "expectedResult": "API returns only archived notes with 200"
  },
  {
    "testId": "TC-003-08",
    "title": "[edge] client requests the archived list",
    "module": "list only archived notes via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Precondition: no notes are archived", "action": "navigate", "selector": "/notes", "value": "precondition: none archived" },
      { "stepNo": 2, "instruction": "Send GET /notes filtered by archived status", "action": "navigate", "selector": "/notes?status=archived", "value": "GET" },
      { "stepNo": 3, "instruction": "Verify 200 with an empty list, not an error", "action": "assert_text", "selector": "body", "value": "200 []" }
    ],
    "expectedResult": "API returns 200 with an empty list, not an error"
  },
  {
    "testId": "TC-003-09",
    "title": "client requests the default or active-filtered list",
    "module": "list only active (non-archived) notes via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Ensure some notes are archived and some are active", "action": "navigate", "selector": "/notes", "value": "precondition: mixed status" },
      { "stepNo": 2, "instruction": "Send GET /notes with the default or active filter", "action": "navigate", "selector": "/notes", "value": "GET" },
      { "stepNo": 3, "instruction": "Verify 200 and only active notes are returned", "action": "assert_text", "selector": "body", "value": "200 only active" }
    ],
    "expectedResult": "API returns only active notes with 200"
  },
  {
    "testId": "TC-003-10",
    "title": "[edge] client requests the active list",
    "module": "list only active (non-archived) notes via the API",
    "steps": [
      { "stepNo": 1, "instruction": "Precondition: all notes are archived", "action": "navigate", "selector": "/notes", "value": "precondition: all archived" },
      { "stepNo": 2, "instruction": "Send GET /notes with the active filter", "action": "navigate", "selector": "/notes", "value": "GET" },
      { "stepNo": 3, "instruction": "Verify 200 with an empty list, not an error", "action": "assert_text", "selector": "body", "value": "200 []" }
    ],
    "expectedResult": "API returns 200 with an empty list, not an error"
  },
  {
    "testId": "TC-003-11",
    "title": "client sends an edit or tag update request for it",
    "module": "edit or tag a note regardless of its archive state",
    "steps": [
      { "stepNo": 1, "instruction": "Send PATCH /notes/{id} with updated title for an archived note", "action": "navigate", "selector": "/notes/{archived-id}", "value": "PATCH {title}" },
      { "stepNo": 2, "instruction": "Verify 200 and the archive status is unchanged", "action": "assert_text", "selector": "body", "value": "200 status archived" }
    ],
    "expectedResult": "API applies the update and returns 200, archive status unchanged"
  },
  {
    "testId": "TC-003-12",
    "title": "[edge] update completes",
    "module": "edit or tag a note regardless of its archive state",
    "steps": [
      { "stepNo": 1, "instruction": "Send an update to an archived note", "action": "navigate", "selector": "/notes/{archived-id}", "value": "PATCH {body}" },
      { "stepNo": 2, "instruction": "Verify the note remains archived and no data fields are lost", "action": "assert_text", "selector": "body", "value": "archived, all fields present" }
    ],
    "expectedResult": "note remains archived and no data fields are lost"
  },
  {
    "testId": "TC-003-13",
    "title": "[error] client calls archive endpoint",
    "module": "receive consistent error handling for malformed archive/unarchive requests",
    "steps": [
      { "stepNo": 1, "instruction": "Send archive request with a malformed note id (non-UUID)", "action": "navigate", "selector": "/notes/not-a-valid-id/archive", "value": "POST" },
      { "stepNo": 2, "instruction": "Verify 400 with a descriptive error body", "action": "assert_text", "selector": "body", "value": "400 descriptive error" }
    ],
    "expectedResult": "API returns 400 with a descriptive error body"
  },
  {
    "testId": "TC-003-14",
    "title": "[edge] client calls unarchive endpoint",
    "module": "receive consistent error handling for malformed archive/unarchive requests",
    "steps": [
      { "stepNo": 1, "instruction": "Send unarchive request with an empty id field", "action": "navigate", "selector": "/notes//unarchive", "value": "POST" },
      { "stepNo": 2, "instruction": "Verify 400 is returned without a server error", "action": "assert_text", "selector": "body", "value": "400 no 500" }
    ],
    "expectedResult": "API returns 400 without server error"
  },
  {
    "testId": "TC-004-01",
    "title": "infrastructure is created",
    "module": "configure the database and application hosting region to an EU-only region at provisioning time",
    "steps": [
      { "stepNo": 1, "instruction": "Provision a new deployment environment", "action": "navigate", "selector": "/", "value": "provision environment" },
      { "stepNo": 2, "instruction": "Verify database and compute resources are located in an approved EU region", "action": "assert_text", "selector": "body", "value": "all resources EU region" }
    ],
    "expectedResult": "database and compute resources are located in an approved EU region"
  },
  {
    "testId": "TC-004-02",
    "title": "[error] the provisioning request is submitted",
    "module": "configure the database and application hosting region to an EU-only region at provisioning time",
    "steps": [
      { "stepNo": 1, "instruction": "Submit a provisioning request for a resource in a non-EU region", "action": "navigate", "selector": "/", "value": "provision non-EU resource" },
      { "stepNo": 2, "instruction": "Verify the request is rejected and the rejection is logged", "action": "assert_text", "selector": "body", "value": "rejected and logged" }
    ],
    "expectedResult": "the request is rejected and logged"
  },
  {
    "testId": "TC-004-03",
    "title": "the auditor requests the residency report",
    "module": "retrieve a report listing the region of every data store and service used by notes_api",
    "steps": [
      { "stepNo": 1, "instruction": "Request the residency report for deployed notes_api infrastructure", "action": "navigate", "selector": "/", "value": "GET residency report" },
      { "stepNo": 2, "instruction": "Verify each data store and service is listed with its region and all are confirmed EU", "action": "assert_text", "selector": "body", "value": "each resource with region, all EU" }
    ],
    "expectedResult": "the report lists each data store/service with its region and confirms all are EU"
  },
  {
    "testId": "TC-004-04",
    "title": "[edge] the report is generated",
    "module": "retrieve a report listing the region of every data store and service used by notes_api",
    "steps": [
      { "stepNo": 1, "instruction": "Generate the report for a service whose region is undetermined", "action": "navigate", "selector": "/", "value": "generate report" },
      { "stepNo": 2, "instruction": "Verify that service is flagged unverified and not assumed compliant", "action": "assert_text", "selector": "body", "value": "unverified" }
    ],
    "expectedResult": "that service is flagged as unverified rather than assumed compliant"
  },
  {
    "testId": "TC-004-05",
    "title": "the record is persisted and backed up",
    "module": "ensure note data persisted via the API is never written to backup or logging destinations outside the EU",
    "steps": [
      { "stepNo": 1, "instruction": "Create or edit a note via the API", "action": "navigate", "selector": "/notes", "value": "POST {title, body}" },
      { "stepNo": 2, "instruction": "Verify primary storage and backups reside in EU regions", "action": "assert_text", "selector": "body", "value": "primary and backup EU" }
    ],
    "expectedResult": "both primary storage and backups reside in EU regions"
  },
  {
    "testId": "TC-004-06",
    "title": "[error] the system attempts to write to it",
    "module": "ensure note data persisted via the API is never written to backup or logging destinations outside the EU",
    "steps": [
      { "stepNo": 1, "instruction": "Configure a logging or backup destination outside the EU and trigger a write", "action": "navigate", "selector": "/", "value": "non-EU destination write" },
      { "stepNo": 2, "instruction": "Verify the write is blocked and an alert is raised", "action": "assert_text", "selector": "body", "value": "blocked and alert raised" }
    ],
    "expectedResult": "the write is blocked and an alert is raised"
  },
  {
    "testId": "TC-004-07",
    "title": "the auditor reviews the service's data processing location",
    "module": "verify that any third-party services or dependencies used by notes_api also store/process data within the EU",
    "steps": [
      { "stepNo": 1, "instruction": "Review the documented processing location of a third-party dependency", "action": "navigate", "selector": "/", "value": "review dependency" },
      { "stepNo": 2, "instruction": "Verify the location is documented as EU-resident", "action": "assert_text", "selector": "body", "value": "EU-resident" }
    ],
    "expectedResult": "the location is documented as EU-resident"
  },
  {
    "testId": "TC-004-08",
    "title": "[edge] the audit is run",
    "module": "verify that any third-party services or dependencies used by notes_api also store/process data within the EU",
    "steps": [
      { "stepNo": 1, "instruction": "Run the audit for a third-party service whose residency cannot be confirmed", "action": "navigate", "selector": "/", "value": "run audit" },
      { "stepNo": 2, "instruction": "Verify the dependency is flagged for remediation before launch", "action": "assert_text", "selector": "body", "value": "flagged for remediation" }
    ],
    "expectedResult": "the dependency is flagged for remediation before launch"
  },
  {
    "testId": "TC-004-09",
    "title": "the pre-launch check is run",
    "module": "run an automated pre-launch residency check across all environments (dev, staging, prod)",
    "steps": [
      { "stepNo": 1, "instruction": "Run the pre-launch residency check with all environments configured", "action": "navigate", "selector": "/", "value": "run check" },
      { "stepNo": 2, "instruction": "Verify pass/fail is reported per environment with region details", "action": "assert_text", "selector": "body", "value": "pass/fail per env with region" }
    ],
    "expectedResult": "it reports pass/fail per environment with region details"
  },
  {
    "testId": "TC-004-10",
    "title": "[error] the check runs",
    "module": "run an automated pre-launch residency check across all environments (dev, staging, prod)",
    "steps": [
      { "stepNo": 1, "instruction": "Run the check with a non-EU resource present in an environment", "action": "navigate", "selector": "/", "value": "run check with non-EU resource" },
      { "stepNo": 2, "instruction": "Verify launch is blocked until the resource is corrected", "action": "assert_text", "selector": "body", "value": "launch blocked" }
    ],
    "expectedResult": "launch is blocked until the resource is corrected"
  },
  {
    "testId": "TC-004-11",
    "title": "the auditor views the audit history",
    "module": "access a historical log of residency audit results over time",
    "steps": [
      { "stepNo": 1, "instruction": "Open the audit history after multiple audits have run", "action": "navigate", "selector": "/", "value": "open audit history" },
      { "stepNo": 2, "instruction": "Verify each past audit's date, scope and result are listed", "action": "assert_text", "selector": "body", "value": "date, scope, result listed" }
    ],
    "expectedResult": "each past audit's date, scope, and result are listed"
  },
  {
    "testId": "TC-004-12",
    "title": "[edge] the auditor views the audit history",
    "module": "access a historical log of residency audit results over time",
    "steps": [
      { "stepNo": 1, "instruction": "Precondition: no audits have been run", "action": "navigate", "selector": "/", "value": "precondition: no audits" },
      { "stepNo": 2, "instruction": "Open the audit history", "action": "navigate", "selector": "/", "value": "open audit history" },
      { "stepNo": 3, "instruction": "Verify an empty state is shown rather than an error", "action": "assert_text", "selector": "body", "value": "empty state" }
    ],
    "expectedResult": "an empty state is shown rather than an error"
  },
  {
    "testId": "TC-005-01",
    "title": "all routes are listed",
    "module": "define the API surface with exactly 5 endpoint groups (create, edit, tag, search, archive note)",
    "steps": [
      { "stepNo": 1, "instruction": "Generate the API spec and list all routes", "action": "navigate", "selector": "/openapi.json", "value": "GET" },
      { "stepNo": 2, "instruction": "Verify only create, edit, tag, search and archive note routes exist", "action": "assert_text", "selector": "body", "value": "5 capability groups only" }
    ],
    "expectedResult": "only routes for create, edit, tag, search, and archive note exist"
  },
  {
    "testId": "TC-005-02",
    "title": "[edge] the route is added to the codebase",
    "module": "define the API surface with exactly 5 endpoint groups (create, edit, tag, search, archive note)",
    "steps": [
      { "stepNo": 1, "instruction": "Add a route outside the 5 capabilities to the codebase", "action": "navigate", "selector": "/", "value": "add out-of-scope route" },
      { "stepNo": 2, "instruction": "Verify the spec-compliance check fails the build", "action": "assert_text", "selector": "body", "value": "build failed" }
    ],
    "expectedResult": "the spec-compliance check fails the build"
  },
  {
    "testId": "TC-005-03",
    "title": "the request is submitted",
    "module": "reject any request payload containing file/binary attachment data on the create or edit endpoints",
    "steps": [
      { "stepNo": 1, "instruction": "Submit a note with only title, body and tags", "action": "navigate", "selector": "/notes", "value": "POST {title, body, tags}" },
      { "stepNo": 2, "instruction": "Verify the note is saved successfully", "action": "assert_text", "selector": "body", "value": "201 saved" }
    ],
    "expectedResult": "the note is saved successfully"
  },
  {
    "testId": "TC-005-04",
    "title": "[error] the request hits create or edit endpoint",
    "module": "reject any request payload containing file/binary attachment data on the create or edit endpoints",
    "steps": [
      { "stepNo": 1, "instruction": "Send a multipart/form-data request with a file field to the create endpoint", "action": "navigate", "selector": "/notes", "value": "POST multipart with file" },
      { "stepNo": 2, "instruction": "Verify 400 with an 'attachments not supported' error", "action": "assert_text", "selector": "body", "value": "400 attachments not supported" }
    ],
    "expectedResult": "the API returns 400 with an 'attachments not supported' error"
  },
  {
    "testId": "TC-005-05",
    "title": "checked for protocol types",
    "module": "ensure no WebSocket, SSE, or polling-for-live-updates endpoints are exposed",
    "steps": [
      { "stepNo": 1, "instruction": "Inspect the API route registry for protocol types", "action": "navigate", "selector": "/openapi.json", "value": "inspect registry" },
      { "stepNo": 2, "instruction": "Verify only standard HTTP request/response endpoints are present", "action": "assert_text", "selector": "body", "value": "HTTP only" }
    ],
    "expectedResult": "only standard HTTP request/response endpoints are present"
  },
  {
    "testId": "TC-005-06",
    "title": "[error] CI scope-guardrail check runs",
    "module": "ensure no WebSocket, SSE, or polling-for-live-updates endpoints are exposed",
    "steps": [
      { "stepNo": 1, "instruction": "Open a pull request that adds a WebSocket or SSE route and run the CI scope-guardrail check", "action": "navigate", "selector": "/", "value": "run CI guardrail" },
      { "stepNo": 2, "instruction": "Verify the check fails and blocks merge", "action": "assert_text", "selector": "body", "value": "check failed, merge blocked" }
    ],
    "expectedResult": "the check fails and blocks merge"
  },
  {
    "testId": "TC-005-07",
    "title": "the scope-compliance suite runs",
    "module": "run an automated scope-compliance test suite against every build",
    "steps": [
      { "stepNo": 1, "instruction": "Run the scope-compliance suite against a build", "action": "navigate", "selector": "/", "value": "run suite" },
      { "stepNo": 2, "instruction": "Verify it reports exactly 5 capability groups with zero extras", "action": "assert_text", "selector": "body", "value": "5 groups, 0 extras" }
    ],
    "expectedResult": "it reports exactly 5 capability groups with zero extras"
  },
  {
    "testId": "TC-005-08",
    "title": "[edge] the suite inspects its parameters",
    "module": "run an automated scope-compliance test suite against every build",
    "steps": [
      { "stepNo": 1, "instruction": "Provide an endpoint mapped to search that accepts attachment-filter parameters", "action": "navigate", "selector": "/notes/search", "value": "GET ?attachment=true" },
      { "stepNo": 2, "instruction": "Verify the suite flags the parameter as out-of-scope", "action": "assert_text", "selector": "body", "value": "flagged out-of-scope" }
    ],
    "expectedResult": "it flags the parameter as out-of-scope"
  },
  {
    "testId": "TC-005-09",
    "title": "a reader views the scope section",
    "module": "maintain a documented out-of-scope list (attachments, mobile-specific endpoints, real-time) in the API spec",
    "steps": [
      { "stepNo": 1, "instruction": "Open the published API documentation scope section", "action": "navigate", "selector": "/docs", "value": "GET" },
      { "stepNo": 2, "instruction": "Verify attachments, mobile endpoints and real-time features are listed as out of scope", "action": "assert_text", "selector": "body", "value": "attachments, mobile, real-time out of scope" }
    ],
    "expectedResult": "attachments, mobile endpoints, and real-time features are explicitly listed as out of scope"
  },
  {
    "testId": "TC-005-10",
    "title": "[edge] they check the documented out-of-scope list first",
    "module": "maintain a documented out-of-scope list (attachments, mobile-specific endpoints, real-time) in the API spec",
    "steps": [
      { "stepNo": 1, "instruction": "Open the out-of-scope list before proposing an attachment endpoint", "action": "navigate", "selector": "/docs", "value": "GET" },
      { "stepNo": 2, "instruction": "Verify the attachment feature is explicitly excluded", "action": "assert_text", "selector": "body", "value": "attachments excluded" }
    ],
    "expectedResult": "they see the feature is explicitly excluded before writing code"
  },
  {
    "testId": "TC-005-11",
    "title": "checked for mobile-only parameters",
    "module": "block any endpoint or parameter designed solely to support a native mobile client (e.g. device push tokens)",
    "steps": [
      { "stepNo": 1, "instruction": "Review API endpoints and parameters for mobile-only fields", "action": "navigate", "selector": "/openapi.json", "value": "GET" },
      { "stepNo": 2, "instruction": "Verify none are present", "action": "assert_text", "selector": "body", "value": "no mobile-only params" }
    ],
    "expectedResult": "none are present"
  },
  {
    "testId": "TC-005-12",
    "title": "[error] submitted to any endpoint",
    "module": "block any endpoint or parameter designed solely to support a native mobile client (e.g. device push tokens)",
    "steps": [
      { "stepNo": 1, "instruction": "Submit a request containing a device push-token field", "action": "navigate", "selector": "/notes", "value": "POST {title, body, device_push_token}" },
      { "stepNo": 2, "instruction": "Verify 400 indicating the field is unsupported", "action": "assert_text", "selector": "body", "value": "400 field unsupported" }
    ],
    "expectedResult": "the API returns 400 indicating the field is unsupported"
  },
  {
    "testId": "TC-006-23",
    "title": "the notes list page loads",
    "module": "view a list of all notes in the web app",
    "steps": [
      { "stepNo": 1, "instruction": "Ensure notes exist in notes_api with distinct titles and created dates", "action": "navigate", "selector": "/notes", "value": "precondition: notes exist" },
      { "stepNo": 2, "instruction": "Open the notes list page", "action": "navigate", "selector": "/notes", "value": "GET /notes" },
      { "stepNo": 3, "instruction": "Verify the sort control is shown above the list with Newest first selected by default", "action": "assert_visible", "selector": "[data-testid=\"US-006-01-sort-control\"]", "value": "Newest first" },
      { "stepNo": 4, "instruction": "Verify the sort control offers Newest first, Oldest first and Title A-Z", "action": "assert_text", "selector": "[data-testid=\"US-006-01-sort-control\"]", "value": "Newest first, Oldest first, Title A-Z" },
      { "stepNo": 5, "instruction": "Verify each note's title and status are rendered", "action": "assert_visible", "selector": "[data-testid=\"US-006-01-note-item\"]", "value": "title and status shown" },
      { "stepNo": 6, "instruction": "Verify the live result count equals the number of notes listed", "action": "assert_text", "selector": "[data-testid=\"US-006-01-result-count\"]", "value": "count equals listed notes" },
      { "stepNo": 7, "instruction": "Verify the first item is the note with the latest created_at", "action": "assert_text", "selector": "[data-testid=\"US-006-01-note-item\"]", "value": "first item latest created_at" },
      { "stepNo": 8, "instruction": "Select Oldest first and verify the list order reverses by created_at", "action": "select", "selector": "[data-testid=\"US-006-01-sort-control\"]", "value": "Oldest first" },
      { "stepNo": 9, "instruction": "Verify the first item is now the note with the earliest created_at", "action": "assert_text", "selector": "[data-testid=\"US-006-01-note-item\"]", "value": "first item earliest created_at" },
      { "stepNo": 10, "instruction": "Select Title A-Z and verify titles are ordered ascending", "action": "select", "selector": "[data-testid=\"US-006-01-sort-control\"]", "value": "Title A-Z" },
      { "stepNo": 11, "instruction": "Verify titles are listed in ascending A-Z order", "action": "assert_text", "selector": "[data-testid=\"US-006-01-note-item\"]", "value": "titles ascending" },
      { "stepNo": 12, "instruction": "Select Oldest first, reload the page and verify the selection persists", "action": "select", "selector": "[data-testid=\"US-006-01-sort-control\"]", "value": "Oldest first" },
      { "stepNo": 13, "instruction": "Reload the notes list page", "action": "navigate", "selector": "/notes", "value": "reload" },
      { "stepNo": 14, "instruction": "Verify the sort control still shows Oldest first and no sort parameter was sent to GET /notes", "action": "assert_text", "selector": "[data-testid=\"US-006-01-sort-control\"]", "value": "Oldest first persisted, no sort param" }
    ],
    "expectedResult": "the web app calls GET /notes, renders each note's title and status, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with Newest first selected by default, and shows a live result count above the list equal to the number of notes listed"
  },
  {
    "testId": "TC-006-24",
    "title": "the notes list page loads",
    "module": "view a list of all notes in the web app",
    "steps": [
      { "stepNo": 1, "instruction": "Precondition: no notes exist yet", "action": "navigate", "selector": "/notes", "value": "precondition: empty dataset" },
      { "stepNo": 2, "instruction": "Open the notes list page", "action": "navigate", "selector": "/notes", "value": "GET /notes" },
      { "stepNo": 3, "instruction": "Verify the empty-state message is shown", "action": "assert_visible", "selector": "[data-testid=\"US-006-01-empty-state\"]", "value": "empty-state message" },
      { "stepNo": 4, "instruction": "Verify the result count shows 0", "action": "assert_text", "selector": "[data-testid=\"US-006-01-result-count\"]", "value": "0" }
    ],
    "expectedResult": "the web app shows an empty-state message and a result count of 0 instead of an error"
  },
  {
    "testId": "TC-006-25",
    "title": "the notes list page loads",
    "module": "view a list of all notes in the web app",
    "steps": [
      { "stepNo": 1, "instruction": "Precondition: notes_api returns an error for GET /notes", "action": "navigate", "selector": "/notes", "value": "precondition: API error" },
      { "stepNo": 2, "instruction": "Open the notes list page", "action": "navigate", "selector": "/notes", "value": "GET /notes" },
      { "stepNo": 3, "instruction": "Verify a user-facing error state is shown rather than a blank screen", "action": "assert_visible", "selector": "[data-testid=\"US-006-01-error-state\"]", "value": "user-facing error message" }
    ],
    "expectedResult": "the web app shows a user-facing error state instead of a blank screen"
  },
  {
    "testId": "TC-006-04",
    "title": "the user submits",
    "module": "create a new note using the web editor",
    "steps": [
      { "stepNo": 1, "instruction": "Open the new note editor", "action": "navigate", "selector": "/notes/new", "value": "" },
      { "stepNo": 2, "instruction": "Enter a title", "action": "fill", "selector": "[data-testid=\"US-006-02-title-input\"]", "value": "Meeting notes" },
      { "stepNo": 3, "instruction": "Enter a body", "action": "fill", "selector": "[data-testid=\"US-006-02-body-input\"]", "value": "Agenda and actions" },
      { "stepNo": 4, "instruction": "Submit the note", "action": "click", "selector": "[data-testid=\"US-006-02-submit\"]", "value": "" },
      { "stepNo": 5, "instruction": "Verify the web app navigates to the saved note", "action": "assert_url", "selector": "/notes/", "value": "/notes/:id" }
    ],
    "expectedResult": "the web app calls POST /notes and navigates to the saved note"
  },
  {
    "testId": "TC-006-05",
    "title": "the user submits",
    "module": "create a new note using the web editor",
    "steps": [
      { "stepNo": 1, "instruction": "Open the new note editor", "action": "navigate", "selector": "/notes/new", "value": "" },
      { "stepNo": 2, "instruction": "Enter a body and leave the title empty", "action": "fill", "selector": "[data-testid=\"US-006-02-body-input\"]", "value": "Body only" },
      { "stepNo": 3, "instruction": "Submit the note", "action": "click", "selector": "[data-testid=\"US-006-02-submit\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify the API validation error is shown inline next to the title", "action": "assert_visible", "selector": "[data-testid=\"US-006-02-title-error\"]", "value": "validation error" },
      { "stepNo": 5, "instruction": "Verify the URL remains on the editor, so no note was created", "action": "assert_url", "selector": "/notes/new", "value": "" }
    ],
    "expectedResult": "the web app shows the API's validation error inline without creating a note"
  },
  {
    "testId": "TC-006-06",
    "title": "the response is received",
    "module": "create a new note using the web editor",
    "steps": [
      { "stepNo": 1, "instruction": "Open the new note editor", "action": "navigate", "selector": "/notes/new", "value": "" },
      { "stepNo": 2, "instruction": "Enter a valid title and body", "action": "fill", "selector": "[data-testid=\"US-006-02-title-input\"]", "value": "Valid title" },
      { "stepNo": 3, "instruction": "Submit the note", "action": "click", "selector": "[data-testid=\"US-006-02-submit\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify the created_at shown matches the server-set created_at returned by the API", "action": "assert_text", "selector": "[data-testid=\"US-006-02-created-at\"]", "value": "matches server created_at" }
    ],
    "expectedResult": "the note's server-set created_at is reflected in the UI"
  },
  {
    "testId": "TC-006-07",
    "title": "the user changes the title or body and saves",
    "module": "edit an existing note's title or body using the web editor",
    "steps": [
      { "stepNo": 1, "instruction": "Open an existing note in the editor", "action": "navigate", "selector": "/notes/{id}/edit", "value": "" },
      { "stepNo": 2, "instruction": "Change the title", "action": "fill", "selector": "[data-testid=\"US-006-03-title-input\"]", "value": "Updated title" },
      { "stepNo": 3, "instruction": "Save the note", "action": "click", "selector": "[data-testid=\"US-006-03-save\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify the updated content is shown", "action": "assert_text", "selector": "[data-testid=\"US-006-03-title-display\"]", "value": "Updated title" }
    ],
    "expectedResult": "the web app calls PUT/PATCH /notes/{id} and shows the updated content"
  },
  {
    "testId": "TC-006-08",
    "title": "the response is received",
    "module": "edit an existing note's title or body using the web editor",
    "steps": [
      { "stepNo": 1, "instruction": "Open an existing note in the editor", "action": "navigate", "selector": "/notes/{id}/edit", "value": "" },
      { "stepNo": 2, "instruction": "Change the title and save while the save request returns 404", "action": "fill", "selector": "[data-testid=\"US-006-03-title-input\"]", "value": "Changed title" },
      { "stepNo": 3, "instruction": "Click save", "action": "click", "selector": "[data-testid=\"US-006-03-save\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify a 'note not found' message is shown", "action": "assert_text", "selector": "[data-testid=\"US-006-03-not-found\"]", "value": "note not found" }
    ],
    "expectedResult": "the web app shows a 'note not found' message and does not apply local changes"
  },
  {
    "testId": "TC-006-09",
    "title": "the response is received",
    "module": "edit an existing note's title or body using the web editor",
    "steps": [
      { "stepNo": 1, "instruction": "Open an existing note in the editor", "action": "navigate", "selector": "/notes/{id}/edit", "value": "" },
      { "stepNo": 2, "instruction": "Clear the title and save so the API returns a field-level validation error", "action": "fill", "selector": "[data-testid=\"US-006-03-title-input\"]", "value": "" },
      { "stepNo": 3, "instruction": "Click save", "action": "click", "selector": "[data-testid=\"US-006-03-save\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify the invalid title field is highlighted", "action": "assert_visible", "selector": "[data-testid=\"US-006-03-title-error\"]", "value": "field highlighted" }
    ],
    "expectedResult": "the web app highlights the invalid field"
  },
  {
    "testId": "TC-006-26",
    "title": "they submit",
    "module": "search notes using a search box with free-text and tag filters",
    "steps": [
      { "stepNo": 1, "instruction": "Select Oldest first on the notes list", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Select Oldest first on the sort control", "action": "select", "selector": "[data-testid=\"US-006-01-sort-control\"]", "value": "Oldest first" },
      { "stepNo": 3, "instruction": "Open the search screen and verify the sort selection carries over", "action": "navigate", "selector": "/notes/search", "value": "" },
      { "stepNo": 4, "instruction": "Verify the sort control still shows Oldest first", "action": "assert_text", "selector": "[data-testid=\"US-006-01-sort-control\"]", "value": "Oldest first" },
      { "stepNo": 5, "instruction": "Enter a keyword in the search box", "action": "fill", "selector": "[data-testid=\"US-006-04-search-input\"]", "value": "keyword" },
      { "stepNo": 6, "instruction": "Submit the search", "action": "click", "selector": "[data-testid=\"US-006-04-search-submit\"]", "value": "" },
      { "stepNo": 7, "instruction": "Verify the search request contains no sort parameter", "action": "assert_url", "selector": "/notes/search", "value": "q=keyword, no sort param" },
      { "stepNo": 8, "instruction": "Verify matching notes render in Oldest first order by created_at", "action": "assert_text", "selector": "[data-testid=\"US-006-04-result-item\"]", "value": "ordered oldest first" },
      { "stepNo": 9, "instruction": "Verify the sort selection is unchanged after results load", "action": "assert_text", "selector": "[data-testid=\"US-006-01-sort-control\"]", "value": "Oldest first" },
      { "stepNo": 10, "instruction": "Verify the live result count equals the number of matches", "action": "assert_text", "selector": "[data-testid=\"US-006-04-result-count\"]", "value": "count equals matches" }
    ],
    "expectedResult": "the web app calls the search endpoint, renders matching notes in the currently selected sort order (Newest first, Oldest first, or Title A-Z) without changing the sort selection, and updates the live result count above the list to the number of matches"
  },
  {
    "testId": "TC-006-27",
    "title": "results load",
    "module": "search notes using a search box with free-text and tag filters",
    "steps": [
      { "stepNo": 1, "instruction": "Open the search screen", "action": "navigate", "selector": "/notes/search", "value": "" },
      { "stepNo": 2, "instruction": "Enter a keyword that matches no notes", "action": "fill", "selector": "[data-testid=\"US-006-04-search-input\"]", "value": "no-match-term" },
      { "stepNo": 3, "instruction": "Submit the search", "action": "click", "selector": "[data-testid=\"US-006-04-search-submit\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify the empty-results message is shown", "action": "assert_visible", "selector": "[data-testid=\"US-006-04-empty-results\"]", "value": "empty-results message" },
      { "stepNo": 5, "instruction": "Verify the result count shows 0", "action": "assert_text", "selector": "[data-testid=\"US-006-04-result-count\"]", "value": "0" }
    ],
    "expectedResult": "the web app shows an empty-results message and a result count of 0 instead of an error"
  },
  {
    "testId": "TC-006-28",
    "title": "they apply it",
    "module": "search notes using a search box with free-text and tag filters",
    "steps": [
      { "stepNo": 1, "instruction": "Select Title A-Z on the sort control", "action": "select", "selector": "[data-testid=\"US-006-01-sort-control\"]", "value": "Title A-Z" },
      { "stepNo": 2, "instruction": "Open the search screen", "action": "navigate", "selector": "/notes/search", "value": "" },
      { "stepNo": 3, "instruction": "Select the tag filter project-x", "action": "select", "selector": "[data-testid=\"US-006-04-tag-filter\"]", "value": "project-x" },
      { "stepNo": 4, "instruction": "Apply the tag filter", "action": "click", "selector": "[data-testid=\"US-006-04-apply-filter\"]", "value": "" },
      { "stepNo": 5, "instruction": "Verify the search request includes tags=project-x", "action": "assert_url", "selector": "/notes/search", "value": "tags=project-x" },
      { "stepNo": 6, "instruction": "Verify only notes with the tag are rendered in Title A-Z order", "action": "assert_text", "selector": "[data-testid=\"US-006-04-result-item\"]", "value": "only tagged, titles ascending" },
      { "stepNo": 7, "instruction": "Verify the live result count equals the number of tagged matches", "action": "assert_text", "selector": "[data-testid=\"US-006-04-result-count\"]", "value": "count equals tagged matches" }
    ],
    "expectedResult": "the web app includes the tag filter in the search request, renders only matching notes in the selected sort order, and updates the live result count to match"
  },
  {
    "testId": "TC-006-13",
    "title": "the user adds a tag",
    "module": "add or remove tags on a note from the web app",
    "steps": [
      { "stepNo": 1, "instruction": "Open a note's tag management screen", "action": "navigate", "selector": "/notes/{id}/tags", "value": "" },
      { "stepNo": 2, "instruction": "Enter a new tag name", "action": "fill", "selector": "[data-testid=\"US-006-05-tag-input\"]", "value": "work" },
      { "stepNo": 3, "instruction": "Click add tag", "action": "click", "selector": "[data-testid=\"US-006-05-tag-add\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify the updated tag list displays the new tag", "action": "assert_text", "selector": "[data-testid=\"US-006-05-tag-list\"]", "value": "work" }
    ],
    "expectedResult": "the web app calls the tag-add endpoint and displays the updated tag list"
  },
  {
    "testId": "TC-006-14",
    "title": "the user removes it",
    "module": "add or remove tags on a note from the web app",
    "steps": [
      { "stepNo": 1, "instruction": "Open a note that has an existing tag", "action": "navigate", "selector": "/notes/{id}/tags", "value": "" },
      { "stepNo": 2, "instruction": "Click remove on the existing tag", "action": "click", "selector": "[data-testid=\"US-006-05-tag-remove\"]", "value": "" },
      { "stepNo": 3, "instruction": "Verify the tag disappears from the displayed list", "action": "assert_text", "selector": "[data-testid=\"US-006-05-tag-list\"]", "value": "tag absent" }
    ],
    "expectedResult": "the web app calls the tag-remove endpoint and the tag disappears from the displayed list"
  },
  {
    "testId": "TC-006-15",
    "title": "the user clicks archive",
    "module": "archive or restore a note from the web app",
    "steps": [
      { "stepNo": 1, "instruction": "Open an active note", "action": "navigate", "selector": "/notes/{id}", "value": "" },
      { "stepNo": 2, "instruction": "Click archive", "action": "click", "selector": "[data-testid=\"US-006-06-archive-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Open the default active notes list", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 4, "instruction": "Verify the note no longer appears in the active list", "action": "assert_text", "selector": "[data-testid=\"US-006-01-note-item\"]", "value": "note absent" }
    ],
    "expectedResult": "the web app calls the archive endpoint and the note disappears from the default active list"
  },
  {
    "testId": "TC-006-16",
    "title": "the user clicks restore",
    "module": "archive or restore a note from the web app",
    "steps": [
      { "stepNo": 1, "instruction": "Open the archived notes screen", "action": "navigate", "selector": "/notes/archived", "value": "" },
      { "stepNo": 2, "instruction": "Open an archived note and click restore", "action": "click", "selector": "[data-testid=\"US-006-06-restore-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Open the active notes list", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 4, "instruction": "Verify the note reappears in the active list", "action": "assert_text", "selector": "[data-testid=\"US-006-01-note-item\"]", "value": "note present" }
    ],
    "expectedResult": "the web app calls the unarchive endpoint and the note reappears in the active list"
  }
]
{"documents": [{"id": "TC-001-01", "type": "test_plan", "path": "test-plans/TC-001-01.md", "content": "---\nid: TC-001-01\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-01]]\nepic: [[EP-001]]\nmodule: create a new note via the API with title and body text\n---\n\n# TC-001-01: POST /notes is called\n\n**Story:** [[US-001-01]]\n**Module:** create a new note via the API with title and body text\n\n## Preconditions\na valid title and body payload\n\n## Expected Result\nAPI returns 201 with the created note including its id\n\n## Acceptance Criteria\nGiven a valid title and body payload, when POST /notes is called, then API returns 201 with the created note including its id\nGiven a payload missing required title field, when POST /notes is called, then API returns 400 with a validation error and no note is created\nGiven a body field at the maximum allowed length, when POST /notes is called, then API returns 201 and stores the full body without truncation\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send POST /notes with a valid title and body payload | navigate | `/notes` | POST {title, body} |\n| 2 | Verify response is 201 with the created note including its id | assert_text | `body` | 201 created note with id |\n", "parent_ids": ["US-001-01", "EP-001"]}, {"id": "TC-001-02", "type": "test_plan", "path": "test-plans/TC-001-02.md", "content": "---\nid: TC-001-02\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-01]]\nepic: [[EP-001]]\nmodule: create a new note via the API with title and body text\n---\n\n# TC-001-02: [error] POST /notes is called\n\n**Story:** [[US-001-01]]\n**Module:** create a new note via the API with title and body text\n\n## Preconditions\na payload missing required title field\n\n## Expected Result\nAPI returns 400 with a validation error and no note is created\n\n## Acceptance Criteria\nGiven a valid title and body payload, when POST /notes is called, then API returns 201 with the created note including its id\nGiven a payload missing required title field, when POST /notes is called, then API returns 400 with a validation error and no note is created\nGiven a body field at the maximum allowed length, when POST /notes is called, then API returns 201 and stores the full body without truncation\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send POST /notes with a payload missing the required title field | navigate | `/notes` | POST {body} |\n| 2 | Verify 400 validation error and that no note was created | assert_text | `body` | 400 validation error |\n", "parent_ids": ["US-001-01", "EP-001"]}, {"id": "TC-001-03", "type": "test_plan", "path": "test-plans/TC-001-03.md", "content": "---\nid: TC-001-03\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-01]]\nepic: [[EP-001]]\nmodule: create a new note via the API with title and body text\n---\n\n# TC-001-03: [edge] POST /notes is called\n\n**Story:** [[US-001-01]]\n**Module:** create a new note via the API with title and body text\n\n## Preconditions\na body field at the maximum allowed length\n\n## Expected Result\nAPI returns 201 and stores the full body without truncation\n\n## Acceptance Criteria\nGiven a valid title and body payload, when POST /notes is called, then API returns 201 with the created note including its id\nGiven a payload missing required title field, when POST /notes is called, then API returns 400 with a validation error and no note is created\nGiven a body field at the maximum allowed length, when POST /notes is called, then API returns 201 and stores the full body without truncation\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send POST /notes with a valid title and a body at the maximum allowed length | navigate | `/notes` | POST {title, body: max-length string} |\n| 2 | Verify response is 201 | assert_text | `body` | 201 |\n| 3 | Verify stored body equals the full max-length string without truncation | assert_text | `body` | body length unchanged |\n", "parent_ids": ["US-001-01", "EP-001"]}, {"id": "TC-001-04", "type": "test_plan", "path": "test-plans/TC-001-04.md", "content": "---\nid: TC-001-04\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-02]]\nepic: [[EP-001]]\nmodule: edit an existing note's title or body via the API\n---\n\n# TC-001-04: PUT/PATCH /notes/{id} is called\n\n**Story:** [[US-001-02]]\n**Module:** edit an existing note's title or body via the API\n\n## Preconditions\nan existing note id and updated fields\n\n## Expected Result\nAPI returns 200 with the updated note reflecting changes\n\n## Acceptance Criteria\nGiven an existing note id and updated fields, when PUT/PATCH /notes/{id} is called, then API returns 200 with the updated note reflecting changes\nGiven a note id that does not exist, when PUT/PATCH /notes/{id} is called, then API returns 404 and no data is altered\nGiven a concurrent edit request on the same note, when two update requests are sent in quick succession, then the last accepted write wins and no fields are partially corrupted\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send PATCH /notes/{id} with updated title and body for an existing note | navigate | `/notes/{id}` | PATCH {title, body} |\n| 2 | Verify response is 200 with the updated note | assert_text | `body` | 200 updated note |\n", "parent_ids": ["US-001-02", "EP-001"]}, {"id": "TC-001-05", "type": "test_plan", "path": "test-plans/TC-001-05.md", "content": "---\nid: TC-001-05\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-02]]\nepic: [[EP-001]]\nmodule: edit an existing note's title or body via the API\n---\n\n# TC-001-05: [error] PUT/PATCH /notes/{id} is called\n\n**Story:** [[US-001-02]]\n**Module:** edit an existing note's title or body via the API\n\n## Preconditions\na note id that does not exist\n\n## Expected Result\nAPI returns 404 and no data is altered\n\n## Acceptance Criteria\nGiven an existing note id and updated fields, when PUT/PATCH /notes/{id} is called, then API returns 200 with the updated note reflecting changes\nGiven a note id that does not exist, when PUT/PATCH /notes/{id} is called, then API returns 404 and no data is altered\nGiven a concurrent edit request on the same note, when two update requests are sent in quick succession, then the last accepted write wins and no fields are partially corrupted\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send PATCH /notes/{id} using a non-existent note id | navigate | `/notes/{nonexistent-id}` | PATCH {title} |\n| 2 | Verify 404 and confirm no data was altered | assert_text | `body` | 404 |\n", "parent_ids": ["US-001-02", "EP-001"]}, {"id": "TC-001-06", "type": "test_plan", "path": "test-plans/TC-001-06.md", "content": "---\nid: TC-001-06\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-02]]\nepic: [[EP-001]]\nmodule: edit an existing note's title or body via the API\n---\n\n# TC-001-06: [edge] two update requests are sent in quick succession\n\n**Story:** [[US-001-02]]\n**Module:** edit an existing note's title or body via the API\n\n## Preconditions\na concurrent edit request on the same note\n\n## Expected Result\nthe last accepted write wins and no fields are partially corrupted\n\n## Acceptance Criteria\nGiven an existing note id and updated fields, when PUT/PATCH /notes/{id} is called, then API returns 200 with the updated note reflecting changes\nGiven a note id that does not exist, when PUT/PATCH /notes/{id} is called, then API returns 404 and no data is altered\nGiven a concurrent edit request on the same note, when two update requests are sent in quick succession, then the last accepted write wins and no fields are partially corrupted\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send PATCH /notes/{id} with payload A | navigate | `/notes/{id}` | PATCH {title: A} |\n| 2 | Send PATCH /notes/{id} with payload B immediately after | navigate | `/notes/{id}` | PATCH {body: B} |\n| 3 | Fetch the note and verify the last accepted write is reflected with no partially corrupted fields | assert_text | `body` | last write wins, fields intact |\n", "parent_ids": ["US-001-02", "EP-001"]}, {"id": "TC-001-07", "type": "test_plan", "path": "test-plans/TC-001-07.md", "content": "---\nid: TC-001-07\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-03]]\nepic: [[EP-001]]\nmodule: retrieve a single note by its id\n---\n\n# TC-001-07: GET /notes/{id} is called\n\n**Story:** [[US-001-03]]\n**Module:** retrieve a single note by its id\n\n## Preconditions\na note exists with a given id\n\n## Expected Result\nAPI returns 200 with the full note content\n\n## Acceptance Criteria\nGiven a note exists with a given id, when GET /notes/{id} is called, then API returns 200 with the full note content\nGiven a note id that does not exist or was deleted, when GET /notes/{id} is called, then API returns 404\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send GET /notes/{id} for an existing note | navigate | `/notes/{id}` | GET |\n| 2 | Verify response is 200 with full note content | assert_text | `body` | 200 full note content |\n", "parent_ids": ["US-001-03", "EP-001"]}, {"id": "TC-001-08", "type": "test_plan", "path": "test-plans/TC-001-08.md", "content": "---\nid: TC-001-08\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-03]]\nepic: [[EP-001]]\nmodule: retrieve a single note by its id\n---\n\n# TC-001-08: [error] GET /notes/{id} is called\n\n**Story:** [[US-001-03]]\n**Module:** retrieve a single note by its id\n\n## Preconditions\na note id that does not exist or was deleted\n\n## Expected Result\nAPI returns 404\n\n## Acceptance Criteria\nGiven a note exists with a given id, when GET /notes/{id} is called, then API returns 200 with the full note content\nGiven a note id that does not exist or was deleted, when GET /notes/{id} is called, then API returns 404\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send GET /notes/{id} for an id that does not exist or was deleted | navigate | `/notes/{deleted-id}` | GET |\n| 2 | Verify 404 response | assert_text | `body` | 404 |\n", "parent_ids": ["US-001-03", "EP-001"]}, {"id": "TC-001-09", "type": "test_plan", "path": "test-plans/TC-001-09.md", "content": "---\nid: TC-001-09\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-04]]\nepic: [[EP-001]]\nmodule: list all existing notes via the API\n---\n\n# TC-001-09: GET /notes is called\n\n**Story:** [[US-001-04]]\n**Module:** list all existing notes via the API\n\n## Preconditions\nmultiple notes exist\n\n## Expected Result\nAPI returns 200 with a list of all notes\n\n## Acceptance Criteria\nGiven multiple notes exist, when GET /notes is called, then API returns 200 with a list of all notes\nGiven no notes exist yet, when GET /notes is called, then API returns 200 with an empty list, not an error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Ensure multiple notes exist | navigate | `/notes` | POST multiple notes |\n| 2 | Send GET /notes | navigate | `/notes` | GET |\n| 3 | Verify response is 200 with a list of all notes | assert_text | `body` | 200 list of all notes |\n", "parent_ids": ["US-001-04", "EP-001"]}, {"id": "TC-001-10", "type": "test_plan", "path": "test-plans/TC-001-10.md", "content": "---\nid: TC-001-10\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-04]]\nepic: [[EP-001]]\nmodule: list all existing notes via the API\n---\n\n# TC-001-10: [edge] GET /notes is called\n\n**Story:** [[US-001-04]]\n**Module:** list all existing notes via the API\n\n## Preconditions\nno notes exist yet\n\n## Expected Result\nAPI returns 200 with an empty list, not an error\n\n## Acceptance Criteria\nGiven multiple notes exist, when GET /notes is called, then API returns 200 with a list of all notes\nGiven no notes exist yet, when GET /notes is called, then API returns 200 with an empty list, not an error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Precondition: no notes exist | navigate | `/notes` | precondition: empty dataset |\n| 2 | Send GET /notes | navigate | `/notes` | GET |\n| 3 | Verify 200 with an empty list, not an error | assert_text | `body` | 200 [] |\n", "parent_ids": ["US-001-04", "EP-001"]}, {"id": "TC-001-11", "type": "test_plan", "path": "test-plans/TC-001-11.md", "content": "---\nid: TC-001-11\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-05]]\nepic: [[EP-001]]\nmodule: receive clear field-level validation errors when note data is malformed\n---\n\n# TC-001-11: [error] POST or PATCH /notes is called\n\n**Story:** [[US-001-05]]\n**Module:** receive clear field-level validation errors when note data is malformed\n\n## Preconditions\na request with an invalid data type for a field\n\n## Expected Result\nAPI returns 400 with a message naming the invalid field\n\n## Acceptance Criteria\nGiven a request with an invalid data type for a field, when POST or PATCH /notes is called, then API returns 400 with a message naming the invalid field\nGiven an empty JSON body, when POST /notes is called, then API returns 400 identifying all missing required fields\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send POST /notes with an invalid data type for a field (title as a number) | navigate | `/notes` | POST {title: 123, body} |\n| 2 | Verify 400 response names the invalid field | assert_text | `body` | 400 names field title |\n", "parent_ids": ["US-001-05", "EP-001"]}, {"id": "TC-001-12", "type": "test_plan", "path": "test-plans/TC-001-12.md", "content": "---\nid: TC-001-12\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-05]]\nepic: [[EP-001]]\nmodule: receive clear field-level validation errors when note data is malformed\n---\n\n# TC-001-12: [edge] POST /notes is called\n\n**Story:** [[US-001-05]]\n**Module:** receive clear field-level validation errors when note data is malformed\n\n## Preconditions\nan empty JSON body\n\n## Expected Result\nAPI returns 400 identifying all missing required fields\n\n## Acceptance Criteria\nGiven a request with an invalid data type for a field, when POST or PATCH /notes is called, then API returns 400 with a message naming the invalid field\nGiven an empty JSON body, when POST /notes is called, then API returns 400 identifying all missing required fields\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send POST /notes with an empty JSON body | navigate | `/notes` | POST {} |\n| 2 | Verify 400 identifies all missing required fields | assert_text | `body` | 400 lists all missing required fields |\n", "parent_ids": ["US-001-05", "EP-001"]}, {"id": "TC-001-13", "type": "test_plan", "path": "test-plans/TC-001-13.md", "content": "---\nid: TC-001-13\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-06]]\nepic: [[EP-001]]\nmodule: have each note's creation and last-modified timestamps tracked automatically\n---\n\n# TC-001-13: POST /notes succeeds\n\n**Story:** [[US-001-06]]\n**Module:** have each note's creation and last-modified timestamps tracked automatically\n\n## Preconditions\na note is created\n\n## Expected Result\nthe response includes a created_at timestamp set by the server\n\n## Acceptance Criteria\nGiven a note is created, when POST /notes succeeds, then the response includes a created_at timestamp set by the server\nGiven a note is edited, when PATCH /notes/{id} succeeds, then the response includes an updated_at timestamp reflecting the edit time\nGiven a client sends its own created_at or updated_at value, when POST or PATCH is called, then API ignores client-supplied timestamps and uses server time\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send POST /notes with a valid title and body | navigate | `/notes` | POST {title, body} |\n| 2 | Verify response includes a server-set created_at timestamp | assert_text | `body` | created_at present |\n", "parent_ids": ["US-001-06", "EP-001"]}, {"id": "TC-001-14", "type": "test_plan", "path": "test-plans/TC-001-14.md", "content": "---\nid: TC-001-14\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-06]]\nepic: [[EP-001]]\nmodule: have each note's creation and last-modified timestamps tracked automatically\n---\n\n# TC-001-14: PATCH /notes/{id} succeeds\n\n**Story:** [[US-001-06]]\n**Module:** have each note's creation and last-modified timestamps tracked automatically\n\n## Preconditions\na note is edited\n\n## Expected Result\nthe response includes an updated_at timestamp reflecting the edit time\n\n## Acceptance Criteria\nGiven a note is created, when POST /notes succeeds, then the response includes a created_at timestamp set by the server\nGiven a note is edited, when PATCH /notes/{id} succeeds, then the response includes an updated_at timestamp reflecting the edit time\nGiven a client sends its own created_at or updated_at value, when POST or PATCH is called, then API ignores client-supplied timestamps and uses server time\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send PATCH /notes/{id} to edit an existing note | navigate | `/notes/{id}` | PATCH {title} |\n| 2 | Verify response includes an updated_at timestamp reflecting the edit | assert_text | `body` | updated_at present |\n", "parent_ids": ["US-001-06", "EP-001"]}, {"id": "TC-001-15", "type": "test_plan", "path": "test-plans/TC-001-15.md", "content": "---\nid: TC-001-15\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-001-06]]\nepic: [[EP-001]]\nmodule: have each note's creation and last-modified timestamps tracked automatically\n---\n\n# TC-001-15: [edge] POST or PATCH is called\n\n**Story:** [[US-001-06]]\n**Module:** have each note's creation and last-modified timestamps tracked automatically\n\n## Preconditions\na client sends its own created_at or updated_at value\n\n## Expected Result\nAPI ignores client-supplied timestamps and uses server time\n\n## Acceptance Criteria\nGiven a note is created, when POST /notes succeeds, then the response includes a created_at timestamp set by the server\nGiven a note is edited, when PATCH /notes/{id} succeeds, then the response includes an updated_at timestamp reflecting the edit time\nGiven a client sends its own created_at or updated_at value, when POST or PATCH is called, then API ignores client-supplied timestamps and uses server time\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send POST /notes with a client-supplied created_at value | navigate | `/notes` | POST {title, body, created_at: client-value} |\n| 2 | Verify created_at is server time and the client value is ignored | assert_text | `body` | server-set created_at |\n| 3 | Send PATCH /notes/{id} with a client-supplied updated_at value and verify it is ignored | navigate | `/notes/{id}` | PATCH {updated_at: client-value} |\n| 4 | Verify updated_at is server time | assert_text | `body` | server-set updated_at |\n", "parent_ids": ["US-001-06", "EP-001"]}, {"id": "TC-002-01", "type": "test_plan", "path": "test-plans/TC-002-01.md", "content": "---\nid: TC-002-01\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-01]]\nepic: [[EP-002]]\nmodule: add one or more tags to a note via the API\n---\n\n# TC-002-01: client sends a request to add tags to the note\n\n**Story:** [[US-002-01]]\n**Module:** add one or more tags to a note via the API\n\n## Preconditions\nan existing note and valid tag strings\n\n## Expected Result\nAPI returns 200 with the note's updated tag list\n\n## Acceptance Criteria\nGiven an existing note and valid tag strings, when client sends a request to add tags to the note, then API returns 200 with the note's updated tag list\nGiven an existing note with no tags, when client submits a duplicate of an existing tag, then API deduplicates and returns the tag list without duplicates\nGiven a note id that does not exist, when client sends a tag-add request to that id, then API returns 404 with an error message\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send request adding valid tag strings to an existing note | navigate | `/notes/{id}/tags` | POST {tags: ['work','idea']} |\n| 2 | Verify 200 with the updated tag list | assert_text | `body` | 200 updated tag list |\n", "parent_ids": ["US-002-01", "EP-002"]}, {"id": "TC-002-02", "type": "test_plan", "path": "test-plans/TC-002-02.md", "content": "---\nid: TC-002-02\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-01]]\nepic: [[EP-002]]\nmodule: add one or more tags to a note via the API\n---\n\n# TC-002-02: [edge] client submits a duplicate of an existing tag\n\n**Story:** [[US-002-01]]\n**Module:** add one or more tags to a note via the API\n\n## Preconditions\nan existing note with no tags\n\n## Expected Result\nAPI deduplicates and returns the tag list without duplicates\n\n## Acceptance Criteria\nGiven an existing note and valid tag strings, when client sends a request to add tags to the note, then API returns 200 with the note's updated tag list\nGiven an existing note with no tags, when client submits a duplicate of an existing tag, then API deduplicates and returns the tag list without duplicates\nGiven a note id that does not exist, when client sends a tag-add request to that id, then API returns 404 with an error message\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Add tag 'work' to a note with no tags, then submit 'work' again | navigate | `/notes/{id}/tags` | POST {tags: ['work']} twice |\n| 2 | Verify returned tag list has no duplicates | assert_text | `body` | deduplicated tag list |\n", "parent_ids": ["US-002-01", "EP-002"]}, {"id": "TC-002-03", "type": "test_plan", "path": "test-plans/TC-002-03.md", "content": "---\nid: TC-002-03\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-01]]\nepic: [[EP-002]]\nmodule: add one or more tags to a note via the API\n---\n\n# TC-002-03: [error] client sends a tag-add request to that id\n\n**Story:** [[US-002-01]]\n**Module:** add one or more tags to a note via the API\n\n## Preconditions\na note id that does not exist\n\n## Expected Result\nAPI returns 404 with an error message\n\n## Acceptance Criteria\nGiven an existing note and valid tag strings, when client sends a request to add tags to the note, then API returns 200 with the note's updated tag list\nGiven an existing note with no tags, when client submits a duplicate of an existing tag, then API deduplicates and returns the tag list without duplicates\nGiven a note id that does not exist, when client sends a tag-add request to that id, then API returns 404 with an error message\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send tag-add request to a note id that does not exist | navigate | `/notes/{nonexistent-id}/tags` | POST {tags: ['work']} |\n| 2 | Verify 404 with an error message | assert_text | `body` | 404 error message |\n", "parent_ids": ["US-002-01", "EP-002"]}, {"id": "TC-002-04", "type": "test_plan", "path": "test-plans/TC-002-04.md", "content": "---\nid: TC-002-04\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-02]]\nepic: [[EP-002]]\nmodule: remove a tag from a note via the API\n---\n\n# TC-002-04: client sends a request to remove that tag\n\n**Story:** [[US-002-02]]\n**Module:** remove a tag from a note via the API\n\n## Preconditions\na note with an existing tag\n\n## Expected Result\nAPI returns 200 and the tag is no longer in the note's tag list\n\n## Acceptance Criteria\nGiven a note with an existing tag, when client sends a request to remove that tag, then API returns 200 and the tag is no longer in the note's tag list\nGiven a note that does not have the specified tag, when client requests removal of that tag, then API returns 404 or 400 indicating tag not found on note\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send request removing an existing tag from a note | navigate | `/notes/{id}/tags/work` | DELETE |\n| 2 | Verify 200 and the tag is absent from the note's tag list | assert_text | `body` | 200 tag absent |\n", "parent_ids": ["US-002-02", "EP-002"]}, {"id": "TC-002-05", "type": "test_plan", "path": "test-plans/TC-002-05.md", "content": "---\nid: TC-002-05\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-02]]\nepic: [[EP-002]]\nmodule: remove a tag from a note via the API\n---\n\n# TC-002-05: [error] client requests removal of that tag\n\n**Story:** [[US-002-02]]\n**Module:** remove a tag from a note via the API\n\n## Preconditions\na note that does not have the specified tag\n\n## Expected Result\nAPI returns 404 or 400 indicating tag not found on note\n\n## Acceptance Criteria\nGiven a note with an existing tag, when client sends a request to remove that tag, then API returns 200 and the tag is no longer in the note's tag list\nGiven a note that does not have the specified tag, when client requests removal of that tag, then API returns 404 or 400 indicating tag not found on note\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Request removal of a tag the note does not have | navigate | `/notes/{id}/tags/absent-tag` | DELETE |\n| 2 | Verify 404 or 400 indicating tag not found on note | assert_text | `body` | 404 or 400 tag not found |\n", "parent_ids": ["US-002-02", "EP-002"]}, {"id": "TC-002-06", "type": "test_plan", "path": "test-plans/TC-002-06.md", "content": "---\nid: TC-002-06\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-03]]\nepic: [[EP-002]]\nmodule: query the search endpoint with free-text terms\n---\n\n# TC-002-06: client calls search endpoint with that term\n\n**Story:** [[US-002-03]]\n**Module:** query the search endpoint with free-text terms\n\n## Preconditions\nnotes exist whose title or body contains the query term\n\n## Expected Result\nAPI returns 200 with matching notes ranked by relevance within 300ms median\n\n## Acceptance Criteria\nGiven notes exist whose title or body contains the query term, when client calls search endpoint with that term, then API returns 200 with matching notes ranked by relevance within 300ms median\nGiven a query term that matches no notes, when client calls search endpoint, then API returns 200 with an empty result list, not an error\nGiven a search request with an empty or missing query parameter, when client calls the search endpoint, then API returns 400 with a validation error message\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send GET search with a term that matches existing notes | navigate | `/notes/search` | GET ?q=term |\n| 2 | Verify 200 with matching notes ranked by relevance | assert_text | `body` | 200 matching notes ranked |\n", "parent_ids": ["US-002-03", "EP-002"]}, {"id": "TC-002-07", "type": "test_plan", "path": "test-plans/TC-002-07.md", "content": "---\nid: TC-002-07\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-03]]\nepic: [[EP-002]]\nmodule: query the search endpoint with free-text terms\n---\n\n# TC-002-07: [edge] client calls search endpoint\n\n**Story:** [[US-002-03]]\n**Module:** query the search endpoint with free-text terms\n\n## Preconditions\na query term that matches no notes\n\n## Expected Result\nAPI returns 200 with an empty result list, not an error\n\n## Acceptance Criteria\nGiven notes exist whose title or body contains the query term, when client calls search endpoint with that term, then API returns 200 with matching notes ranked by relevance within 300ms median\nGiven a query term that matches no notes, when client calls search endpoint, then API returns 200 with an empty result list, not an error\nGiven a search request with an empty or missing query parameter, when client calls the search endpoint, then API returns 400 with a validation error message\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send GET search with a term that matches no notes | navigate | `/notes/search` | GET ?q=no-match-term |\n| 2 | Verify 200 with an empty result list, not an error | assert_text | `body` | 200 [] |\n", "parent_ids": ["US-002-03", "EP-002"]}, {"id": "TC-002-08", "type": "test_plan", "path": "test-plans/TC-002-08.md", "content": "---\nid: TC-002-08\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-03]]\nepic: [[EP-002]]\nmodule: query the search endpoint with free-text terms\n---\n\n# TC-002-08: [error] client calls the search endpoint\n\n**Story:** [[US-002-03]]\n**Module:** query the search endpoint with free-text terms\n\n## Preconditions\na search request with an empty or missing query parameter\n\n## Expected Result\nAPI returns 400 with a validation error message\n\n## Acceptance Criteria\nGiven notes exist whose title or body contains the query term, when client calls search endpoint with that term, then API returns 200 with matching notes ranked by relevance within 300ms median\nGiven a query term that matches no notes, when client calls search endpoint, then API returns 200 with an empty result list, not an error\nGiven a search request with an empty or missing query parameter, when client calls the search endpoint, then API returns 400 with a validation error message\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send GET search with an empty query parameter | navigate | `/notes/search` | GET ?q= |\n| 2 | Verify 400 with a validation error message | assert_text | `body` | 400 validation error |\n", "parent_ids": ["US-002-03", "EP-002"]}, {"id": "TC-002-09", "type": "test_plan", "path": "test-plans/TC-002-09.md", "content": "---\nid: TC-002-09\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-04]]\nepic: [[EP-002]]\nmodule: filter search results by one or more tags\n---\n\n# TC-002-09: client calls search endpoint with that tag as a filter parameter\n\n**Story:** [[US-002-04]]\n**Module:** filter search results by one or more tags\n\n## Preconditions\nnotes tagged with a specific tag exist\n\n## Expected Result\nAPI returns 200 with only notes containing that tag\n\n## Acceptance Criteria\nGiven notes tagged with a specific tag exist, when client calls search endpoint with that tag as a filter parameter, then API returns 200 with only notes containing that tag\nGiven notes exist matching both a keyword and a tag filter, when client calls search with both parameters combined, then API returns 200 with notes satisfying both conditions\nGiven a filter request referencing a tag that does not exist in the system, when client calls search endpoint with that tag, then API returns 200 with an empty result list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send GET search filtered by a tag that exists on notes | navigate | `/notes/search` | GET ?tags=project-x |\n| 2 | Verify only notes containing that tag are returned | assert_text | `body` | 200 only tagged notes |\n", "parent_ids": ["US-002-04", "EP-002"]}, {"id": "TC-002-10", "type": "test_plan", "path": "test-plans/TC-002-10.md", "content": "---\nid: TC-002-10\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-04]]\nepic: [[EP-002]]\nmodule: filter search results by one or more tags\n---\n\n# TC-002-10: client calls search with both parameters combined\n\n**Story:** [[US-002-04]]\n**Module:** filter search results by one or more tags\n\n## Preconditions\nnotes exist matching both a keyword and a tag filter\n\n## Expected Result\nAPI returns 200 with notes satisfying both conditions\n\n## Acceptance Criteria\nGiven notes tagged with a specific tag exist, when client calls search endpoint with that tag as a filter parameter, then API returns 200 with only notes containing that tag\nGiven notes exist matching both a keyword and a tag filter, when client calls search with both parameters combined, then API returns 200 with notes satisfying both conditions\nGiven a filter request referencing a tag that does not exist in the system, when client calls search endpoint with that tag, then API returns 200 with an empty result list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send GET search with both a keyword and a tag filter | navigate | `/notes/search` | GET ?q=keyword&tags=project-x |\n| 2 | Verify returned notes satisfy both conditions | assert_text | `body` | 200 notes matching both |\n", "parent_ids": ["US-002-04", "EP-002"]}, {"id": "TC-002-11", "type": "test_plan", "path": "test-plans/TC-002-11.md", "content": "---\nid: TC-002-11\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-04]]\nepic: [[EP-002]]\nmodule: filter search results by one or more tags\n---\n\n# TC-002-11: [error] client calls search endpoint with that tag\n\n**Story:** [[US-002-04]]\n**Module:** filter search results by one or more tags\n\n## Preconditions\na filter request referencing a tag that does not exist in the system\n\n## Expected Result\nAPI returns 200 with an empty result list\n\n## Acceptance Criteria\nGiven notes tagged with a specific tag exist, when client calls search endpoint with that tag as a filter parameter, then API returns 200 with only notes containing that tag\nGiven notes exist matching both a keyword and a tag filter, when client calls search with both parameters combined, then API returns 200 with notes satisfying both conditions\nGiven a filter request referencing a tag that does not exist in the system, when client calls search endpoint with that tag, then API returns 200 with an empty result list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send GET search filtered by a tag that does not exist | navigate | `/notes/search` | GET ?tags=nonexistent-tag |\n| 2 | Verify 200 with an empty result list | assert_text | `body` | 200 [] |\n", "parent_ids": ["US-002-04", "EP-002"]}, {"id": "TC-002-12", "type": "test_plan", "path": "test-plans/TC-002-12.md", "content": "---\nid: TC-002-12\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-05]]\nepic: [[EP-002]]\nmodule: list all distinct tags currently in use\n---\n\n# TC-002-12: client calls the list-tags endpoint\n\n**Story:** [[US-002-05]]\n**Module:** list all distinct tags currently in use\n\n## Preconditions\nnotes with various tags exist in the system\n\n## Expected Result\nAPI returns 200 with a deduplicated list of all tags in use\n\n## Acceptance Criteria\nGiven notes with various tags exist in the system, when client calls the list-tags endpoint, then API returns 200 with a deduplicated list of all tags in use\nGiven no notes have any tags yet, when client calls the list-tags endpoint, then API returns 200 with an empty list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Ensure notes with various tags exist | navigate | `/notes` | POST notes with tags |\n| 2 | Send GET to the list-tags endpoint | navigate | `/tags` | GET |\n| 3 | Verify 200 with a deduplicated list of all tags in use | assert_text | `body` | 200 deduplicated tag list |\n", "parent_ids": ["US-002-05", "EP-002"]}, {"id": "TC-002-13", "type": "test_plan", "path": "test-plans/TC-002-13.md", "content": "---\nid: TC-002-13\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-05]]\nepic: [[EP-002]]\nmodule: list all distinct tags currently in use\n---\n\n# TC-002-13: [edge] client calls the list-tags endpoint\n\n**Story:** [[US-002-05]]\n**Module:** list all distinct tags currently in use\n\n## Preconditions\nno notes have any tags yet\n\n## Expected Result\nAPI returns 200 with an empty list\n\n## Acceptance Criteria\nGiven notes with various tags exist in the system, when client calls the list-tags endpoint, then API returns 200 with a deduplicated list of all tags in use\nGiven no notes have any tags yet, when client calls the list-tags endpoint, then API returns 200 with an empty list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Precondition: no notes have any tags | navigate | `/tags` | precondition: no tags |\n| 2 | Send GET to the list-tags endpoint | navigate | `/tags` | GET |\n| 3 | Verify 200 with an empty list | assert_text | `body` | 200 [] |\n", "parent_ids": ["US-002-05", "EP-002"]}, {"id": "TC-002-14", "type": "test_plan", "path": "test-plans/TC-002-14.md", "content": "---\nid: TC-002-14\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-06]]\nepic: [[EP-002]]\nmodule: receive paginated search results\n---\n\n# TC-002-14: client requests a page via page/limit parameters\n\n**Story:** [[US-002-06]]\n**Module:** receive paginated search results\n\n## Preconditions\na search query matches more results than the page size\n\n## Expected Result\nAPI returns 200 with the correct page of results and total count\n\n## Acceptance Criteria\nGiven a search query matches more results than the page size, when client requests a page via page/limit parameters, then API returns 200 with the correct page of results and total count\nGiven a requested page number beyond available results, when client calls search with that page number, then API returns 200 with an empty result list and correct total count\nGiven a negative or non-numeric page or limit parameter, when client calls search endpoint, then API returns 400 with a validation error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send GET search with page and limit where results exceed page size | navigate | `/notes/search` | GET ?q=term&page=1&limit=10 |\n| 2 | Verify the correct page of results and total count are returned | assert_text | `body` | 200 page and total count |\n", "parent_ids": ["US-002-06", "EP-002"]}, {"id": "TC-002-15", "type": "test_plan", "path": "test-plans/TC-002-15.md", "content": "---\nid: TC-002-15\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-06]]\nepic: [[EP-002]]\nmodule: receive paginated search results\n---\n\n# TC-002-15: [edge] client calls search with that page number\n\n**Story:** [[US-002-06]]\n**Module:** receive paginated search results\n\n## Preconditions\na requested page number beyond available results\n\n## Expected Result\nAPI returns 200 with an empty result list and correct total count\n\n## Acceptance Criteria\nGiven a search query matches more results than the page size, when client requests a page via page/limit parameters, then API returns 200 with the correct page of results and total count\nGiven a requested page number beyond available results, when client calls search with that page number, then API returns 200 with an empty result list and correct total count\nGiven a negative or non-numeric page or limit parameter, when client calls search endpoint, then API returns 400 with a validation error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send GET search with a page number beyond available results | navigate | `/notes/search` | GET ?q=term&page=999&limit=10 |\n| 2 | Verify 200 with an empty result list and correct total count | assert_text | `body` | 200 [] with total count |\n", "parent_ids": ["US-002-06", "EP-002"]}, {"id": "TC-002-16", "type": "test_plan", "path": "test-plans/TC-002-16.md", "content": "---\nid: TC-002-16\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-002-06]]\nepic: [[EP-002]]\nmodule: receive paginated search results\n---\n\n# TC-002-16: [error] client calls search endpoint\n\n**Story:** [[US-002-06]]\n**Module:** receive paginated search results\n\n## Preconditions\na negative or non-numeric page or limit parameter\n\n## Expected Result\nAPI returns 400 with a validation error\n\n## Acceptance Criteria\nGiven a search query matches more results than the page size, when client requests a page via page/limit parameters, then API returns 200 with the correct page of results and total count\nGiven a requested page number beyond available results, when client calls search with that page number, then API returns 200 with an empty result list and correct total count\nGiven a negative or non-numeric page or limit parameter, when client calls search endpoint, then API returns 400 with a validation error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send GET search with a negative or non-numeric page or limit | navigate | `/notes/search` | GET ?q=term&page=-1 |\n| 2 | Verify 400 with a validation error | assert_text | `body` | 400 validation error |\n", "parent_ids": ["US-002-06", "EP-002"]}, {"id": "TC-003-01", "type": "test_plan", "path": "test-plans/TC-003-01.md", "content": "---\nid: TC-003-01\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-01]]\nepic: [[EP-003]]\nmodule: archive a note via the API\n---\n\n# TC-003-01: client calls archive endpoint for that note\n\n**Story:** [[US-003-01]]\n**Module:** archive a note via the API\n\n## Preconditions\nan active note with a valid id exists\n\n## Expected Result\nAPI returns 200 and note status is set to archived, content unchanged\n\n## Acceptance Criteria\nGiven an active note with a valid id exists, when client calls archive endpoint for that note, then API returns 200 and note status is set to archived, content unchanged\nGiven note id does not exist, when client calls archive endpoint, then API returns 404 with error body\nGiven note is already archived, when client calls archive endpoint again, then API returns 200 idempotently without error or state change\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send archive request for an active note with a valid id | navigate | `/notes/{id}/archive` | POST |\n| 2 | Verify 200 and note status is archived with content unchanged | assert_text | `body` | 200 status archived, content unchanged |\n", "parent_ids": ["US-003-01", "EP-003"]}, {"id": "TC-003-02", "type": "test_plan", "path": "test-plans/TC-003-02.md", "content": "---\nid: TC-003-02\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-01]]\nepic: [[EP-003]]\nmodule: archive a note via the API\n---\n\n# TC-003-02: [error] client calls archive endpoint\n\n**Story:** [[US-003-01]]\n**Module:** archive a note via the API\n\n## Preconditions\nnote id does not exist\n\n## Expected Result\nAPI returns 404 with error body\n\n## Acceptance Criteria\nGiven an active note with a valid id exists, when client calls archive endpoint for that note, then API returns 200 and note status is set to archived, content unchanged\nGiven note id does not exist, when client calls archive endpoint, then API returns 404 with error body\nGiven note is already archived, when client calls archive endpoint again, then API returns 200 idempotently without error or state change\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send archive request for a note id that does not exist | navigate | `/notes/{nonexistent-id}/archive` | POST |\n| 2 | Verify 404 with error body | assert_text | `body` | 404 error body |\n", "parent_ids": ["US-003-01", "EP-003"]}, {"id": "TC-003-03", "type": "test_plan", "path": "test-plans/TC-003-03.md", "content": "---\nid: TC-003-03\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-01]]\nepic: [[EP-003]]\nmodule: archive a note via the API\n---\n\n# TC-003-03: [edge] client calls archive endpoint again\n\n**Story:** [[US-003-01]]\n**Module:** archive a note via the API\n\n## Preconditions\nnote is already archived\n\n## Expected Result\nAPI returns 200 idempotently without error or state change\n\n## Acceptance Criteria\nGiven an active note with a valid id exists, when client calls archive endpoint for that note, then API returns 200 and note status is set to archived, content unchanged\nGiven note id does not exist, when client calls archive endpoint, then API returns 404 with error body\nGiven note is already archived, when client calls archive endpoint again, then API returns 200 idempotently without error or state change\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send archive request for a note that is already archived | navigate | `/notes/{id}/archive` | POST |\n| 2 | Verify 200 with no error and no state change | assert_text | `body` | 200 idempotent |\n", "parent_ids": ["US-003-01", "EP-003"]}, {"id": "TC-003-04", "type": "test_plan", "path": "test-plans/TC-003-04.md", "content": "---\nid: TC-003-04\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-02]]\nepic: [[EP-003]]\nmodule: restore (unarchive) a previously archived note via the API\n---\n\n# TC-003-04: client calls unarchive endpoint for that note\n\n**Story:** [[US-003-02]]\n**Module:** restore (unarchive) a previously archived note via the API\n\n## Preconditions\nan archived note with a valid id exists\n\n## Expected Result\nAPI returns 200 and note status is set to active, content unchanged\n\n## Acceptance Criteria\nGiven an archived note with a valid id exists, when client calls unarchive endpoint for that note, then API returns 200 and note status is set to active, content unchanged\nGiven note id does not exist, when client calls unarchive endpoint, then API returns 404 with error body\nGiven note is already active, when client calls unarchive endpoint, then API returns 200 idempotently without error or state change\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send unarchive request for an archived note with a valid id | navigate | `/notes/{id}/unarchive` | POST |\n| 2 | Verify 200 and note status is active with content unchanged | assert_text | `body` | 200 status active, content unchanged |\n", "parent_ids": ["US-003-02", "EP-003"]}, {"id": "TC-003-05", "type": "test_plan", "path": "test-plans/TC-003-05.md", "content": "---\nid: TC-003-05\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-02]]\nepic: [[EP-003]]\nmodule: restore (unarchive) a previously archived note via the API\n---\n\n# TC-003-05: [error] client calls unarchive endpoint\n\n**Story:** [[US-003-02]]\n**Module:** restore (unarchive) a previously archived note via the API\n\n## Preconditions\nnote id does not exist\n\n## Expected Result\nAPI returns 404 with error body\n\n## Acceptance Criteria\nGiven an archived note with a valid id exists, when client calls unarchive endpoint for that note, then API returns 200 and note status is set to active, content unchanged\nGiven note id does not exist, when client calls unarchive endpoint, then API returns 404 with error body\nGiven note is already active, when client calls unarchive endpoint, then API returns 200 idempotently without error or state change\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send unarchive request for a note id that does not exist | navigate | `/notes/{nonexistent-id}/unarchive` | POST |\n| 2 | Verify 404 with error body | assert_text | `body` | 404 error body |\n", "parent_ids": ["US-003-02", "EP-003"]}, {"id": "TC-003-06", "type": "test_plan", "path": "test-plans/TC-003-06.md", "content": "---\nid: TC-003-06\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-02]]\nepic: [[EP-003]]\nmodule: restore (unarchive) a previously archived note via the API\n---\n\n# TC-003-06: [edge] client calls unarchive endpoint\n\n**Story:** [[US-003-02]]\n**Module:** restore (unarchive) a previously archived note via the API\n\n## Preconditions\nnote is already active\n\n## Expected Result\nAPI returns 200 idempotently without error or state change\n\n## Acceptance Criteria\nGiven an archived note with a valid id exists, when client calls unarchive endpoint for that note, then API returns 200 and note status is set to active, content unchanged\nGiven note id does not exist, when client calls unarchive endpoint, then API returns 404 with error body\nGiven note is already active, when client calls unarchive endpoint, then API returns 200 idempotently without error or state change\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send unarchive request for a note that is already active | navigate | `/notes/{id}/unarchive` | POST |\n| 2 | Verify 200 with no error and no state change | assert_text | `body` | 200 idempotent |\n", "parent_ids": ["US-003-02", "EP-003"]}, {"id": "TC-003-07", "type": "test_plan", "path": "test-plans/TC-003-07.md", "content": "---\nid: TC-003-07\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-03]]\nepic: [[EP-003]]\nmodule: list only archived notes via the API\n---\n\n# TC-003-07: client requests the list filtered by archived status\n\n**Story:** [[US-003-03]]\n**Module:** list only archived notes via the API\n\n## Preconditions\nsome notes are archived and some are active\n\n## Expected Result\nAPI returns only archived notes with 200\n\n## Acceptance Criteria\nGiven some notes are archived and some are active, when client requests the list filtered by archived status, then API returns only archived notes with 200\nGiven no notes are currently archived, when client requests the archived list, then API returns 200 with an empty list, not an error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Ensure some notes are archived and some are active | navigate | `/notes` | precondition: mixed status |\n| 2 | Send GET /notes filtered by archived status | navigate | `/notes?status=archived` | GET |\n| 3 | Verify 200 and only archived notes are returned | assert_text | `body` | 200 only archived |\n", "parent_ids": ["US-003-03", "EP-003"]}, {"id": "TC-003-08", "type": "test_plan", "path": "test-plans/TC-003-08.md", "content": "---\nid: TC-003-08\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-03]]\nepic: [[EP-003]]\nmodule: list only archived notes via the API\n---\n\n# TC-003-08: [edge] client requests the archived list\n\n**Story:** [[US-003-03]]\n**Module:** list only archived notes via the API\n\n## Preconditions\nno notes are currently archived\n\n## Expected Result\nAPI returns 200 with an empty list, not an error\n\n## Acceptance Criteria\nGiven some notes are archived and some are active, when client requests the list filtered by archived status, then API returns only archived notes with 200\nGiven no notes are currently archived, when client requests the archived list, then API returns 200 with an empty list, not an error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Precondition: no notes are archived | navigate | `/notes` | precondition: none archived |\n| 2 | Send GET /notes filtered by archived status | navigate | `/notes?status=archived` | GET |\n| 3 | Verify 200 with an empty list, not an error | assert_text | `body` | 200 [] |\n", "parent_ids": ["US-003-03", "EP-003"]}, {"id": "TC-003-09", "type": "test_plan", "path": "test-plans/TC-003-09.md", "content": "---\nid: TC-003-09\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-04]]\nepic: [[EP-003]]\nmodule: list only active (non-archived) notes via the API\n---\n\n# TC-003-09: client requests the default or active-filtered list\n\n**Story:** [[US-003-04]]\n**Module:** list only active (non-archived) notes via the API\n\n## Preconditions\nsome notes are archived and some are active\n\n## Expected Result\nAPI returns only active notes with 200\n\n## Acceptance Criteria\nGiven some notes are archived and some are active, when client requests the default or active-filtered list, then API returns only active notes with 200\nGiven all notes are archived, when client requests the active list, then API returns 200 with an empty list, not an error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Ensure some notes are archived and some are active | navigate | `/notes` | precondition: mixed status |\n| 2 | Send GET /notes with the default or active filter | navigate | `/notes` | GET |\n| 3 | Verify 200 and only active notes are returned | assert_text | `body` | 200 only active |\n", "parent_ids": ["US-003-04", "EP-003"]}, {"id": "TC-003-10", "type": "test_plan", "path": "test-plans/TC-003-10.md", "content": "---\nid: TC-003-10\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-04]]\nepic: [[EP-003]]\nmodule: list only active (non-archived) notes via the API\n---\n\n# TC-003-10: [edge] client requests the active list\n\n**Story:** [[US-003-04]]\n**Module:** list only active (non-archived) notes via the API\n\n## Preconditions\nall notes are archived\n\n## Expected Result\nAPI returns 200 with an empty list, not an error\n\n## Acceptance Criteria\nGiven some notes are archived and some are active, when client requests the default or active-filtered list, then API returns only active notes with 200\nGiven all notes are archived, when client requests the active list, then API returns 200 with an empty list, not an error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Precondition: all notes are archived | navigate | `/notes` | precondition: all archived |\n| 2 | Send GET /notes with the active filter | navigate | `/notes` | GET |\n| 3 | Verify 200 with an empty list, not an error | assert_text | `body` | 200 [] |\n", "parent_ids": ["US-003-04", "EP-003"]}, {"id": "TC-003-11", "type": "test_plan", "path": "test-plans/TC-003-11.md", "content": "---\nid: TC-003-11\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-05]]\nepic: [[EP-003]]\nmodule: edit or tag a note regardless of its archive state\n---\n\n# TC-003-11: client sends an edit or tag update request for it\n\n**Story:** [[US-003-05]]\n**Module:** edit or tag a note regardless of its archive state\n\n## Preconditions\nan archived note exists\n\n## Expected Result\nAPI applies the update and returns 200, archive status unchanged\n\n## Acceptance Criteria\nGiven an archived note exists, when client sends an edit or tag update request for it, then API applies the update and returns 200, archive status unchanged\nGiven an archived note is updated, when update completes, then note remains archived and no data fields are lost\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send PATCH /notes/{id} with updated title for an archived note | navigate | `/notes/{archived-id}` | PATCH {title} |\n| 2 | Verify 200 and the archive status is unchanged | assert_text | `body` | 200 status archived |\n", "parent_ids": ["US-003-05", "EP-003"]}, {"id": "TC-003-12", "type": "test_plan", "path": "test-plans/TC-003-12.md", "content": "---\nid: TC-003-12\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-05]]\nepic: [[EP-003]]\nmodule: edit or tag a note regardless of its archive state\n---\n\n# TC-003-12: [edge] update completes\n\n**Story:** [[US-003-05]]\n**Module:** edit or tag a note regardless of its archive state\n\n## Preconditions\nan archived note is updated\n\n## Expected Result\nnote remains archived and no data fields are lost\n\n## Acceptance Criteria\nGiven an archived note exists, when client sends an edit or tag update request for it, then API applies the update and returns 200, archive status unchanged\nGiven an archived note is updated, when update completes, then note remains archived and no data fields are lost\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send an update to an archived note | navigate | `/notes/{archived-id}` | PATCH {body} |\n| 2 | Verify the note remains archived and no data fields are lost | assert_text | `body` | archived, all fields present |\n", "parent_ids": ["US-003-05", "EP-003"]}, {"id": "TC-003-13", "type": "test_plan", "path": "test-plans/TC-003-13.md", "content": "---\nid: TC-003-13\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-06]]\nepic: [[EP-003]]\nmodule: receive consistent error handling for malformed archive/unarchive requests\n---\n\n# TC-003-13: [error] client calls archive endpoint\n\n**Story:** [[US-003-06]]\n**Module:** receive consistent error handling for malformed archive/unarchive requests\n\n## Preconditions\nan archive request is sent with a malformed note id (non-UUID or invalid format)\n\n## Expected Result\nAPI returns 400 with a descriptive error body\n\n## Acceptance Criteria\nGiven an archive request is sent with a malformed note id (non-UUID or invalid format), when client calls archive endpoint, then API returns 400 with a descriptive error body\nGiven an unarchive request is sent with an empty id field, when client calls unarchive endpoint, then API returns 400 without server error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send archive request with a malformed note id (non-UUID) | navigate | `/notes/not-a-valid-id/archive` | POST |\n| 2 | Verify 400 with a descriptive error body | assert_text | `body` | 400 descriptive error |\n", "parent_ids": ["US-003-06", "EP-003"]}, {"id": "TC-003-14", "type": "test_plan", "path": "test-plans/TC-003-14.md", "content": "---\nid: TC-003-14\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-003-06]]\nepic: [[EP-003]]\nmodule: receive consistent error handling for malformed archive/unarchive requests\n---\n\n# TC-003-14: [edge] client calls unarchive endpoint\n\n**Story:** [[US-003-06]]\n**Module:** receive consistent error handling for malformed archive/unarchive requests\n\n## Preconditions\nan unarchive request is sent with an empty id field\n\n## Expected Result\nAPI returns 400 without server error\n\n## Acceptance Criteria\nGiven an archive request is sent with a malformed note id (non-UUID or invalid format), when client calls archive endpoint, then API returns 400 with a descriptive error body\nGiven an unarchive request is sent with an empty id field, when client calls unarchive endpoint, then API returns 400 without server error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send unarchive request with an empty id field | navigate | `/notes//unarchive` | POST |\n| 2 | Verify 400 is returned without a server error | assert_text | `body` | 400 no 500 |\n", "parent_ids": ["US-003-06", "EP-003"]}, {"id": "TC-004-01", "type": "test_plan", "path": "test-plans/TC-004-01.md", "content": "---\nid: TC-004-01\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-004-01]]\nepic: [[EP-004]]\nmodule: configure the database and application hosting region to an EU-only region at provisioning time\n---\n\n# TC-004-01: infrastructure is created\n\n**Story:** [[US-004-01]]\n**Module:** configure the database and application hosting region to an EU-only region at provisioning time\n\n## Preconditions\na new deployment environment is being provisioned\n\n## Expected Result\ndatabase and compute resources are located in an approved EU region\n\n## Acceptance Criteria\nGiven a new deployment environment is being provisioned, when infrastructure is created, then database and compute resources are located in an approved EU region\nGiven an attempt to provision a resource in a non-EU region, when the provisioning request is submitted, then the request is rejected and logged\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Provision a new deployment environment | navigate | `/` | provision environment |\n| 2 | Verify database and compute resources are located in an approved EU region | assert_text | `body` | all resources EU region |\n", "parent_ids": ["US-004-01", "EP-004"]}, {"id": "TC-004-02", "type": "test_plan", "path": "test-plans/TC-004-02.md", "content": "---\nid: TC-004-02\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-004-01]]\nepic: [[EP-004]]\nmodule: configure the database and application hosting region to an EU-only region at provisioning time\n---\n\n# TC-004-02: [error] the provisioning request is submitted\n\n**Story:** [[US-004-01]]\n**Module:** configure the database and application hosting region to an EU-only region at provisioning time\n\n## Preconditions\nan attempt to provision a resource in a non-EU region\n\n## Expected Result\nthe request is rejected and logged\n\n## Acceptance Criteria\nGiven a new deployment environment is being provisioned, when infrastructure is created, then database and compute resources are located in an approved EU region\nGiven an attempt to provision a resource in a non-EU region, when the provisioning request is submitted, then the request is rejected and logged\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Submit a provisioning request for a resource in a non-EU region | navigate | `/` | provision non-EU resource |\n| 2 | Verify the request is rejected and the rejection is logged | assert_text | `body` | rejected and logged |\n", "parent_ids": ["US-004-01", "EP-004"]}, {"id": "TC-004-03", "type": "test_plan", "path": "test-plans/TC-004-03.md", "content": "---\nid: TC-004-03\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-004-02]]\nepic: [[EP-004]]\nmodule: retrieve a report listing the region of every data store and service used by notes_api\n---\n\n# TC-004-03: the auditor requests the residency report\n\n**Story:** [[US-004-02]]\n**Module:** retrieve a report listing the region of every data store and service used by notes_api\n\n## Preconditions\nnotes_api infrastructure is deployed\n\n## Expected Result\nthe report lists each data store/service with its region and confirms all are EU\n\n## Acceptance Criteria\nGiven notes_api infrastructure is deployed, when the auditor requests the residency report, then the report lists each data store/service with its region and confirms all are EU\nGiven a service's region is undetermined, when the report is generated, then that service is flagged as unverified rather than assumed compliant\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Request the residency report for deployed notes_api infrastructure | navigate | `/` | GET residency report |\n| 2 | Verify each data store and service is listed with its region and all are confirmed EU | assert_text | `body` | each resource with region, all EU |\n", "parent_ids": ["US-004-02", "EP-004"]}, {"id": "TC-004-04", "type": "test_plan", "path": "test-plans/TC-004-04.md", "content": "---\nid: TC-004-04\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-004-02]]\nepic: [[EP-004]]\nmodule: retrieve a report listing the region of every data store and service used by notes_api\n---\n\n# TC-004-04: [edge] the report is generated\n\n**Story:** [[US-004-02]]\n**Module:** retrieve a report listing the region of every data store and service used by notes_api\n\n## Preconditions\na service's region is undetermined\n\n## Expected Result\nthat service is flagged as unverified rather than assumed compliant\n\n## Acceptance Criteria\nGiven notes_api infrastructure is deployed, when the auditor requests the residency report, then the report lists each data store/service with its region and confirms all are EU\nGiven a service's region is undetermined, when the report is generated, then that service is flagged as unverified rather than assumed compliant\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Generate the report for a service whose region is undetermined | navigate | `/` | generate report |\n| 2 | Verify that service is flagged unverified and not assumed compliant | assert_text | `body` | unverified |\n", "parent_ids": ["US-004-02", "EP-004"]}, {"id": "TC-004-05", "type": "test_plan", "path": "test-plans/TC-004-05.md", "content": "---\nid: TC-004-05\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-004-03]]\nepic: [[EP-004]]\nmodule: ensure note data persisted via the API is never written to backup or logging destinations outside the EU\n---\n\n# TC-004-05: the record is persisted and backed up\n\n**Story:** [[US-004-03]]\n**Module:** ensure note data persisted via the API is never written to backup or logging destinations outside the EU\n\n## Preconditions\na note is created or edited via the API\n\n## Expected Result\nboth primary storage and backups reside in EU regions\n\n## Acceptance Criteria\nGiven a note is created or edited via the API, when the record is persisted and backed up, then both primary storage and backups reside in EU regions\nGiven a logging or backup destination configured outside the EU, when the system attempts to write to it, then the write is blocked and an alert is raised\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Create or edit a note via the API | navigate | `/notes` | POST {title, body} |\n| 2 | Verify primary storage and backups reside in EU regions | assert_text | `body` | primary and backup EU |\n", "parent_ids": ["US-004-03", "EP-004"]}, {"id": "TC-004-06", "type": "test_plan", "path": "test-plans/TC-004-06.md", "content": "---\nid: TC-004-06\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-004-03]]\nepic: [[EP-004]]\nmodule: ensure note data persisted via the API is never written to backup or logging destinations outside the EU\n---\n\n# TC-004-06: [error] the system attempts to write to it\n\n**Story:** [[US-004-03]]\n**Module:** ensure note data persisted via the API is never written to backup or logging destinations outside the EU\n\n## Preconditions\na logging or backup destination configured outside the EU\n\n## Expected Result\nthe write is blocked and an alert is raised\n\n## Acceptance Criteria\nGiven a note is created or edited via the API, when the record is persisted and backed up, then both primary storage and backups reside in EU regions\nGiven a logging or backup destination configured outside the EU, when the system attempts to write to it, then the write is blocked and an alert is raised\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Configure a logging or backup destination outside the EU and trigger a write | navigate | `/` | non-EU destination write |\n| 2 | Verify the write is blocked and an alert is raised | assert_text | `body` | blocked and alert raised |\n", "parent_ids": ["US-004-03", "EP-004"]}, {"id": "TC-004-07", "type": "test_plan", "path": "test-plans/TC-004-07.md", "content": "---\nid: TC-004-07\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-004-04]]\nepic: [[EP-004]]\nmodule: verify that any third-party services or dependencies used by notes_api also store/process data within the EU\n---\n\n# TC-004-07: the auditor reviews the service's data processing location\n\n**Story:** [[US-004-04]]\n**Module:** verify that any third-party services or dependencies used by notes_api also store/process data within the EU\n\n## Preconditions\nnotes_api depends on a third-party service\n\n## Expected Result\nthe location is documented as EU-resident\n\n## Acceptance Criteria\nGiven notes_api depends on a third-party service, when the auditor reviews the service's data processing location, then the location is documented as EU-resident\nGiven a third-party service's data residency cannot be confirmed, when the audit is run, then the dependency is flagged for remediation before launch\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Review the documented processing location of a third-party dependency | navigate | `/` | review dependency |\n| 2 | Verify the location is documented as EU-resident | assert_text | `body` | EU-resident |\n", "parent_ids": ["US-004-04", "EP-004"]}, {"id": "TC-004-08", "type": "test_plan", "path": "test-plans/TC-004-08.md", "content": "---\nid: TC-004-08\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-004-04]]\nepic: [[EP-004]]\nmodule: verify that any third-party services or dependencies used by notes_api also store/process data within the EU\n---\n\n# TC-004-08: [edge] the audit is run\n\n**Story:** [[US-004-04]]\n**Module:** verify that any third-party services or dependencies used by notes_api also store/process data within the EU\n\n## Preconditions\na third-party service's data residency cannot be confirmed\n\n## Expected Result\nthe dependency is flagged for remediation before launch\n\n## Acceptance Criteria\nGiven notes_api depends on a third-party service, when the auditor reviews the service's data processing location, then the location is documented as EU-resident\nGiven a third-party service's data residency cannot be confirmed, when the audit is run, then the dependency is flagged for remediation before launch\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Run the audit for a third-party service whose residency cannot be confirmed | navigate | `/` | run audit |\n| 2 | Verify the dependency is flagged for remediation before launch | assert_text | `body` | flagged for remediation |\n", "parent_ids": ["US-004-04", "EP-004"]}, {"id": "TC-004-09", "type": "test_plan", "path": "test-plans/TC-004-09.md", "content": "---\nid: TC-004-09\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-004-05]]\nepic: [[EP-004]]\nmodule: run an automated pre-launch residency check across all environments (dev, staging, prod)\n---\n\n# TC-004-09: the pre-launch check is run\n\n**Story:** [[US-004-05]]\n**Module:** run an automated pre-launch residency check across all environments (dev, staging, prod)\n\n## Preconditions\nall environments are configured\n\n## Expected Result\nit reports pass/fail per environment with region details\n\n## Acceptance Criteria\nGiven all environments are configured, when the pre-launch check is run, then it reports pass/fail per environment with region details\nGiven a non-EU resource is detected in any environment, when the check runs, then launch is blocked until the resource is corrected\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Run the pre-launch residency check with all environments configured | navigate | `/` | run check |\n| 2 | Verify pass/fail is reported per environment with region details | assert_text | `body` | pass/fail per env with region |\n", "parent_ids": ["US-004-05", "EP-004"]}, {"id": "TC-004-10", "type": "test_plan", "path": "test-plans/TC-004-10.md", "content": "---\nid: TC-004-10\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-004-05]]\nepic: [[EP-004]]\nmodule: run an automated pre-launch residency check across all environments (dev, staging, prod)\n---\n\n# TC-004-10: [error] the check runs\n\n**Story:** [[US-004-05]]\n**Module:** run an automated pre-launch residency check across all environments (dev, staging, prod)\n\n## Preconditions\na non-EU resource is detected in any environment\n\n## Expected Result\nlaunch is blocked until the resource is corrected\n\n## Acceptance Criteria\nGiven all environments are configured, when the pre-launch check is run, then it reports pass/fail per environment with region details\nGiven a non-EU resource is detected in any environment, when the check runs, then launch is blocked until the resource is corrected\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Run the check with a non-EU resource present in an environment | navigate | `/` | run check with non-EU resource |\n| 2 | Verify launch is blocked until the resource is corrected | assert_text | `body` | launch blocked |\n", "parent_ids": ["US-004-05", "EP-004"]}, {"id": "TC-004-11", "type": "test_plan", "path": "test-plans/TC-004-11.md", "content": "---\nid: TC-004-11\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-004-06]]\nepic: [[EP-004]]\nmodule: access a historical log of residency audit results over time\n---\n\n# TC-004-11: the auditor views the audit history\n\n**Story:** [[US-004-06]]\n**Module:** access a historical log of residency audit results over time\n\n## Preconditions\nmultiple residency audits have been run\n\n## Expected Result\neach past audit's date, scope, and result are listed\n\n## Acceptance Criteria\nGiven multiple residency audits have been run, when the auditor views the audit history, then each past audit's date, scope, and result are listed\nGiven no audits have been run yet, when the auditor views the audit history, then an empty state is shown rather than an error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open the audit history after multiple audits have run | navigate | `/` | open audit history |\n| 2 | Verify each past audit's date, scope and result are listed | assert_text | `body` | date, scope, result listed |\n", "parent_ids": ["US-004-06", "EP-004"]}, {"id": "TC-004-12", "type": "test_plan", "path": "test-plans/TC-004-12.md", "content": "---\nid: TC-004-12\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-004-06]]\nepic: [[EP-004]]\nmodule: access a historical log of residency audit results over time\n---\n\n# TC-004-12: [edge] the auditor views the audit history\n\n**Story:** [[US-004-06]]\n**Module:** access a historical log of residency audit results over time\n\n## Preconditions\nno audits have been run yet\n\n## Expected Result\nan empty state is shown rather than an error\n\n## Acceptance Criteria\nGiven multiple residency audits have been run, when the auditor views the audit history, then each past audit's date, scope, and result are listed\nGiven no audits have been run yet, when the auditor views the audit history, then an empty state is shown rather than an error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Precondition: no audits have been run | navigate | `/` | precondition: no audits |\n| 2 | Open the audit history | navigate | `/` | open audit history |\n| 3 | Verify an empty state is shown rather than an error | assert_text | `body` | empty state |\n", "parent_ids": ["US-004-06", "EP-004"]}, {"id": "TC-005-01", "type": "test_plan", "path": "test-plans/TC-005-01.md", "content": "---\nid: TC-005-01\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-005-01]]\nepic: [[EP-005]]\nmodule: define the API surface with exactly 5 endpoint groups (create, edit, tag, search, archive note)\n---\n\n# TC-005-01: all routes are listed\n\n**Story:** [[US-005-01]]\n**Module:** define the API surface with exactly 5 endpoint groups (create, edit, tag, search, archive note)\n\n## Preconditions\nthe API spec is generated\n\n## Expected Result\nonly routes for create, edit, tag, search, and archive note exist\n\n## Acceptance Criteria\nGiven the API spec is generated, when all routes are listed, then only routes for create, edit, tag, search, and archive note exist\nGiven a developer proposes a new route outside the 5 capabilities, when the route is added to the codebase, then the spec-compliance check fails the build\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Generate the API spec and list all routes | navigate | `/openapi.json` | GET |\n| 2 | Verify only create, edit, tag, search and archive note routes exist | assert_text | `body` | 5 capability groups only |\n", "parent_ids": ["US-005-01", "EP-005"]}, {"id": "TC-005-02", "type": "test_plan", "path": "test-plans/TC-005-02.md", "content": "---\nid: TC-005-02\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-005-01]]\nepic: [[EP-005]]\nmodule: define the API surface with exactly 5 endpoint groups (create, edit, tag, search, archive note)\n---\n\n# TC-005-02: [error] the route is added to the codebase\n\n**Story:** [[US-005-01]]\n**Module:** define the API surface with exactly 5 endpoint groups (create, edit, tag, search, archive note)\n\n## Preconditions\na developer proposes a new route outside the 5 capabilities\n\n## Expected Result\nthe spec-compliance check fails the build\n\n## Acceptance Criteria\nGiven the API spec is generated, when all routes are listed, then only routes for create, edit, tag, search, and archive note exist\nGiven a developer proposes a new route outside the 5 capabilities, when the route is added to the codebase, then the spec-compliance check fails the build\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Add a route outside the 5 capabilities to the codebase | navigate | `/` | add out-of-scope route |\n| 2 | Verify the spec-compliance check fails the build | assert_text | `body` | build failed |\n", "parent_ids": ["US-005-01", "EP-005"]}, {"id": "TC-005-03", "type": "test_plan", "path": "test-plans/TC-005-03.md", "content": "---\nid: TC-005-03\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-005-02]]\nepic: [[EP-005]]\nmodule: reject any request payload containing file/binary attachment data on the create or edit endpoints\n---\n\n# TC-005-03: the request is submitted\n\n**Story:** [[US-005-02]]\n**Module:** reject any request payload containing file/binary attachment data on the create or edit endpoints\n\n## Preconditions\na note is created with only title, body, and tags\n\n## Expected Result\nthe note is saved successfully\n\n## Acceptance Criteria\nGiven a note is created with only title, body, and tags, when the request is submitted, then the note is saved successfully\nGiven a request includes a multipart/form-data file field or binary attachment, when the request hits create or edit endpoint, then the API returns 400 with an 'attachments not supported' error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Submit a note with only title, body and tags | navigate | `/notes` | POST {title, body, tags} |\n| 2 | Verify the note is saved successfully | assert_text | `body` | 201 saved |\n", "parent_ids": ["US-005-02", "EP-005"]}, {"id": "TC-005-04", "type": "test_plan", "path": "test-plans/TC-005-04.md", "content": "---\nid: TC-005-04\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-005-02]]\nepic: [[EP-005]]\nmodule: reject any request payload containing file/binary attachment data on the create or edit endpoints\n---\n\n# TC-005-04: [error] the request hits create or edit endpoint\n\n**Story:** [[US-005-02]]\n**Module:** reject any request payload containing file/binary attachment data on the create or edit endpoints\n\n## Preconditions\na request includes a multipart/form-data file field or binary attachment\n\n## Expected Result\nthe API returns 400 with an 'attachments not supported' error\n\n## Acceptance Criteria\nGiven a note is created with only title, body, and tags, when the request is submitted, then the note is saved successfully\nGiven a request includes a multipart/form-data file field or binary attachment, when the request hits create or edit endpoint, then the API returns 400 with an 'attachments not supported' error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Send a multipart/form-data request with a file field to the create endpoint | navigate | `/notes` | POST multipart with file |\n| 2 | Verify 400 with an 'attachments not supported' error | assert_text | `body` | 400 attachments not supported |\n", "parent_ids": ["US-005-02", "EP-005"]}, {"id": "TC-005-05", "type": "test_plan", "path": "test-plans/TC-005-05.md", "content": "---\nid: TC-005-05\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-005-03]]\nepic: [[EP-005]]\nmodule: ensure no WebSocket, SSE, or polling-for-live-updates endpoints are exposed\n---\n\n# TC-005-05: checked for protocol types\n\n**Story:** [[US-005-03]]\n**Module:** ensure no WebSocket, SSE, or polling-for-live-updates endpoints are exposed\n\n## Preconditions\nthe API's route registry is inspected\n\n## Expected Result\nonly standard HTTP request/response endpoints are present\n\n## Acceptance Criteria\nGiven the API's route registry is inspected, when checked for protocol types, then only standard HTTP request/response endpoints are present\nGiven a pull request adds a WebSocket or SSE route, when CI scope-guardrail check runs, then the check fails and blocks merge\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Inspect the API route registry for protocol types | navigate | `/openapi.json` | inspect registry |\n| 2 | Verify only standard HTTP request/response endpoints are present | assert_text | `body` | HTTP only |\n", "parent_ids": ["US-005-03", "EP-005"]}, {"id": "TC-005-06", "type": "test_plan", "path": "test-plans/TC-005-06.md", "content": "---\nid: TC-005-06\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-005-03]]\nepic: [[EP-005]]\nmodule: ensure no WebSocket, SSE, or polling-for-live-updates endpoints are exposed\n---\n\n# TC-005-06: [error] CI scope-guardrail check runs\n\n**Story:** [[US-005-03]]\n**Module:** ensure no WebSocket, SSE, or polling-for-live-updates endpoints are exposed\n\n## Preconditions\na pull request adds a WebSocket or SSE route\n\n## Expected Result\nthe check fails and blocks merge\n\n## Acceptance Criteria\nGiven the API's route registry is inspected, when checked for protocol types, then only standard HTTP request/response endpoints are present\nGiven a pull request adds a WebSocket or SSE route, when CI scope-guardrail check runs, then the check fails and blocks merge\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open a pull request that adds a WebSocket or SSE route and run the CI scope-guardrail check | navigate | `/` | run CI guardrail |\n| 2 | Verify the check fails and blocks merge | assert_text | `body` | check failed, merge blocked |\n", "parent_ids": ["US-005-03", "EP-005"]}, {"id": "TC-005-07", "type": "test_plan", "path": "test-plans/TC-005-07.md", "content": "---\nid: TC-005-07\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-005-04]]\nepic: [[EP-005]]\nmodule: run an automated scope-compliance test suite against every build\n---\n\n# TC-005-07: the scope-compliance suite runs\n\n**Story:** [[US-005-04]]\n**Module:** run an automated scope-compliance test suite against every build\n\n## Preconditions\na build is produced\n\n## Expected Result\nit reports exactly 5 capability groups with zero extras\n\n## Acceptance Criteria\nGiven a build is produced, when the scope-compliance suite runs, then it reports exactly 5 capability groups with zero extras\nGiven an endpoint exists that technically maps to 'search' but accepts attachment-filter parameters, when the suite inspects its parameters, then it flags the parameter as out-of-scope\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Run the scope-compliance suite against a build | navigate | `/` | run suite |\n| 2 | Verify it reports exactly 5 capability groups with zero extras | assert_text | `body` | 5 groups, 0 extras |\n", "parent_ids": ["US-005-04", "EP-005"]}, {"id": "TC-005-08", "type": "test_plan", "path": "test-plans/TC-005-08.md", "content": "---\nid: TC-005-08\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-005-04]]\nepic: [[EP-005]]\nmodule: run an automated scope-compliance test suite against every build\n---\n\n# TC-005-08: [edge] the suite inspects its parameters\n\n**Story:** [[US-005-04]]\n**Module:** run an automated scope-compliance test suite against every build\n\n## Preconditions\nan endpoint exists that technically maps to 'search' but accepts attachment-filter parameters\n\n## Expected Result\nit flags the parameter as out-of-scope\n\n## Acceptance Criteria\nGiven a build is produced, when the scope-compliance suite runs, then it reports exactly 5 capability groups with zero extras\nGiven an endpoint exists that technically maps to 'search' but accepts attachment-filter parameters, when the suite inspects its parameters, then it flags the parameter as out-of-scope\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Provide an endpoint mapped to search that accepts attachment-filter parameters | navigate | `/notes/search` | GET ?attachment=true |\n| 2 | Verify the suite flags the parameter as out-of-scope | assert_text | `body` | flagged out-of-scope |\n", "parent_ids": ["US-005-04", "EP-005"]}, {"id": "TC-005-09", "type": "test_plan", "path": "test-plans/TC-005-09.md", "content": "---\nid: TC-005-09\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-005-05]]\nepic: [[EP-005]]\nmodule: maintain a documented out-of-scope list (attachments, mobile-specific endpoints, real-time) in the API spec\n---\n\n# TC-005-09: a reader views the scope section\n\n**Story:** [[US-005-05]]\n**Module:** maintain a documented out-of-scope list (attachments, mobile-specific endpoints, real-time) in the API spec\n\n## Preconditions\nthe published API documentation\n\n## Expected Result\nattachments, mobile endpoints, and real-time features are explicitly listed as out of scope\n\n## Acceptance Criteria\nGiven the published API documentation, when a reader views the scope section, then attachments, mobile endpoints, and real-time features are explicitly listed as out of scope\nGiven a future contributor proposes an attachment endpoint, when they check the documented out-of-scope list first, then they see the feature is explicitly excluded before writing code\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open the published API documentation scope section | navigate | `/docs` | GET |\n| 2 | Verify attachments, mobile endpoints and real-time features are listed as out of scope | assert_text | `body` | attachments, mobile, real-time out of scope |\n", "parent_ids": ["US-005-05", "EP-005"]}, {"id": "TC-005-10", "type": "test_plan", "path": "test-plans/TC-005-10.md", "content": "---\nid: TC-005-10\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-005-05]]\nepic: [[EP-005]]\nmodule: maintain a documented out-of-scope list (attachments, mobile-specific endpoints, real-time) in the API spec\n---\n\n# TC-005-10: [edge] they check the documented out-of-scope list first\n\n**Story:** [[US-005-05]]\n**Module:** maintain a documented out-of-scope list (attachments, mobile-specific endpoints, real-time) in the API spec\n\n## Preconditions\na future contributor proposes an attachment endpoint\n\n## Expected Result\nthey see the feature is explicitly excluded before writing code\n\n## Acceptance Criteria\nGiven the published API documentation, when a reader views the scope section, then attachments, mobile endpoints, and real-time features are explicitly listed as out of scope\nGiven a future contributor proposes an attachment endpoint, when they check the documented out-of-scope list first, then they see the feature is explicitly excluded before writing code\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open the out-of-scope list before proposing an attachment endpoint | navigate | `/docs` | GET |\n| 2 | Verify the attachment feature is explicitly excluded | assert_text | `body` | attachments excluded |\n", "parent_ids": ["US-005-05", "EP-005"]}, {"id": "TC-005-11", "type": "test_plan", "path": "test-plans/TC-005-11.md", "content": "---\nid: TC-005-11\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-005-06]]\nepic: [[EP-005]]\nmodule: block any endpoint or parameter designed solely to support a native mobile client (e.g. device push tokens)\n---\n\n# TC-005-11: checked for mobile-only parameters\n\n**Story:** [[US-005-06]]\n**Module:** block any endpoint or parameter designed solely to support a native mobile client (e.g. device push tokens)\n\n## Preconditions\nthe API endpoints are reviewed\n\n## Expected Result\nnone are present\n\n## Acceptance Criteria\nGiven the API endpoints are reviewed, when checked for mobile-only parameters, then none are present\nGiven a request includes a device push-token field, when submitted to any endpoint, then the API returns 400 indicating the field is unsupported\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Review API endpoints and parameters for mobile-only fields | navigate | `/openapi.json` | GET |\n| 2 | Verify none are present | assert_text | `body` | no mobile-only params |\n", "parent_ids": ["US-005-06", "EP-005"]}, {"id": "TC-005-12", "type": "test_plan", "path": "test-plans/TC-005-12.md", "content": "---\nid: TC-005-12\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-005-06]]\nepic: [[EP-005]]\nmodule: block any endpoint or parameter designed solely to support a native mobile client (e.g. device push tokens)\n---\n\n# TC-005-12: [error] submitted to any endpoint\n\n**Story:** [[US-005-06]]\n**Module:** block any endpoint or parameter designed solely to support a native mobile client (e.g. device push tokens)\n\n## Preconditions\na request includes a device push-token field\n\n## Expected Result\nthe API returns 400 indicating the field is unsupported\n\n## Acceptance Criteria\nGiven the API endpoints are reviewed, when checked for mobile-only parameters, then none are present\nGiven a request includes a device push-token field, when submitted to any endpoint, then the API returns 400 indicating the field is unsupported\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Submit a request containing a device push-token field | navigate | `/notes` | POST {title, body, device_push_token} |\n| 2 | Verify 400 indicating the field is unsupported | assert_text | `body` | 400 field unsupported |\n", "parent_ids": ["US-005-06", "EP-005"]}, {"id": "TC-006-23", "type": "test_plan", "path": "test-plans/TC-006-23.md", "content": "---\nid: TC-006-23\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-01]]\nepic: [[EP-006]]\nmodule: view a list of all notes in the web app\n---\n\n# TC-006-23: the notes list page loads\n\n**Story:** [[US-006-01]]\n**Module:** view a list of all notes in the web app\n\n## Preconditions\nnotes exist in notes_api\n\n## Expected Result\nthe web app calls GET /notes, renders each note's title and status, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with Newest first selected by default, and shows a live result count above the list equal to the number of notes listed\n\n## Acceptance Criteria\nGiven notes exist in notes_api, when the notes list page loads, then the web app calls GET /notes, renders each note's title and status, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with Newest first selected by default, and shows a live result count above the list equal to the number of notes listed\nGiven no notes exist yet, when the notes list page loads, then the web app shows an empty-state message and a result count of 0 instead of an error\nGiven notes_api returns an error, when the notes list page loads, then the web app shows a user-facing error state instead of a blank screen\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Ensure notes exist in notes_api with distinct titles and created dates | navigate | `/notes` | precondition: notes exist |\n| 2 | Open the notes list page | navigate | `/notes` | GET /notes |\n| 3 | Verify the sort control is shown above the list with Newest first selected by default | assert_visible | `[data-testid=\"US-006-01-sort-control\"]` | Newest first |\n| 4 | Verify the sort control offers Newest first, Oldest first and Title A-Z | assert_text | `[data-testid=\"US-006-01-sort-control\"]` | Newest first, Oldest first, Title A-Z |\n| 5 | Verify each note's title and status are rendered | assert_visible | `[data-testid=\"US-006-01-note-item\"]` | title and status shown |\n| 6 | Verify the live result count equals the number of notes listed | assert_text | `[data-testid=\"US-006-01-result-count\"]` | count equals listed notes |\n| 7 | Verify the first item is the note with the latest created_at | assert_text | `[data-testid=\"US-006-01-note-item\"]` | first item latest created_at |\n| 8 | Select Oldest first and verify the list order reverses by created_at | select | `[data-testid=\"US-006-01-sort-control\"]` | Oldest first |\n| 9 | Verify the first item is now the note with the earliest created_at | assert_text | `[data-testid=\"US-006-01-note-item\"]` | first item earliest created_at |\n| 10 | Select Title A-Z and verify titles are ordered ascending | select | `[data-testid=\"US-006-01-sort-control\"]` | Title A-Z |\n| 11 | Verify titles are listed in ascending A-Z order | assert_text | `[data-testid=\"US-006-01-note-item\"]` | titles ascending |\n| 12 | Select Oldest first, reload the page and verify the selection persists | select | `[data-testid=\"US-006-01-sort-control\"]` | Oldest first |\n| 13 | Reload the notes list page | navigate | `/notes` | reload |\n| 14 | Verify the sort control still shows Oldest first and no sort parameter was sent to GET /notes | assert_text | `[data-testid=\"US-006-01-sort-control\"]` | Oldest first persisted, no sort param |\n", "parent_ids": ["US-006-01", "EP-006"]}, {"id": "TC-006-24", "type": "test_plan", "path": "test-plans/TC-006-24.md", "content": "---\nid: TC-006-24\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-01]]\nepic: [[EP-006]]\nmodule: view a list of all notes in the web app\n---\n\n# TC-006-24: the notes list page loads\n\n**Story:** [[US-006-01]]\n**Module:** view a list of all notes in the web app\n\n## Preconditions\nno notes exist yet\n\n## Expected Result\nthe web app shows an empty-state message and a result count of 0 instead of an error\n\n## Acceptance Criteria\nGiven notes exist in notes_api, when the notes list page loads, then the web app calls GET /notes, renders each note's title and status, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with Newest first selected by default, and shows a live result count above the list equal to the number of notes listed\nGiven no notes exist yet, when the notes list page loads, then the web app shows an empty-state message and a result count of 0 instead of an error\nGiven notes_api returns an error, when the notes list page loads, then the web app shows a user-facing error state instead of a blank screen\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Precondition: no notes exist yet | navigate | `/notes` | precondition: empty dataset |\n| 2 | Open the notes list page | navigate | `/notes` | GET /notes |\n| 3 | Verify the empty-state message is shown | assert_visible | `[data-testid=\"US-006-01-empty-state\"]` | empty-state message |\n| 4 | Verify the result count shows 0 | assert_text | `[data-testid=\"US-006-01-result-count\"]` | 0 |\n", "parent_ids": ["US-006-01", "EP-006"]}, {"id": "TC-006-25", "type": "test_plan", "path": "test-plans/TC-006-25.md", "content": "---\nid: TC-006-25\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-01]]\nepic: [[EP-006]]\nmodule: view a list of all notes in the web app\n---\n\n# TC-006-25: the notes list page loads\n\n**Story:** [[US-006-01]]\n**Module:** view a list of all notes in the web app\n\n## Preconditions\nnotes_api returns an error\n\n## Expected Result\nthe web app shows a user-facing error state instead of a blank screen\n\n## Acceptance Criteria\nGiven notes exist in notes_api, when the notes list page loads, then the web app calls GET /notes, renders each note's title and status, shows a sort control above the list (Newest first, Oldest first, Title A-Z) with Newest first selected by default, and shows a live result count above the list equal to the number of notes listed\nGiven no notes exist yet, when the notes list page loads, then the web app shows an empty-state message and a result count of 0 instead of an error\nGiven notes_api returns an error, when the notes list page loads, then the web app shows a user-facing error state instead of a blank screen\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Precondition: notes_api returns an error for GET /notes | navigate | `/notes` | precondition: API error |\n| 2 | Open the notes list page | navigate | `/notes` | GET /notes |\n| 3 | Verify a user-facing error state is shown rather than a blank screen | assert_visible | `[data-testid=\"US-006-01-error-state\"]` | user-facing error message |\n", "parent_ids": ["US-006-01", "EP-006"]}, {"id": "TC-006-04", "type": "test_plan", "path": "test-plans/TC-006-04.md", "content": "---\nid: TC-006-04\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-02]]\nepic: [[EP-006]]\nmodule: create a new note using the web editor\n---\n\n# TC-006-04: the user submits\n\n**Story:** [[US-006-02]]\n**Module:** create a new note using the web editor\n\n## Preconditions\na title and body are entered in the editor\n\n## Expected Result\nthe web app calls POST /notes and navigates to the saved note\n\n## Acceptance Criteria\nGiven a title and body are entered in the editor, when the user submits, then the web app calls POST /notes and navigates to the saved note\nGiven the title field is left empty, when the user submits, then the web app shows the API's validation error inline without creating a note\nGiven the create request succeeds, when the response is received, then the note's server-set created_at is reflected in the UI\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open the new note editor | navigate | `/notes/new` |  |\n| 2 | Enter a title | fill | `[data-testid=\"US-006-02-title-input\"]` | Meeting notes |\n| 3 | Enter a body | fill | `[data-testid=\"US-006-02-body-input\"]` | Agenda and actions |\n| 4 | Submit the note | click | `[data-testid=\"US-006-02-submit\"]` |  |\n| 5 | Verify the web app navigates to the saved note | assert_url | `/notes/` | /notes/:id |\n", "parent_ids": ["US-006-02", "EP-006"]}, {"id": "TC-006-05", "type": "test_plan", "path": "test-plans/TC-006-05.md", "content": "---\nid: TC-006-05\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-02]]\nepic: [[EP-006]]\nmodule: create a new note using the web editor\n---\n\n# TC-006-05: the user submits\n\n**Story:** [[US-006-02]]\n**Module:** create a new note using the web editor\n\n## Preconditions\nthe title field is left empty\n\n## Expected Result\nthe web app shows the API's validation error inline without creating a note\n\n## Acceptance Criteria\nGiven a title and body are entered in the editor, when the user submits, then the web app calls POST /notes and navigates to the saved note\nGiven the title field is left empty, when the user submits, then the web app shows the API's validation error inline without creating a note\nGiven the create request succeeds, when the response is received, then the note's server-set created_at is reflected in the UI\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open the new note editor | navigate | `/notes/new` |  |\n| 2 | Enter a body and leave the title empty | fill | `[data-testid=\"US-006-02-body-input\"]` | Body only |\n| 3 | Submit the note | click | `[data-testid=\"US-006-02-submit\"]` |  |\n| 4 | Verify the API validation error is shown inline next to the title | assert_visible | `[data-testid=\"US-006-02-title-error\"]` | validation error |\n| 5 | Verify the URL remains on the editor, so no note was created | assert_url | `/notes/new` |  |\n", "parent_ids": ["US-006-02", "EP-006"]}, {"id": "TC-006-06", "type": "test_plan", "path": "test-plans/TC-006-06.md", "content": "---\nid: TC-006-06\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-02]]\nepic: [[EP-006]]\nmodule: create a new note using the web editor\n---\n\n# TC-006-06: the response is received\n\n**Story:** [[US-006-02]]\n**Module:** create a new note using the web editor\n\n## Preconditions\nthe create request succeeds\n\n## Expected Result\nthe note's server-set created_at is reflected in the UI\n\n## Acceptance Criteria\nGiven a title and body are entered in the editor, when the user submits, then the web app calls POST /notes and navigates to the saved note\nGiven the title field is left empty, when the user submits, then the web app shows the API's validation error inline without creating a note\nGiven the create request succeeds, when the response is received, then the note's server-set created_at is reflected in the UI\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open the new note editor | navigate | `/notes/new` |  |\n| 2 | Enter a valid title and body | fill | `[data-testid=\"US-006-02-title-input\"]` | Valid title |\n| 3 | Submit the note | click | `[data-testid=\"US-006-02-submit\"]` |  |\n| 4 | Verify the created_at shown matches the server-set created_at returned by the API | assert_text | `[data-testid=\"US-006-02-created-at\"]` | matches server created_at |\n", "parent_ids": ["US-006-02", "EP-006"]}, {"id": "TC-006-07", "type": "test_plan", "path": "test-plans/TC-006-07.md", "content": "---\nid: TC-006-07\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-03]]\nepic: [[EP-006]]\nmodule: edit an existing note's title or body using the web editor\n---\n\n# TC-006-07: the user changes the title or body and saves\n\n**Story:** [[US-006-03]]\n**Module:** edit an existing note's title or body using the web editor\n\n## Preconditions\nan existing note is opened in the editor\n\n## Expected Result\nthe web app calls PUT/PATCH /notes/{id} and shows the updated content\n\n## Acceptance Criteria\nGiven an existing note is opened in the editor, when the user changes the title or body and saves, then the web app calls PUT/PATCH /notes/{id} and shows the updated content\nGiven the save request returns 404, when the response is received, then the web app shows a 'note not found' message and does not apply local changes\nGiven a save request returns a field-level validation error, when the response is received, then the web app highlights the invalid field\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open an existing note in the editor | navigate | `/notes/{id}/edit` |  |\n| 2 | Change the title | fill | `[data-testid=\"US-006-03-title-input\"]` | Updated title |\n| 3 | Save the note | click | `[data-testid=\"US-006-03-save\"]` |  |\n| 4 | Verify the updated content is shown | assert_text | `[data-testid=\"US-006-03-title-display\"]` | Updated title |\n", "parent_ids": ["US-006-03", "EP-006"]}, {"id": "TC-006-08", "type": "test_plan", "path": "test-plans/TC-006-08.md", "content": "---\nid: TC-006-08\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-03]]\nepic: [[EP-006]]\nmodule: edit an existing note's title or body using the web editor\n---\n\n# TC-006-08: the response is received\n\n**Story:** [[US-006-03]]\n**Module:** edit an existing note's title or body using the web editor\n\n## Preconditions\nthe save request returns 404\n\n## Expected Result\nthe web app shows a 'note not found' message and does not apply local changes\n\n## Acceptance Criteria\nGiven an existing note is opened in the editor, when the user changes the title or body and saves, then the web app calls PUT/PATCH /notes/{id} and shows the updated content\nGiven the save request returns 404, when the response is received, then the web app shows a 'note not found' message and does not apply local changes\nGiven a save request returns a field-level validation error, when the response is received, then the web app highlights the invalid field\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open an existing note in the editor | navigate | `/notes/{id}/edit` |  |\n| 2 | Change the title and save while the save request returns 404 | fill | `[data-testid=\"US-006-03-title-input\"]` | Changed title |\n| 3 | Click save | click | `[data-testid=\"US-006-03-save\"]` |  |\n| 4 | Verify a 'note not found' message is shown | assert_text | `[data-testid=\"US-006-03-not-found\"]` | note not found |\n", "parent_ids": ["US-006-03", "EP-006"]}, {"id": "TC-006-09", "type": "test_plan", "path": "test-plans/TC-006-09.md", "content": "---\nid: TC-006-09\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-03]]\nepic: [[EP-006]]\nmodule: edit an existing note's title or body using the web editor\n---\n\n# TC-006-09: the response is received\n\n**Story:** [[US-006-03]]\n**Module:** edit an existing note's title or body using the web editor\n\n## Preconditions\na save request returns a field-level validation error\n\n## Expected Result\nthe web app highlights the invalid field\n\n## Acceptance Criteria\nGiven an existing note is opened in the editor, when the user changes the title or body and saves, then the web app calls PUT/PATCH /notes/{id} and shows the updated content\nGiven the save request returns 404, when the response is received, then the web app shows a 'note not found' message and does not apply local changes\nGiven a save request returns a field-level validation error, when the response is received, then the web app highlights the invalid field\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open an existing note in the editor | navigate | `/notes/{id}/edit` |  |\n| 2 | Clear the title and save so the API returns a field-level validation error | fill | `[data-testid=\"US-006-03-title-input\"]` |  |\n| 3 | Click save | click | `[data-testid=\"US-006-03-save\"]` |  |\n| 4 | Verify the invalid title field is highlighted | assert_visible | `[data-testid=\"US-006-03-title-error\"]` | field highlighted |\n", "parent_ids": ["US-006-03", "EP-006"]}, {"id": "TC-006-26", "type": "test_plan", "path": "test-plans/TC-006-26.md", "content": "---\nid: TC-006-26\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-04]]\nepic: [[EP-006]]\nmodule: search notes using a search box with free-text and tag filters\n---\n\n# TC-006-26: they submit\n\n**Story:** [[US-006-04]]\n**Module:** search notes using a search box with free-text and tag filters\n\n## Preconditions\nthe user enters a keyword in the search box\n\n## Expected Result\nthe web app calls the search endpoint, renders matching notes in the currently selected sort order (Newest first, Oldest first, or Title A-Z) without changing the sort selection, and updates the live result count above the list to the number of matches\n\n## Acceptance Criteria\nGiven the user enters a keyword in the search box, when they submit, then the web app calls the search endpoint, renders matching notes in the currently selected sort order (Newest first, Oldest first, or Title A-Z) without changing the sort selection, and updates the live result count above the list to the number of matches\nGiven the search returns no matches, when results load, then the web app shows an empty-results message and a result count of 0 instead of an error\nGiven the user selects a tag filter, when they apply it, then the web app includes the tag filter in the search request, renders only matching notes in the selected sort order, and updates the live result count to match\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Select Oldest first on the notes list | navigate | `/notes` |  |\n| 2 | Select Oldest first on the sort control | select | `[data-testid=\"US-006-01-sort-control\"]` | Oldest first |\n| 3 | Open the search screen and verify the sort selection carries over | navigate | `/notes/search` |  |\n| 4 | Verify the sort control still shows Oldest first | assert_text | `[data-testid=\"US-006-01-sort-control\"]` | Oldest first |\n| 5 | Enter a keyword in the search box | fill | `[data-testid=\"US-006-04-search-input\"]` | keyword |\n| 6 | Submit the search | click | `[data-testid=\"US-006-04-search-submit\"]` |  |\n| 7 | Verify the search request contains no sort parameter | assert_url | `/notes/search` | q=keyword, no sort param |\n| 8 | Verify matching notes render in Oldest first order by created_at | assert_text | `[data-testid=\"US-006-04-result-item\"]` | ordered oldest first |\n| 9 | Verify the sort selection is unchanged after results load | assert_text | `[data-testid=\"US-006-01-sort-control\"]` | Oldest first |\n| 10 | Verify the live result count equals the number of matches | assert_text | `[data-testid=\"US-006-04-result-count\"]` | count equals matches |\n", "parent_ids": ["US-006-04", "EP-006"]}, {"id": "TC-006-27", "type": "test_plan", "path": "test-plans/TC-006-27.md", "content": "---\nid: TC-006-27\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-04]]\nepic: [[EP-006]]\nmodule: search notes using a search box with free-text and tag filters\n---\n\n# TC-006-27: results load\n\n**Story:** [[US-006-04]]\n**Module:** search notes using a search box with free-text and tag filters\n\n## Preconditions\nthe search returns no matches\n\n## Expected Result\nthe web app shows an empty-results message and a result count of 0 instead of an error\n\n## Acceptance Criteria\nGiven the user enters a keyword in the search box, when they submit, then the web app calls the search endpoint, renders matching notes in the currently selected sort order (Newest first, Oldest first, or Title A-Z) without changing the sort selection, and updates the live result count above the list to the number of matches\nGiven the search returns no matches, when results load, then the web app shows an empty-results message and a result count of 0 instead of an error\nGiven the user selects a tag filter, when they apply it, then the web app includes the tag filter in the search request, renders only matching notes in the selected sort order, and updates the live result count to match\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open the search screen | navigate | `/notes/search` |  |\n| 2 | Enter a keyword that matches no notes | fill | `[data-testid=\"US-006-04-search-input\"]` | no-match-term |\n| 3 | Submit the search | click | `[data-testid=\"US-006-04-search-submit\"]` |  |\n| 4 | Verify the empty-results message is shown | assert_visible | `[data-testid=\"US-006-04-empty-results\"]` | empty-results message |\n| 5 | Verify the result count shows 0 | assert_text | `[data-testid=\"US-006-04-result-count\"]` | 0 |\n", "parent_ids": ["US-006-04", "EP-006"]}, {"id": "TC-006-28", "type": "test_plan", "path": "test-plans/TC-006-28.md", "content": "---\nid: TC-006-28\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-04]]\nepic: [[EP-006]]\nmodule: search notes using a search box with free-text and tag filters\n---\n\n# TC-006-28: they apply it\n\n**Story:** [[US-006-04]]\n**Module:** search notes using a search box with free-text and tag filters\n\n## Preconditions\nthe user selects a tag filter\n\n## Expected Result\nthe web app includes the tag filter in the search request, renders only matching notes in the selected sort order, and updates the live result count to match\n\n## Acceptance Criteria\nGiven the user enters a keyword in the search box, when they submit, then the web app calls the search endpoint, renders matching notes in the currently selected sort order (Newest first, Oldest first, or Title A-Z) without changing the sort selection, and updates the live result count above the list to the number of matches\nGiven the search returns no matches, when results load, then the web app shows an empty-results message and a result count of 0 instead of an error\nGiven the user selects a tag filter, when they apply it, then the web app includes the tag filter in the search request, renders only matching notes in the selected sort order, and updates the live result count to match\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Select Title A-Z on the sort control | select | `[data-testid=\"US-006-01-sort-control\"]` | Title A-Z |\n| 2 | Open the search screen | navigate | `/notes/search` |  |\n| 3 | Select the tag filter project-x | select | `[data-testid=\"US-006-04-tag-filter\"]` | project-x |\n| 4 | Apply the tag filter | click | `[data-testid=\"US-006-04-apply-filter\"]` |  |\n| 5 | Verify the search request includes tags=project-x | assert_url | `/notes/search` | tags=project-x |\n| 6 | Verify only notes with the tag are rendered in Title A-Z order | assert_text | `[data-testid=\"US-006-04-result-item\"]` | only tagged, titles ascending |\n| 7 | Verify the live result count equals the number of tagged matches | assert_text | `[data-testid=\"US-006-04-result-count\"]` | count equals tagged matches |\n", "parent_ids": ["US-006-04", "EP-006"]}, {"id": "TC-006-13", "type": "test_plan", "path": "test-plans/TC-006-13.md", "content": "---\nid: TC-006-13\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-05]]\nepic: [[EP-006]]\nmodule: add or remove tags on a note from the web app\n---\n\n# TC-006-13: the user adds a tag\n\n**Story:** [[US-006-05]]\n**Module:** add or remove tags on a note from the web app\n\n## Preconditions\na note is open\n\n## Expected Result\nthe web app calls the tag-add endpoint and displays the updated tag list\n\n## Acceptance Criteria\nGiven a note is open, when the user adds a tag, then the web app calls the tag-add endpoint and displays the updated tag list\nGiven a note has an existing tag, when the user removes it, then the web app calls the tag-remove endpoint and the tag disappears from the displayed list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open a note's tag management screen | navigate | `/notes/{id}/tags` |  |\n| 2 | Enter a new tag name | fill | `[data-testid=\"US-006-05-tag-input\"]` | work |\n| 3 | Click add tag | click | `[data-testid=\"US-006-05-tag-add\"]` |  |\n| 4 | Verify the updated tag list displays the new tag | assert_text | `[data-testid=\"US-006-05-tag-list\"]` | work |\n", "parent_ids": ["US-006-05", "EP-006"]}, {"id": "TC-006-14", "type": "test_plan", "path": "test-plans/TC-006-14.md", "content": "---\nid: TC-006-14\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-05]]\nepic: [[EP-006]]\nmodule: add or remove tags on a note from the web app\n---\n\n# TC-006-14: the user removes it\n\n**Story:** [[US-006-05]]\n**Module:** add or remove tags on a note from the web app\n\n## Preconditions\na note has an existing tag\n\n## Expected Result\nthe web app calls the tag-remove endpoint and the tag disappears from the displayed list\n\n## Acceptance Criteria\nGiven a note is open, when the user adds a tag, then the web app calls the tag-add endpoint and displays the updated tag list\nGiven a note has an existing tag, when the user removes it, then the web app calls the tag-remove endpoint and the tag disappears from the displayed list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open a note that has an existing tag | navigate | `/notes/{id}/tags` |  |\n| 2 | Click remove on the existing tag | click | `[data-testid=\"US-006-05-tag-remove\"]` |  |\n| 3 | Verify the tag disappears from the displayed list | assert_text | `[data-testid=\"US-006-05-tag-list\"]` | tag absent |\n", "parent_ids": ["US-006-05", "EP-006"]}, {"id": "TC-006-15", "type": "test_plan", "path": "test-plans/TC-006-15.md", "content": "---\nid: TC-006-15\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-06]]\nepic: [[EP-006]]\nmodule: archive or restore a note from the web app\n---\n\n# TC-006-15: the user clicks archive\n\n**Story:** [[US-006-06]]\n**Module:** archive or restore a note from the web app\n\n## Preconditions\nan active note is open\n\n## Expected Result\nthe web app calls the archive endpoint and the note disappears from the default active list\n\n## Acceptance Criteria\nGiven an active note is open, when the user clicks archive, then the web app calls the archive endpoint and the note disappears from the default active list\nGiven an archived note is viewed, when the user clicks restore, then the web app calls the unarchive endpoint and the note reappears in the active list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open an active note | navigate | `/notes/{id}` |  |\n| 2 | Click archive | click | `[data-testid=\"US-006-06-archive-button\"]` |  |\n| 3 | Open the default active notes list | navigate | `/notes` |  |\n| 4 | Verify the note no longer appears in the active list | assert_text | `[data-testid=\"US-006-01-note-item\"]` | note absent |\n", "parent_ids": ["US-006-06", "EP-006"]}, {"id": "TC-006-16", "type": "test_plan", "path": "test-plans/TC-006-16.md", "content": "---\nid: TC-006-16\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-26T20:27:52Z\nstory: [[US-006-06]]\nepic: [[EP-006]]\nmodule: archive or restore a note from the web app\n---\n\n# TC-006-16: the user clicks restore\n\n**Story:** [[US-006-06]]\n**Module:** archive or restore a note from the web app\n\n## Preconditions\nan archived note is viewed\n\n## Expected Result\nthe web app calls the unarchive endpoint and the note reappears in the active list\n\n## Acceptance Criteria\nGiven an active note is open, when the user clicks archive, then the web app calls the archive endpoint and the note disappears from the default active list\nGiven an archived note is viewed, when the user clicks restore, then the web app calls the unarchive endpoint and the note reappears in the active list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open the archived notes screen | navigate | `/notes/archived` |  |\n| 2 | Open an archived note and click restore | click | `[data-testid=\"US-006-06-restore-button\"]` |  |\n| 3 | Open the active notes list | navigate | `/notes` |  |\n| 4 | Verify the note reappears in the active list | assert_text | `[data-testid=\"US-006-01-note-item\"]` | note present |\n", "parent_ids": ["US-006-06", "EP-006"]}]}
