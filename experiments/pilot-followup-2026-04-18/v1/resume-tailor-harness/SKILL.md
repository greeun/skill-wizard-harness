---
name: resume-tailor-harness
description: Produce company-tailored résumés (PDF-ready markdown) plus a fit-gap analysis using a Planner → Generator → Evaluator harness. Adapts Anthropic's "Harness Design for Long-Running Application Development" (Prithvi Rajasekaran, 2026) to the résumé-tailoring domain — role-separated subagents, file-based handoffs, sprint contracts, rubric scoring with weak-by-default axes doubled, context resets, and safeguards against self-evaluation bias and context anxiety. Use when the user wants a résumé aimed at a specific company/JD with traceable claims. Trigger phrases — EN "company-specific resume", "tailored resume with evaluator", "resume harness", "multi-agent resume", "tailored resume for <company>". KO "이력서 하네스", "맞춤 이력서", "회사 맞춤 이력서", "이력서 플래너 제너레이터", "이력서 자율 작성", "하네스로 이력서 작성".
---

# resume-tailor-harness

A Planner → Generator → Evaluator harness for building a résumé that is (a) evidence-traceable and (b) visibly tailored to one specific target company. Ported from Anthropic's harness-design article for long-running coding agents to the résumé domain.

## When to use

Invoke this skill whenever the user wants a résumé aimed at a **named** company/role and is willing to provide:

- Target company name (and optionally role/JD link or paste)
- Background notes (projects, roles, metrics, artifacts — however raw)

If any input is missing, the main session MUST ask for it before dispatch. Do not dispatch a subagent with missing inputs.

## What the skill produces

1. `resume.md` — 1–2 page, PDF-ready markdown résumé
2. `fit_gap.md` — fit-gap analysis with 2–4 dated/scoped action items
3. `evidence_map.md` — every résumé claim ↔ source line in applicant notes
4. `company_fit_map.md` — every company-specific phrasing ↔ cited public source

The `evidence_map.md` and `company_fit_map.md` are the traceability artefacts that make the evaluator's job possible — they are not optional output.

## Architecture

The main (orchestrator) session does **only** intake + dispatch + final assembly. All substantive work runs in subagents with fresh context. This mirrors the article's separation of "the agent doing the work from the agent judging it."

```
                ┌────────────────────────────┐
 user prompt ── │  MAIN SESSION (orchestrator)│  intake + dispatch only
                └──────────────┬─────────────┘
                               │ writes intake.md
                               ▼
                ┌────────────────────────────┐
                │  PLANNER subagent          │  fresh context
                │  → produces plan.md        │
                │    (sprint list + contract │
                │     criteria, no writing)  │
                └──────────────┬─────────────┘
                               │
             ┌─────────────────┼───────────────────┐
             ▼                 ▼                   ▼
     Sprint 1 (research)  Sprint 2 (evidence)  Sprint N (assembly)
     each sprint runs:
       GENERATOR subagent  → sprint_N_output.md + self_eval.md
       EVALUATOR subagent  → sprint_N_grade.md (pass/fail + feedback)
       loop on fail (max 3 iterations per sprint)
                               │
                               ▼
                ┌────────────────────────────┐
                │ MAIN SESSION assembles     │
                │ resume.md + fit_gap.md     │
                │ + maps from artefacts      │
                └────────────────────────────┘
```

Every arrow is a file handoff. No subagent ever reads another subagent's raw transcript — they read the persisted artefacts.

## Sprint decomposition (Full tier)

Per the article, sprints exist to bound context and give each role a negotiable "done" criterion. The résumé domain decomposes into:

| # | Sprint | Generator output | Evaluator focus |
|---|--------|------------------|-----------------|
| 1 | Company research | `company_brief.md` (priorities, values, tech stack, recent announcements — each with URL citation) | Are citations real/specific? Not generic boilerplate? |
| 2 | JD/role analysis | `role_spec.md` (required skills, implied skills, seniority signals) | Maps every requirement back to JD text |
| 3 | Applicant evidence extraction | `evidence_pool.md` (every claimable fact from notes, with source line numbers) | No invented facts; every fact has a source line |
| 4 | Fit-mapping | `fit_matrix.md` (role requirement × applicant evidence → match strength) | No false matches; gaps explicit |
| 5 | Résumé drafting | `resume.md` + `evidence_map.md` + `company_fit_map.md` | Rubric score — see `references/rubric.md` |
| 6 | Fit-gap analysis | `fit_gap.md` | ≥2 dated/scoped action items; gaps come from Sprint 4 |

Each sprint uses the sprint contract pattern in `references/sprint-playbook.md`.

## Main session workflow

### Step 1 — Intake (main session, no subagents)

Collect from the user:

- **target_company**: name, optional website/careers URL
- **target_role**: title + JD (paste or URL)
- **applicant_notes**: raw background (projects, roles, metrics, artefacts, links)
- **language**: output language (KO/EN — intake is bilingual per user context)
- **constraints**: page count preference (default 1–2), tone, any redactions

Write all of this to `./intake.md` in the working directory.

### Step 2 — Planner dispatch

Dispatch a subagent with the prompt in `references/planner-prompt.md`. Its inputs: `intake.md`. Its output: `plan.md` listing the six sprints above, each with:

- Sprint goal (one sentence)
- Generator contract (what file it must produce, what shape)
- Evaluator contract (pass/fail criteria, references to rubric rows)
- Allowed tools (WebFetch for research sprints; no Web access for sprints 3–6)

Main session reads `plan.md` and confirms sprint contracts look coherent. If not, re-dispatch planner with notes.

### Step 3 — Sprint loop (for each sprint 1..6)

For sprint `N`:

1. Dispatch **Generator subagent** with `references/generator-prompt.md` + `plan.md` + all prior sprint artefacts. Fresh context. It produces `sprint_N_output.md` and `sprint_N_self_eval.md`.
2. Dispatch **Evaluator subagent** with `references/evaluator-prompt.md` + `references/rubric.md` + `references/evaluator-calibration.md` + the sprint contract row from `plan.md` + `sprint_N_output.md`. **Fresh context.** It writes `sprint_N_grade.md` with PASS or FAIL, per-criterion score, and specific, file-located feedback.
3. If FAIL: re-dispatch Generator with the grade as input. Max 3 iterations per sprint. If still failing on iteration 3, main session surfaces the failure to the user and asks for input (more notes, relaxed constraint, etc.).
4. If PASS: advance to sprint N+1.

**Context reset** between every subagent dispatch is automatic — each subagent starts fresh. This is the article's preferred strategy over in-place compaction, especially for preventing context anxiety during the longer research and drafting sprints.

### Step 4 — Final assembly (main session)

After Sprint 6 passes, main session:

1. Copies `resume.md`, `fit_gap.md`, `evidence_map.md`, `company_fit_map.md` to the output directory.
2. Presents a summary: rubric scores, iteration counts, cost signals if available.
3. Offers one more optional human-review cycle.

## Files in this skill

- `SKILL.md` — this file (orchestrator playbook)
- `references/planner-prompt.md` — system prompt for Planner subagent
- `references/generator-prompt.md` — system prompt for Generator subagent
- `references/evaluator-prompt.md` — system prompt for Evaluator subagent
- `references/rubric.md` — scoring rubric with weighted axes
- `references/evaluator-calibration.md` — few-shot score examples to prevent drift + leniency
- `references/sprint-playbook.md` — sprint contract template and per-sprint specifications

## Non-negotiable rules (the load-bearing scaffolding)

These map directly to article anti-patterns. Do **not** remove unless you've tested that the model no longer needs them.

1. **Separate evaluator from generator.** The article: "Separating the agent doing the work from the agent judging it proves to be a strong lever." Never let the generator grade itself as the final word.
2. **File-based handoffs only.** Subagents do not share live context. They read and write files.
3. **Sprint contracts negotiated before writing.** Generator's first action in each sprint is to acknowledge the contract in its self-eval.
4. **Weak-by-default axes doubled in rubric.** Per the intake: Evidence quality and Company-specific fit are 2× weight. Evaluator must treat them as hard thresholds — any FAIL on these two axes fails the sprint.
5. **Context resets, not compaction.** Each subagent dispatch is fresh context. Do not summarize and continue.
6. **Evaluator skepticism.** Evaluator prompt is tuned to look for the article's "telltale AI patterns" — consultant-speak, vague metrics, template bullets.
7. **No speculative claims.** Every applicant statement traces to a source line. Every company-fit phrase traces to a cited source. The evaluator verifies both maps.

## Quality axes (from intake)

| Axis | Weight | Pass threshold |
|------|--------|----------------|
| Evidence quality | 2× | Every claim has a source line; no vague consultant-speak; every metric is concrete |
| Company-specific fit | 2× | Swap-test: changing the company name breaks the résumé's fit; every company phrase cited |
| Professional craft | 1× | 1–2 pages rendered; ATS-parseable; typographic/structural hierarchy present |
| Actionability | 1× | Fit-gap has ≥2 dated/scoped action items derived from Sprint 4 gaps |

Full weighting and calibration live in `references/rubric.md`.

## Failure surfacing

If a sprint fails on iteration 3, do NOT silently continue. Per the article, "shallow testing" and "feature stubbing" are real failure modes — the analog here is "résumé that reads plausible but has unsupported claims." Surface the failure honestly:

- Which axis failed
- What evidence was missing
- What the user could provide to unblock

The harness is designed to fail loudly rather than ship a polished-but-hollow résumé.
