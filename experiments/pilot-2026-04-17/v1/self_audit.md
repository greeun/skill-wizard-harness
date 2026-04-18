# Self-Audit — v1 Tailored Résumé Harness

Auditor: the same session that planned and generated the skill (v1 wizard design).
Known failure mode: self-evaluation bias — the auditor sees their own prior output,
which predictably inflates pass rates. This audit is conducted anyway per the v1 flow.

Skill tier: **Full** (per intake.md §"Model & tier").

## V1 Architecture (Frontend Design Harness → mapped to our résumé domain)

| # | Row | Verdict | Location in generated skill |
|---|---|---|---|
| V1-1 | GAN-style generator/evaluator split (separate Agent calls) | PASS | `SKILL.md` §Activation flow — diagram shows `Agent(Planner) / Agent(Generator) / Agent(Evaluator)` as separate dispatched calls with file-only communication |
| V1-2 | 4 grading criteria, 1–5 scored | PASS | `references/rubric.md` §Criteria — 4 criteria, §Scoring guide shows 1–5 scale |
| V1-3 | 2× weighting on weak-by-default axes | PASS | `references/rubric.md` §Criteria — Evidence Quality 2×, Company-Specific Fit 2×; weight rationale column explains why; also reiterated in §"Why 2× on rows 1 and 2" |
| V1-4 | Criteria as qualities, not references | PASS | `references/rubric.md` §Style-magnet warning — explicit prohibition on brand names in criteria; criteria themselves use "evidence-backed", "company-specific" (qualities) |
| V1-5 | Few-shot calibration examples | PASS | `references/evaluator-calibration.md` — 2/5, 4/5, 5/5 anchors for all four criteria, domain-realistic; referenced from `evaluator-prompt.md` |
| V1-6 | Iteration range 5–15 per generation | PASS | `SKILL.md` §Iteration range — "Cap … at 5–15 iterations, not a single fixed value"; Dutch Art Museum iter-10 citation |
| V1-7 | Wall-clock tolerance (up to ~4h) | PASS | `SKILL.md` §Iteration wisdom bullet 1 — "wall-clock runs up to ~4 hours … and still considers that normal" |
| V1-8 | Evaluator tuning via few-shot examples | PASS | `references/evaluator-prompt.md` §Tuning note + `references/evaluator-calibration.md` + `SKILL.md` §Evaluator tuning workflow step c |
| V1-9 | Strategic decision REFINE/PIVOT/ESCALATE | PASS | `references/generator-prompt.md` §Direction-change rule — full REFINE / PIVOT / ESCALATE block with design_memo.md pattern for pivot |
| V1-10 | Dutch Art Museum late-leap wisdom | PASS | `SKILL.md` §Iteration range cites the iter-10 breakthrough; §Iteration wisdom bullet 1 elaborates |
| V1-11 | Middle iterations can beat final | PASS | `references/evaluator-prompt.md` §Iteration Quality Note; also `SKILL.md` §Iteration wisdom bullet 2 |

## V1 Architecture (Full-Stack Coding Harness → mapped to our résumé domain)

| # | Row | Verdict | Location |
|---|---|---|---|
| V1-12 | Planner 1–4 sentences → ambitious full spec | PASS | `references/planner-prompt.md` — reads `brief.md` (short request), produces 8-section spec; rule 4 says "Be AMBITIOUS about scope — the Generator should attempt the strongest possible résumé, not the safest" |
| V1-13 | Planner product-level, NOT implementation | PASS | `references/planner-prompt.md` rule 1 — "Stay at the PRODUCT level, not the implementation level … Do NOT prescribe exact sentence wording or bullet phrasing — the Generator owns those" |
| V1-14 | Planner weaves in domain-specific differentiation hooks | PASS | `references/planner-prompt.md` rule 4 — "Differentiation hook: a named signature evidence"; spec §6 requires a named signature evidence and expert-nuance hooks |
| V1-15 | Generator self-evaluates before handoff | PASS | `references/generator-prompt.md` rule 5 — "Never mark work complete until every check in the sprint contract passes when you verify it yourself"; report §"Verification I performed" |
| V1-16 | Evaluator adversarial probes + evidence | PASS | `references/evaluator-prompt.md` §"Adversarial probes (résumé domain)" — 7 domain-specific probes; §"Capture evidence" requires quoting bullets, citing line numbers, pasting URLs |
| V1-17 | Hard threshold per criterion | PASS | `references/rubric.md` §Verdict logic — "Any 2×-weighted < 4 → FAIL; Any 1×-weighted < 3 → FAIL"; also in `evaluator-prompt.md` §Verdict logic |
| V1-18 | Sprint contract negotiation before code | PASS | `references/generator-prompt.md` rule 2 (Generator writes sprint_contract.md and waits); `references/evaluator-prompt.md` workflow step 2 (Evaluator rejects weak contracts); `references/sprint-playbook.md` provides three sprint contract templates |
| V1-19 | File-based communication only | PASS | `SKILL.md` §File Handoff Contract — full table with 11 files, producer, consumer, purpose |
| V1-20 | Evaluator tuning loop across runs | PASS | `SKILL.md` §Evaluator tuning workflow — numbered (a)–(d) operational loop: read logs → identify divergence → update prompt/calibration → rerun |
| V1-21 | Context anxiety → handoff.md + reset | PASS | `references/generator-prompt.md` §"Context-anxiety signals" — 5 observable triggers, emits HANDOFF_NEEDED: handoff.md; hard rule "Do NOT use compaction — it preserves the anxiety state" |
| V1-22 | Context reset ≠ compaction | PASS | `SKILL.md` §Principles #3 — "Context anxiety ≠ context compaction … Compaction preserves the anxiety state and does not clear it" |

## V2 Architecture (tier = Full, so most rows are N/A)

| # | Row | Verdict | Location |
|---|---|---|---|
| V2-1 | Sprint construct removed (if Simplified) | N/A — tier=Full | Per intake.md, tier is Full because evidence quality is 2×-weighted and benefits from per-sprint pressure |
| V2-2 | Evaluator single end-of-run pass (if Simplified) | N/A — tier=Full | Full tier runs per-sprint Evaluator (3 sprints) |
| V2-3 | Evaluator cost is not fixed yes/no | PASS | `SKILL.md` §V1 vs V2 guidance table — row "Opus 4.5+ (simplified pick)" states "Evaluator cost is not a fixed yes/no — it depends on task-model boundary" |
| V2-4 | Context anxiety largely eliminated on Opus 4.5+ | PASS | `SKILL.md` §V1 vs V2 guidance table — row "Opus 4.5" states "Opus 4.5 has largely eliminated context anxiety" |

## General Principles

| # | Row | Verdict | Location |
|---|---|---|---|
| G-1 | Every harness component encodes an assumption | PASS | `SKILL.md` §Principles #1 — explicit: sprint contract assumes verification worth overhead, Evaluator assumes self-praise, ledger assumes claim drift; "remove these components one at a time and measure" |
| G-2 | Radical simplification failed → methodical one-at-a-time | PASS | `SKILL.md` §Principles #1 — "radical simplification failed — methodical one-at-a-time removal is required"; also in `references/sprint-playbook.md` §"When to remove sprints" |
| G-3 | Building Effective Agents simplicity principle citation | PASS | `SKILL.md` §Principles #2 — quoted verbatim with source attribution: *"find the simplest solution possible, and only increase complexity when needed"* (Building Effective Agents) |
| G-4 | Experiment with target model; read traces (operational loop) | PASS | `SKILL.md` §Evaluator tuning workflow — operational numbered (a)–(d) loop, not single sentence |
| G-5 | Harness space shifts, not shrinks, with model improvement | PASS | `SKILL.md` §Iteration wisdom bullet 3 — "Model capability shifts the harness space, it does not shrink it" |

## Gap-Patch Probes

| # | Row | Verdict | Location |
|---|---|---|---|
| P-1 | Sensory-limit human-checkpoint | N/A — intake §Sensory limits declares "None" | `SKILL.md` §"Sensory limits & human checkpoints" explicitly declares no limits and states "no human-checkpoint gate is required"; future-proofs by noting that adding PDF rendering or audio WOULD require a checkpoint. This is the correct behavior per P-1 wording: the checkpoint is required "if skill-spec.md §4 declares sensory limits". |
| P-2 | Planner ambition + differentiation hook | PASS | `references/planner-prompt.md` rule 4 — ambitious scope + named differentiation hook (signature evidence, expert nuance); spec §6 dedicated to these |
| P-3 | Operational tuning loop (numbered a–d) | PASS | `SKILL.md` §Evaluator tuning workflow — numbered a, b, c, d |

---

## Summary

- Total rows evaluated: 33
- PASS: 31
- N/A with justification: 3 (V2-1, V2-2 — tier=Full; P-1 — no sensory limits declared in intake)
- FAIL: 0

## Overall Verdict

**PASS**

Every checklist row is either mapped to a concrete line/section in the generated skill, or
marked N/A with justification tied to the intake.md-declared tier / sensory-limit settings.
No silent omissions.

## Self-evaluation bias disclaimer

This audit was produced by the same session that designed and generated the skill (v1
wizard flow). The v1 design is known to inflate pass rates relative to an independent
auditor. An independent Evaluator agent would likely probe harder on:
- Whether the "product-level vs implementation-level" line in `planner-prompt.md` is
  tight enough given §3 specifies "Voice & tone intent" as qualities.
- Whether the sprint 2 ledger schema is actually specific enough to catch drift.
- Whether 11 files in the handoff contract is excessive (simplicity principle).

These are exactly the kinds of issues a v2 meta-harness would surface because its Evaluator
never saw the Generator's working notes. This audit is therefore the upper bound on v1
self-assessment, not a calibrated verdict.
