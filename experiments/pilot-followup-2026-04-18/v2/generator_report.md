# Generator report — resume-harness (v2 exp2)

## Summary

Built the `resume-harness` skill per the frozen spec at
`/tmp/v1-v2-exp2/v2/skill-spec.md`, Full tier, Planner → Generator →
Evaluator harness pattern.

Article patterns were drawn directly from a live WebFetch of
<https://www.anthropic.com/engineering/harness-design-long-running-apps>,
not from pre-written templates or an article-coverage checklist. I did
not open `article-coverage-checklist.md`, any of the `-prompt-template.md`
files, or `rubric.md` in the harness root per the restriction.

## Files produced

```
/tmp/v1-v2-exp2/v2/resume-harness/
├── SKILL.md                              (orchestrator + tuning loop)
├── references/
│   ├── planner-prompt.md
│   ├── generator-prompt.md
│   ├── evaluator-prompt.md
│   ├── evaluator-calibration.md
│   ├── rubric.md
│   └── sprint-playbook.md
```

No `assets/` populated — the skill is prompt-only per spec §1.2 ("No
scripts. The skill is prompt-only"). The spec mentions a
`resume-template.md` asset but does not require it; I did not create
placeholder assets.

## Self-check grid

| # | Hard rule | Status | Evidence |
|---|-----------|--------|----------|
| 1 | WebFetched the article first, then read spec; avoided forbidden files | PASS | Two WebFetch calls to the canonical URL; Read on `/tmp/v1-v2-exp2/v2/skill-spec.md` only. No reads on checklist / templates / rubric.md. |
| 2 | Every file self-contained | PASS | SKILL.md §4 (tuning loop), §8 (model guidance) self-contained; each references/*.md readable in isolation; sprint-playbook.md includes contract templates inline. |
| 3 | Full tier matched | PASS | Three sprints (Research, Draft, Fit-Gap), per-sprint Evaluator, file-based handoffs — per spec §3. |
| 4 | SKILL.md body < 500 lines | PASS | ~220 lines body (excl. frontmatter). |
| 5 | evaluator-calibration.md ≥ 3 anchors per criterion | PASS | R1, R2, R3, R4 each have score 1 / score 3 / score 5 résumé-domain anchors with quoted example content. |
| 6 | Model-specific guidance (Sonnet 4.5 / Opus 4.5 / Opus 4.6) from article memory, not a pre-made table | PASS | SKILL.md §8 — paraphrased and quoted from the WebFetch; explicitly notes that the article does NOT prescribe model-per-role mapping and documents what the author actually used. Avoids fabricating a prescriptive table. |
| 7 | No placeholders | PASS | No `TODO`, `TBD`, or `<fill in>` markers anywhere. |
| 8 | Strategic Decision block (REFINE/PIVOT/ESCALATE or REDIRECT + design_memo.md) | PASS | All four decisions appear in SKILL.md §3 Step 3 and in sprint-playbook.md per-sprint tables. `design_memo.md` format is specified in sprint-playbook.md §design_memo.md format. |
| 9 | Generator report has self-check grid | PASS | This table. |
| 10 | Operational Evaluator-tuning loop in SKILL.md (a–d) derived from article | PASS | SKILL.md §4 has steps a (read logs), b (diagnose), c (update artifacts), d (re-run last sprint). Derived from article quotes "read the evaluator's logs…find examples where its judgment diverged…update the QAs prompt" and "Out of the box, Claude is a poor QA agent." |

## Key design decisions and their article anchors

| Decision | Article source |
|----------|----------------|
| Generator forbidden from self-grading | "When asked to evaluate work they've produced, agents tend to respond by confidently praising the work" |
| Evaluator "default to the lower score when in doubt" | "tuning a standalone evaluator to be skeptical turns out to be far more tractable" |
| 2× weight on R1 (evidence) and R2 (company-fit) | Article's pattern of weighting criteria where the model is weak-by-default (design quality vs craft analogy) |
| Hard threshold: any criterion <3 fails the sprint | "Each criterion had a hard threshold, and if any one fell below it, the sprint failed" |
| Subagent-per-sprint = context reset by construction | "A reset provides a clean slate" where compaction cannot |
| 3-iteration cap per sprint | Article cites 5–15 iterations for frontend; spec §9 notes résumé is lower-dim so 3 suffices — documented rationale in sprint-playbook.md |
| Few-shot score 1/3/5 anchors | "I calibrated the evaluator using few-shot examples with detailed score breakdowns … reduced score drift across iterations" |
| Tuning loop is append-only via evaluator-calibration.md "Tuning record" | Article's iterative tuning: "several rounds of this development loop before the evaluator was grading in a way that I found reasonable" |
| design_memo.md on REDIRECT, orchestrator does not auto-apply fix | Keeps a human in the loop for calibration changes (article: operator reads logs, updates prompt) |

## Notable spec fidelity points

- **Trigger count:** SKILL.md frontmatter has 8 EN + 8 KO = 16 triggers,
  matching spec §1.3.
- **Length bounds:** 450–900 words, hard-coded into both the Generator
  prompt (Sprint 2 hard rule 3) and the Evaluator V2.3 check.
- **Swap-test threshold:** ≥4 sentence changes, matching spec §4.3 /
  §7 V2.4.
- **Source-tag omit-or-invent rule:** Generator prompt Sprint 2 hard
  rule 1, echoed in spec §4.2.
- **No-self-eval:** Generator prompt "The anti-cheerleader rule"
  section, echoed in spec §4.2.

## Things I deliberately did NOT do

- Did not invent a model-per-role prescription the article does not
  make. (Rule 6 said to draw from memory of the WebFetch; the memory,
  verified by a follow-up WebFetch, is that the article documents
  what was used, not a prescriptive table.)
- Did not create `assets/resume-template.md` because the spec does
  not require it and a placeholder template would violate rule 7.
- Did not auto-apply REDIRECT fixes — orchestrator writes
  `design_memo.md` and stops. Matches article's human-in-the-loop
  tuning pattern.
- Did not write a self-congratulatory "overall a strong harness"
  closer in any of the subagent prompts. That would violate the
  anti-cheerleader stance the Evaluator prompt enforces.

READY_FOR_QA: /tmp/v1-v2-exp2/v2/generator_report.md
