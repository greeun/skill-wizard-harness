# Evaluator Calibration — Few-Shot Anchors

These 2/5, 4/5, 5/5 anchors calibrate the Evaluator across iterations and across runs.
The article: *"calibrating the evaluator using few-shot examples with detailed score
breakdowns ensured the evaluator's judgment aligned with my preferences, and reduced
score drift across iterations."*

When the Evaluator scores a criterion, it must compare the actual output to these anchors.

---

## Evidence Quality (2×)

**2/5 — below expectations:**
> "Led cross-functional initiatives to drive strategic value for the organization, partnering
> with stakeholders to deliver outcomes aligned to business goals."
>
> Why 2/5: Vague verbs ("led", "drive", "deliver"). No metric, no named artifact, no
> verifiable system. Could be written about any applicant in any industry. The evidence
> ledger either has no source line or the source line is equally vague.

**4/5 — solid, professional:**
> "Led migration of order-processing service from Rails to Go; cut p99 latency from 820ms
> to 180ms (measured on Datadog, Q2 2024); service now handles 2.1× prior peak load."
>
> Why 4/5: Named system, before/after metric, measurement tool, dated. Source line in
> background notes reads "order-processing migration to Go, latency 820→180ms (Datadog
> dashboard link)". Traceable.

**5/5 — exceptional:**
> "Led migration of order-processing service from Rails to Go; cut p99 latency from 820ms
> to 180ms (Datadog, Q2 2024). Architecture decision record published at <repo-url>/adr/014;
> referenced by three downstream teams adopting the same pattern."
>
> Why 5/5: Everything in 4/5 plus a verifiable external artifact (ADR URL) and a
> second-order impact (downstream adoption). The claim survives a hiring manager pulling
> up the ADR in the interview.

---

## Company-Specific Fit (2×)

**2/5 — below expectations:**
> Résumé summary: "Experienced engineer passionate about building high-quality software
> for innovative companies."
>
> Why 2/5: Zero company-specific phrasing. Swap "innovative companies" for the target
> company name and nothing changes. Company-fit is cosmetic only.

**4/5 — solid, professional:**
> Résumé summary: "Infrastructure engineer focused on reliability at consumer scale;
> experience aligns with [TargetCo]'s stated priority on minimizing customer-visible
> incidents (careers page, 2025-11 post-mortem culture section)."
>
> Why 4/5: Names a specific TargetCo priority with a sourced URL fragment. A reader
> swapping the company name would notice the résumé no longer fits.

**5/5 — exceptional:**
> Résumé summary weaves TargetCo's stated pillar language ("signal over noise",
> "customer-visible reliability") into the applicant's actual experience at prior employer,
> and the Selected Projects section leads with a project whose architecture mirrors a
> pattern TargetCo publicly described in their 2025 engineering blog — cited inline.
>
> Why 5/5: Fit is structural (section ordering reflects TargetCo's priorities) AND
> linguistic (TargetCo's own vocabulary appears naturally), AND cited.

---

## Professional Craft (1×)

**2/5:** Résumé is 4 pages; mixed fonts; "Skills" section contains 80 items in alphabetical
order with no grouping. ATS would shred it.

**4/5:** 1.7 pages at 11pt; consistent section hierarchy (H2 for role employers, H3 for
role titles); skills grouped into 3–4 themed buckets; dates right-aligned; no orphan lines.

**5/5:** Everything in 4/5 plus deliberate whitespace that guides the eye to the two
strongest bullets per role; no wasted space; fits cleanly on standard letter AND A4.

---

## Actionability / Next-Steps (1×)

**2/5:**
> fit_gap.md:
> - Update portfolio
> - Get a reference
>
> Why 2/5: No date, no scope, not executable this week.

**4/5:**
> fit_gap.md:
> 1. By 2026-04-24: publish a case-study write-up of the order-processing migration
>    project on personal blog; link from résumé header.
> 2. By 2026-04-28: request a written reference from former manager Jane Kim (email draft
>    ready at `drafts/jane-reference-email.md`).
> 3. By 2026-05-01: complete the free TargetCo Open Source contributor onboarding
>    (link in company_brief.md).
>
> Why 4/5: Each item is dated, scoped, and executable. Three items sits in the 2–4 range.

**5/5:** Everything in 4/5 plus an explicit sequencing rationale ("complete #3 before #2
so the reference can cite your OSS contribution") and a fallback for each item if blocked.

---

## Calibration maintenance

When a calibration anchor stops matching a picky human recruiter's judgment, the harness
operator edits this file (operational tuning loop step c in SKILL.md §Evaluator tuning
workflow). Do NOT silently adjust scoring.
