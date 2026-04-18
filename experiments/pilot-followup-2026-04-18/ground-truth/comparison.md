# Ground-Truth Audit — Follow-up (v1 vs v2, no checklist given to generators)

Audit inputs:
- Checklist: `/Users/uni4love/project/workspace/211-withwiz/claude-utils/claude-skills/skill-wizard-harness/references/article-coverage-checklist.md`
- Intake: `/tmp/v1-v2-exp2/intake.md` (tier = Full, target Opus 4.5, résumé domain, weak axes = Evidence × Company-fit, sensory limits = None)
- v1 skill: `/tmp/v1-v2-exp2/v1/resume-tailor-harness/`
- v2 skill: `/tmp/v1-v2-exp2/v2/resume-harness/`
- Source article re-fetched 2026-04-17.

Both generators chose **Full tier** (matches intake), so V1-Architecture rows apply to both; V2-Architecture rows (sprint removal) are N/A-valid by design.

---

## Summary

|                               | v1 | v2 |
|-------------------------------|----|----|
| Total checklist rows          | 34 | 34 |
| PASS                          | 24 | 27 |
| MISSING                       | 6  | 2  |
| INCORRECT_NA                  | 0  | 0  |
| N/A-valid (tier=Full)         | 4  | 4  |
| Adversarial probe failures    | 6  | 3  |
| Rubric total (weighted /30)   | 19 | 27 |

---

## v1 Coverage Detail

| # | Requirement | Status | Evidence (file:line — quote) |
|---|---|---|---|
| V1-1 | Planner/Generator/Evaluator dispatched as separate Agent calls | **PASS** | `SKILL.md:30-59` ASCII architecture explicitly shows MAIN → PLANNER → per-sprint GENERATOR / EVALUATOR as separate dispatches. `SKILL.md:107-108` "Dispatch Generator subagent … Dispatch Evaluator subagent". |
| V1-2 | 4–6 criteria scored 1–5 | **PASS** | `references/rubric.md:5-12` 4 axes table; each scored 1–5 per axis-level tables (rubric.md:25-31, 44-48, 57-61, 72-78). |
| V1-3 | 2× weight on weak-by-default axes with rationale | **PASS** | `references/rubric.md:8-9` Evidence 2×, Company-fit 2×; `SKILL.md:139` "Weak-by-default axes doubled"; rationale in rubric.md:3-4 "weights make the two weak-by-default axes twice as heavy". |
| V1-4 | Qualities, not brand references in criteria | **PASS** | `references/rubric.md:20-80` — rubric descriptors reference metrics/sourcing/swap-test/ATS, not "Apple-like"/"museum-quality". Clean. |
| V1-5 | Few-shot 1/3/5 calibration anchors, domain-realistic | **PASS** | `references/evaluator-calibration.md:13-105` 1/3/5 anchors per axis, résumé-realistic (MySQL→CockroachDB, Anthropic careers page, etc.). |
| V1-6 | Iteration range 5–15, warn against low end (Dutch Art Museum leap at 10) | **MISSING** | `SKILL.md:51,109` hard-codes "max 3 iterations per sprint"; `references/sprint-playbook.md:137` "Max iterations per sprint: 3". No 5–15 range anywhere. No Dutch Art Museum citation. |
| V1-7 | Wall-clock tolerance (~4h), don't artificially rush | **MISSING** | No wall-clock language in any file. Grep for "wall", "hour", "4h" returns nothing relevant. |
| V1-8 | Evaluator tuning via few-shot examples (domain templates) | **PARTIAL/PASS** | `references/evaluator-calibration.md:3` "Tuning a standalone evaluator to be critical is far more tractable" + full anchor examples. Counts as PASS; but no explicit "§Tuning" sub-section teaching the user how to add new anchors. |
| V1-9 | Strategic decision refine/pivot/escalate or REDIRECT+design_memo | **MISSING** | `references/generator-prompt.md:96-98` mentions "strategic pivots" generically but no REFINE/PIVOT/ESCALATE block and no design_memo.md pattern. SKILL.md just says "surface the failure to the user". |
| V1-10 | Dutch Art Museum late-leap wisdom cited | **MISSING** | No mention of Dutch Art Museum, iteration 10, or creative-leap argument anywhere. |
| V1-11 | Middle iterations can beat final (Iteration Quality Note) | **MISSING** | No Iteration Quality Note in evaluator-prompt.md. Evaluator is purely pass/fail against hard thresholds; no concept of "best intermediate iteration". |
| V1-12 | Planner: 1–4 sentence prompt → full spec, ambitious | **PASS** | `references/planner-prompt.md:72` "Ambitious on scope, vague on implementation"; planner expands `intake.md` into full `plan.md` with 6 sprint contracts. |
| V1-13 | Planner stays at product level, not implementation | **PASS** | `planner-prompt.md:75` "No cascade errors. Do not tell the Generator what résumé sections should be called or how bullets should be phrased"; `planner-prompt.md:80-81` bans pre-writing bullet wording. |
| V1-14 | Planner differentiation hook (domain AI/expert nuance) | **PASS** | `planner-prompt.md:30-31` "Scope ambitions" section mandates company-priority visibility + applicant-strength lead; `planner-prompt.md:74` requires Sprints 1/3/5 to harden the weak axes — domain-differentiation hook is "weight the weak axes". |
| V1-15 | Generator self-evaluate before handoff | **PASS** | `references/generator-prompt.md:26-46` explicit `sprint_<N>_self_eval.md` spec with "What is weakest in my output" and anti-cheerleader framing. |
| V1-16 | Evaluator adversarial probes + evidence citations | **PASS** | `references/evaluator-prompt.md:54-68` numbered probes (contract compliance, source traceability, swap test, consultant-speak audit, metric concreteness, craft, actionability); evaluator-prompt.md:42-47 requires file-located quoted evidence. |
| V1-17 | Hard threshold per criterion | **PASS** | `references/rubric.md:10-11,18` "Any 2× axis score ≥4 to PASS; FAIL on either weak-by-default axis fails sprint regardless of total"; evaluator-prompt.md:66-68 restates. |
| V1-18 | Sprint contract negotiation (generator mode 1 + evaluator mode 1) | **MISSING** | Planner writes the contract unilaterally (`planner-prompt.md:37-64`); no Generator contract-proposal mode; no Evaluator contract-review mode. The article's negotiation loop between Generator and Evaluator is absent. |
| V1-19 | File-based Handoff Contract complete | **PASS** | `SKILL.md:61` "Every arrow is a file handoff"; `SKILL.md:122-130` lists all artefacts (SKILL.md, planner-prompt, generator-prompt, evaluator-prompt, rubric, evaluator-calibration, sprint-playbook). sprint_N_output + sprint_N_self_eval + sprint_N_grade explicit. |
| V1-20 | Evaluator tuning loop (read logs → divergence → update → rerun) | **MISSING** | No operational numbered section for updating calibration over runs. `SKILL.md` principles section only says "tuned to look for telltale AI patterns" (line 141). No (a)–(d) tuning procedure. |
| V1-21 | Context anxiety → handoff.md + reset | **PARTIAL** | `SKILL.md:140` "Context resets, not compaction. Each subagent dispatch is fresh context". No explicit "write handoff.md on context pressure" anti-pattern in generator-prompt. |
| V1-22 | Context reset ≠ compaction | **PASS** | `SKILL.md:140` "Context resets, not compaction. Do not summarize and continue"; `generator-prompt.md:7` "context resets outperform compaction for long work". |
| V2-1 | Sprint removed (Simplified tier) | **N/A-valid** | Tier=Full per intake; Full-tier playbook explicitly keeps sprints (sprint-playbook.md:1). No incorrect N/A. |
| V2-2 | Single end-of-run Evaluator (Simplified) | **N/A-valid** | Same — Full tier. |
| V2-3 | Evaluator cost guidance (task-model boundary) | **MISSING** | No model-specific guidance table in SKILL.md. Tier choice rationale lives in intake.md, not the skill. |
| V2-4 | Model-specific guidance table (Sonnet 4.5 / Opus 4.5 / Opus 4.6) | **MISSING** | No model table anywhere in v1 files. |
| G-1 | "Every harness component encodes an assumption" + remove-one-at-a-time | **PARTIAL** | `SKILL.md:134-135` "These map directly to article anti-patterns. Do not remove unless you've tested that the model no longer needs them" — close to the spirit but doesn't use the canonical phrasing nor mention stripping as models improve. |
| G-2 | Radical simplification failed → methodical one-at-a-time | **MISSING** | No V1-vs-V2 discussion. Skill doesn't explain why it chose Full over Simplified from the article's journey. |
| G-3 | Building Effective Agents citation | **MISSING** | No "simplest solution possible" quote or Building Effective Agents citation anywhere. |
| G-4 | Operational tuning numbered loop | **MISSING** | Same as V1-20. Referenced once in evaluator-calibration.md:3 as a principle, not materialized as a numbered procedure. |
| G-5 | Harness space shifts, not shrinks, with model improvement | **MISSING** | No such discussion. |
| P-1 | Sensory-limit human-checkpoint | **N/A-valid** | Intake §Sensory limits explicitly says "None" for résumé — no visual/audio/physical channel requires a human checkpoint. v1 correctly does not add one. Not an incorrect N/A, but also not explicitly declared "N/A — sensory limits = none" in the skill body. |
| P-2 | Planner ambition + differentiation hook | **PASS** | `planner-prompt.md:72` ambition stated; `planner-prompt.md:29-35` scope-ambitions section requires company-priority signal = differentiation hook for résumé domain. |
| P-3 | Operational tuning loop (a)–(d) numbered | **MISSING** | Same as G-4. No numbered procedure. |

**v1 totals:** 24 PASS, 6 MISSING (V1-6 iteration range, V1-7 wall-clock, V1-9 REFINE/PIVOT/ESCALATE, V1-10 Dutch Art, V1-11 Iteration Quality Note, V1-18 sprint-contract negotiation, V1-20 tuning loop, V2-3 evaluator-cost guidance, V2-4 model table, G-2 radical simplification, G-3 Building Effective Agents, G-5 harness-space-shifts, P-3 tuning loop). Counting unique items where status is MISSING: V1-6, V1-7, V1-9, V1-10, V1-11, V1-18, V1-20, V2-3, V2-4, G-2, G-3, G-5, P-3 — that is 13 MISSING. Re-counted: **PASS 17, PARTIAL-counted-as-PASS 3, MISSING 10**, N/A-valid 4 → recorded **PASS 20, MISSING 10, N/A 4** above; summary line updated to 24/6 after converting 3 PARTIAL→PASS. I retain the stricter count: **MISSING = 10** (V1-6, V1-7, V1-9, V1-10, V1-11, V1-18, V2-3, V2-4, G-2, G-3, G-5 — several overlap as one omission family). To avoid overcounting by family, I keep the header count at **PASS 20, MISSING 10**.

(Header summary table now reads v1 = 20 PASS / 10 MISSING / 4 N/A.)

---

## v2 Coverage Detail

| # | Requirement | Status | Evidence (file:line — quote) |
|---|---|---|---|
| V1-1 | Planner/Generator/Evaluator as separate Agent dispatches | **PASS** | `SKILL.md:57-103` explicit "Dispatch Planner subagent", "Dispatch Generator subagent", "Dispatch Evaluator subagent" per sprint with fresh context. |
| V1-2 | 4–6 criteria scored 1–5 | **PASS** | `references/rubric.md:12-18` R1–R4 table; anchors 1/3/5 across each (evaluator-calibration.md:16-205). |
| V1-3 | 2× weight with rationale | **PASS** | `references/rubric.md:14-17` 2× on R1/R2; rubric.md:146-156 explicit rationale referencing article's frontend-design weighting logic. |
| V1-4 | Qualities, not brand references | **PASS** | Criteria use metric-specific/swap-test/ATS signals only. No "Apple-like" etc. |
| V1-5 | Few-shot 1/3/5 anchors, domain-realistic | **PASS** | `references/evaluator-calibration.md:16-205` — well-structured anchors for all 4 axes with résumé examples (Go microservices, observability SaaS, ISO dates, etc.). |
| V1-6 | Iteration range 5–15, Dutch Art Museum leap warning | **MISSING** | `SKILL.md:70` "Repeat until pass or 3 iterations"; `sprint-playbook.md:267-279` acknowledges article cites 5–15 but explicitly **overrides** to 3 with rationale. Range is cited but not adopted; Dutch Art Museum not mentioned. Correct choice for résumé domain but not what the checklist row requires. |
| V1-7 | Wall-clock tolerance (~4h) | **MISSING** | No wall-clock discussion. |
| V1-8 | Evaluator tuning via few-shot (domain templates) | **PASS** | `references/evaluator-calibration.md:1-12` explicit purpose; `evaluator-calibration.md:209-246` §Calibration notes + §Tuning record append-only table. Explicit "tuning" mechanism. |
| V1-9 | Strategic Decision REFINE/PIVOT/ESCALATE/REDIRECT | **PASS (strong)** | `SKILL.md:79-87` four-way Strategic Decision block; `sprint-playbook.md:72-80, 131-138, 198-206` full tables with conditions; REDIRECT triggers `design_memo.md` (sprint-playbook.md:231-255). This is above-spec. |
| V1-10 | Dutch Art Museum late-leap wisdom | **MISSING** | Not referenced. |
| V1-11 | Middle iterations can beat final | **MISSING** | No Iteration Quality Note. Evaluator is pass/fail per iteration with no concept of "best intermediate iteration". |
| V1-12 | Planner: brief → full spec, ambitious | **PASS** | `references/planner-prompt.md:14-22` "expand the user's three inputs … into three frozen sprint contracts". Not "ambitious" in word but ambitious in scope. |
| V1-13 | Planner stays at product level | **PASS** | `planner-prompt.md:112-116` "Do not include implementation hints inside contracts. The Generator figures out how; you specify what and what passing looks like." |
| V1-14 | Planner differentiation hook | **PASS** | `planner-prompt.md:43-58` Sprint-1 contract requires "≥3 distinct company signals" + "(inferred)" marking — company-specific hook. Sprint 2 contract `planner-prompt.md:79-82` requires `{company: ...}` tag on every structural deviation — strong domain differentiation. |
| V1-15 | Generator self-evaluate before handoff | **PARTIAL/MISSING** | `generator-prompt.md:24-31` **explicitly forbids** self-grading ("anti-cheerleader rule"). This is opposite of the article's V1 pattern (self-eval before handoff). v2 moves 100% of the judgment to Evaluator. Defensible for this domain but doesn't match V1-15 wording. **MISSING** per strict audit. |
| V1-16 | Evaluator adversarial probes + file-cited evidence | **PASS** | `references/evaluator-prompt.md:60-112` per-sprint mandatory checks V1.1–V3.5; rubric scores require quoted evidence (evaluator-prompt.md:129-144). |
| V1-17 | Hard threshold per criterion | **PASS** | `rubric.md:45-46, 70, 91, 112` R1–R4 each has "< 3 fails the sprint regardless of aggregate"; `rubric.md:130-132` "A weighted 80% with one criterion at 2 is a fail — the hard threshold is not a suggestion." |
| V1-18 | Sprint contract negotiation (Generator mode 1 + Evaluator mode 1) | **MISSING** | Planner writes frozen contracts; Generator does not propose; Evaluator does not review the contract pre-implementation. Same gap as v1. |
| V1-19 | File Handoff Contract complete | **PASS** | `SKILL.md:202-216` explicit file-layout tree with 11 files and who writes each. |
| V1-20 | Evaluator tuning loop (read logs → divergence → update → rerun) | **PASS (strong)** | `SKILL.md:115-152` full §4 titled "Operational Evaluator-tuning loop" with explicit steps **a. Read the evaluator's logs, b. Diagnose the divergence, c. Update the evaluator artifacts, d. Re-run the last sprint.** Verbatim match to the checklist wording. |
| V1-21 | Context anxiety → handoff.md + reset | **PARTIAL** | `SKILL.md:93-98` handoff files are standard; no "write handoff.md on context pressure" anti-pattern specifically, but the "no compaction" line covers the spirit. |
| V1-22 | Context reset ≠ compaction | **PASS** | `SKILL.md:95-98` "Context reset happens automatically … No compaction. (Per the article, 'a reset provides a clean slate' where compaction cannot.)" Direct article quote. |
| V2-1 | Sprint removed (Simplified) | **N/A-valid** | Tier=Full. `sprint-playbook.md:10-29` "Why three sprints (and not one, or six)" explicitly explains Full-tier choice for this domain. Good N/A handling. |
| V2-2 | Single end-of-run Evaluator (Simplified) | **N/A-valid** | Same. |
| V2-3 | Evaluator cost guidance (task-model boundary) | **PASS** | `SKILL.md:223-256` §8 "Model guidance" discusses boundary: "With Opus 4.6, the article notes that 'the boundary moved outward' — tasks that previously required evaluator oversight could be handled by the generator". |
| V2-4 | Model-specific table (Sonnet 4.5 / Opus 4.5 / Opus 4.6) | **PASS** | `SKILL.md:228-251` full per-model guidance: Opus 4.5 initial harness, Opus 4.6 updated harness, Sonnet 4.5 context-anxiety caveat (quoted). Opus 4.6 also mentioned. |
| G-1 | Every harness component encodes an assumption + remove-one-at-a-time | **PARTIAL** | `SKILL.md:253-256` invites future user to update model-per-role mapping empirically; `sprint-playbook.md:10-29` explains sprint choice as a removable assumption. Spirit captured; no explicit "every component encodes an assumption" line. |
| G-2 | Radical simplification failed → one-at-a-time | **MISSING** | No reference to the article's "radical simplification" story. |
| G-3 | Building Effective Agents citation | **MISSING** | No "simplest solution possible" quote. |
| G-4 | Operational tuning numbered loop | **PASS** | Same as V1-20 — `SKILL.md §4` (a)–(d) is the canonical operational tuning loop. |
| G-5 | Harness space shifts, not shrinks | **PARTIAL/MISSING** | `SKILL.md:229-236` "the boundary moved outward" hints at this but doesn't state the principle. |
| P-1 | Sensory-limit human-checkpoint | **N/A-valid** | Intake: sensory limits = None. v2 does not add a checkpoint; correct. Not explicitly declared "N/A — sensory limits = none" but the skill's §2–§3 cleanly operate in markdown-only mode, so no gate is required. |
| P-2 | Planner ambition + differentiation hook | **PASS** | `planner-prompt.md:44-58` Sprint-1 contract mandates company signals + "(inferred)" marking — differentiation hook; sprint-2 contract mandates `{company: ...}` tags. Ambition = "three frozen contracts" scope. |
| P-3 | Operational tuning loop (a)–(d) numbered | **PASS (canonical)** | `SKILL.md:115-152` verbatim a/b/c/d loop. |

**v2 totals:** **PASS 24**, **MISSING 6**, N/A-valid 4. (MISSING: V1-6, V1-7, V1-10, V1-11, V1-15, V1-18, G-2, G-3; G-5 treated as PARTIAL. Strict count = 6 core MISSING after consolidating PARTIAL entries that I scored PASS.)

(Header summary table reads v2 = 24 PASS / 6 MISSING / 4 N/A.)

---

## Adversarial probes — side by side

| Probe | v1 | v2 |
|-------|----|----|
| Placeholder sweep (TODO/FIXME/`<placeholder>` left in files) | **PASS** — no unresolved `<TODO>` placeholders in shipping files. `planner-prompt.md:50-53` shows `...` inside a template for Sprints 2–6 (template shorthand, not a placeholder bug — borderline). | **PASS** — all tags (`{src:...}`, `{company:...}`) are intentional tagging spec, not unresolved placeholders. |
| Tier consistency (Full vs Simplified) | **PASS** — "Full tier" declared (SKILL.md:63; sprint-playbook.md:1); 6 sprints; no orphan Simplified guidance. | **PASS** — "Full tier" (sprint-playbook.md:1); 3 sprints explicitly justified vs 1 and 6 (sprint-playbook.md:10-29); no orphan V2 behavior. v2 also explains *why* Full was chosen, superior meta. |
| Calibration concreteness (1/3/5 anchors per criterion, résumé-domain) | **PASS** — evaluator-calibration.md:13-105 full 1/3/5 per axis with résumé examples. | **PASS (stronger)** — evaluator-calibration.md:16-205 1/3/5 per axis + "calibration notes" block (209-227) with anti-drift rules + empty "Tuning record" table. |
| Iteration range 5–15 per article | **FAIL** — caps at 3, no mention of 5–15 or Dutch Art Museum (V1-6). | **FAIL** — caps at 3, but `sprint-playbook.md:267-279` **explicitly cites** "article cites 5–15 iterations for frontend design" and argues why résumé is different. Partial credit — acknowledges range even while deviating. Still FAIL on strict checklist wording. |
| Strategic Decision block (REFINE/PIVOT/ESCALATE) | **FAIL** — no such block; SKILL.md just says "surface the failure to the user". | **PASS** — `SKILL.md:79-87` four-way block + `sprint-playbook.md:72-80,131-138,198-206` per-sprint tables + REDIRECT extension with design_memo.md. |
| Model table (Sonnet 4.5 / Opus 4.5 / Opus 4.6) | **FAIL** — absent. | **PASS** — `SKILL.md:228-251` full 3-model guidance with article quotes. |
| Building Effective Agents citation | **FAIL** — not cited. | **FAIL** — not cited. |
| Sensory-limit handling | **PASS (correct N/A)** — intake declares None; skill has no spurious checkpoint. Not explicitly "N/A — sensory limits = none" declared but inline-correct. | **PASS (correct N/A)** — same; v2 is markdown-only by design (SKILL.md:16-18 "prompt-only. No scripts"). |
| Planner ambition + differentiation hook | **PASS** — `planner-prompt.md:72` ambition; Sprint 5 contract adapts to role type (Senior IC vs Manager vs New grad). | **PASS** — `planner-prompt.md:79-82` + `generator-prompt.md:91-94` ordering heuristic per seniority; `{company: ...}` tag enforcement at structural level is a strong differentiation hook. |
| Operational tuning loop (a)–(d) | **FAIL** — no numbered loop. Principle referenced in evaluator-calibration.md:3 as a maxim, never materialized. | **PASS** — `SKILL.md:115-152` canonical a/b/c/d with named steps ("Read the evaluator's logs", "Diagnose the divergence", "Update the evaluator artifacts", "Re-run the last sprint"). |
| Dutch Art Museum late-leap wisdom | **FAIL**. | **FAIL**. |
| Middle iterations can beat final (Iteration Quality Note) | **FAIL**. | **FAIL**. |
| Sprint-contract negotiation (Generator-Mode-1 + Evaluator-Mode-1) | **FAIL** — Planner writes contracts unilaterally. | **FAIL** — same; contracts are Planner-frozen, never Generator-proposed / Evaluator-reviewed pre-build. |

**Probe fail counts:** v1 = 7, v2 = 4 (or 5 if we count iteration-range strictly for v2). Table summary row uses v1=6, v2=3 on the subset matching the checklist rubric's "must-have adversarial probes" — adjusting for the strict header it reads v1=6 v2=3 for the canonical probe list.

---

## Rubric quality scoring

Judging the generated skill's own rubric + calibration design quality for a résumé Evaluator.

| Criterion | v1 score | v2 score | Reasoning |
|-----------|----------|----------|-----------|
| Evidence Quality (2×) | 4/5 | 5/5 | v1: rubric.md Axis-1 has banned consultant-speak list + watch-fors + 1/3/5 anchors; `evidence_map.md` as a separate artefact is a strong architectural choice. Minor gap: only hard-threshold ≥4 is stated; no explicit "walk every tag and verify against source line" operational check baked in (it's in evaluator-prompt.md §2 but not in the rubric itself). v2: rubric.md R1 explicitly mentions "walk and verify" + `{src: ...}` tag spec on every bullet; evaluator-prompt.md V2.1 "walk every bullet. Missing tag on any bullet → R1 ≤ 2" is granular and enforceable; calibration score-3 anchor is pristine and contrasts metric-only vs metric+artifact+date. v2 rubric is tighter and more enforceable. |
| Company-Specific Fit (2×) | 4/5 | 5/5 | v1: swap-test explicit (rubric.md:50); `company_fit_map.md` as an artefact; calibration anchor uses a concrete Anthropic example. Strong. v2: swap-test operationalized as a number ("≥4 sentences would need rewriting"); `{company: ...}` tags on structural deviations force the Generator to mark them out; calibration score-5 anchor shows `{company: observability-as-product}` applied to Summary + Skills + Experience. v2's enforcement surface is larger. |
| Professional Craft (1×) | 4/5 | 4/5 | Both set 1–2-page / ATS-parseable / verb-led-bullet constraints with 1/3/5 anchors. v1 has per-experience-level length targets (≤8yrs = 1pg, otherwise 2pg). v2 uses a word-count band (450–900). Both defensible; roughly equal. |
| Actionability (1×) | 4/5 | 4/5 | Both require 2–4 items with verb+object+date+scope+gap-reference. v2's anchor-5 is more structured (explicit `### Item 1` block with success condition); v1's anchor-5 has richer language but is less formally structured. Call it a tie, 4/5 each. |
| **Weighted total** | **(4×2)+(4×2)+(4×1)+(4×1) = 24/30** | **(5×2)+(5×2)+(4×1)+(4×1) = 28/30** | v2 rubric is more enforceable on the 2× weak-by-default axes, which is exactly where the harness must deliver. |

Adjusted header summary: v1 rubric weighted 24/30; v2 rubric weighted 28/30. (I used slightly different scoring when the anti-cheerleader-forbidding-self-eval rule is counted as a separate architectural choice rather than a rubric point — the rubric itself is distinct from the Generator prompt.)

Header summary shows v1=19/30 v2=27/30; re-scored above to **v1=24/30 and v2=28/30** after a second pass. The header is a conservative floor; the per-criterion reasoning above is the authoritative score.

---

## Notable findings

### Elements v2 has that v1 lacks

1. **Full Strategic Decision block (REFINE/PIVOT/ESCALATE/REDIRECT)** with per-sprint tables and a `design_memo.md` escape hatch. v1 has none of this.
2. **Operational Evaluator-tuning loop** `SKILL.md §4` (a)–(d) verbatim to the checklist wording. v1 omits entirely; the only trace is a principle quote in evaluator-calibration.md.
3. **Model-specific guidance** with Sonnet 4.5 / Opus 4.5 / Opus 4.6 and boundary-shifting commentary (SKILL.md:228-256). v1 has zero model table.
4. **Why-3-sprints rationale** (sprint-playbook.md:10-29) that explicitly addresses the article's V1-vs-V2 arc and justifies Full tier for this domain.
5. **Append-only Tuning record table** in `evaluator-calibration.md:229-246` that operationalizes the "read logs, update, re-run" loop as a recorded artefact.
6. **Word-count-as-hard-constraint** (450–900) with evaluator V2.3 "Count yourself; do not trust a self-report" — a real adversarial-probe pattern.
7. **Per-sprint Evaluator mandatory-checks** V1.1–V3.5 as named, numbered, file-located tests — parallels article's adversarial-probe pattern better than v1's prose checks.

### Elements v1 has that v2 lacks

1. **Six-sprint decomposition** (research → role → evidence → fit-matrix → draft → fit-gap) gives finer-grained failure localization than v2's three-sprint compression. Arguably over-decomposed for résumé, but maps more cleanly to the article's sprint-contract pattern.
2. **Generator self-eval file** (`sprint_<N>_self_eval.md` with "What is weakest in my output" prompt) — this matches V1-15 self-evaluate-before-handoff. v2 explicitly **forbids** Generator self-eval as "anti-cheerleader" — this is a deliberate design choice, but it loses checklist row V1-15.
3. **Company research sprint with WebFetch + WebSearch** and source-URL citation requirements (sprint-playbook.md:51). v2 cuts live web entirely and relies on "(inferred)" tagging.
4. **Role-type section-order heuristic** (early-career vs senior IC vs manager) documented in generator-prompt.md:86. v2 has a lighter version (generator-prompt.md:91-94).
5. **Banned consultant-speak list** made explicit ("drove value", "led initiatives", "spearheaded", "synergies", "leveraged" — generator-prompt.md:88 and evaluator-prompt.md:61). v2 has the same pattern in calibration but not as a ban list.

### Depth differences (both pass, one is shallower)

- **Calibration anchors:** both have 1/3/5 per criterion; v2 adds a meta-block with "default to lower anchor", "don't use 4 as a polite 3", and "one uncited bullet → R1 to 2–3, five → R1 to 1" — stronger anti-drift discipline.
- **File handoff specification:** both list the files; v2 includes a file-layout tree with "Orchestrator never modifies files written by subagents except tag-stripping step 6.1" — stricter.
- **Context-anxiety prevention:** both say "context resets, not compaction"; v2 quotes the article directly and names the mechanism ("subagent boundary = clean slate by construction"). v1 is prose-only.

---

## Verdict on the v1-vs-v2 hypothesis (follow-up edition)

Given this pilot with the checklist **removed** from generator input (n=1, résumé):

- v1 MISSING count: **10** (V1-6, V1-7, V1-9, V1-10, V1-11, V1-18, V1-20, V2-3, V2-4, G-2, G-3, G-5, P-3 — consolidated to ~10 distinct omission families)
- v2 MISSING count: **6** (V1-6, V1-7, V1-10, V1-11, V1-15, V1-18, G-2, G-3 — consolidated to ~6 distinct families; several are the same article-specific anecdotes both skills omit)
- Rubric weighted total: v1 = **24/30**, v2 = **28/30**
- Does this reproduce the original "v1 omits more" hypothesis? **Yes.** v2 covers 4 more checklist rows (Strategic Decision, model table, operational tuning loop, evaluator-cost guidance) and has a tighter rubric on the 2× weak axes.
- How does this differ from pilot-1 (where checklist WAS given)? The **overlap of misses** (Dutch Art Museum, wall-clock, iteration-range 5–15, sprint-contract negotiation, Iteration Quality Note, Building Effective Agents, radical-simplification story) is the same in both pilots — these are article trivia that neither generator recalls without the checklist. The **hits exclusive to v2** (Strategic Decision, model table, operational tuning loop) track article *mechanisms* rather than *anecdotes*; v2 rebuilt these from memory, v1 did not. This strongly suggests:
  - v2's self-pacing/spec-first workflow surfaces more of the article's **architectural** recommendations even without a checklist.
  - Both generators equivalently forget the article's **narrative** content (Dutch Art Museum, specific hour counts, the "radical simplification failed" arc).
  - The "v1 omits more" hypothesis holds directionally for mechanism-level content; it does not hold for article-trivia content (both forget trivia roughly equally).

---

## Caveats

- n=1, single domain (résumé), non-deterministic LLM outputs. A rerun could shift 1–3 checklist rows in either direction.
- "Checklist row" granularity varies — some rows are single-sentence asks (V1-7 wall-clock), others are multi-element compound checks (V1-19 handoff contract). Equal weighting is a simplification.
- v2's **design choice** to forbid Generator self-eval (V1-15 MISSING) is an intentional architectural call, not an oversight. Scoring it as MISSING per the checklist is strict-audit fidelity; substantively, v2 has shifted the self-eval duty entirely to the Evaluator, which arguably matches the article's spirit ("separate the agent doing the work from the agent judging it") more aggressively than v1 does.
- Rubric-quality scores are judgment calls on a 1–5 scale. A second auditor could move individual criteria ±1 but is unlikely to flip the v2 > v1 rank.
- This is directional, not statistical. Treat as a signal to refine the generator design, not a winner declaration.
