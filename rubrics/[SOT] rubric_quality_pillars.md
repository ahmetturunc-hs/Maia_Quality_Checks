# Rubric Quality Checks

Run these checks against one or more task folders, then write two report files into a versioned results folder.

## Inputs

A **parent directory** containing one or more task folders. Each task folder has:
- `rubric/rubric.json` — list of criterion objects with fields: `item`, `points`, `criterion`, `category`, `criterion_type`, `verification_type`, `type`, optionally `rationale`.
- `prompt/prompt.txt` — the task prompt that the rubric is meant to evaluate.
- `input-files/` — supporting input files referenced by the prompt (e.g., spreadsheets, source documents, assumptions). Read these too when evaluating contradictions and prompt coverage. Values that originate in input files are NOT contradictions when echoed in the rubric.

The task folder name is the **task ID** (used in reports).

## Output format

Do reasoning silently. **Do NOT print scratch work, intermediate analysis, value verification, or recounts.** Write only the final report files.

Create a folder `results-vN/` (where `N` is the next unused version number) **in the same directory as this MD file**. Inside, write two files:

### File 1: `results-vN/aggregated_results.md`

```markdown
# Aggregated Rubric Quality Check Results — vN

## Tasks evaluated (N tasks)
- task_id_1
- task_id_2
- ...

## Per-task pass/fail (binary)
| Task ID | Result |
|---|---|
| task_id_1 | ❌ Failed at least one check |
| task_id_2 | ✅ Passed all checks |
| ... | ... |

A task is `✅ Passed all checks` only when every applicable check is PASS or N/A. Any single FAIL → `❌ Failed at least one check`.

## Check-level fail rates
| Check | Description | Tasks failing | % failing |
|---|---|---|---|
| A1 | Rubric matches the prompt | X/N | X% |
| A2 | No contradictions | X/N | X% |
| A3 | Atomicity | X/N | X% |
| A4 | Self-containment | X/N | X% |
| A5 | Output only | X/N | X% |
| A6 | No vague criteria | X/N | X% |
| A7 | MECE — no duplicates | X/N | X% |
| A8 | MECE — no cascading | X/N | X% |
| A9 | Comprehensive grading | X/N | X% |
| B1 | Verification type correctness | X/N | X% |
| B2 | Negative direction | X/N | X% |
| B3 | Implicit vs explicit tagging | X/N | X% |
| B4 | criterion_type / type consistency | X/N | X% |
| B5 | JSON validity | X/N | X% |
| B6 | Category validity | X/N | X% |
| C1 | Weight band adherence | X/N | X% |
| C2 | Weight distribution spread | X/N | X% |
| C3 | Similar criteria, similar weights | X/N | X% |
| C4 | Negative criteria narrowly scoped | X/N (Y N/A) | X% |
| C5 | Category distribution | X/N | X% |
| C6 | Trivial IF weight | X/N (Y N/A) | X% |

## Notable patterns
- 3–6 bullets describing patterns observed across the dataset.
- Examples: which checks fire most often, common rubric-author tendencies, structural issues that repeat, surprising findings.
```

### File 2: `results-vN/task_level_results.md`

```markdown
# Task-Level Rubric Quality Check Results — vN

## Task task_id_1
- ❌ A3 Atomicity — items 17: bundles X and Y
- ❌ A6 No vague criteria — items 17, 35: "supported by"/"relevant to" not operationalized
- ❌ A8 Cascading — item 3 upstream of items 4, 5, 6
- (only failed checks listed; N/A noted only when relevant)

## Task task_id_2
✅ All checks passed
```

For each task, list ONLY the failed checks (and N/A entries if you want to note them). Tasks that pass everything get a single `✅ All checks passed` line. Item numbers must be the actual rubric item numbers from `rubric.json`.

---

## A. Rubric Criteria Level Checks

### A1. Rubric matches the prompt

The rubric must evaluate THIS prompt, not a different one. Check that no criterion references files, deliverables, sections, values, or entities that do not appear in `prompt.txt` OR in `input-files/`.

- **PASS**: Every criterion's referenced artifacts exist in the prompt or its input files.
- **FAIL**: Any criterion mentions a file/deliverable/section/entity absent from both the prompt and the input files.

### A2. Rubric does not contradict the prompt or input files

If the prompt or any input file specifies a value, threshold, name, or constraint, the rubric must not assert a different one. A value drawn from an input file is NOT a contradiction.

- **PASS**: All numeric values, names, and constraints in the rubric match the prompt and input files.
- **FAIL**: Any criterion asserts a value that conflicts with the prompt or an input file (e.g., prompt/input says X = 4, rubric says X = 5).

### A3. Atomicity

Each criterion should check one thing. Inherently linked pairs (e.g., a Resp Code + Desc Code that together form a single code pair) are acceptable. Bundling unrelated checks is not.

- **PASS**: Every criterion checks a single concept, OR a tightly linked pair that cannot be meaningfully separated.
- **FAIL**: A criterion bundles independent checks (e.g., "The response says X AND Y AND Z" where X/Y/Z could be graded independently).

### A4. Self-containment

The criterion text alone must give the grader everything needed to grade it. References to external context (e.g., "the resources sheet", "question 1", "the spec", "the JBP threshold", "the Q3 target") are not acceptable — the actual values must be embedded. **This applies even when the value lives in an input file**: the exact number/name must appear in the criterion text. It is not OK to name a concept and rely on the grader to resolve it from inputs.

- **PASS**: Every criterion is graspable from its own text without consulting another file or context.
- **FAIL**: A criterion refers to "the X sheet/file/section/threshold/target" or "question N" without inlining the relevant values.
  - Example bad: "Batches are assigned to all 24 team members listed in the resources sheet."
  - Example good: "Batches are assigned to all 24 team members: Ahmet, Gaurav, Varun, ..."
  - Example bad: "Reports attainment against the Q3 target."
  - Example good: "Reports attainment against the Q3 target of $26,500."
  - Example bad: "The response provides the answer to question 1."
  - Example good: "The response provides the answer as x = 5."

### A5. Output only

Criteria must evaluate the OUTPUT (final artifacts), not the process used to produce it.

- **PASS**: Every criterion checks a property of the deliverable.
- **FAIL**: A criterion describes intermediate steps or process (e.g., "first creates the Excel file, then derives x = 5").

### A6. No vague criteria

Criteria must be specific enough to be graded consistently. Subjective adjectives without operationalization are vague. The fix is to give a concrete, checkable test (a phrase to look for, a length cap, a forbidden pattern, etc.).

Semantic-relationship verbs ("supported by", "consistent with", "binding driver", "reflects") are acceptable when they describe the logical relationship in the abstract. They become a FAIL when the rubric expects a specific named answer or value and doesn't inline it.

- **PASS**: Every criterion specifies concrete, checkable evidence. Logical-relationship phrasing is fine when no specific value is required.
- **FAIL**: A criterion uses vague terms ("professional", "clear", "appropriate", "well-structured", "concise") without naming specific examples or thresholds. OR a criterion uses a semantic-relationship verb but expects a specific named answer that isn't inlined.
  - Example bad: "The response uses a professional tone."
  - Example good: "The response uses phrases like 'with regards to' or 'pertaining to'."
  - Example bad: "The customer response uses clear and concise language appropriate for a premium brand."
  - Example good: "The customer response does not include an additional paragraph describing the brand's benefits (which would make it verbose)."
  - Example acceptable: "The final recommendation is supported by the valuation screen and the portfolio-fit analysis." (logical relation, no specific named answer expected)
  - Example bad: "The memo identifies the binding driver of the final recommendation." (a specific binding driver is expected — must inline it: "...identifies the PEG screen as the binding driver of the final recommendation.")

### A7. MECE — no duplicates / overlap

Two criteria must not check the same condition. This includes **inverted-positive duplicates**: a positive criterion that rewards behavior X paired with a negative criterion that penalizes "not X" — both fire on the same observable variable and double-count it.

- **PASS**: No two criteria evaluate the same condition (no straight duplicates and no inverted-positive pairs).
- **FAIL**: List pairs that overlap. Two patterns to flag:
  - Straight duplicate: two items checking the same property (e.g., both check "the file is named X").
  - Inverted-positive duplicate: a positive item and a negative item that resolve the same condition. Example: positive `+75` "Escalation begins in Year 2" and negative `-75` "Escalation begins in Year 1 rather than Year 2" — same observable, opposite weights, double-counted. Keep one (typically the positive).

### A8. MECE — cascading, keep only the most downstream

When criterion A is strictly upstream of criterion B (B cannot pass unless A passes, and B's check is more specific), keep only B.

- **Exception**: Do NOT flag the standard "file format" + "file name" pair (e.g., "The deliverable is an Excel file" upstream of "The Excel file is named nonpostdistribution.xlsx"). This pair is treated as acceptable.
- **PASS**: No upstream/downstream cascades exist between criteria, OR the only cascades are file-format/file-name pairs.
- **FAIL**: List the upstream item(s) that should be removed.
  - Example to flag: Item 8 "The workbook contains exactly three worksheets" upstream of items 9, 10, 11 each naming a specific worksheet — flag item 8.
  - Example to flag: Item X "The memo includes a Risk Assessment section" upstream of item Y "The Risk Assessment includes at least three material risks" — flag item X.

### A9. Comprehensive grading

Every major deliverable mentioned in the prompt must have at least one criterion covering it.

- **PASS**: Each named deliverable / required section / required output is covered by at least one criterion.
- **FAIL**: List the deliverables / required sections from the prompt that have zero corresponding criteria.

---

## B. Rubric Metadata Level Checks

### B1. Verification type correctness

`verification_type` must be `multimodal` only when grading requires visual layout, color, audio, or image analysis. Everything else (including heading presence, section presence, text matching, numeric values) must be `text`.

- **PASS**: Every `verification_type` matches what the criterion actually requires.
- **FAIL**: List items mistagged (e.g., a heading-presence check tagged `multimodal`, or a color check tagged `text`).

### B2. Negative criteria — direction matches phrasing

Negative-weighted criteria must be logically consistent with their phrasing: when the criterion is TRUE, the response should deserve to be penalized.

- **PASS**: Every negative-weighted criterion describes a failure mode such that triggering it = bad behavior.
- **FAIL**: List items where the negative weight would penalize correct behavior, or where a positive weight would reward incorrect behavior.
  - Example bad: criterion "The response does not say 2 = 3" with weight `-75` — correct behavior gets penalized.

### B3. Implicit vs explicit tagging

Set `criterion_type` (and `type`) to `explicit` only when the prompt clearly demands the exact checked property — including the specific value. When the prompt names a structure but the rubric author invents specifics (titles, counts, values) the prompt did not state, tag as `implicit`.

- **PASS**: Tagging is consistent with whether the prompt explicitly demands the exact property.
- **FAIL**: List items where tagging disagrees with the prompt evidence.
  - Example: prompt says "create three sheets" but does NOT name them. A criterion checking the sheet is titled "Company Financials" must be tagged `implicit` (the title was the rubric author's invention, not a prompt directive).
  - Example: prompt says "include a Risk Assessment section". A criterion checking that section's presence is `explicit`.

### B4. criterion_type / type consistency

The `criterion_type` and `type` fields are duplicates and must agree.

- **PASS**: Every item has `criterion_type == type`.
- **FAIL**: List items where they differ.

### B5. JSON validity

`rubric.json` must parse and conform to the expected shape (list of objects with the required fields).

- **PASS**: File parses; every item has `item`, `points`, `criterion`, `category`, `criterion_type`, `verification_type`, `type`.
- **FAIL**: Note the parse error or list items missing required fields.

### B6. Category validity and consistency

`category` must be exactly one of: `Extraction`, `Formatting`, `Instruction Following`, `Reasoning`. The category must also be consistent with what the criterion checks.

- Use this as a guide:
  - **Instruction Following** — checks compliance with an explicit prompt directive (file names, sections, counts, ranges).
  - **Reasoning** — checks a derived conclusion, calculation, or judgment.
  - **Formatting** — checks layout, headers, styling, presentation.
  - **Extraction** — checks a specific value pulled from input materials.
- **Reasoning vs Extraction (common confusion)**:
  - Extraction: "The Company Financials sheet states FY2024 total revenue as $3,055.555 million" — the value is pulled verbatim from a source document.
  - Reasoning: "The Company Financials sheet calculates FY2024 to FY2025 revenue growth at approximately 29.4%" — the value is derived via a calculation on extracted inputs.
  - **Aggregations across rows are Reasoning, not Extraction.** Even if every underlying value lives in the input, summing/averaging/counting them is arithmetic.
    - Example: "Total consumer sell-through across all five SKUs as 12,328 units" requires summation → Reasoning, not Extraction.
  - Rule of thumb: if the value appears verbatim in a single cell/line of the input materials → Extraction. If it requires any arithmetic (including summation), comparison, or judgment → Reasoning.
- **PASS**: Every category is a valid value AND fits the criterion's actual nature.
- **FAIL**: List items with invalid or mismatched categories.

---

## C. Rubric Weighting Checks

### C1. Weight band adherence

Each criterion's weight should reflect its importance:
- **Core** (70–100) — explicitly asked in the prompt OR crucial to the answer.
- **Important** (50–70) — important to the answer but not directly asked.
- **Quality** (30–50) — helpful but secondary.
- **Polish** (10–30) — nice-to-have.

- **PASS**: Every weight sits in the band that fits the criterion's importance.
- **FAIL**: List items where the weight does not match the criterion's importance tier (e.g., a core deliverable weighted 30, or a polish item weighted 90).

### C2. Weight distribution spread

Weights must not all cluster in a narrow band. The rubric should use the full range across criteria.

- **PASS**: Weights span at least three of the four bands and are not clustered (e.g., not >80% of items at the same weight).
- **FAIL**: Note the clustering (e.g., "38 of 45 items weighted 70 or 80").

### C3. Similar criteria, similar weights

Criteria that check the SAME pattern at the SAME level of specificity must be weighted the same. "Similar" means same shape, same category, same level of detail — NOT just same topic.

- A group of N section-presence checks (one per section) → all should be the same weight.
- A group of N value-extraction checks of the same form (e.g., one revenue figure per fiscal year) → all should be the same weight.
- Two criteria that share a topic but check different attributes (e.g., one checks "column has a date", another checks "the date is the original payment date") are NOT siblings — they may legitimately have different weights, OR may be a cascading issue (see A8).
- **PASS**: All same-pattern, same-specificity criteria carry equivalent weights.
- **FAIL**: List groups of same-pattern criteria with diverging weights (e.g., 7 section-presence checks weighted 50, 40, 70, 70, 50, 60, 40).

### C4. Negative criteria narrowly scoped

When negative criteria exist, each one must target a concrete, identifiable failure mode — not a vague "has errors" catch-all. This check evaluates *scope*, not *existence* (the rubric is not required to have any negative criteria).

- **N/A**: Rubric has zero negative criteria. Print `N/A — no negative criteria` and do not count this as PASS or FAIL.
- **PASS**: Every negative criterion names a specific failure mode (e.g., "contains TBD or TODO", "cites an external source", "includes payment dates outside the 12/06–12/16 window").
- **FAIL**: List negative items that are vague (e.g., "has errors", "is incorrect", "is unprofessional").

### C5. Category distribution

The rubric's criteria should be distributed across categories within these target ranges:
- **Reasoning**: at least 50% of criteria
- **Instruction Following**: no more than 40% of criteria
- **Extraction**: 5–10% of criteria
- **Formatting**: 2–5% of criteria

Count all criteria (positive and negative) when computing percentages.

- **PASS**: All four categories fall within their target ranges, OR the deviation is clearly justified by the task type (e.g., a prompt with no input files may have 0% Extraction; a purely procedural prompt may legitimately push IF above 40%).
- **FAIL**: One or more categories fall outside their target range without a clear task-type justification. Report the actual distribution (e.g., "Reasoning 30%, IF 62%, Extraction 5%, Formatting 3%") and flag which categories are out of range.

### C6. Trivial instruction following weight

Trivial Instruction Following criteria — those that any model reading the prompt would almost certainly get right, requiring no reasoning, no domain knowledge, and no judgment — must be weighted in the 10–30 range. Examples of trivial criteria: file format checks ("the response is a PDF"), artifact count checks ("the response outputs exactly 3 files"), and self-evident output presence checks ("the deliverable includes a title page").

The test: if a model would have to actively ignore the prompt to fail this criterion, it is trivial.

- **N/A**: Rubric has no Instruction Following criteria that are clearly trivial.
- **PASS**: All trivial Instruction Following criteria are weighted between 10 and 30.
- **FAIL**: List trivial Instruction Following criteria weighted above 30 (e.g., item 3 "The response is a PDF file" weighted 80).

---

## Procedure

1. Determine the next unused version number `N` by inspecting sibling folders to this MD file. If `results-v1/` does not exist, use `v1`. Otherwise use the next integer (`v2`, `v3`, ...).
2. List all task folder names in the parent directory provided by the user. These are the task IDs.
3. For each task folder:
   1. Load `rubric/rubric.json`, `prompt/prompt.txt`, and every file under `input-files/`.
   2. If JSON parsing fails, mark only B5 as FAIL and treat all other checks as not run for that task.
   3. Otherwise, run checks in order: A1 → A9, B1 → B6, C1 → C6. Collect the list of FAIL / N/A check IDs for this task.
4. After processing every task, write the two report files described under "Output format" into `results-vN/`. Do not print check-by-check results to the conversation; the report files are the output.
5. Send the user a one-line confirmation with the path to `results-vN/` and a one-sentence headline (e.g., total tasks evaluated, how many passed all checks).
