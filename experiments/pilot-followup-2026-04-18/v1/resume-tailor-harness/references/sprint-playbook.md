# Sprint playbook (Full tier)

This file defines the canonical six-sprint structure for the résumé-tailoring harness. The Planner reads this and instantiates it into `plan.md` for a specific intake. The Generator and Evaluator read it to understand the contract shape.

## Why sprint contracts exist

From the harness-design article: sprint contracts "bridge the gap between high-level user stories and testable implementation details without over-specifying technical approach upfront." Before any generation, Planner writes what "done" looks like for this sprint; Evaluator tests against that definition; Generator works to it.

The generator-proposes / evaluator-reviews negotiation happens implicitly via `plan.md` — the Planner drafts the contract, the Generator acknowledges it in its self-eval, the Evaluator grades against it.

## Sprint contract template

Every sprint in `plan.md` must have this shape:

```markdown
### Sprint <N> — <Name>

- **Goal** (one sentence): ...
- **Inputs** (files the generator reads): ...
- **Generator deliverable** (files the generator produces):
  - `sprint_<N>_output.md` (concrete name per sprint below)
  - `sprint_<N>_self_eval.md`
- **Evaluator pass criteria** (testable, specific):
  - Criterion 1 ...
  - Criterion 2 ...
  - ...
- **Allowed tools**: ...
- **Max iterations**: 3
- **Rubric axes applied** (if any): ...
- **Hard thresholds**: ...
```

## The six sprints

### Sprint 1 — Company research

- **Goal:** build a sourced profile of the target company's priorities, values, tech, and recent moves.
- **Inputs:** `intake.md`
- **Deliverable:** `company_brief.md` with sections:
  - Stated priorities (from about/careers pages)
  - Values / cultural signals
  - Tech stack (from engineering blog, job descriptions, or GitHub)
  - Recent announcements (≤12 months)
  - Hiring signals for this role (if JD gives hints)
  - Each bullet tagged `[source: URL]`
- **Evaluator criteria:**
  - Every fact has a URL
  - ≥3 distinct source domains
  - No generic filler
  - ≥2 recent announcements ≤12 months old (or explicit note that none found)
- **Tools:** WebFetch, WebSearch
- **Rubric:** not rubric-scored; criteria-checklist only

### Sprint 2 — Role / JD analysis

- **Goal:** decompose the JD into must-have, nice-to-have, implied skills, and seniority signals.
- **Inputs:** `intake.md`
- **Deliverable:** `role_spec.md` with a table:

  | Category | Skill / requirement | Source quote from JD |
  |----------|---------------------|----------------------|
  | Must-have | ... | ... |
  | Nice-to-have | ... | ... |
  | Implied | ... | ... |
  | Seniority signal | ... | ... |
- **Evaluator criteria:**
  - Every entry has a direct JD quote (or a note "implied, no direct quote")
  - Must-have count ≥3
  - No category is empty unless JD genuinely lacks signal for it
- **Tools:** none (no web access — use JD text from intake)
- **Rubric:** not rubric-scored; criteria-checklist only

### Sprint 3 — Evidence extraction

- **Goal:** parse applicant notes into an atomic, sourced evidence pool.
- **Inputs:** `intake.md`, `role_spec.md`
- **Deliverable:** `evidence_pool.md` — list of every claimable fact from notes, each with:
  - Fact statement (plain, no elevation)
  - Category (role / project / metric / artefact / skill)
  - `[notes: L<line>]` tag
  - `[CLARIFY]` tag if ambiguous
- **Evaluator criteria:**
  - Every entry has a `[notes: L<n>]` tag
  - No invented claims (Evaluator spot-checks ≥10 random entries against notes)
  - Claims not elevated ("built dashboard" not upgraded to "architected platform")
- **Tools:** none
- **Rubric:** not rubric-scored; criteria-checklist only

### Sprint 4 — Fit matrix

- **Goal:** map role requirements (Sprint 2) against applicant evidence (Sprint 3) to locate strengths and gaps.
- **Inputs:** `role_spec.md`, `evidence_pool.md`
- **Deliverable:** `fit_matrix.md` table:

  | Role requirement | Matching evidence (with source) | Match strength (strong/partial/gap) | Note |
  |------------------|----------------------------------|-------------------------------------|------|
- **Evaluator criteria:**
  - Every must-have from `role_spec.md` has a row
  - Every evidence cell cites an `evidence_pool.md` line or is marked "gap"
  - No inflated matches (Evaluator spot-checks: does the evidence actually satisfy the requirement?)
- **Tools:** none
- **Rubric:** not rubric-scored; criteria-checklist only

### Sprint 5 — Résumé draft (central sprint, full rubric applied)

- **Goal:** produce the PDF-ready résumé with full traceability.
- **Inputs:** `intake.md`, `company_brief.md`, `role_spec.md`, `evidence_pool.md`, `fit_matrix.md`
- **Deliverables:**
  - `resume.md` — the résumé
  - `evidence_map.md` — every applicant claim in `resume.md` → its `[notes: L<n>]` source (via `evidence_pool.md`)
  - `company_fit_map.md` — every company-specific phrase in `resume.md` → its `[source: URL]` (via `company_brief.md`)
- **Evaluator criteria (full rubric):** see `references/rubric.md`
- **Hard thresholds:**
  - Evidence quality ≥4
  - Company-specific fit ≥4
  - Page count 1–2 (rendered)
- **Tools:** none
- **Rubric:** yes — all four axes, Evidence and Company-fit at 2× weight

### Sprint 6 — Fit-gap analysis

- **Goal:** turn the gaps from Sprint 4 into 2–4 scoped action items.
- **Inputs:** `fit_matrix.md`, `intake.md`
- **Deliverable:** `fit_gap.md` with sections:
  - Strengths to lean on (from strong matches)
  - Gaps to address (from gap/partial matches)
  - Action items (2–4, each: verb + object + date + scope + explicit gap reference)
- **Evaluator criteria:** Actionability rubric axis + general readability
- **Hard thresholds:**
  - ≥2 dated/scoped action items
  - Every action item references a specific `fit_matrix.md` gap
- **Tools:** none
- **Rubric:** Actionability axis (1×) + Craft light check

## Iteration policy

- Max iterations per sprint: **3**.
- On iteration 2, Generator reads the prior grade and self-eval; on iteration 3, if still failing, main session surfaces the failure to the user.
- If the same axis fails across iterations with the same root cause, the generator should consider a strategic pivot (e.g., request re-running Sprint 3 for more evidence, rather than re-phrasing Sprint 5 bullets).

## Anti-patterns the playbook prevents

| Article anti-pattern | How this playbook prevents it |
|----------------------|-------------------------------|
| Over-scoping | Sprints 1–4 are research/analysis, not writing. Sprint 5 writes against prepared artefacts, not from raw user input. |
| Shallow testing | Evaluator's hard thresholds and swap test catch surface-plausible but unsupported résumés. |
| Feature stubbing | Sprint-contract "deliverables" list named files — missing files = instant FAIL. |
| Loss of coherence | Each sprint starts with fresh context and reads artefacts, not prior transcripts. |
| Context anxiety | Six bounded sprints; no single subagent runs long enough to hit perceived context limits. |
| Leniency bias | Evaluator is a separate subagent, with calibration anchors and the swap-test protocol. |
| Cascade errors from over-specified plans | Planner states WHAT the Generator must produce, not HOW to phrase bullets. |
