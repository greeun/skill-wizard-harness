# Pilot Follow-up Experiment — v1 vs v2 (2026-04-18)

Single-domain (résumé-writing), n=1 blind-audit with the methodological fix from pilot-1:
**generators received only the article URL, not `article-coverage-checklist.md`**.

## Question
Does v2's structural role separation reduce article-element omissions when the test
actually measures article memory (not list-copying)?

## Method
- Shared `intake.md` given to both conditions.
- v1: one `general-purpose` agent played Planner + Generator + Evaluator sequentially.
  Forbidden from reading checklist.md, rubric.md, or any pre-made role templates.
- v2: three separate `general-purpose` agents dispatched sequentially; same restriction.
- Both had WebFetch access to the article URL.
- Ground-truth auditor (fourth agent) scored both outputs BLIND to each condition's
  self-assessment, using the checklist (which only the auditor had access to).
- Auditor also scored rubric quality (1–5 per criterion) to capture depth.

## Result (see `ground-truth/comparison.md` for full detail)
- v1 MISSING: 10 / 34 rows
- v2 MISSING: 6 / 34 rows
- Rubric weighted total (/30): v1 = 24, v2 = 28
- Adversarial probe failures: v1 = 6, v2 = 3
- **Hypothesis REPRODUCED in this fairer test.**

## Which elements were missed — the qualitative insight

Both conditions equivalently forgot **article anecdotes** (Dutch Art Museum at iter 10,
wall-clock tolerance ~4h, iteration range 5–15, "Building Effective Agents" quote,
"radical simplification failed" narrative). Neither generator recalled narrative trivia
from the article without a checklist prompt.

v2 outperformed v1 on **architectural mechanisms**:
- Strategic Decision block (REFINE/PIVOT/ESCALATE/REDIRECT) — v2 PASS, v1 MISSING
- Model-specific guidance table (Sonnet 4.5 / Opus 4.5 / Opus 4.6) — v2 PASS, v1 MISSING
- Operational Evaluator-tuning loop (a)–(d) numbered — v2 PASS, v1 MISSING
- Evaluator-cost task-model-boundary language — v2 PASS, v1 MISSING

Interpretation: the **spec-first workflow** in v2 (Planner writes a frozen spec before
Generator starts) forces explicit enumeration of harness components. The single-session
v1 drifts into "just generate the skill" mode and loses structural recall.

v2 also had a tighter rubric on the 2× weak-by-default axes (Evidence Quality,
Company-Specific Fit): 5/5 vs 4/5 each. The rubric-quality gap (+4 weighted points) is
precisely where the harness is supposed to add value per the article.

## Caveat on v2's V1-15 MISSING

v2 explicitly FORBADE Generator self-evaluation (anti-cheerleader rule) and moved all
judgment to Evaluator. This was scored MISSING against the article's V1-15 ("Generator
self-evaluate before handoff"), but is arguably a stronger interpretation of the article's
"separate the agent doing the work from the agent judging it" spirit. A follow-up audit
could reclassify this as a design enhancement, not a miss.

## Outputs preserved
- `intake.md` — shared input (same as pilot-1)
- `v1/resume-tailor-harness/` — v1-generated skill
- `v1/self_audit.md` — v1's self-generated coverage estimate (~42/48 from memory)
- `v2/skill-spec.md` — v2 Planner spec
- `v2/resume-harness/` — v2 Generator output
- `v2/generator_report.md` — v2 Generator self-report
- `ground-truth/comparison.md` — independent blind audit with rubric scoring

## Takeaways that shape SKILL.md
1. The original v2 value proposition holds **when measured correctly**: v2 recalls more
   article **mechanisms**, produces tighter rubrics, and exercises more adversarial probes.
2. Both conditions fail on article **anecdotes** — no amount of role separation fixes the
   failure to cite Dutch Art Museum or the "radical simplification" arc. Those require
   explicit reference materials.
3. The pilot-1 flat result (v1=0, v2=2 MISSING) was an artifact of giving both the
   checklist as input.
4. n=1 still, so treat this as directional confirmation, not statistical proof.
