# Aggregated Rubric Quality Check Results — v1

## Tasks evaluated (10 tasks)
- 001bcc19
- 209c8744
- 53c69e21
- 9772ed23
- 2e58beea
- 442e03f0
- 73ea865b
- a39503e1
- d8997902
- f79e0563

## Per-task pass/fail (binary)

| Task ID | Result |
|---|---|
| 001bcc19 | ❌ Failed at least one check |
| 209c8744 | ❌ Failed at least one check |
| 53c69e21 | ❌ Failed at least one check |
| 9772ed23 | ❌ Failed at least one check |
| 2e58beea | ❌ Failed at least one check |
| 442e03f0 | ❌ Failed at least one check |
| 73ea865b | ❌ Failed at least one check |
| a39503e1 | ❌ Failed at least one check |
| d8997902 | ❌ Failed at least one check |
| f79e0563 | ❌ Failed at least one check |

**0 of 10 tasks (0%) passed every applicable check. Every rubric in the dataset has at least one issue.**

## Check-level fail rates

| Check | Description | Tasks failing | % failing |
|---|---|---|---|
| A1 | Rubric matches the prompt | 0/10 | 0% |
| A2 | No contradictions | 0/10 | 0% |
| A3 | Atomicity | 1/10 | 10% |
| A4 | Self-containment | 1/10 | 10% |
| A5 | Output only | 0/10 | 0% |
| A6 | No vague criteria | 4/10 | 40% |
| A7 | MECE — no duplicates | 4/10 | 40% |
| A8 | MECE — no cascading | 7/10 | 70% |
| A9 | Comprehensive grading | 0/10 | 0% |
| B1 | Verification type correctness | 3/10 | 30% |
| B2 | Negative direction | 0/10 (2 N/A) | 0% |
| B3 | Implicit vs explicit tagging | 1/10 | 10% |
| B4 | criterion_type / type consistency | 1/10 | 10% |
| B5 | JSON validity | 1/10 | 10% |
| B6 | Category validity | 4/10 | 40% |
| C1 | Weight band adherence | 0/10 | 0% |
| C2 | Weight distribution spread | 1/10 | 10% |
| C3 | Similar criteria, similar weights | 8/10 | 80% |
| C4 | Negative criteria narrowly scoped | 0/10 (2 N/A) | 0% |

## Notable patterns

- **C3 (similar criteria, similar weights) is the dominant defect at 80%.** Rubric authors regularly weight sibling-pattern criteria (section-presence checks, per-domain extractions, per-row eligibility checks) with small inconsistent differences (e.g., 50/50/50/55, 60/60/60/70, 25/25/25/20). This is the single most actionable finding — auditing weights within each "shape group" before publishing would catch most of these in seconds.

- **A8 (cascading) at 70% is structural.** The most common upstream/downstream pattern is "section/tab is present" upstream of "section/tab contains specific content". Even with the file-format/file-name exception, this pattern is endemic across rubrics that grade multi-section deliverables. Removing the upstream item (per the procedure) would clean this up.

- **A6, A7, B6 cluster at 40% — three independent quality issues each affecting four out of ten rubrics.** Vague criteria (subjective adjectives like "clear", "concise", "analytical"), inverted-positive duplicates (positive item rewards X paired with negative item penalizing not-X), and Reasoning/Extraction category mistags (especially aggregations tagged as Extraction) recur across the dataset.

- **B1 (verification type) at 30% — multimodal mistags are common in two directions.** Some rubrics tag pure text-presence checks as `multimodal`; others tag genuine color/layout checks as `text`. The line is well-defined in the procedure but rubric authors apply it inconsistently.

- **0% fail on A1, A2, A5, A9, C1 across this dataset** — these checks did not catch issues here, but with only 10 tasks the absence is not strong evidence the issues never occur. A2 and A9 in particular benefit from input-files awareness, which was added in v1.

- **442e03f0 has a schema-wide defect** — every item is missing the `type` field. This is the only rubric with a structural JSON shape problem (B4 + B5 fail).

- **All ten rubrics fail at least one check.** No rubric in the dataset is fully clean. The headline opportunity is reducing C3 and A8 through routine pre-publication auditing.
