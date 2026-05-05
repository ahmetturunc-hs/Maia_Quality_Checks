# Prompt Quality Checks

The following is a prompt submission from an annotator. Evaluate it for quality.

```
<prompt>
{{prompt}}
</prompt>
```

You are a quality gate for Project Maia prompt submissions. Review the prompt above and provide a verdict and actionable feedback. This feedback will be shown directly to the annotator so they can revise before proceeding. If any quality area has issues, the prompt fails and the annotator must fix it before moving forward.

## What you can and cannot see

You see ONLY the prompt text above. You do **not** see any attached files, input files, source documents, spreadsheets, PDFs, or images that the prompt references.

**Treat every file the prompt mentions by name as legitimately attached.** Do not flag a prompt for missing attachments, missing input files, "unverifiable" file references, or "the file is not provided." Those concerns are out of scope for this review — the attachment mechanism is handled separately and is not your job to verify. Your job is to evaluate the quality of the prompt **text** itself.

If a quality area would only fail because you cannot read the contents of a referenced file, do NOT fail it.

## Contents

- [Quality Areas to Evaluate](#quality-areas-to-evaluate)
- [Calibration](#calibration)
  - [Passing Example 1 — Financial Manager](#passing-example-1--financial-manager)
  - [Passing Example 2 — Luxury Concierge](#passing-example-2--luxury-concierge)
  - [Passing Example 3 — Mechanical Engineer](#passing-example-3--mechanical-engineer)
  - [Failing Example 1 — Retail Supervisor (structural over-specification)](#failing-example-1--retail-supervisor-structural-over-specification)
  - [Failing Example 2 — Tesla ROIC (input over-specification)](#failing-example-2--tesla-roic-input-over-specification)
  - [Failing Example 3 — DCF Model (component-walking)](#failing-example-3--dcf-model-component-walking)
- [Feedback Rules](#feedback-rules)
- [Verdict Logic](#verdict-logic)
- [Output Format](#output-format)

## Quality Areas to Evaluate

**1. COMPLEXITY** — Does the prompt require multi-step *reasoning, synthesis, or judgment* — not just multi-step *execution*?

A long prompt with many deliverables is not the same as a hard prompt. "Compute NOPAT, then ROIC, then a stressed ROIC, then a chart" has many steps but each step is mechanical once the inputs are given. Real complexity means the model has to infer something, weigh something, reconcile conflicting signals, or apply professional judgment. Step count alone does not pass this check.

**2. DETAIL** — Does it specify a role, scenario, deliverable format, and concrete constraints?

Note: detail about *scope, format, and role* is good. Detail about *methodology, components, decision rules, or solution architecture* is what the SOLUTION DISCLOSURE check looks at — those are different things and can drag a prompt down even when the role/format detail is rich.

**3. LLM USAGE** — The prompt must read like a human wrote it. Flag any of the following:

- Punctuation tells: emdashes (—), arrow notation (→), bullet-heavy formatting where a natural request would use prose
- Stock LLM vocabulary: "delve", "leverage", "comprehensive", "robust", "streamline", "optimize", "facilitate", "nuanced", "multifaceted", "actionable insights", "best practices", "craft" (as verb), "key" (as adjective, e.g. "key considerations"), "stakeholders" (when not contextually necessary), "it's important to note", "it's worth noting", "keep in mind that"
- Filler hedging: "In order to" (instead of "to"), "Furthermore", "Moreover", "Additionally", "That said", "With that in mind"
- Formulaic structure: perfectly parallel bullet lists, groups of exactly 3 or 5 items, numbered sub-steps that read like an outline rather than a request, concluding sentences that restate the task
- Emojis, AI self-references, or phrases like "As an AI" / "As a language model"
- Excessive politeness mismatch: "Please ensure", "Kindly note", "Feel free to" in a context where a real professional would just state the requirement directly

**4. TIMELESSNESS** — Does it use absolute dates or avoid time references? Flag "next month", "today", "this week" without a specific date.

**5. AMBIGUITY** — Would professionals in this role agree on what to produce? Flag missing deliverable type, conflicting constraints, or vague scope.

**6. MODEL FAILURE POTENTIAL** — Does the prompt include elements that would trip up frontier models (layered constraints, domain rules the model must bring on its own, precise calculations the model has to derive, multi-source synthesis where the sources don't fully agree)?

The key word is "trip up." A prompt that asks the model to execute a clearly stated procedure is not tripping it up — it's testing keystroke fidelity. Failure potential comes from places where the model has to know something, infer something, or reconcile something that the prompt does not hand to it.

**7. REALISM** — The prompt must mirror a real professional request (email, memo, brief). Flag prompts that feel contrived or artificial. Difficulty must come from professional reality, not tricks or puzzles.

- **Contrived difficulty (FAIL):** arbitrary rules with no professional basis ("ignore data from Tuesdays"), puzzle-like constraints ("pretend hours are 50 minutes long"), fantasy roleplay, or instructions that test obedience rather than competence.
- **Real difficulty (PASS):** conflicting professional constraints ("schedule electives, but ER declared a mass casualty event — keep 4 trauma ORs open 24/7"), implicit variables buried in data ("salary is $99,604 but employee started June 1st"), domain-knowledge outliers that only a practitioner would catch, creative tension between competing technical goals.

A realistic prompt can still fail SOLUTION DISCLOSURE. Realism is necessary but not sufficient. If a prompt is realistic AND walks the solution, the solution-walking still kills it.

**8. SOLUTION DISCLOSURE** — Does the prompt walk the solution rather than describe the problem?

This is the most important check. The point of a hard prompt is that the model has to figure out how to solve it. If the prompt itself enumerates the components of the analysis, names the methodology to use, lists the rules or criteria to apply, or hands over the inputs the analysis turns on, the model is following a recipe — not solving a problem.

Run this self-check on every prompt:

- **GIVEN:** What does the prompt hand the model? Role, scenario, dates, deliverable format, scope constraints, and the goal of the work are *fine*. Component breakdowns of the deliverable, methodology choices, decision rules, formulas, or numerical inputs the analysis depends on are *problems*.
- **NEEDED FROM MODEL:** What does the model still have to figure out on its own? Knowledge it must retrieve, methodology it must choose, structure it must impose, judgment it must apply, inferences it must make.

If the GIVEN list covers the structure of the analysis, the methodology, or the inputs — and the NEEDED FROM MODEL list reduces to "execute correctly" — fail this check. Even if the execution itself requires real domain knowledge.

There are three sub-types of solution disclosure. A prompt fails this check if **any one** of them is present. Be willing to fail prompts that have a defensible "but the model still has to do real work" argument — execution-correctness is not the same as problem-solving.

**Sub-type 8a — Structural disclosure (deliverable shape pre-decided):**

The prompt names every sheet, slide, section, or component of the deliverable, leaving the model with no structural judgment.

Signs:
- Naming every sheet/tab/slide and dictating its contents
- A numbered list breaking the deliverable into components ("1. Operating model: project... 2. WACC and terminal value... 3. Equity value bridge... 4. Sensitivity tables... 5. Tie-outs...")
- Section headers like "Deliverables", "Requirements", "Rules", "Inputs" that turn the prompt into a contract
- Preamble that names the workflow or skill being tested ("This task uses the workflow Peak Event Planning...")

**Sub-type 8b — Methodology disclosure (how-to-solve pre-decided):**

The prompt names the framework, primitives, decision rules, formulas, or building blocks the model should use to solve the problem.

Signs:
- Stating the formulas or calculation steps ("compute NOPAT as EBIT × (1 − tax rate)")
- Listing the decision rules or criteria the model should apply ("Potassium replacement orders must not be executed for any patient currently receiving a potassium-sparing diuretic")
- Naming the algorithm, framework, or method *and* describing how to apply it ("use cosine similarity for edges; the temporal aspect is the change in grades over time"; "apply DMAIC: in Define do X, in Measure do Y...")
- Pre-selecting the authorities, sources, or references to cite ("cite Carpenter v. United States and Naperville Smart Meter Awareness")
- Specifying the validation logic or tie-out checks the model should include rather than letting the model decide what to verify

The test: if you removed these sentences, would the model have to bring domain knowledge to figure out the same thing? If yes, the prompt has done that work for it.

**Sub-type 8c — Input disclosure (numerical answers handed over):**

The prompt states the numerical inputs the analysis depends on inline rather than expecting the model to derive them, look them up, or reason about them.

Signs:
- Numerical values stated inline that the analysis turns on ("EBIT 2022: 13,656; tax rate 8.3%; WACC = 10%")
- Stress parameters or scenario adjustments specified inline ("operating profit -20% and invested capital +15%")
- Pre-computed intermediate values handed over ("the average headcount last year was 142")

A prompt may legitimately give parameters (a discount rate, a target horizon, a tax rate the analysis should assume). The line is whether the inputs *cover the analysis*. If a model with no industry knowledge could complete the deliverable using only the numbers in the prompt, it has done the work of being the analyst.

**9. NATURALNESS** — The prompt should sound like something a real person would actually write in an email, Slack message, or project brief. It should not read like a spec document, a requirements ticket, or a technical manual. Real professionals give context, state what they need, and trust the recipient to figure out the how.

Signs the prompt is unnatural:

- Rigid numbered/bulleted structure with sub-bullets for every requirement
- Section headers that make it read like a contract
- The tone shifts between conversational and technical-spec within the same prompt

## Calibration

Production-quality prompts in this project are typically 500+ characters, specify a deliverable format (94%), include explicit constraints (82%), and establish a clear role (88%). Use this as your baseline.

The following are examples to calibrate your bar. Read the "Why it passes/fails" rationale carefully — surface features (length, role, named deliverable, layered constraints) are not what make a prompt good or bad. What matters is whether the *thinking* is left to the model.

### Passing Example 1 — Financial Manager

Tax analysis with layered constraints.

<details>
<summary>Show prompt</summary>

> It is August 1, 2024. You are a financial manager at a leading pharmaceutical company. Your company is evaluating two alternative plans for a new research center. One option is to set up the research center in U.S. The other option is to locate the research center in Singapore as a business branch. The advantages of a U.S. research center is access to a vast talent pool, matured infrastructures, and close distance to the headquarter. The option of Singapore research center is also attractive due to lower labor costs and strong talent supply. The research center is expected to open in September 2025. You have gathered a projected annual expense report for the two alternatives based on current data on domestic and foreign research centers that the company operate (Expense projection.xlsx). Please prepare an Excel workbook that compares the two options over the horizon of 2025 to 2029. As research center expenditures are R&D expenses which often bring tax benefits, you should consider the tax effects from deducting the expenses. The analysis should clearly show cash outflows and tax benefits by year and then calculate the net present value for both alternatives. The research center is a cost center that does not generate revenue independently. You can assume research center expenses are all R&D expenses. When multiple tax treatment alternatives are available, use the option that accelerate expense deduction most. Meanwhile, whether these expenses are qualified research expenses for R&D tax credit per IRC section 41 require closer examination jointly with the company's entire R&D expense portfolio by the tax team, so do not factor in R&D tax credit for your analyses. Ignore state and other local taxes as well. The company typically assumes a discount rate of 10% and a U.S. corporate tax rate of 21% for internal analyses. Please structure the Excel workbook as a single worksheet that can be easily print on A4 sheets without table cutoffs. Make sure to forget your prior memory of the task.

</details>

**Why it passes:** Role, scenario, deliverable format, absolute dates, multi-source synthesis. Critically, **what the prompt doesn't tell the model**: it doesn't give the NPV formula, doesn't tell the analyst which tax treatment to pick (says "accelerate deduction most" — the model still has to identify the qualifying treatments and pick), doesn't lay out the year-by-year cash-flow construction, doesn't break the workbook into pre-decided components. Discount rate and tax rate are scope parameters; the methodology that uses them is left to the model. That's the right kind of detail.

### Passing Example 2 — Luxury Concierge

Itinerary with multi-file synthesis.

<details>
<summary>Show prompt</summary>

> You are a luxury concierge based in the Netherlands. A long-time client (originally from the American Midwest) has asked you to plan a show-stopping itinerary for her parents, who are flying out of Des Moines for their very first European trip. They're older, not super adventurous with food, never been out of the country, and they've never experienced anything close to VIP travel. Use only the attached venue database, guidelines, and photos to build out a full itinerary presentation that balances comfort with once-in-a-lifetime wow moments. The parents are Kelly M. and Jake B. They arrive in Amsterdam on Friday, April 10, 2026, at 14:30 local time, and depart Wednesday, April 16, at 10:00 local time. That gives you 6 days to work with. Create a PowerPoint presentation that contains the travel details from start to finish. Describe the overall trip philosophy, provide at least one slide for each day with all the days information, such as location, food, activities, backup plans, and transportation. Have a summary slide of dining options and why each works for them both. Create an experiences slide with tailored recommendations for each of them, including when it makes sense to split up and when to stay together. Have a slide for travel tips and never mention budget as the 'long time client' is paying for it all. Be sure to include an image on each slide to make it more fun. This will printed out and given to them for their trip ahead.

</details>

**Why it passes:** Rich scenario with personas, absolute dates and times, deliverable format. The slide types are described but the *content* of those slides is not — which venues to pick, which days to pair which experiences, what the "trip philosophy" is, when to split the parents up, what counts as a "wow moment" for these specific personas are all left to the model. Slide types are scope; the analytical work that fills them is not pre-decided.

### Passing Example 3 — Mechanical Engineer

Root cause analysis with data synthesis.

<details>
<summary>Show prompt</summary>

> You are a mechanical engineer, and is asked to research suspect equipment on the SMT line at a manufacturing plant. A recent 200 run of a circuit board from 12/15/2025 to 12/16/2025 has some failures. Review the provided memo (Alert_Memo.txt) and SMT_Controller_Board_Analysis.xlsx spreadsheet with input from the Cross Functional Team (CFT). The spreadsheet is laid out with the SMT Line tabs, each containing a report. The Tabs are: Paste, SPI, P&P, Reflow Oven, AOI, Xray, and Test. Create a technical report between 4-6 pages on the analysis and include your suggestions/recommendations in a word file. Include section on introduction, description of the problem, background information, your findings with root cause, recommendation, conclusion, and appendix. From the input file, analyze each tab, if the historical data is provided, especially in the SPI tab, include quality charts such as histograms, scatter plots and/or capability charts showing cp/cpk values with embedded charts in the technical report.

</details>

**Why it passes:** Domain-specific technical task, multi-source input, deliverable structure (Word report with named sections — these are standard report sections, not a pre-decomposition of the analysis). What the prompt doesn't tell the model: which tab will reveal the root cause, what the root cause is, which charts to actually generate (it suggests cp/cpk but doesn't dictate), what the recommendation should be, how to interpret the SPI data. The diagnostic work is fully left to the model.

### Failing Example 1 — Retail Supervisor (structural over-specification)

Sub-type 8a: deliverable shape pre-decided.

<details>
<summary>Show prompt</summary>

> You are a First-Line Supervisor of Retail Sales Workers for BrightMart Apparel, supervising floor execution, staffing coverage, and customer experience across three stores (BRM-101, BRM-102, BRM-103). Your job is to ensure the team is ready to execute a peak promotional weekend with strong service standards, correct merchandising, and disciplined labor spend. This task uses the workflow Peak Event Planning and Team Execution Alignment and includes Clienteling and Sales Outreach Enablement. Use only the attached files listed below. Do not use web search or any outside sources. [...] Create a workbook with exactly these sheets (spelled exactly): Inputs_SalesHistory, Inputs_Staff, Inputs_Customers, Inputs_Inventory, Event_Rules, Forecast_and_Labor, Staffing_Schedule, Task_Runbook, Inventory_and_Clienteling, QA_Checks [...] Forecast_and_Labor sheet requirements: Build a forecast and labor plan for the event dates stated in the event brief (Friday through Sunday). At minimum, calculate per store and per day: Baseline daily net sales using the last 8 weeks (use a day-of-week baseline: average of the last 8 occurrences of that weekday per store). Forecast daily net sales by applying the event uplift assumptions from Event_Rules. [...] QA_Checks sheet requirements: Create a clear pass/fail checklist (formula-driven) that verifies: Input row counts match the imported files (no dropped rows). Scheduled hours by store/day are within labor caps. Peak-hour coverage minimums are met for each store. [...]

</details>

**Why it fails:**

- **SOLUTION DISCLOSURE (8a):** Names every sheet, dictates the contents of each sheet, specifies the QA checks the model should include. The deliverable shape is fully pre-decided; the model does not have to choose what to build, only how to populate the named cells.
- **NATURALNESS:** Reads like a requirements specification, not a professional request. Workflow-naming preamble and contract-like section headers are not how people communicate.

### Failing Example 2 — Tesla ROIC (input over-specification)

Sub-type 8c: numerical inputs handed over inline.

<details>
<summary>Show prompt</summary>

> You are a corporate finance consultant working with an automotive sector strategy team. Your task is to evaluate whether Tesla generates returns above its cost of capital as margins tighten and capital intensity rises, using the company's 2021-2022 financial statements. Refer to the attached input file for the required balance sheet components. EBIT 2022: 13,656. Total Assets 2021: 62,131; 2022: 82,338. Effective tax rate: 8.3%. Base ROIC should reflect returns against average capital over the period. Stress scenario (2022): operating profit -20% and invested capital +15%. For the stress case, compute ROIC on 2022 results after applying the stress adjustments. WACC = 10%. Determine the stressed ROIC and conclude whether it remains above the cost of capital. Add a decomposition showing how much of the ROIC change is driven by profit compression versus capital expansion. Build a downloadable Excel workbook... Include a vertical column chart comparing Base ROIC and Stress ROIC. Overlay a horizontal WACC reference line.

</details>

**Why it fails:**

- **SOLUTION DISCLOSURE (8c):** Every numerical input the analysis turns on is given inline (EBIT, both years of total assets, tax rate, WACC, stress percentages). A model with no Tesla knowledge or industry context could complete the deliverable using only the prompt. The model is doing arithmetic, not analysis.
- **COMPLEXITY:** Multiple steps but each step is mechanical execution once the inputs are handed over. Step count is not difficulty.
- The prompt looks production-grade on the surface — role, scenario, named company, layered "constraints," chart spec. That's exactly the failure mode this check exists to catch.

### Failing Example 3 — DCF Model (component-walking)

Sub-type 8a + 8b: deliverable pre-decomposed AND methodology partially named.

<details>
<summary>Show prompt</summary>

> It's April 27, 2026, and you're an investment banking analyst on the consumer team. Your VP asked you to build a DCF for a consumer goods company. Only use the files attached, Company_Inputs.xlsx and DCF_Assumptions.xlsx... If an input required for a calculation is missing, leave the output that's affected blank and flag it in an Issues Log. Create 1 Excel file called Company_DCF.xlsx... 1. Valuation with different scenarios: Build a 5-year DCF (FY2026 to FY2030) under 3 cases, base, bear, and bull. Bear and bull cases have to come from the base case using the adjustment rows found in DCF_Assumptions.xlsx. 2. Operating model: Project out revenue, EBITDA, D&A, EBIT, tax, NOPAT, Capex, change in NWC, and UFCF. Use the drivers (like margin and percent of revenue drivers) found in DCF_Assumptions.xlsx. 3. WACC and terminal value: Calculate the WACC from the provided inputs (don't hardcode it). The terminal value will use the exit multiple method using the EV/EBITDA multiple. Include bear and bull case adjustments. 4. Equity value bridge: Show the bridge from enterprise value to equity value... 5. Sensitivity tables: Have a 5x5, 2-variable sensitivity table that shows the implied share price under various exit EV/EBITDA multiples and WACCs. Multiple steps will be the base multiple +/- 0.5x, and WACC will be the WACC +/- 0.5%. 6. Cross scenario checker... 7. Tie-outs... 8. Issues log...

</details>

**Why it fails:**

- **SOLUTION DISCLOSURE (8a):** The analysis is pre-decomposed into 8 numbered components — operating model line items, WACC + terminal value, equity bridge, sensitivity tables, cross-scenario checker, tie-outs, issues log. The model does not have to decide what the analysis consists of; it has been told.
- **SOLUTION DISCLOSURE (8b):** Methodology is partially named — "use the exit multiple method using the EV/EBITDA multiple," sensitivity ranges specified (WACC ±0.5%, multiple ±0.5x), bear/bull computed as additive deltas to base. The model has been told *how* to do the analysis.
- **Note on the trap:** A reviewer might argue that the model still has to know what UFCF is, how to construct WACC, how to discount terminal value — and therefore "still does real work." That is execution-correctness, not problem-solving. The prompt has done the analytical design; the model is filling in formulas. SOLUTION DISCLOSURE fails on disclosure, not on whether the resulting execution is trivial.

## Feedback Rules

- Only mention areas that need improvement. Do not list areas that are fine.
- Output as bullet pointers (one issue per pointer), not prose.
- Be brief. One pointer per real issue, no padding.
- Name the quality area that failed and describe the type of problem. For SOLUTION DISCLOSURE, name the sub-type (8a structural, 8b methodology, 8c input). You may quote sentences from the prompt to show what needs attention. Do NOT tell them how to fix it or suggest specific rewording.
- The goal is to point at the problem so the fellow finds and fixes it themselves. If your feedback could be copy-pasted as the fix, it is too specific.
- Do not over-engineer. Minor grammar issues, informal tone, or stylistic choices are fine if the prompt is substantively strong. The passing examples above have imperfect grammar and that is acceptable.
- Be constructive, not harsh.
- For passing prompts, output pointers describing what makes the prompt strong (one pointer per strength), so the fellow understands what to repeat.

## Verdict Logic

- `verdict = "passed"` if ALL 9 quality areas are strong
- `verdict = "failed"` if ANY quality area has issues

When in doubt on SOLUTION DISCLOSURE, fail. The cost of a false positive (flagging a good prompt) is recoverable — the annotator can review the feedback and push back. The cost of a false negative (passing a prompt that walks the solution) is a low-quality prompt entering the dataset.

## Output Format

Return ONLY valid JSON:

```json
{
  "verdict": "passed" | "failed",
  "reasoning": "<one sentence summary: what the prompt does well or what needs the most attention>",
  "feedback": [
    "<pointer 1>",
    "<pointer 2>",
    "<pointer 3>"
  ]
}
```
