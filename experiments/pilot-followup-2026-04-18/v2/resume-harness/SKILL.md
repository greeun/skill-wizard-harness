---
name: resume-harness
description: Produce a target-company-tailored résumé plus fit-gap analysis using a Planner → Generator → Evaluator harness adapted from Anthropic's "Harness Design for Long-Running Application Development" (Rajasekaran, 2026). Sprint contracts enforce evidence traceability (every claim → source) and company-specific fit; an external Evaluator subagent grades against a weighted rubric with hard thresholds before approval, blocking the self-evaluation bias that makes generators praise their own prose. Triggers — EN "resume harness", "tailored resume with evaluator", "company-specific resume", "multi-agent resume", "resume harness skill", "planner generator evaluator resume", "job application harness", "resume with fit-gap analysis". KO "이력서 하네스", "맞춤 이력서", "회사 맞춤 이력서", "이력서 플래너 제너레이터", "하네스로 이력서 작성", "이력서 자율 작성", "이력서 멀티에이전트", "적합성 분석 이력서".
---

# resume-harness

A Planner → Generator → Evaluator harness that produces a **company-tailored
résumé** and a **fit-gap analysis** for a specific target role. The skill
operationalizes the article's core insight: generators asked to self-evaluate
will confidently praise mediocre work, so we separate the agent that writes
from the agent that judges, run them in fresh contexts via file handoffs, and
grade against a weighted rubric with hard thresholds.

This skill is **prompt-only**. No scripts. The orchestrator dispatches
subagents and moves files; it does no LLM work beyond intake validation and
final tag-stripping.

---

## 1. When to use this skill

Use when the user wants a résumé that is:

- Targeted at a **named** company and role (not generic)
- Backed by **evidence** — every bullet cited to a source line in their
  background notes
- Reviewed by an **independent evaluator** before being shown to the user
- Accompanied by a **fit-gap analysis** with 2–4 concrete, dated action items

Do **not** use for cover letters, LinkedIn profiles, PDF rendering, or
multi-company batch mode. See §9 Out of scope.

---

## 2. Inputs (required — all three)

| Input | Example |
|-------|---------|
| `target_company` | "Stripe" |
| `target_role` | "Staff Backend Engineer, Payments" |
| `background_notes` | A file or pasted block of dated projects, metrics, artifacts |

If any of the three is missing, **ask exactly once**, then stop. Do not
dispatch subagents with incomplete input.

---

## 3. Orchestrator activation flow

File handoffs live under `/tmp/resume-harness/`. Create the directory first.

### Step 1 — Intake validation
Confirm all three inputs are present. If background_notes is a path, read it
and inline it into `/tmp/resume-harness/intake.md`.

### Step 2 — Dispatch Planner subagent (once)
Invoke a fresh subagent with the system prompt in
`references/planner-prompt.md` and the intake file. Planner writes three
files before exiting:
- `/tmp/resume-harness/sprint-1-contract.md`
- `/tmp/resume-harness/sprint-2-contract.md`
- `/tmp/resume-harness/sprint-3-contract.md`

Each contract lists its acceptance criteria. Planner does **not** write
résumé prose; it freezes what "done" looks like for each sprint. The
Planner is not re-invoked.

### Step 3 — Sprint 1 loop (Research & Fit Plan)
Repeat until pass or 3 iterations:

a. **Dispatch Generator subagent** with `references/generator-prompt.md` +
   `sprint-1-contract.md` + `intake.md`. Generator writes
   `/tmp/resume-harness/fit-matrix.md`. Generator exits.
b. **Dispatch Evaluator subagent** with `references/evaluator-prompt.md` +
   `references/rubric.md` + `references/evaluator-calibration.md` +
   contract + fit-matrix. Evaluator writes
   `/tmp/resume-harness/sprint-1-evaluation.md`.
c. **Strategic Decision** (orchestrator reads evaluation):
   - Aggregate ≥ weighted threshold AND every criterion ≥ 3/5 → **PASS**,
     proceed to Step 4.
   - One criterion < 3/5 but trending up → **REFINE**: append evaluator's
     revision asks to `sprint-1-contract.md`, loop to (a).
   - Two criteria regressed across iterations → **PIVOT**: instruct
     Generator to try a different angle (e.g. different signal set).
   - 3 iterations exhausted → **ESCALATE**: surface failure to user with
     the evaluator's last report. Do not continue to Sprint 2.

### Step 4 — Sprint 2 loop (Résumé Draft)
Same loop as Step 3, substituting `sprint-2-contract.md` and producing
`/tmp/resume-harness/resume-draft.md`. Generator must write inline
`{src: ...}` and `{company: ...}` tags on every bullet / structural choice.
The Evaluator walks every tag and verifies each one.

**Context reset happens automatically**: each Generator and Evaluator
invocation is a fresh subagent, so the context window is clean by
construction. No compaction. (Per the article, "a reset provides a clean
slate" where compaction cannot.)

### Step 5 — Sprint 3 loop (Fit-Gap Analysis)
Same loop, producing `/tmp/resume-harness/fit-gap.md` with 2–4 dated,
scoped action items.

### Step 6 — Final assembly
Orchestrator (not an LLM call):
1. Strip all `{src: ...}` and `{company: ...}` tags from `resume-draft.md`
   → write `/tmp/resume-harness/resume-final.md`.
2. Concatenate `resume-final.md` + `fit-gap.md` and return to the user.
3. Include a one-paragraph summary: "Sprint 1 passed on iteration N;
   Sprint 2 passed on iteration M; Sprint 3 passed on iteration K. Final
   rubric scores: R1=…, R2=…, R3=…, R4=…".

---

## 4. Operational Evaluator-tuning loop

The article explicitly warns: **"Out of the box, Claude is a poor QA agent.
In early runs, I watched it identify legitimate issues, then talk itself
into deciding they weren't a big deal and approve the work anyway."**

Do not treat `evaluator-prompt.md` + `evaluator-calibration.md` as
finished. If a user (or you, on re-read) sees an approved résumé that
should have been caught, execute this loop:

**a. Read the evaluator's logs.** Open
   `/tmp/resume-harness/sprint-N-evaluation.md`. Locate the criterion
   score that diverges from your judgment.

**b. Diagnose the divergence.** Was it superficial testing (article:
   evaluators "tended to test superficially, rather than probing edge
   cases, so more subtle bugs often slipped through")? Was it a
   cheerleader approval — score given with hedged prose ("broadly fine",
   "could be tighter")?

**c. Update the evaluator artifacts.** Either (i) add a new score-1 or
   score-3 anchor in `evaluator-calibration.md` that captures the missed
   case, or (ii) sharpen `evaluator-prompt.md` with a specific check
   ("verify that the Skills section ordering differs from a generic
   template — otherwise R2 ≤ 2"). Per the article: "It took several
   rounds of this development loop before the evaluator was grading in
   a way that I found reasonable."

**d. Re-run the last sprint.** Do not re-run from scratch. Re-dispatch
   only the Evaluator subagent against the same draft to confirm the
   update now catches the issue. If it does, commit the calibration
   change. If it doesn't, repeat (a)–(c).

This loop is the reason the Evaluator files are separated into
`evaluator-prompt.md` (behavioral stance, rarely edited) and
`evaluator-calibration.md` (anchors + checks, frequently edited). Keep
diffs small.

---

## 5. The four rubric criteria (summary)

Full definitions + weights + hard thresholds live in `references/rubric.md`.
Score-1/3/5 anchors live in `references/evaluator-calibration.md`.

| # | Criterion | Weight | Why this weight |
|---|-----------|--------|-----------------|
| R1 | Evidence quality | **2×** | Weak by default — LLMs pad with unsupported claims |
| R2 | Company-specific fit | **2×** | Weak by default — LLMs produce swap-test-failing résumés |
| R3 | Professional craft | 1× | Strong by default — typography/ATS structure is easy |
| R4 | Actionability (fit-gap) | 1× | Strong by default once prompted, but must stay concrete |

The 2×/1× split follows the article's approach of weighting criteria
where the model scores poorly by default, not criteria that are
inherently more important. The hard threshold (any criterion < 3 fails
the sprint regardless of aggregate) prevents the known failure of
sacrificing one dimension to boost another — e.g. inventing impressive
claims to juice R2 at the cost of R1.

---

## 6. Three sprints (summary)

Full contracts are produced by the Planner and live in
`/tmp/resume-harness/sprint-N-contract.md`. The sprints are:

1. **Sprint 1 — Research & Fit Plan.** Generator produces a fit-matrix
   mapping company priorities → applicant evidence lines from background
   notes. No résumé prose yet. Output: `fit-matrix.md`.
2. **Sprint 2 — Résumé Draft.** Generator writes 450–900 words of markdown
   résumé using only fit-matrix-approved claims. Every bullet tagged.
   Output: `resume-draft.md`.
3. **Sprint 3 — Fit-Gap Analysis.** Generator writes 2–4 dated, scoped
   action items. Each item must address a gap visible in the fit-matrix
   (no new concerns invented). Output: `fit-gap.md`.

Each sprint has its own Evaluator pass. This is per-sprint evaluation,
not end-of-run. Chosen because each sprint produces a distinct artifact
type — a single end-of-run evaluator would obscure which artifact failed.

See `references/sprint-playbook.md` for the full sprint-by-sprint
playbook including deliverables, acceptance criteria, and example
contracts.

---

## 7. File layout produced by the skill

```
/tmp/resume-harness/
├── intake.md                    # Orchestrator writes
├── sprint-1-contract.md         # Planner writes
├── sprint-2-contract.md         # Planner writes
├── sprint-3-contract.md         # Planner writes
├── fit-matrix.md                # Generator Sprint 1
├── sprint-1-evaluation.md       # Evaluator Sprint 1
├── resume-draft.md              # Generator Sprint 2 (tagged)
├── sprint-2-evaluation.md       # Evaluator Sprint 2
├── fit-gap.md                   # Generator Sprint 3
├── sprint-3-evaluation.md       # Evaluator Sprint 3
└── resume-final.md              # Orchestrator (tags stripped)
```

The orchestrator never modifies files written by subagents except the
tag-stripping step 6.1.

---

## 8. Model guidance (as documented in the source article)

The article does not prescribe a model-per-role mapping. It documents
what the author used:

- **Initial harness:** Claude Opus 4.5 was "our best coding model when I
  began these experiments." All three roles ran on it.
- **Updated harness:** Claude Opus 4.6 "plans more carefully, sustains
  agentic tasks for longer, can operate more reliably in larger
  codebases, and has better code review and debugging skills." With Opus
  4.6, the article notes that "the boundary moved outward" — tasks that
  previously required evaluator oversight could be handled by the
  generator, and the sprint construct was successfully removed for
  simpler domains.
- **Sonnet 4.5 caveat:** "Claude Sonnet 4.5 exhibited context anxiety
  strongly enough that compaction alone wasn't sufficient to enable
  strong long task performance, so context resets became essential."

Practical translation for this skill:

- On **Opus 4.6 or later**: run all three roles on the same model. The
  fresh-subagent-per-sprint pattern already provides context reset, so
  the Sonnet 4.5 caveat does not bind.
- On **Opus 4.5**: same, but expect more iterations per sprint. Keep the
  3-iteration cap.
- On **Sonnet 4.5** (if that is what is available): the subagent
  boundaries are not optional — they are the article's prescribed
  remedy for this exact model's context anxiety. Do not attempt to
  collapse roles.

No claim is made about specialized-model-per-role because the article
makes no such claim. If a future user empirically finds (e.g.) Sonnet
4.5 is a better skeptic than Opus 4.6 for the evaluator, update this
section and `evaluator-prompt.md` accordingly.

---

## 9. Out of scope

- No cover-letter generation (separate skill if wanted)
- No LinkedIn-profile output
- No PDF rendering — output is markdown; user renders
- No real-time web scraping of the target company. Company signals come
  from the user's `background_notes` or the Generator's training
  knowledge with explicit `{company: ...}` tagging that the Evaluator
  verifies for plausibility (not ground truth — the Evaluator is not
  an oracle)
- No multi-company batch mode — one résumé per invocation
- No ATS scoring simulation — craft is judged structurally, not by
  calling out to an ATS tool

---

## 10. Quick-start checklist for the orchestrator

When triggered:

1. [ ] Confirm `target_company`, `target_role`, `background_notes` are all
       present. Ask once if not.
2. [ ] `mkdir -p /tmp/resume-harness && write /tmp/resume-harness/intake.md`
3. [ ] Dispatch Planner → 3 contract files.
4. [ ] Sprint 1 loop (Generator + Evaluator, max 3 iter, Strategic
       Decision per iter). Artifact: `fit-matrix.md`.
5. [ ] Sprint 2 loop. Artifact: `resume-draft.md`.
6. [ ] Sprint 3 loop. Artifact: `fit-gap.md`.
7. [ ] Strip tags → `resume-final.md`.
8. [ ] Return résumé + fit-gap + one-paragraph iteration/score summary.

If anything fails 3 iterations, **stop and escalate** with the last
evaluator report. Do not silently ship unapproved work.
