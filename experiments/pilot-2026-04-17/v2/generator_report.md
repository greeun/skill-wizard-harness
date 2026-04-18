# Generator Report — Skill: tailored-resume-harness

## Files produced

All files present at specified paths under `/tmp/v1-v2-exp/v2/tailored-resume-harness/`:

- `SKILL.md` (280 lines) — orchestrator, 6 user-confirmation gates, numbered activation flow
  (11 steps), model-specific table, operational tuning loop, safety gates, no-compaction rule,
  V1-vs-V2 justification (Full tier chosen; V2 N/A with reason).
- `references/planner-prompt.md` (128 lines) — Planner subagent prompt; 5 hard rules verbatim
  from template with domain substitutions, spec.md output schema (sections 1–8), differentiation
  hook (rule 4), BLOCKED escape hatch.
- `references/generator-prompt.md` (153 lines) — Generator subagent prompt; 5 operating rules,
  content/strategy hybrid production process (steps a–e), REFINE/PIVOT/ESCALATE Strategic
  Decision block, 5 context-anxiety signals + résumé-specific tell, compaction forbidden.
- `references/evaluator-prompt.md` (179 lines) — Evaluator subagent prompt; adversary posture,
  self-evaluation bias warning, 9 adversarial probes (source traceability, company-swap,
  metric-invention, consultant-speak, filler, company-citation, length, ATS, fit-gap
  actionability), verdict logic, calibration rules, tuning note.
- `references/evaluator-calibration.md` (165 lines) — 12 score anchors (3 per criterion: 1/3/5)
  with concrete résumé-domain examples (Stripe, payment migration, p99 latency, SCA/PSD2).
- `references/rubric.md` (94 lines) — 4 criteria, 2× weighting justification, scoring scale,
  verdict logic with ETHICAL/SECURITY severity tags, 8 domain-specific invariants.
- `references/sprint-playbook.md` (185 lines) — Full-tier Sprint 1/2/3 instructions, contract
  templates, Evaluator per-sprint probe scoping, cross-sprint invariants, iteration handling.

## Spec mapping

| Spec section | Implemented in |
|-|-|
| §1 Domain & Purpose | SKILL.md intro + V1-vs-V2 rationale |
| §2 Metadata (description, directory) | SKILL.md frontmatter + file layout under references/ |
| §3 Rubric (4 criteria, 2× weighting) | references/rubric.md + evaluator-calibration.md |
| §4 Architecture (Full tier, 3 sprints, iter cap 10, file handoffs) | SKILL.md §Architecture + §File handoff contract + sprint-playbook.md |
| §5 Role customizations (Planner/Generator/Evaluator) | references/planner-prompt.md, generator-prompt.md, evaluator-prompt.md |
| §5 Few-shot calibration (3 anchors per criterion) | references/evaluator-calibration.md |
| §6 Orchestrator (11 steps, 6 gates A–F, safety gates) | SKILL.md §Activation flow + §Safety gates |
| §7 Article coverage (V1-1…V1-22, V2-1…V2-4 N/A, G-1…G-5, P-1…P-3) | Distributed across SKILL.md and references; V2 N/A with justification in §V1 vs V2 guidance; P-1 N/A sensory limits resolved in §Safety gates |
| §8 Domain invariants (source-index integrity, freshness, no-fab, length, ATS, language, PII, fit-gap referentiality) | references/rubric.md §Domain-specific invariants + sprint-playbook.md per-sprint checks |
| §9 Test scenarios | Scoped to orchestrator gates (thin-notes → Gate C, bilingual → Gate A, happy-path → full flow) |

Note on spec §2 directory: spec listed `article-principles.md` and `resume-formatting.md` as
optional auxiliary references, but the Generator's required-output list in this task excludes
them. The essential content from both is folded into `rubric.md` (principles) and
`sprint-playbook.md` / Evaluator probes (formatting). No information loss.

## Self-check

- [x] All files exist (7 files, all paths verified via `ls`).
- [x] SKILL.md body < 500 lines (actual: 280).
- [x] evaluator-calibration.md has ≥ 3 anchors per criterion (4 criteria × 3 anchors = 12
      total, all with concrete résumé-domain examples; Score 1/3/5 per criterion).
- [x] Tier implementation matches spec (Full: per-sprint contracts + per-sprint Evaluator
      + sprint-playbook.md shipped).
- [x] No placeholders (swept for `[DOMAIN]`, `[OUTPUT_TYPE]`, `[...]`, `TBD`, `FIXME`,
      `see spec`, `see intake` — zero hits).
- [x] Model-specific table present in SKILL.md (Sonnet 4.5 / Opus 4.5 / Opus 4.6 rows,
      verbatim from source wizard).
- [x] Operational tuning loop present — numbered (a)–(d) in SKILL.md §Evaluator tuning
      workflow (lines 194–208).
- [x] Sensory-limit gate: spec §4 declared sensory limits = NONE. SKILL.md §Safety gates
      explicitly states "Sensory limits: none" with justification that visual craft is
      LLM-verifiable via structural cues. No spurious human-checkpoint gate added.
- [x] Strategic Decision block (REFINE/PIVOT/ESCALATE) in generator-prompt.md with
      domain-specific interpretations.
- [x] File-based handoff contract documented in SKILL.md (8-row table of files + writers +
      readers).
- [x] V1 vs V2 guidance with explicit N/A justification for V2-1, V2-2 (not silent omission).
- [x] Context reset ≠ compaction stated explicitly (SKILL.md principle 4 and §Activation
      flow).

## Known limitations

- **No live test dispatch.** This Generator produced the skill files but did not run the
  three test scenarios from spec §9. Scenario validation is left to the Evaluator or a
  subsequent live run with a real applicant.
- **Article cross-check deferred.** Planner cited the coverage checklist (article-coverage-
  checklist.md) row-by-row in spec §7. The generated SKILL.md implements each row but does
  not inline every V1-n citation in the prose — citations are implicit in the flow structure
  (e.g., V1-1 is dispatched subagents, V1-22 is compaction ban). An audit subagent can
  walk the checklist against the generated files if coverage-fidelity verification is
  required.
- **Two optional reference files omitted.** `article-principles.md` and `resume-formatting.md`
  from spec §2 directory were not shipped — their content is folded into rubric.md and
  sprint-playbook.md / evaluator probes respectively. If the orchestrator or a future
  Evaluator expects them as standalone files, they would need to be extracted. No behavioral
  gap introduced.
- **Bilingual note within generator-prompt.md is thin.** The Planner prompt handles
  bilingual input explicitly; the Generator prompt treats language as a single
  spec.md-dictated parameter. Extended bilingual handling (proper-noun preservation heuristics,
  transliteration rules) is left to the Planner's spec.md §6 output.
- **Sprint 1 rubric scoring is partial by design.** sprint-playbook.md documents that C4 is
  N/A in Sprint 1 and C3 is partial — the Evaluator must apply the full rubric only in
  Sprint 2+. This is correct per spec but means the Evaluator prompt itself carries the
  full rubric; per-sprint scope comes from sprint-playbook.md. Consistency maintained.

READY_FOR_QA: /tmp/v1-v2-exp/v2/generator_report.md
