---
name: tailored-resume-harness
description: Produce a company-tailored résumé (PDF-ready markdown) plus a fit-gap analysis using a Planner → Generator → Evaluator harness. Every applicant claim is traced to a source line in the background notes, and every company-specific phrasing is traced to the target company's public material. Use when the user wants a rigorous, evidence-backed résumé tailored to a specific company. Trigger phrases — EN: "company-specific resume", "tailored resume with evaluator", "resume harness". KO: "이력서 하네스", "맞춤 이력서".
---

# Tailored Résumé Harness

Operationalizes Anthropic's *"Harness Design for Long-Running Application Development"* (Prithvi Rajasekaran, 2026) for the résumé-writing domain. A Planner spec, a Generator draft, and an independent Evaluator critique run as separate Agent sessions with file-based handoffs.

## When to use

The user wants a résumé that (a) visibly reflects a specific target company's priorities and (b) sources every applicant claim to a concrete metric, project, or artifact. If the user wants a generic résumé, a single-pass chat is cheaper; use this harness only when evidence traceability and company-fit rigor matter.

Trigger phrases:
- EN: "company-specific resume", "tailored resume with evaluator", "resume harness"
- KO: "이력서 하네스", "맞춤 이력서"

## Inputs required (collect in main session, do NOT pass through to Planner conversation)

1. **Target company** — name + careers-page URL + (optional) recent announcement URL.
2. **Target role / JD** — pasted job description or JD URL.
3. **Applicant background notes** — freeform notes, project list, prior résumés, metrics.

Write these three inputs to `brief.md` before dispatching the Planner. The Planner reads `brief.md`; it does NOT see the main session.

## Activation flow (harness architecture)

The orchestrator (this skill, running in the main session) dispatches three SEPARATE `Agent` calls. Each subagent sees only the files named below — never the main session transcript and never the other subagents' reasoning.

```
main session
  │
  ├── Agent(Planner)   reads: brief.md
  │                    writes: spec.md
  │                    emits: SPEC_READY
  │
  ├── Agent(Generator) reads: spec.md, sprint_contract.md (after Evaluator approves)
  │                    writes: sprint_contract.md, resume.md, fit_gap.md,
  │                            generator_report.md, evidence_ledger.md,
  │                            company_brief.md
  │                    emits: READY_FOR_QA (or HANDOFF_NEEDED / DEADLOCK)
  │
  └── Agent(Evaluator) reads: spec.md, sprint_contract.md, generator_report.md,
                              resume.md, fit_gap.md, evidence_ledger.md,
                              company_brief.md, applicant background notes
                       writes: critique.md
                       emits: CRITIQUE_READY
```

Generator/Evaluator loop until PASS or the iteration cap is reached.

### Iteration range

Cap the Generator/Evaluator loop at **5–15 iterations**, not a single fixed value. The source article found that a Dutch Art Museum design breakthrough arrived at iteration 10 — capping at 5 would have missed it. The lower end of this range is the floor, not the target.

### Iteration wisdom

- Do not pick the low end of 5–15 just because iteration feels slow. The article reports wall-clock runs up to **~4 hours** for a single artifact and still considers that normal.
- **Middle iterations can beat the final.** If iteration 7 had a stronger evidence ledger than iteration 12, the Evaluator should say so explicitly in the Iteration Quality Note. Do not assume monotonic improvement.
- Model capability shifts the harness space, it does not shrink it. As models improve, some components (per-sprint Evaluator, sprint contracts) can be removed, but only one at a time — radical simplification has been shown to fail.

### File Handoff Contract

All cross-agent communication happens via files. No agent reads another agent's chain of thought.

| File | Producer | Consumer(s) | Purpose |
|---|---|---|---|
| `brief.md` | Main session | Planner | Raw inputs: company, JD, background notes |
| `spec.md` | Planner | Generator, Evaluator | Résumé spec: audience, structure, quality bar, Definition of Done |
| `sprint_contract.md` | Generator → Evaluator approves | Both | Per-sprint deliverables + observable checks |
| `company_brief.md` | Generator | Evaluator | Target company's priorities/values/stack with source URLs |
| `evidence_ledger.md` | Generator | Evaluator | Every résumé claim → source line in applicant background notes |
| `resume.md` | Generator | Evaluator | The tailored résumé (PDF-ready markdown) |
| `fit_gap.md` | Generator | Evaluator | Fit-gap analysis with 2–4 dated/scoped action items |
| `generator_report.md` | Generator | Evaluator | Strategic Decision (REFINE/PIVOT/ESCALATE) + self-verification |
| `critique.md` | Evaluator | Generator (next sprint) | Verdict + rubric scores + blocking issues |
| `handoff.md` | Generator (on anxiety) | Next Generator session | Remaining work, written cleanly before context fills |
| `design_memo.md` | Generator (on pivot) | Evaluator | PIVOT justification, awaits approval |

## Sprint plan (Full tier)

Three sprints, each independently contracted and evaluated. See `references/sprint-playbook.md` for details.

1. **Sprint 1 — Company Brief**: Generator produces `company_brief.md` from the company's public material. Evaluator verifies source URLs resolve and claims are not fabricated.
2. **Sprint 2 — Evidence Ledger**: Generator produces `evidence_ledger.md` — a table of every résumé claim candidate mapped to a specific line in the applicant background notes. Evaluator rejects claims whose source line does not actually support them.
3. **Sprint 3 — Résumé + Fit-Gap**: Generator produces `resume.md` and `fit_gap.md` drawing only from the approved ledger and brief. Evaluator runs the full rubric.

## Principles (copied from the source article)

1. **Every harness component encodes an assumption.** The sprint contract assumes per-sprint verification is worth its overhead; the Evaluator assumes the Generator will over-praise its own work; the ledger assumes applicants' claims drift from their notes. When you upgrade the underlying model, remove these components **one at a time** and measure. The article explicitly warns that radical simplification failed — methodical one-at-a-time removal is required.
2. **Find the simplest solution possible, and only increase complexity when needed** (*Building Effective Agents*). This skill runs Full tier because the evidence-traceability requirement justifies sprint contracts. If you were willing to weaken evidence quality, the Simplified tier would be defensible.
3. **Context anxiety ≠ context compaction.** If the Generator catches itself hedging ("at a high level", "for brevity") or losing section depth mid-document, it must emit `HANDOFF_NEEDED: handoff.md` and stop cleanly. **Compaction preserves the anxiety state and does not clear it.** A fresh Generator session reading `handoff.md` is the correct recovery.
4. **Experiment with the target model and read traces.** See §Evaluator tuning workflow below. Model upgrades shift the harness design space — they do not shrink it.

## V1 vs V2 guidance (model/tier selection)

| Model | Recommended tier | Why |
|---|---|---|
| Sonnet 4 / Opus 4 | Full (V1) | Sprint contracts meaningfully catch drift; per-sprint Evaluator catches self-evaluation bias. |
| Opus 4.5 | Full (V1) when evidence traceability is load-bearing; Simplified (V2) when not | Opus 4.5 has largely eliminated context anxiety, but 2× criteria (evidence quality, company fit) still benefit from per-sprint pressure. |
| Opus 4.5+ (simplified pick) | Simplified (V2) — single continuous Generator, one end-of-run Evaluator pass capped 3–5 rounds | Evaluator cost is not a fixed yes/no — it depends on task-model boundary. |

This skill-spec chose **Full** because evidence quality is a 2×-weighted axis; see `references/rubric.md`.

## Sensory limits & human checkpoints

The spec declares **no sensory limits**: the résumé is plain-text/markdown output and the fit-gap is a markdown document. All quality axes (typography hierarchy, section structure, length) are LLM-verifiable through structural cues. Therefore **no human-checkpoint gate is required** in the orchestrator. If a future version adds PDF rendering or audio, a human checkpoint MUST be added before final delivery.

## Evaluator tuning workflow (operational)

The article is explicit: an untuned Evaluator is too lenient. Treat the first runs as drafts. Run this numbered loop whenever a new domain, company, or applicant profile is introduced:

a. **Read the logs.** Open `critique.md` alongside `resume.md`, `fit_gap.md`, and the applicant background notes. For every rubric score, ask: *would a picky recruiter have scored this the same?*
b. **Identify divergence.** Typical failure patterns for this domain: accepting vague verbs ("led", "drove", "spearheaded") as adequate evidence; missing company-specific keywords the JD obviously demands; praising length compliance when content is thin.
c. **Update the Evaluator.** Edit `references/evaluator-prompt.md` and `references/evaluator-calibration.md` with concrete counter-examples targeting the divergence. Add a 2/5, 4/5, 5/5 anchor for the criterion that drifted.
d. **Rerun** the same brief end-to-end. Expect 3–5 calibration cycles before verdicts correlate with a picky human recruiter. Stop tuning when blocking issues the Evaluator raises are reproducible by a human.

## Outputs the user receives

On final PASS:
- `resume.md` — PDF-ready markdown résumé, 1–2 pages at standard font.
- `fit_gap.md` — fit-gap analysis with 2–4 concrete dated/scoped action items.
- `evidence_ledger.md` — appendix showing every claim traced to source.
- `critique.md` — the passing Evaluator verdict (for the user's audit trail).

## Reference files

- `references/planner-prompt.md` — Planner system prompt
- `references/generator-prompt.md` — Generator system prompt (includes Strategic Decision block)
- `references/evaluator-prompt.md` — Evaluator system prompt (includes adversarial probes, Iteration Quality Note)
- `references/evaluator-calibration.md` — few-shot 2/5, 4/5, 5/5 anchors per criterion
- `references/rubric.md` — 4 weighted criteria with verdict logic
- `references/sprint-playbook.md` — per-sprint contract templates (Full tier)
