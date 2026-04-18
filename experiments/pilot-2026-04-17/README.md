# Pilot Experiment — v1 vs v2 (2026-04-17)

Single-domain (résumé-writing), n=1 blind-audit comparison of the wizard's v1 (single-session
role play) vs v2 (meta-harness with 3 dispatched subagents).

## Question
Does v2's structural role separation reduce article-element omissions relative to v1?

## Method
- Shared `intake.md` given to both conditions.
- v1: one `general-purpose` agent played Planner + Generator + Evaluator sequentially.
- v2: three separate `general-purpose` agents dispatched per the real SKILL.md flow.
- Ground-truth auditor (fourth agent) scored both outputs BLIND to each condition's
  self-assessment, using `references/article-coverage-checklist.md`.

## Result (see `ground-truth/comparison.md` for full detail)
- v1 MISSING: 0 / 34 rows
- v2 MISSING: 2 / 34 rows (V2-3 "Evaluator cost not fixed yes/no", G-5 "harness space shifts not shrinks")
- Hypothesis NOT reproduced.

## Methodological limitation
Both conditions received `article-coverage-checklist.md` as input. The checklist lists every
article element explicitly, so the experiment tested "can the agent copy from a given list"
rather than "did the agent remember the article." The original v1 failure mode
(self-evaluation bias → quality drift) was not well stressed by this design.

A follow-up experiment with `experiments/pilot-followup-*` should:
- Give BOTH conditions only the article URL, not the checklist.
- Use the checklist only in the ground-truth audit.
- Score both coverage AND rubric quality (4 criteria × 1–5 scale) to capture depth.

## Outputs preserved
- `intake.md` — shared input
- `v1/tailored-resume-harness/` — v1-generated skill files
- `v1/self_audit.md` — v1's biased self-audit
- `v2/skill-spec.md` — v2 Planner output
- `v2/tailored-resume-harness/` — v2 Generator output
- `v2/generator_report.md` — v2 Generator self-report
- `ground-truth/comparison.md` — independent blind audit

## Takeaways that shaped SKILL.md
1. "v1 omits 5–7 elements" claim removed from SKILL.md:14–17 — not reproduced.
2. v2 value proposition reframed: "operational depth" (richer calibration, more probes,
   Opus 4.6 handling, PII/ETHICAL tags) rather than "coverage advantage."
3. Follow-up methodology documented as Test scenarios §2 TODO.
