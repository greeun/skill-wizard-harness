# Sprint playbook — resume-harness (Full tier)

> The full, sprint-by-sprint playbook for the orchestrator. SKILL.md
> summarizes the flow; this file provides the detailed contract
> templates, handoff files, and the Strategic Decision logic the
> orchestrator applies between iterations.

---

## Why three sprints (and not one, or six)

The article's V1-to-V2 simplification showed that for some domains
(simple frontend), collapsing sprints into a single run is possible
once the model improves. For this skill, we keep three sprints because:

1. **Evidence-traceability requires a sprint boundary.** If Research
   and Drafting happen in one context, the Generator will weave
   source-tagging into prose-writing and the Evaluator will have to
   disentangle two failure modes. Separating them means the Evaluator
   grades "is the fit-matrix good?" cleanly before any prose exists.

2. **Résumé and fit-gap are distinct artifact types.** Grading them
   in a single end-of-run evaluator pass would obscure which one
   failed. Per-sprint evaluation localizes the failure.

3. **Three sprints is the minimum that respects both constraints.**
   Four or more sprints would introduce file-handoff overhead without
   adding new artifact types. Two would collapse #1 or #2.

---

## Sprint 1 — Research & Fit Plan

### Inputs
- `intake.md` (orchestrator-written, contains target_company,
  target_role, background_notes)
- `sprint-1-contract.md` (Planner-written)

### Output
- `fit-matrix.md` (Generator-written)

### Contract template (Planner writes this)

```markdown
# Sprint 1 contract — Research & Fit Plan

## Goal
Produce a fit-matrix mapping {target_company} priorities to applicant
evidence lines from background_notes.

## Deliverable
`/tmp/resume-harness/fit-matrix.md` — a two-column markdown table:
| Company signal | Applicant evidence (line refs) |

## Acceptance criteria
1. ≥3 distinct company signals cited.
2. Every row cites ≥1 background-note line by explicit line number.
3. Signals inferred from training knowledge (not user input) are
   marked `(inferred)`.
4. Every cited background-note line exists in `intake.md`.
5. Gap rows (signal with no applicant evidence) are marked `(gap)`
   and will seed Sprint 3.

## Out of scope
No résumé prose. No fit-gap items.
```

### Evaluator checks (from evaluator-prompt.md §Sprint 1)
V1.1–V1.4. Plus rubric scoring of R1 and R2 (R3 N/A for this sprint,
R4 N/A until Sprint 3).

### Strategic Decision rules after Sprint 1 evaluation

| Condition | Decision | Action |
|-----------|----------|--------|
| Aggregate ≥ 80% AND all criteria ≥ 3 | **PASS** | Advance to Sprint 2 |
| One criterion at 2, trending up vs prior iteration | **REFINE** | Append Evaluator's revision asks to contract; re-dispatch Generator |
| Two criteria at 2 OR regressing vs prior iter | **PIVOT** | Instruct Generator to try a different signal set or a different slice of the background notes |
| 3 iterations exhausted | **ESCALATE** | Surface to user: either background notes are too thin, contract is wrong, or company is too much of a stretch |
| Evaluator flags fabricated signals OR invented line refs | **REDIRECT** | Write `/tmp/resume-harness/design_memo.md` documenting which anchor or check was insufficient; do not advance until calibration is updated |

---

## Sprint 2 — Résumé Draft

### Inputs
- `intake.md`
- `fit-matrix.md`
- `sprint-2-contract.md`
- `sprint-1-evaluation.md` (for any cross-sprint flags)

### Output
- `resume-draft.md` (tagged; tags stripped at final assembly)

### Contract template

```markdown
# Sprint 2 contract — Résumé Draft

## Goal
Produce a 450–900-word markdown résumé using only claims that appear
in `fit-matrix.md`.

## Deliverable
`/tmp/resume-harness/resume-draft.md`, markdown, ATS-safe.

## Acceptance criteria
1. Word count: 450–900 inclusive.
2. Every bullet has a `{src: background-note-L##}` tag resolving to a
   real `intake.md` line.
3. Every structural deviation from a generic résumé has a
   `{company: ...}` tag referencing a real fit-matrix row.
4. No invented credentials.
5. Swap test: ≥4 sentences would need rewriting if the target company
   name changed.

## Inputs available
`intake.md`, `fit-matrix.md`, `sprint-2-contract.md`, and (if it
exists) `sprint-1-evaluation.md`.

## Out of scope
Fit-gap items. Cover letter. LinkedIn version.
```

### Evaluator checks
V2.1–V2.6. Plus rubric scoring of R1, R2, R3.

### Strategic Decision rules after Sprint 2 evaluation

| Condition | Decision | Action |
|-----------|----------|--------|
| Aggregate ≥ 80% AND all criteria ≥ 3 | **PASS** | Advance to Sprint 3 |
| R1 at 2 (some uncited bullets) | **REFINE** | Generator tightens sourcing; can omit bullets rather than fabricate |
| R2 at 2 (swap test ≤3) | **REFINE** | Generator adds company-tagged structural choices; surfaces more fit-matrix rows |
| R3 at 2 (length / structural violation) | **REFINE** | Generator trims or restructures |
| Two criteria at 2 | **PIVOT** | Contract may be wrong; Generator proposes which constraint to relax in its next-iter handoff note |
| 3 iterations exhausted | **ESCALATE** | Surface to user with last evaluation |
| Evaluator flagged invented credential (V2.5) | **REDIRECT** | Immediate halt; write `design_memo.md` — this is a safety issue, not a quality issue |

### Why REFINE, PIVOT, ESCALATE, REDIRECT are separate

- **REFINE** — direction is right, execution needs sharpening. Small diff.
- **PIVOT** — direction may be wrong. Larger rewrite, potentially
  different slice of the fit-matrix.
- **ESCALATE** — automated iteration has plateaued. Human judgment
  needed (usually: "is the background material sufficient for this
  company?").
- **REDIRECT** — something is wrong upstream. Usually: the contract is
  unachievable, or the calibration let a bad output through. A
  `design_memo.md` is written documenting the issue for the operator
  to fix before re-running.

---

## Sprint 3 — Fit-Gap Analysis

### Inputs
- `fit-matrix.md`
- `sprint-3-contract.md`
- `sprint-2-evaluation.md` (only for cross-sprint flags)

Note: `resume-draft.md` is **not** re-read here. Sprint 3 addresses
gaps from the fit-matrix, not the draft. This enforces the
separation-of-concerns that keeps the Generator from retroactively
editing the résumé to patch what the fit-gap identifies.

### Output
- `fit-gap.md`

### Contract template

```markdown
# Sprint 3 contract — Fit-Gap Analysis

## Goal
Produce 2–4 dated, scoped action items addressing gaps visible in
`fit-matrix.md`.

## Deliverable
`/tmp/resume-harness/fit-gap.md`.

## Acceptance criteria
1. Exactly 2, 3, or 4 items.
2. Each item has an ISO date and an artifact-location scope.
3. Each item references a gap row from `fit-matrix.md` by row
   identifier.
4. Each item has a verifiable success condition.

## Out of scope
Revisions to `resume-draft.md`. Cover letter. New concerns not visible
in `fit-matrix.md`.
```

### Evaluator checks
V3.1–V3.5. Plus rubric scoring of R1 (implicit — items must be
evidence-respecting) and R4.

### Strategic Decision rules after Sprint 3 evaluation

| Condition | Decision | Action |
|-----------|----------|--------|
| Aggregate ≥ 80% AND all criteria ≥ 3 | **PASS** | Advance to final assembly |
| R4 at 2 (vague / undated items) | **REFINE** | Generator adds dates, scopes, success conditions |
| Item count wrong (1 or 5+) | **REFINE** | Generator adjusts |
| Item references a concern outside fit-matrix | **REFINE** | Generator rewrites that item to reference a real gap, or drops it |
| 3 iterations exhausted | **ESCALATE** | Surface to user |

---

## Final assembly (orchestrator, not subagent)

1. Read `resume-draft.md`.
2. Strip every `{src: ...}` and `{company: ...}` tag. Tags are
   enclosed in curly braces on the same line as the content; remove
   the whole tag including surrounding whitespace.
3. Write result to `/tmp/resume-harness/resume-final.md`.
4. Read `fit-gap.md`.
5. Concatenate `resume-final.md` + horizontal rule + `fit-gap.md`.
6. Prepend a one-paragraph summary:
   > "Sprint 1 passed on iteration N. Sprint 2 passed on iteration M.
   > Sprint 3 passed on iteration K. Final rubric scores: R1=a, R2=b,
   > R3=c, R4=d."
7. Return to user.

The orchestrator performs no LLM-dispatched work at final assembly —
it is a mechanical transformation. This keeps the three-agent
separation intact through the final step.

---

## design_memo.md format (only written on REDIRECT)

When the orchestrator decides REDIRECT, it writes
`/tmp/resume-harness/design_memo.md` with:

```markdown
# Design memo — <YYYY-MM-DD>

## Trigger
<Which evaluation and which check/criterion triggered REDIRECT>

## Observed failure
<What the artifact did that the check should have caught earlier,
quoted>

## Root cause hypothesis
- <contract too vague? calibration anchor missing? rubric threshold
  wrong?>

## Proposed fix
- <concrete edit to contract | rubric.md | evaluator-calibration.md>

## Outstanding
- <anything the operator must decide before re-running>
```

The orchestrator does **not** auto-apply the fix. A human operator
reads the memo, edits the calibration or contract, and re-triggers the
skill. This matches the article's tuning loop: "read the evaluator's
logs, find examples where its judgment diverged from mine, and update
the QAs prompt to solve for those issues."

---

## Iteration cap rationale

Max 3 iterations per sprint. The article cites 5–15 iterations for
frontend design, but:

- Résumé is a much lower-dimensional artifact than a frontend design.
  There are fewer axes along which to iterate usefully.
- Three iterations is already enough to catch the vast majority of
  "Generator first produces padded claims; Evaluator catches; Generator
  omits" cycles.
- Beyond 3, plateau is the usual outcome — the contract or calibration
  is wrong, and further Generator iterations won't help.

Therefore ESCALATE at 3 rather than burning tokens on iterations that
empirically do not improve scores.

---

## Context reset inventory

The skill's context resets are all-subagent-based:

- **Planner** runs in its own fresh subagent once, then exits.
- **Each Generator invocation** is a fresh subagent. No memory of
  prior iterations except what is in files.
- **Each Evaluator invocation** is a fresh subagent. No memory of
  prior grades except what is in `sprint-N-evaluation.md`.

This means **no compaction is needed** — each role gets a clean
slate by construction. Per the article: compaction "summarizes in
place so the same agent can keep going on a shortened history …
it doesn't give the agent a clean slate, which means context
anxiety can still persist." The subagent boundary gives the clean
slate.
