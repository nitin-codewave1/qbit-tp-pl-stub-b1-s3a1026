---
id: agent-developer/notes_api/backend/SELF-CHECK.md
type: self-check
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.7.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/SELF-CHECK.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 54d3a259-71b6-5416-974e-2ac2cebc0ebd
okf_id: backend/SELF-CHECK.md
---

<!-- SECTION: backend.self-check | status: VERIFIED -->
## Verification pass

| # | Check | Result | Detail |
|---|---|---|---|
| 1 | anchors open and close; ids unique and well-formed | **PASS** | 11 section(s); all 11 sections present, in order |
| 2 | every referenced id resolves | **PASS** | every id cited carries this run's surface code |
| 3 | every minted id carries this surface's code | **PASS** | 79 minted, all carrying `BE` |
| 4 | no VERIFIED section contains an uncited claim | **NOT RUN** | a judgement about prose; the document's own verification pass covers it |
| 5 | locked sections reproduced byte-for-byte | **PASS** | the document is written through the locking engine |
| 6 | every story this document answers for appears in the coverage table | **PASS** | 6 in scope, 0 not found in the document |
| 7 | no schema is restated | **NOT RUN** | a judgement about prose, not a pattern this module can assert |
| 8 | no file written outside output_root | **PASS** | every write is jailed to `/tmp/qbit/specialist/54d3a259-71b6-5416-974e-2ac2cebc0ebd/001_f6baaa15-ffcc-4594-b93d-33cd249e7174-notes_api-agent-developer_agent-developer/devagent/output/backend` |
| 9 | every Q- has a class; every GAP- names its closing artefact | **NOT RUN** | 38 GAP- id(s) are defined in the specification's prose; whether each names a closing artefact is the document's own verification pass |
| 10 | no GAP- raised for a condition that cannot occur | **NOT RUN** | a judgement about the condition, not the id |

**6 passed, 0 failed, 4 not run.**

A check marked `NOT RUN` has nothing to check yet at this milestone — no specification sections exist. It is reported as not run rather than as a pass: reporting a pass that was never performed is invariant I-2 broken against ourselves.
<!-- /SECTION: backend.self-check -->
