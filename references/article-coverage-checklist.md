# Article Fidelity Coverage Checklist

The Evaluator subagent uses this checklist to audit a generated harness skill against
Anthropic's *"Harness Design for Long-Running Application Development"*
(Prithvi Rajasekaran, 2026).

Every row below is a concrete article requirement. Each row must be mapped to a specific
location in the generated skill. **Any empty row = silent omission = FAIL.**

---

## V1 Architecture (Frontend Design Harness)

| # | Article element | Required artifact in generated skill | Location |
|---|---|---|---|
| V1-1 | GAN-style generator/evaluator split | Planner, Generator, Evaluator are dispatched as **separate `Agent` calls** from the orchestrator | SKILL.md activation flow |
| V1-2 | 4 grading criteria, 1–5 scored | Rubric file has 4–6 criteria, each scored 1–5 | references/rubric.md |
| V1-3 | 2× weighting on weak-by-default axes | Each criterion has explicit weight + rationale; 2× applied to the domain's weakest axes for Claude | references/rubric.md |
| V1-4 | Criteria as qualities, not references | No "museum-quality" / "Apple-like" / brand references in criteria | references/rubric.md |
| V1-5 | Few-shot calibration examples | `evaluator-calibration.md` (or equivalent) with score 1/3/5 anchors per criterion, domain-realistic | Evaluator prompt + separate file |
| V1-6 | Iteration range 5–15 per generation | Orchestrator cap is documented as a **range**, not single value; warning against picking low end (Dutch Art Museum leap at iter 10) | SKILL.md activation flow |
| V1-7 | Wall-clock tolerance (up to ~4h) | Not an automatic failure — but skill should not artificially rush | Iteration wisdom |
| V1-8 | Evaluator tuning via few-shot examples | References describe domain-specific few-shot templates | references/evaluator-prompt-template.md §Tuning |
| V1-9 | Strategic decision refine-vs-pivot after each evaluation | Generator prompt has REFINE/PIVOT/ESCALATE block OR REDIRECT+design_memo.md pattern | references/generator-prompt-template.md |
| V1-10 | Dutch Art Museum late-leap wisdom | "Iteration wisdom" section warns against tight caps, cites iteration 10 breakthrough | SKILL.md |
| V1-11 | Middle iterations can beat final | Evaluator prompt has Iteration Quality Note | references/evaluator-prompt-template.md |

## V1 Architecture (Full-Stack Coding Harness)

| # | Article element | Required artifact in generated skill | Location |
|---|---|---|---|
| V1-12 | Planner: 1–4 sentence prompt → full spec, ambitious scope | Planner prompt states "be ambitious", produces comprehensive spec.md | references/planner-prompt-template.md |
| V1-13 | Planner: product/high-level, NOT implementation details | Planner prompt has "stay at product level" hard rule | references/planner-prompt-template.md |
| V1-14 | Planner: weave in AI/domain-specific features | Planner prompt requires differentiation hooks section | references/planner-prompt-template.md |
| V1-15 | Generator: self-evaluate before handoff | Generator prompt requires verifying each acceptance criterion before READY_FOR_QA | references/generator-prompt-template.md |
| V1-16 | Evaluator: adversarial probes + evidence | Evaluator prompt has domain-specific adversarial probes + requires file/URL citations | references/evaluator-prompt-template.md |
| V1-17 | Hard threshold per criterion | Rubric verdict logic: "Any 2× < 4 → FAIL; Any 1× < 3 → FAIL" | references/rubric.md |
| V1-18 | Sprint contract negotiation before code | Generator prompt Mode 1 (contract proposal) + Evaluator prompt Mode 1 (contract review) | Both role prompts |
| V1-19 | File-based communication only | File Handoff Contract section complete (spec.md, sprint_contract.md, generator_report.md, critique.md, handoff.md) | SKILL.md |
| V1-20 | Evaluator tuning loop across runs | Documented operational section: read logs → find divergence → update prompt/calibration → rerun | SKILL.md |
| V1-21 | Context anxiety → handoff.md + reset | Generator prompt has anti-pattern: "write handoff.md on context pressure; don't compact" | references/generator-prompt-template.md |
| V1-22 | Context reset ≠ compaction | Explicit "compaction doesn't clear context anxiety" warning | SKILL.md principles |

## V2 Architecture (Opus 4.5/4.6 simplification)

| # | Article element | Required artifact in generated skill | Location |
|---|---|---|---|
| V2-1 | Sprint construct removed entirely (when tier = Simplified) | If Simplified tier chosen: NO per-sprint Evaluator, NO sprint_contract.md negotiation, single continuous Generator session | SKILL.md orchestrator |
| V2-2 | Evaluator moved to single end-of-run pass (V2) | Simplified orchestrator has 1 Evaluator pass total, capped at 3–5 rounds | SKILL.md orchestrator |
| V2-3 | Evaluator cost is not fixed yes/no | Model-specific guidance acknowledges evaluator value depends on task-model boundary | SKILL.md V1 vs V2 guidance |
| V2-4 | Context anxiety largely eliminated on Opus 4.5+ | Model-specific guidance table copied into skill | SKILL.md V1 vs V2 guidance |

## General Principles

| # | Article element | Required artifact in generated skill | Location |
|---|---|---|---|
| G-1 | Every harness component encodes an assumption | Explicit principle + "remove one at a time when model upgrades" guidance | SKILL.md principles |
| G-2 | Radical simplification failed → methodical one-at-a-time | Documented in V1 vs V2 section | SKILL.md |
| G-3 | "Building Effective Agents" simplicity principle | Citation: *"find the simplest solution possible, and only increase complexity when needed"* | SKILL.md |
| G-4 | Experiment with the target model; read traces | **Operational numbered loop** (read logs → divergence → update → rerun), not mere acknowledgement | SKILL.md §Evaluator tuning workflow |
| G-5 | Harness space shifts, not shrinks, with model improvement | Documented in iteration wisdom or closing guidance | SKILL.md |

## Gap-Patch Probes (added post-audit)

| # | Article element | Required artifact in generated skill | Location |
|---|---|---|---|
| P-1 | Sensory-limit human-checkpoint | If skill-spec.md §4 declares sensory limits, SKILL.md orchestrator has an explicit human-checkpoint gate at the relevant stage | SKILL.md orchestrator + evaluator-prompt.md |
| P-2 | Planner ambition + differentiation hook | Generated planner-prompt.md explicitly instructs "be ambitious about scope" AND includes a domain-specific differentiation hook (AI features, expert nuance, etc.) | references/planner-prompt.md |
| P-3 | Operational tuning loop | Generated SKILL.md has a numbered (a)–(d) tuning procedure, not a single-sentence acknowledgement | SKILL.md §Evaluator tuning workflow |

---

## Coverage verdict rule

**PASS**: every row mapped to a concrete line/section. No "handled implicitly" excuses.
**FAIL**: one or more rows empty, or mapping points to a section that doesn't actually contain the element.

If the chosen tier makes a row inapplicable (e.g., V2-1 when tier=Full), the Evaluator must write
"N/A — tier=X" with justification. Silent omission (blank) is not acceptable.
