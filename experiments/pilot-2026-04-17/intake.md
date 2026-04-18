# Intake

## Domain
A skill that produces tailored résumés for specific target companies. Given a target company name,
a target role/JD, and an applicant's background notes, the skill outputs a ready-to-submit résumé
(PDF-ready markdown) plus a fit-gap analysis.

## Quality axes
1. **Evidence quality** (2× weak-by-default) — every claim about the applicant is backed by a
   concrete metric, project, or verifiable artifact. No vague "led initiatives to drive value"
   consultant-speak.
2. **Company-specific fit** (2× weak-by-default) — the résumé visibly reflects the target
   company's stated priorities/values/tech stack. A reader who swaps the target company should
   notice the résumé no longer fits.
3. **Professional craft** (1×) — typography, section structure, length discipline (1–2 pages),
   ATS-parseable formatting.
4. **Actionability / next-steps** (1×) — the accompanying fit-gap analysis names 2–4 concrete
   things the applicant should do before submitting (portfolio updates, references, certs).

## Verification approach
- Every applicant claim in the résumé has a matching line in the applicant-background notes
  (source traceability).
- Every company-specific phrasing in the résumé maps to a cited element in the target company's
  public-facing material (careers page, product docs, recent announcements).
- Fit-gap analysis has ≥2 concrete action items, each dated/scoped.
- Résumé length: 1–2 pages when rendered at standard font size.

## Sensory limits
None. Résumé is plain-text/markdown output; no audio or physical-interaction channels. Visual
craft (typography, hierarchy) is LLM-verifiable through structural cues (section order, bullet
depth, length bounds).

## Model & tier
- Target model: Opus 4.5
- Tier: Full
- Tier rationale: Even Opus 4.5 benefits from sprint contracts when the domain requires tight
  evidence traceability (every claim → source). Simplified tier would risk weakening the
  evidence-quality criterion, which is the whole point of this skill.

## User context
- Languages: bilingual (KO/EN) — trigger phrases should include both
- Special trigger phrases: "이력서 하네스", "맞춤 이력서", "company-specific resume",
  "tailored resume with evaluator", "resume harness"
