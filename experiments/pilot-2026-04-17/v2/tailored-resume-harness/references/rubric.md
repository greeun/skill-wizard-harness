# Rubric — Tailored Résumé Harness

The Evaluator grades every sprint's delivered artifact against these four criteria. 2× weight
on the two axes where Claude is weak-by-default for this domain. 1× on the two Claude handles
adequately without pressure.

## Design principle

Per the source article's rubric design guide: *"Weight 2× the criteria where Claude is weakest.
Weight 1× where it's already adequate."* For résumé tailoring, Claude's defaults are:

- **Weak:** grounding claims in specific sources (Claude smooths specifics into
  professional-sounding prose); writing company-specific voice (Claude writes "universally
  employable" résumés — generic enough to send anywhere).
- **Adequate:** producing clean markdown structure; generating "next steps" lists when asked.

So we 2× weight Evidence Quality and Company-Specific Fit; 1× weight Professional Craft and
Actionability.

Per the article's Style-Magnet warning, criteria are phrased as qualities ("evidence quality",
"company-specific fit"), NOT as references ("FAANG-caliber", "McKinsey-style"). Company
references appear in `spec.md` intent only, never in the rubric wording.

---

## Criteria

| # | Criterion | Weight | Measures | Why this weight |
|---|---|---|---|---|
| C1 | **Evidence Quality** | **2×** | Every bullet about the applicant cites a concrete artifact (metric, shipped project, named employer, date, URL) in applicant-notes. No consultant-speak. | Claude smooths vagueness into professional-sounding prose by default; this forces a source-citation check per bullet. |
| C2 | **Company-Specific Fit** | **2×** | Language, skill emphasis, and ordering visibly reflect the target company's stated priorities. A reader swapping the company name would notice the résumé no longer fits. | Claude writes "universally employable" résumés by default — generic enough to send anywhere, specific enough for nowhere. |
| C3 | **Professional Craft** | 1× | ATS-parseable markdown, 1–2 page render length, consistent bullet depth, section order follows industry convention, no typos. | Claude already produces clean markdown structure without pressure. |
| C4 | **Actionability (fit-gap)** | 1× | Fit-gap analysis names ≥ 2 concrete, dated, scoped action items (portfolio updates, references, certs, practice interview topics). | Claude already generates "next steps" lists when asked; 1× keeps it honest without over-indexing. |

## Scoring scale

| Score | Meaning |
|-------|---------|
| 5 | Exceeds expectations — a picky senior recruiter would be impressed |
| 4 | Meets expectations — solid, professional quality |
| 3 | Acceptable but noticeably weak — needs improvement |
| 2 | Below expectations — significant gaps |
| 1 | Unacceptable — fundamental problems |

See `references/evaluator-calibration.md` for concrete 1/3/5 anchors per criterion. Use
anchors, not intuition.

## Verdict logic

```
All criteria ≥ 4 AND adversarial probes clean → PASS
Any 2×-weighted criterion (C1 or C2) < 4      → FAIL
Any 1×-weighted criterion (C3 or C4) < 3      → FAIL
Any Definition-of-Done item from spec.md §8 unverified → FAIL
Any source-traceability violation (bullet without citation, fabricated metric) → FAIL with
  ETHICAL severity tag
Any PII leak (SSN, full DOB, government ID, full home address) → FAIL with SECURITY
  severity tag
```

## Calibration checkpoint

If the Evaluator scores every criterion ≥ 4 on first pass, it must apply these secondary
lenses and add findings to `critique.md` before finalizing:

1. **ATS parser lens.** Mentally parse the résumé with a standard ATS. Are section names
   recognized? Do any bullets rely on tables, images, or multi-column markdown that would
   break parsing? If yes, C3 drops.
2. **Hiring-manager first-30-second skim lens.** Read only the top third of the résumé.
   What does the reader remember? "Senior engineer" = generic; "Shipped payment migration
   at Stripe-relevant scale with verified metric" = specific. If it's generic, C2 drops.
3. **Competitor-swap lens.** Swap the target company for a direct competitor. If the résumé
   still fits, C2 is capped at 3.

## Domain-specific invariants (beyond the criteria)

The Evaluator also verifies these invariants every sprint — they can individually trigger a
FAIL regardless of rubric scores:

1. **Source-index integrity.** Sprint 2 bullets must all be in the Sprint 1 approved
   source-index. New bullets require a `design_memo.md` approved by the Evaluator.
2. **Company-citation freshness.** Every company-material citation in spec.md §6 must be
   dated within the last 24 months.
3. **No-fabrication.** Every metric on the résumé must appear in applicant-notes (same
   number, same units). Rounding OK, invention FAIL.
4. **Length render check.** Estimate rendered page count at ~450 words/page. > 2 pages =
   hard FAIL. > 1.75 pages = warning.
5. **ATS section-header compliance.** Use Experience / Work Experience, Education, Skills,
   Projects, Certifications. Creative headers = FAIL C3.
6. **Output-language consistency.** If Gate-A picked language X, the résumé is fully in X
   (proper nouns preserved).
7. **PII leak check.** No SSN, full DOB, government ID, full home address.
8. **Fit-gap referentiality.** Every fit-gap action item tied to a specific gap documented
   in generator_report.md.
