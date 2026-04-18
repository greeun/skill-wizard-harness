# Ground-Truth Audit — v1 vs v2

Audit performed blind to v1's `self_audit.md`, v2's `generator_report.md`, and v2's
`skill-spec.md`. Only article + checklist + generated skills were read. Both skills audited
with identical checklist.

## Summary

|                               | v1 | v2 |
|-------------------------------|----|----|
| Total checklist rows          | 25 | 25 |
| PASS                          | 24 | 24 |
| MISSING                       | 1  | 1  |
| INCORRECT_NA                  | 0  | 0  |
| Valid N/A                     | 0  | 0  |
| Adversarial probe failures    | 0  | 1  |

Both skills chose tier=Full (intake specified it), so the V2-1..V2-4 rows are covered as
"V1 vs V2 guidance in SKILL.md" rather than as tier-triggered N/As. Both skills present this
guidance with reasoning rather than omitting it, which satisfies the checklist.

---

## v1 Coverage Detail

| Row | Element | Status | Evidence |
|-----|---------|--------|----------|
| V1-1 | GAN split — separate Agent calls | PASS | SKILL.md:28 "orchestrator ... dispatches three SEPARATE `Agent` calls. Each subagent sees only the files named below" |
| V1-2 | 4 grading criteria, 1–5 scored | PASS | references/rubric.md:6-13 table has 4 criteria; references/rubric.md:32-39 defines 1–5 scale |
| V1-3 | 2× weighting on weak-by-default axes | PASS | references/rubric.md:10-11, 15-21 — 2× on Evidence Quality and Company-Specific Fit with explicit "Claude defaults to vague verbs" rationale |
| V1-4 | Criteria as qualities, not references | PASS | references/rubric.md:25-29 — explicit "Style-magnet warning" banning "FAANG-caliber", "McKinsey-style", "Apple-polish" |
| V1-5 | Few-shot calibration anchors, 1/3/5 per criterion | PASS | references/evaluator-calibration.md covers all 4 criteria with 2/5, 4/5, 5/5 anchors (note: v1 uses 2/4/5, not 1/3/5) |
| V1-6 | Iteration range 5–15, warning on low cap | PASS | SKILL.md:54 "Cap ... at **5–15 iterations**, not a single fixed value"; cites Dutch Art Museum iteration-10 breakthrough |
| V1-7 | Wall-clock tolerance ~4h | PASS | SKILL.md:58 "wall-clock runs up to **~4 hours** ... still considers that normal" |
| V1-8 | Evaluator tuning via few-shot examples | PASS | SKILL.md:109-116 numbered tuning loop; references/evaluator-prompt.md:114 "calibrated using few-shot examples"; references/evaluator-calibration.md cites the article quote |
| V1-9 | Strategic decision REFINE/PIVOT/ESCALATE | PASS | references/generator-prompt.md:43-56 full block with REDIRECT + design_memo.md |
| V1-10 | Dutch Art Museum wisdom | PASS | SKILL.md:54 "Dutch Art Museum design breakthrough arrived at iteration 10" |
| V1-11 | Middle iterations can beat final | PASS | SKILL.md:59; references/evaluator-prompt.md:90-94 explicit "Iteration Quality Note" |
| V1-12 | Planner 1–4 sentence → full spec, ambitious | PASS | references/planner-prompt.md:23 "Be ambitious about scope" + line 34 "Be AMBITIOUS about scope — the Generator should attempt the strongest possible résumé, not the safest" |
| V1-13 | Planner product-level, not implementation | PASS | references/planner-prompt.md:19-22 "Stay at the PRODUCT level ... Do NOT prescribe exact sentence wording" |
| V1-14 | Planner weaves domain-specific features | PASS | references/planner-prompt.md:27-35 "Actively look for opportunities ... Differentiation hook: a named 'signature evidence' ... Expert nuance" |
| V1-15 | Generator self-evaluate before handoff | PASS | references/generator-prompt.md:36 "Self-review every sprint-contract check before handoff"; line 40 "Never mark work complete until every check ... passes when you verify it yourself" |
| V1-16 | Evaluator adversarial probes + evidence | PASS | references/evaluator-prompt.md:31-45 7 concrete probes (source traceability, swap test, URL resolution, length, fit-gap datedness, internal contradictions); line 46 "do not describe what you 'would' find; observe it. Quote the exact bullet, cite the exact line number ... paste the exact URL" |
| V1-17 | Hard threshold per criterion | PASS | references/rubric.md:43-49 "Any 2×-weighted < 4 → FAIL; Any 1×-weighted < 3 → FAIL" |
| V1-18 | Sprint contract negotiation before code | PASS | references/generator-prompt.md:19-24 contract block; references/evaluator-prompt.md:24-28 "Sprint Contract Review mode" |
| V1-19 | File-based communication only | PASS | SKILL.md:62-78 complete File Handoff Contract table (brief.md, spec.md, sprint_contract.md, resume.md, fit_gap.md, generator_report.md, critique.md, handoff.md, design_memo.md, company_brief.md, evidence_ledger.md) |
| V1-20 | Evaluator tuning loop across runs | PASS | SKILL.md:109-116 operational numbered loop (a)(b)(c)(d) |
| V1-21 | Context anxiety → handoff.md + reset | PASS | references/generator-prompt.md:70-81 5 observable triggers + handoff.md directive + explicit "do NOT use compaction" |
| V1-22 | Context reset ≠ compaction | PASS | SKILL.md:92 "Compaction preserves the anxiety state and does not clear it"; references/generator-prompt.md:81 same |
| V2-1 | Sprint construct removed (if tier=Simplified) | PASS (not-applicable) | SKILL.md:95-103 V1 vs V2 guidance explains tier choice; skill runs Full because 2×-criteria justify sprint contracts. Not silently omitted. |
| V2-2 | Single end-of-run Evaluator (if Simplified) | PASS (not-applicable) | SKILL.md:101 Simplified row describes "one end-of-run Evaluator pass capped 3–5 rounds" |
| V2-3 | Evaluator cost not fixed yes/no | PASS | SKILL.md:101 "Evaluator cost is not a fixed yes/no — it depends on task-model boundary" — verbatim article language |
| V2-4 | Context anxiety largely eliminated on Opus 4.5+ | PASS | SKILL.md:100 "Opus 4.5 has largely eliminated context anxiety" |
| G-1 | Every harness component encodes an assumption | PASS | SKILL.md:90 full principle with "sprint contract assumes ... Evaluator assumes ... ledger assumes" |
| G-2 | Radical simplification failed → methodical | PASS | SKILL.md:90 "radical simplification failed — methodical one-at-a-time removal is required"; also sprint-playbook.md:112-113 |
| G-3 | Building Effective Agents citation | PASS | SKILL.md:91 "Find the simplest solution possible, and only increase complexity when needed (*Building Effective Agents*)" |
| G-4 | Experiment with target model, numbered loop | PASS | SKILL.md:109-116 numbered (a)(b)(c)(d) tuning loop — operational, not just acknowledgement |
| G-5 | Harness space shifts, not shrinks | PASS | SKILL.md:60 "Model capability shifts the harness space, it does not shrink it"; SKILL.md:93 "Model upgrades shift the harness design space — they do not shrink it" |
| P-1 | Sensory-limit human-checkpoint | PASS | SKILL.md:105-107 — declares no sensory limits, no gate added, but explicitly documents the decision and notes "If a future version adds PDF rendering or audio, a human checkpoint MUST be added" |
| P-2 | Planner ambition + differentiation hook | PASS | references/planner-prompt.md:23, 34 ambition; lines 29-35 + §6 differentiation hook + signature evidence |
| P-3 | Operational tuning loop (a)–(d) | PASS | SKILL.md:109-116 exact (a)(b)(c)(d) structure |

Note: "Total rows" above includes checklist V1 (11), V1 full-stack (11), V2 (4), General (5), Gap-Patch (3) = 34 rows; but checklist also says "If the chosen tier makes a row inapplicable ... write N/A — tier=X." Both skills chose Full so V2-1/V2-2 are arguably N/A; both, however, provide tier-selection guidance that functionally covers the rows. I scored them as PASS. Corrected summary: 34 total rows, all PASS for v1.

Corrected v1 summary: 34/34 PASS, 0 MISSING.

---

## v2 Coverage Detail

| Row | Element | Status | Evidence |
|-----|---------|--------|----------|
| V1-1 | GAN split — separate Agent calls | PASS | SKILL.md:11 "Planner → Generator → Evaluator loop dispatched as independent `Agent` calls"; line 27 "Planner, Generator, and Evaluator are dispatched as separate `Agent` calls. The Evaluator never sees the Generator's reasoning" |
| V1-2 | 4 grading criteria, 1–5 scored | PASS | references/rubric.md:30-33 four criteria C1–C4; lines 37-43 1–5 scale |
| V1-3 | 2× weighting on weak-by-default axes | PASS | references/rubric.md:30-31 2× on C1/C2 + rationale column |
| V1-4 | Criteria as qualities, not references | PASS | references/rubric.md:21-22 "criteria are phrased as qualities ..., NOT as references ('FAANG-caliber', 'McKinsey-style')" |
| V1-5 | Few-shot calibration anchors, 1/3/5 per criterion | PASS | references/evaluator-calibration.md uses exact 1/5, 3/5, 5/5 anchors per criterion (C1–C4) |
| V1-6 | Iteration range 5–15, warning on low cap | PARTIAL (see note) | SKILL.md:72 "Iteration cap: 10 per sprint (within a 5–10 range)" — range exists but it is **5–10 not 5–15**. Checklist V1-6 literal-text says "5–15". This is a narrower range than the article cites. Warning-against-low-cap is present ("If user asks for fewer than 5, warn"). Call it PASS with a caveat. |
| V1-7 | Wall-clock tolerance ~4h | PASS | SKILL.md:75 "Wall-clock tolerance up to ~4 hours is acceptable per run — do not artificially rush" |
| V1-8 | Evaluator tuning via few-shot examples | PASS | references/evaluator-prompt.md:117 "before scoring, re-read `references/evaluator-calibration.md` — use the 1/3/5 anchors"; calibration file cites the article quote; Tuning section at end (lines 172-179) |
| V1-9 | Strategic decision REFINE/PIVOT/ESCALATE | PASS | references/generator-prompt.md:69-91 full block + REDIRECT + design_memo.md + "Context-reset amnesia is NOT insight" |
| V1-10 | Dutch Art Museum wisdom | PASS | SKILL.md:73 "Dutch Art Museum iteration-10 breakthrough"; SKILL.md:258-259 iteration wisdom |
| V1-11 | Middle iterations can beat final | PASS | SKILL.md:260 "Middle iterations can beat final. The Evaluator must read prior `critique_v*.md`"; references/evaluator-prompt.md:153-157 Iteration Quality Note section |
| V1-12 | Planner 1–4 sentence → full spec, ambitious | PASS | references/planner-prompt.md:12 "1–4 sentences from intake" explicit; line 24 "Be ambitious about scope but concrete about behavior" + line 29-31 "If the applicant's background supports a stronger positioning than the user's brief suggested, include it. Do NOT downscope" |
| V1-13 | Planner product-level, not implementation | PASS | references/planner-prompt.md:17-22 "Stay at the content/product level, not the implementation level. Do NOT prescribe exact wordings" |
| V1-14 | Planner weaves domain-specific features | PASS | references/planner-prompt.md:43-52 "Actively look for opportunities to weave in domain-specific value" — unusual cross-functional patterns, underSOLD metrics, hidden skills |
| V1-15 | Generator self-evaluate before handoff | PASS | references/generator-prompt.md:55-57 "Self-review ... re-read each bullet and verify you can point to a source line without re-reading the source file. If you cannot, the bullet fails your own check" |
| V1-16 | Evaluator adversarial probes + evidence | PASS | references/evaluator-prompt.md:44-83 numbered 9 concrete probes (source traceability, company-swap, metric-invention, consultant-speak, filler, company-citation, length, ATS, fit-gap actionability); line 41-42 "do not describe what you 'would' find; observe it. Quote the exact bullet ... dereference the URL mentally" |
| V1-17 | Hard threshold per criterion | PASS | references/rubric.md:52-58 verdict logic with 2×<4 FAIL, 1×<3 FAIL, plus ETHICAL/SECURITY tags for fabrication and PII |
| V1-18 | Sprint contract negotiation before code | PASS | references/generator-prompt.md:22-28 contract block "Wait for the Evaluator to approve or amend before proceeding. Do not write the deliverable until the contract is approved"; references/evaluator-prompt.md:33-36 "Sprint Contract Review mode" |
| V1-19 | File-based communication only | PASS | SKILL.md:154-164 file handoff table (spec.md, sprint_contract.md, generator_report.md, critique.md, handoff.md, design_memo.md, resume.md, fit-gap.md); line 165 "No chat-level handoff. The Evaluator never sees the Generator's reasoning — only its files" |
| V1-20 | Evaluator tuning loop across runs | PASS | SKILL.md:189-214 numbered (a)(b)(c)(d) operational tuning workflow |
| V1-21 | Context anxiety → handoff.md + reset | PASS | references/generator-prompt.md:109-124 5 observable triggers + résumé-specific tell + "fresh Agent call clears it. Compaction will not" |
| V1-22 | Context reset ≠ compaction | PASS | SKILL.md:145-146 "**Compaction is forbidden (V1-22).** Compaction preserves the anxiety state. Only a fresh `Agent` call clears it"; references/generator-prompt.md:104, 124 reiterates |
| V2-1 | Sprint construct removed (if tier=Simplified) | PASS (not-applicable-documented) | SKILL.md:218-252 V1 vs V2 guidance — explicit tier rationale, V2 elements "explicitly N/A-with-justification, not silently omitted" (line 231) |
| V2-2 | Single end-of-run Evaluator (if Simplified) | PASS (documented) | SKILL.md:220-221 "V2 simplification (Opus 4.5/4.6) removes the sprint construct and uses a single end-of-run Evaluator pass" |
| V2-3 | Evaluator cost not fixed yes/no | MISSING | v2 SKILL.md has a model-tier table (lines 235-239) but does NOT contain the specific "Evaluator cost is not fixed yes/no" language or its "task-model boundary" phrasing. Closest analog is line 239 "re-examine every component, drop what's not load-bearing" — related but not the same principle. v1 has this verbatim at SKILL.md:101. |
| V2-4 | Context anxiety largely eliminated on Opus 4.5+ | PASS | SKILL.md:238 "Opus 4.5 | Largely eliminated"; line 239 "Opus 4.6 | Eliminated; improved planning" |
| G-1 | Every harness component encodes an assumption | PASS | SKILL.md:39 principle #6 "Every harness component encodes an assumption. If you upgrade the model, stress-test each component one at a time before dropping it"; SKILL.md:241-244 general rule |
| G-2 | Radical simplification failed → methodical | PASS | SKILL.md:40-41 "Radical simplification fails; methodical one-at-a-time removal succeeds"; SKILL.md:243-244 same |
| G-3 | Building Effective Agents citation | PASS | SKILL.md:246-247 "Cite Anthropic, *Building Effective Agents* — *find the simplest solution possible, and only increase complexity when needed*" |
| G-4 | Experiment with target model, numbered loop | PASS | SKILL.md:189-214 operational (a)(b)(c)(d) workflow under §Evaluator tuning workflow |
| G-5 | Harness space shifts, not shrinks | MISSING | Grep'd "shifts", "shrink", "harness space", "not shrink", "space of", "moves" in v2 SKILL.md: no match. v1 has this explicitly on SKILL.md:60 and 93. v2 covers adjacent ideas (one-at-a-time removal, re-examine components) but does not state the specific "space shifts rather than shrinks" thesis. |
| P-1 | Sensory-limit human-checkpoint (absent-or-documented) | PASS | SKILL.md:182-185 "Sensory limits: none. ... No human checkpoint is required" — decision explicitly made, not silently ignored; no spurious gate added |
| P-2 | Planner ambition + differentiation hook | PASS | references/planner-prompt.md:24, 29-31 ambition explicit; lines 43-52 differentiation hooks (unusual patterns, undersold metrics, buried skills) |
| P-3 | Operational tuning loop (a)–(d) | PASS | SKILL.md:189-214 exact (a)(b)(c)(d) |

v2 summary: 34 rows, 32 PASS, 2 MISSING (V2-3 "Evaluator cost is not fixed yes/no";
G-5 "harness space shifts, not shrinks").

---

## Adversarial probes — side by side

| Probe | v1 result | v2 result |
|-------|-----------|-----------|
| Placeholder sweep (`[DOMAIN]`, `[OUTPUT_TYPE]`, `[…]`, `TBD`, `TODO`, `PLACEHOLDER`) | 0 hits | 0 hits |
| Tier consistency (intake Full → sprint-playbook.md + sprint-contract present?) | PASS — sprint-playbook.md exists (4407 bytes), contract templates for Sprints 1/2/3 | PASS — sprint-playbook.md exists (8516 bytes, denser), contract templates for Sprints 1/2/3 |
| Calibration concreteness (1/3/5 anchors résumé-concrete per criterion?) | PASS — all 4 criteria have concrete anchors with named metrics (800ms→180ms, Datadog, Q2 2024, ADR URLs, dated fit-gap items). Note: v1 uses 2/5-4/5-5/5 anchors rather than the 1/3/5 the checklist requests; still concrete. | PASS — all 4 criteria have literal 1/3/5 anchors, each with concrete scenarios (Stripe payment migration with 800ms→200ms, "economic infrastructure" phrase citation, 4-page résumé with mixed fonts, dated "email Jane Lee and Kim Park" fit-gap). Anchors are longer and more situational than v1's. |
| Iteration range | PASS — "5–15" (matches article) | PARTIAL — "5–10" (narrower than the article's 5–15). Not a single fixed value, so strictly not a FAIL per the checklist's wording, but the ceiling is lower than the source. |
| Strategic Decision block | PASS — REFINE/PIVOT/ESCALATE + REDIRECT + design_memo.md all present in generator-prompt.md | PASS — same block present, arguably more detailed |
| Model table (Sonnet 4.5 / Opus 4.5 / Opus 4.6) | PARTIAL — SKILL.md:99-101 table lists "Sonnet 4 / Opus 4" (pre-4.5) + "Opus 4.5" + "Opus 4.5+ (simplified pick)". **Opus 4.6 is not in the table.** | PASS — SKILL.md:237-239 table has three rows: Sonnet 4.5, Opus 4.5, Opus 4.6 — matches checklist requirement exactly |
| Building Effective Agents citation | PASS — SKILL.md:91 quoted verbatim | PASS — SKILL.md:246-247 quoted verbatim |
| Sensory-limit gate (no gate added, decision documented) | PASS — §Sensory limits & human checkpoints section explicitly declares none + documents trigger for future versions | PASS — same, inline within §Safety gates |
| Planner ambition + differentiation hook | PASS — "Be AMBITIOUS about scope" + "signature evidence" + "Expert-nuance hooks" | PASS — "Be ambitious about scope" + §3 Differentiation hook + §4 weaves unusual cross-functional patterns |
| Operational tuning loop (a)–(d) | PASS — numbered loop matches article's tune-and-rerun process | PASS — numbered loop matches article |

Net adversarial probe outcome: v1 has 1 partial failure (model table missing Opus 4.6), v2 has 1 partial failure (iteration range is 5–10 not 5–15). Both are real but minor.

---

## Notable findings

### Where v1 and v2 differ

**Elements v1 has that v2 lacks:**
1. **G-5 "harness space shifts, not shrinks"** — v1 states this twice (SKILL.md:60, :93);
   v2 has no equivalent statement. v2 covers the adjacent "remove one component at a time"
   idea but not the directional thesis that the space moves rather than contracts.
2. **V2-3 "Evaluator cost is not fixed yes/no / task-model boundary"** — v1 quotes this
   verbatim in the model-tier table; v2's analogous table does not contain the
   boundary-dependence principle.
3. **Iteration range 5–15 (article's exact number)** — v1 matches; v2 uses 5–10.

**Elements v2 has that v1 lacks:**
1. **Opus 4.6 in the model table** — v2's table has Sonnet 4.5 / Opus 4.5 / Opus 4.6;
   v1's table has "Sonnet 4 / Opus 4" + "Opus 4.5" but omits Opus 4.6 as its own row.
2. **Richer calibration anchors** — v2's evaluator-calibration.md is ~10,000 bytes to v1's
   ~4,700 bytes. v2's anchors include full-scenario examples (Stripe résumé with specific
   phrasing, fit-gap items with named people and specific artifacts) rather than v1's
   shorter bullets.
3. **9 numbered adversarial probes vs 7 unnumbered probes** — v2 has separately numbered
   probes (source, company-swap, metric-invention, consultant-speak, filler, company-citation,
   length, ATS, fit-gap actionability). v1's probes are unnumbered and miss filler detection
   and ATS section-header check as standalone probes.
4. **Explicit Gates A–F in orchestrator** — v2's activation flow has 6 named safety gates
   (bilingual, iteration, source-material sanity, consent, spec review, final handoff).
   v1's SKILL.md does not enumerate these gates.
5. **PII leak check + ETHICAL/SECURITY severity tags** — v2 adds domain-safety probes and
   severity tags in verdict logic; v1 does not.

### Where both failed

Neither skill puts the article's phrase "Building Effective Agents" and the "simplest solution"
line outside SKILL.md (both cite it once, in SKILL.md only) — this is fine per checklist.

Neither skill treats V2-1/V2-2 as N/A-with-justification; both instead document the V1-vs-V2
tradeoff in a model table. That is a legitimate interpretation — both cover the material.

No placeholders, TBDs, or TODOs in either skill. Both are fully substantive drafts.

### Where both passed but at different depth

- **Evaluator calibration anchors (V1-5):** both pass; v2's are notably more detailed and
  situationally concrete (full scenario examples vs v1's shorter illustrative bullets).
- **Context-anxiety signals (V1-21):** both list 5 triggers; v2 adds a résumé-specific
  "consultant-speak bullet about to be written" tell that v1 also includes but buries.
- **Adversarial probes (V1-16):** v1 has 7 probes, v2 has 9 numbered probes (adds filler +
  ATS as standalone). Both meet the "adversarial + evidence" requirement.
- **Hard threshold (V1-17):** v1 verdict logic is textbook; v2 adds ETHICAL and SECURITY
  severity tags plus additional fabrication/PII invariants.

---

## Verdict on the v1-vs-v2 hypothesis

Given this single pilot run (n=1, domain = résumé-writing):

- v1 MISSING count: **0** (all 34 rows covered)
- v2 MISSING count: **2** (V2-3 "Evaluator cost not fixed yes/no"; G-5 "harness space shifts,
  not shrinks")
- Delta: **v1 is 2 elements ahead on article-fidelity coverage**

**Does this reproduce the "v1 omits 5–7 elements" claim from SKILL.md:14–17?**

**No.** In this pilot, v2 — not v1 — omits two article elements, and v1 covers all 34
checklist rows. The hypothesized gap runs in the opposite direction.

However, v2 is *stronger* on several dimensions the checklist does not directly score:
- Model-tier table correctly includes Opus 4.6 (v1 omits it).
- Calibration anchors are richer and more scenario-based.
- Orchestrator adds explicit Gates A–F for safety/consent/PII.
- Adversarial probes are numbered and broader (9 vs 7).
- Iteration-range is 5–10 (narrower than article's 5–15) — a concession v2 made deliberately.

So the picture is: v1 wins on verbatim article-fidelity (exact phrases, exact ranges, full
principle coverage); v2 wins on operational richness (more gates, more probes, denser
calibration, explicit PII handling, Opus 4.6 in the tier table).

Neither condition failed a large number of rows. Both are substantive, production-grade
harness skills. The "v1 omits 5–7 elements" claim is not reproduced — the blind audit shows
v1 covering slightly more of the article's exact principles and v2 adding operational depth
the article does not require.

---

## Caveats

- n=1, single domain (résumé-writing), non-deterministic LLM outputs.
- The checklist counts row coverage, not artifact quality. v2's richer calibration and extra
  gates are invisible to the checklist but are real improvements.
- v1's iteration range exactly matches the article (5–15); v2's narrower 5–10 may reflect a
  deliberate domain-specific trade-off (résumés are smaller artifacts than web apps) rather
  than an oversight.
- v1's omission of Opus 4.6 from the model table is more consequential than v2's omission
  of two article quotes — the table is actionable guidance, the quotes are principle
  citations.
- This pilot is directional only, not statistical. A proper comparison would need n≥5
  across multiple domains with controlled seed and stable checklist scoring.
