# Rubric Cosmetic Checks

Run these checks against one or more task folders, then write two report files into a versioned results folder.

These checks cover only metadata and structural correctness — none of them require changing criterion text, changing weights, dropping criteria, or adding criteria.

## Inputs

A **parent directory** containing one or more task folders. Each task folder has:
- `rubric/rubric.json` — list of criterion objects with fields: `item`, `points`, `criterion`, `category`, `criterion_type`, `verification_type`, `type`, optionally `rationale`.
- `prompt/prompt.txt` — the task prompt that the rubric is meant to evaluate.

The task folder name is the **task ID** (used in reports).

## Output format

Do reasoning silently. **Do NOT print scratch work, intermediate analysis, value verification, or recounts.** Write only the final report files.

Create a folder `results-vN/` (where `N` is the next unused version number) **in the same directory as this MD file**. Inside, write two files:

### File 1: `results-vN/aggregated_results.md`

```markdown
# Aggregated Rubric Cosmetic Check Results — vN

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
| B1 | Verification type correctness | X/N | X% |
| B3 | Implicit vs explicit tagging | X/N | X% |
| B4 | criterion_type / type consistency | X/N | X% |
| B5 | JSON validity | X/N | X% |
| B6 | Category validity | X/N | X% |

## Notable patterns
- 3–6 bullets describing patterns observed across the dataset.
- Examples: which checks fire most often, common rubric-author tendencies, structural issues that repeat, surprising findings.
```

### File 2: `results-vN/task_level_results.md`

```markdown
# Task-Level Rubric Cosmetic Check Results — vN

## Task task_id_1
- ❌ B4 criterion_type / type consistency — items 3, 17: criterion_type is "explicit" but type is "implicit"
- ❌ B6 Category validity — item 22: tagged Extraction but requires arithmetic → should be Reasoning
- (only failed checks listed; N/A noted only when relevant)

## Task task_id_2
✅ All checks passed
```

For each task, list ONLY the failed checks. Tasks that pass everything get a single `✅ All checks passed` line. Item numbers must be the actual rubric item numbers from `rubric.json`.

---

## B. Rubric Metadata Level Checks

### B1. Verification type correctness

`verification_type` must be `multimodal` only when grading requires visual layout, color, audio, or image analysis. Everything else (including heading presence, section presence, text matching, numeric values) must be `text`.

- **PASS**: Every `verification_type` matches what the criterion actually requires.
- **FAIL**: List items mistagged (e.g., a heading-presence check tagged `multimodal`, or a color check tagged `text`).

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

## Procedure

1. Determine the next unused version number `N` by inspecting sibling folders to this MD file. If `results-v1/` does not exist, use `v1`. Otherwise use the next integer (`v2`, `v3`, ...).
2. List all task folder names in the parent directory provided by the user. These are the task IDs.
3. For each task folder:
   1. Load `rubric/rubric.json` and `prompt/prompt.txt`.
   2. If JSON parsing fails, mark only B5 as FAIL and treat all other checks as not run for that task.
   3. Otherwise, run checks in order: B1, B3, B4, B5, B6. Collect the list of FAIL check IDs for this task.
4. After processing every task, write the two report files described under "Output format" into `results-vN/`. Do not print check-by-check results to the conversation; the report files are the output.
5. Send the user a one-line confirmation with the path to `results-vN/` and a one-sentence headline (e.g., total tasks evaluated, how many passed all checks).
