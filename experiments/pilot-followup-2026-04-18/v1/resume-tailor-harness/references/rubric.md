# Rubric — résumé tailoring

This rubric is the Evaluator's scoring instrument for the rubric-scored sprints (Sprint 5 résumé draft; Sprint 6 fit-gap analysis). The four axes come from the user's intake. The weights make the two weak-by-default axes twice as heavy.

## Weight summary

| Axis | Weight | Weak-by-default? | Hard threshold |
|------|--------|------------------|----------------|
| Evidence quality | 2× | yes | score ≥4 to PASS |
| Company-specific fit | 2× | yes | score ≥4 to PASS |
| Professional craft | 1× | no | score ≥3 to PASS |
| Actionability / next-steps | 1× | no (Sprint 6 only) | score ≥3 to PASS |

Total possible weighted score: (2+2+1+1) × 5 = **30**.

PASS requires **all four** hard thresholds AND weighted total ≥22.

FAIL on either weak-by-default axis fails the sprint regardless of total.

## Axis 1 — Evidence quality (2×)

**What it measures:** Every claim about the applicant is backed by a concrete metric, project, or verifiable artefact from the applicant notes. No vague consultant-speak.

| Score | Description |
|-------|-------------|
| 5 | Every bullet has a specific metric/artefact/named system. Every claim has a matching line in `evidence_map.md`. No banned consultant-speak. Metrics are measurable (numbers, percentages, time, scale). |
| 4 | ≥90% of bullets specific and sourced. ≤2 minor softenings (e.g., "improved" instead of a number) but not on headline items. |
| 3 | ~70% specific. Several bullets are plausible but unsourced. 1–2 instances of banned consultant-speak. **Hard-threshold FAIL.** |
| 2 | Résumé reads plausibly but evidence_map.md has holes. Consultant-speak common. |
| 1 | Most bullets are unverifiable or invented. |

**Watch-for failures:**
- "Led cross-functional initiatives" with no specific team size, timeframe, or outcome
- "Significantly improved performance" with no number
- Claims about seniority ("principal-level impact") without role/title evidence
- Technologies listed in a skills bar that don't appear in any project bullet

## Axis 2 — Company-specific fit (2×)

**What it measures:** The résumé visibly reflects the target company's specific priorities, values, tech stack, or recent announcements — not industry-generic AI/tech themes.

| Score | Description |
|-------|-------------|
| 5 | At least 4 distinct company-specific phrasings, each sourced in `company_fit_map.md`. Swap test clearly breaks the résumé. Language echoes terminology from the company's own material (careers page, product docs, recent announcements). |
| 4 | 2–3 distinct company-specific phrasings, all sourced. Swap test noticeably breaks it. |
| 3 | Only 1 specific phrasing, or phrasings are industry-generic rather than company-specific. **Hard-threshold FAIL.** |
| 2 | Could be sent to any company in the industry. |
| 1 | Looks like a stock template. |

**Swap test protocol:** mentally replace the target company's name with a peer's (e.g., if target is Anthropic, replace with OpenAI). Does the résumé still read as if it were written for the new company? If yes → FAIL. The article's principle: "penalize telltale AI patterns" — a résumé that works for any company is a telltale AI pattern.

## Axis 3 — Professional craft (1×)

**What it measures:** Typography, section hierarchy, length discipline, ATS-parseability.

| Score | Description |
|-------|-------------|
| 5 | 1–2 pages at standard font. Clear section hierarchy (H1 name, H2 sections, bullet bodies). Section order fits role type. ATS-parseable (no tables for layout, no images, no text in shapes). Consistent bullet verb tense. |
| 4 | 1 craft issue (e.g., 2.2 pages, or one inconsistent heading level). |
| 3 | 2 craft issues. Pass minimum. |
| 2 | Length out of range OR ATS-blocking structures present. |
| 1 | Unreadable structure, ignores format. |

**Length targets by experience:**
- ≤8 years total experience: 1 page (≈500–900 words content)
- \>8 years: 2 pages (≈900–1500 words)

## Axis 4 — Actionability / next-steps (1×, Sprint 6 only)

**What it measures:** The fit-gap analysis gives the applicant 2–4 concrete things to do before submitting.

| Score | Description |
|-------|-------------|
| 5 | 3–4 action items, each with verb + object + date + scope + explicit gap reference. Items are scoped work (e.g., "add case-study for project X to portfolio by May 1 — closes gap on public-artifact axis"). |
| 4 | 2–3 items, all well-scoped. Minor looseness on date or scope. |
| 3 | Exactly 2 items, at least one well-scoped. Pass minimum. |
| 2 | 1 scoped item and filler. |
| 1 | Vague advice ("network more", "build your brand"). |

**Forbidden action item shapes:**
- Life advice ("be more confident")
- Unscoped ("improve skills")
- Unrelated to Sprint 4 gaps (every action must cite the gap it closes)

## How the Evaluator uses this rubric

1. Score each axis 1–5 based on the descriptor closest to what the output demonstrates.
2. Apply weights: weighted_score = raw × weight.
3. Check the four hard thresholds.
4. PASS requires all thresholds met AND weighted total ≥22.
5. Any FAIL triggers specific, file-located feedback to the Generator.
