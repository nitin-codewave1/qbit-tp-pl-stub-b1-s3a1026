---
id: agent-developer/web_app/web-app/SELF-CHECK.md
type: self-check
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/SELF-CHECK.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 6a137249-ccab-5a0a-bd2d-8ea991e30fa2
okf_id: web_app:web-app/SELF-CHECK.md
---

<!-- SECTION: web-app.self-check | status: VERIFIED -->
## Verification pass

| # | Check | Result | Detail |
|---|---|---|---|
| 1 | anchors open and close; ids unique and well-formed | **FAIL** | 12 section(s); 1 missing |
| 2 | every referenced id resolves | **NOT RUN** | 21 foreign id(s) cited — resolvable only against an upstream this run did not receive: Q-BE-003, CON-BE-003, Q-BE-012, GAP-BE-038, GAP-BE-053 |
| 3 | every minted id carries this surface's code | **PASS** | 51 minted, all carrying `WEB` |
| 4 | no VERIFIED section contains an uncited claim | **NOT RUN** | a judgement about prose; the document's own verification pass covers it |
| 5 | locked sections reproduced byte-for-byte | **PASS** | the document is written through the locking engine |
| 6 | every story this document answers for appears in the coverage table | **FAIL** | 36 in scope, 36 not found in the document: US-001-01, US-001-02, US-001-03, US-001-04, US-001-05, US-001-06, US-002-01, US-002-02 |
| 7 | no schema is restated | **NOT RUN** | a judgement about prose, not a pattern this module can assert |
| 8 | no file written outside output_root | **PASS** | every write is jailed to `/tmp/qbit/specialist/6a137249-ccab-5a0a-bd2d-8ea991e30fa2/005_f6baaa15-ffcc-4594-b93d-33cd249e7174-web_app-agent-developer_agent-developer/devagent/output/web-app` |
| 9 | every Q- has a class; every GAP- names its closing artefact | **NOT RUN** | 23 GAP- id(s) are defined in the specification's prose; whether each names a closing artefact is the document's own verification pass |
| 10 | no GAP- raised for a condition that cannot occur | **NOT RUN** | a judgement about the condition, not the id |

**3 passed, 2 failed, 5 not run.**

A check marked `NOT RUN` has nothing to check yet at this milestone — no specification sections exist. It is reported as not run rather than as a pass: reporting a pass that was never performed is invariant I-2 broken against ourselves.
<!-- /SECTION: web-app.self-check -->
