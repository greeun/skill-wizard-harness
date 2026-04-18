# Rubric — Tailored Résumé Harness

Four weighted criteria. Each is a **quality**, not a **reference** — no brand names,
no "FAANG-caliber" or "McKinsey-style" magnets.

## Criteria

| Criterion | Weight | Weight rationale | What it measures |
|---|---|---|---|
| Evidence Quality | **2×** | Claude defaults to vague verbs and hollow outcome claims without pressure. This is the weak-by-default axis that most hurts applicants. | Every applicant claim in `resume.md` is backed by a concrete metric, project, or verifiable artifact that traces to a source line in the applicant background notes. |
| Company-Specific Fit | **2×** | Claude produces a plausible generic résumé by default. Tailoring to a specific company requires structural pressure. | The résumé visibly reflects the target company's stated priorities/values/stack. A reader who swapped the target company would notice the résumé no longer fits. Phrasings traceable to `company_brief.md`. |
| Professional Craft | 1× | Claude handles layout, section hierarchy, and length discipline adequately by default. | Typography, section structure, length discipline (1–2 pages at 11pt standard font), ATS-parseable formatting. |
| Actionability / Next-Steps | 1× | Claude can enumerate actions adequately; what it misses is dating and scoping, which the Evaluator probes. | `fit_gap.md` names 2–4 concrete action items before submission, each dated and scoped. |

**Why 2× on rows 1 and 2:** Per the source article, weight 2× the criteria where the
model is weakest by default. For résumé writing, Claude's default output is:
- plausible-sounding but unsourced claims ("led cross-functional initiatives"), and
- company-swappable generic positioning.
The 2× weighting creates the pressure that produces sourced specificity and company-visible
tailoring. Professional Craft and Actionability layout get 1× because the model already
handles them.

## Style-magnet warning

Do NOT introduce reference phrases into the criteria themselves. The criteria must stay
as qualities ("evidence-backed", "company-specific"). References like "McKinsey-caliber",
"Google-engineer-style", or "Apple-polish" belong in `spec.md` design intent ONLY, not
in the rubric, because they push the Generator toward visual/linguistic convergence with
a single brand and hurt originality.

## Scoring guide

| Score | Meaning |
|---|---|
| 5 | Exceeds expectations — a picky senior recruiter would be impressed |
| 4 | Meets expectations — solid, professional quality |
| 3 | Acceptable but noticeably weak — needs improvement |
| 2 | Below expectations — significant gaps |
| 1 | Unacceptable — fundamental problems |

Few-shot anchors per criterion live in `evaluator-calibration.md`.

## Verdict logic (hard thresholds)

```
All criteria ≥ 4 AND adversarial probes clean  → PASS
Any 2×-weighted criterion < 4                   → FAIL
Any 1×-weighted criterion < 3                   → FAIL
Any Definition-of-Done item from spec.md unverified → FAIL
```

The 2×-weighted thresholds are the whole point of the harness. Do not let the Evaluator
talk itself past them with hedges like "acceptable for a first pass".

## Calibration checkpoint

When every category is ≥ 4, apply these secondary checks before finalizing PASS:

1. **Picky recruiter lens**: would a senior recruiter with 10+ years at the target
   company's tier catch any bullet they would not believe?
2. **Hiring manager lens**: would the hiring manager ask "how exactly did you do that?"
   and find the résumé has no answer?
3. **Applicant lens**: is there a stronger proof point in the background notes that the
   résumé failed to surface?

Any finding from these checks must be written into `critique.md` before PASS is issued.
