# Task-Level Rubric Quality Check Results — v1

## Task 001bcc19
- ❌ A6 No vague criteria — items 17, 35, 41: semantic verbs ("supported by", "relevant to", "binding driver") expect specific named answers not inlined
- ❌ A8 Cascading — items 3, 14, 39: upstream of 4/5/6, 43, 40 respectively
- ❌ C3 Similar criteria, similar weights — items 4,5,6 (sheet-presence at 70) diverge from items 11–15 (section-presence at 40/50/50/50/50)

## Task 209c8744
- ❌ A6 No vague criteria — items 13, 44: "analytical interpretation" / "analytical tone" not operationalized
- ❌ A8 Cascading — items 1, 2, 3, 4, 5: section-heading items upstream of content-of-section checks
- ❌ B1 Verification type — items 5, 15, 16, 22: text checks tagged multimodal
- ❌ C3 Similar criteria, similar weights — items 23–30: same-pattern extractions weighted 30, 20, 20, 20, 20, 20, 20, 30
- N/A B2 — no negative criteria
- N/A C4 — no negative criteria

## Task 53c69e21
- ❌ A4 Self-containment — item 3: references "resources sheet" without inlining the 24 team member names
- ❌ B3 Implicit vs explicit tagging — item 44: "28 DO NOT POST accounts" count is rubric-author derived; should be implicit
- ❌ B6 Category — items 35, 36, 37, 43: aggregation/filtering across rows is Reasoning, not Instruction Following
- ❌ C3 Similar criteria, similar weights — items 35, 36 (90) vs item 37 (80): same coverage pattern, divergent weights

## Task 9772ed23
- ❌ A6 No vague criteria — items 35, 36: "clear and concise" / "professional and courteous" not operationalized
- ❌ A7 MECE — duplicates — items 17/37 and 21/38: positive eligibility paired with inverted-negative on same observable
- ❌ A8 Cascading — items 1, 13, 17, 21: upstream of more specific downstream criteria
- ❌ C2 Weight distribution spread — weights cluster heavily at 10/25; spread uneven across bands
- ❌ C3 Similar criteria, similar weights — items 13, 15, 17, 19, 21 (Eligible-No checks) weighted 25, 25, 25, 25, 20

## Task 2e58beea
- ❌ A8 Cascading — items 1, 2, 5: upstream of file-name / tab-presence checks (non file-format/name pair)
- ❌ B6 Category — items 22, 16, 40: aggregation/derived values tagged Extraction, should be Reasoning
- N/A B2 — no negative criteria
- N/A C4 — no negative criteria

## Task 442e03f0
- ❌ A8 Cascading — items 3, 42: upstream of items 4–7 and item 43
- ❌ B4 criterion_type / type consistency — items 1–45: missing `type` field on every item
- ❌ B5 JSON validity — items 1–45: missing required `type` field on every item
- ❌ C3 Similar criteria, similar weights — items 18, 20, 23, 26: same-pattern Deployability checks defined for only 4 of 8 industries

## Task 73ea865b
- ❌ A3 Atomicity — items 16, 18: bundle determination plus disqualifying-reason exclusions
- ❌ A7 MECE — duplicates — items 15, 34: both penalize affirmative certify language (inverted-positive)
- ❌ A8 Cascading — item 3 upstream of 8/37; item 16 upstream of 17; item 18 upstream of 19
- ❌ B6 Category — items 11, 12, 13, 14: out-of-scope/scope-application checks are Instruction Following, not Reasoning
- ❌ C3 Similar criteria, similar weights — items 17, 19–26 (FALSE-per-domain at 65) diverge from items 11–14 (same pattern at 50)

## Task a39503e1
- ❌ A6 No vague criteria — items 25, 33: "visually distinct CTA" / "coherent color palette" not operationalized
- ❌ B1 Verification type — items 33, 34, 35, 36, 41, 42, 43: layout/branding tagged text/multimodal inconsistently
- ❌ B6 Category — items 37, 38, 39: contact info extraction tagged Reasoning, should be Extraction
- ❌ C3 Similar criteria, similar weights — items 10–13 (60) diverge from item 9 (70): same-pattern division team-list checks

## Task d8997902
- ❌ A7 MECE — duplicates — items 17, 18: both check the community meeting determination section
- ❌ A8 Cascading — items 17, 18 upstream/downstream of each other (section presence cascading)
- ❌ C3 Similar criteria, similar weights — items 22, 23, 24, 25: signature/date pattern weighted 50, 50, 50, 55

## Task f79e0563
- ❌ A7 MECE — duplicates — items 19, 40: inverted-positive duplicate on escalation start year
- ❌ B1 Verification type — items 34, 35: color checks tagged text, should be multimodal
