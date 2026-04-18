# Evaluator calibration

This file is the Evaluator's anti-drift harness. The article's lesson: "Tuning a standalone evaluator to be critical is far more tractable than making a generator critical of its own work" — but only if the evaluator has concrete anchors. Without anchors, self-evaluation bias reappears in the evaluator itself.

Read this file every time you grade. Anchor your scores to these examples.

## How to use

For each axis, we show a **5-level**, a **3-level (minimum pass)**, and a **1-level** example bullet or excerpt. When scoring, ask: "which of these three is closest to what I'm reading?"

---

## Axis 1 — Evidence quality

### Score 5 example

> "Led migration of payments ledger from MySQL to CockroachDB for $TARGET's retail platform, cutting P99 transaction-commit latency from 340 ms to 95 ms across 12 services serving 8M monthly orders (Q2–Q4 2025). [source: applicant-notes L22–L28]"

Why 5: specific system named, measurable delta, time-bounded, scale-bounded, sourced.

### Score 3 example (minimum pass — but this fails hard threshold for 2× axis)

> "Led migration to a new database to improve performance across services at previous role."

Why 3: identifies the general action but has no metric, no scale, no timeframe. Source line only identifies the project existed, not the metrics. On a 2× weak-by-default axis, this scores 3 — which is a hard-threshold FAIL.

### Score 1 example

> "Drove innovative improvements to backend systems, leveraging cross-functional expertise to deliver measurable business impact."

Why 1: consultant-speak. Every word is a flag from the anti-pattern list. No system, no metric, no time. Even if the applicant's notes describe real work, this sentence would pass through any screener unchanged.

---

## Axis 2 — Company-specific fit

### Score 5 example

(Target company: Anthropic; role: research engineer)

> "Built internal evaluation harness for LLM-backed coding agents, applying the sprint-contract and independent-evaluator pattern from Anthropic's Harness Design article [source: https://www.anthropic.com/engineering/harness-design-long-running-apps — referenced in careers page evaluator team JD]."

Why 5: draws on a specific, cited Anthropic artefact; uses terminology from that artefact ("sprint-contract", "independent-evaluator"); if you swap "Anthropic" → "Google", the sentence visibly no longer fits.

### Score 3 example (minimum pass but fails hard threshold)

> "Passionate about AI safety and building responsible models."

Why 3: touches a company theme but is industry-generic. Works for Anthropic, OpenAI, DeepMind, any lab — swap test fails. Hard-threshold FAIL.

### Score 1 example

> "Interested in cutting-edge technology at a fast-moving company."

Why 1: interchangeable across industries, not just companies. Template default.

---

## Axis 3 — Professional craft

### Score 5 example (structural — you evaluate the whole document, not one bullet)

- H1: applicant name + contact (compact, one line)
- H2 sections: Summary, Experience, Projects, Skills, Education — order chosen by role type
- Each role: H3 role title + company + dates on one line; 3–5 bullets below
- Bullet verbs in consistent tense (past for past roles; present for current)
- Page count: 1 page for ≤8y, 2 pages otherwise
- No tables-for-layout, no images, no text inside shapes

### Score 3 example

- One inconsistency: skills section uses a 2-column table (ATS risk) OR the résumé runs 2.3 pages OR bullet tense mixes past and present in one role

### Score 1 example

- Multi-column layout throughout, images for section headers, non-standard fonts, unclear section boundaries

---

## Axis 4 — Actionability / next-steps (Sprint 6)

### Score 5 example

> 1. **Add case-study doc for Project Lighthouse to portfolio (by Apr 25, 2026; ~3 hrs).** Closes Sprint 4 gap "public artefact for infra work" — no writeup currently exists.
> 2. **Refresh LinkedIn headline to lead with payments-systems expertise (by Apr 20, 2026; 20 min).** Closes Sprint 4 gap "signal matching JD's payments focus."
> 3. **Line up reference from ex-manager at <Company Y> (by May 1, 2026; email + scheduling).** Closes Sprint 4 gap "senior reference in payments domain."

Why 5: each item has verb, object, date, scope, explicit gap reference. Items come from Sprint 4's matrix.

### Score 3 example (minimum pass)

> 1. Update portfolio with recent project (next 2 weeks).
> 2. Ask former manager for a reference before applying.

Why 3: 2 items, loosely scoped, one with a date. Meets the minimum but misses the gap-reference requirement.

### Score 1 example

> - Network more with people at the company.
> - Brush up on interview skills.
> - Be confident.

Why 1: life advice, unscoped, no dates, no gap mapping.

---

## Re-read before finalizing

After writing your grade, re-read this file and answer:

1. Did I use these anchors, or did I grade from vibes?
2. Did I apply the hard thresholds for 2× axes?
3. Did I do the swap test for company-specific fit?
4. Did my feedback point to file + line, or did I hand-wave?

If any answer is no, revise the grade before returning it.
