# Sprint Playbook (Full Tier) — Tailored Résumé Harness

Full tier splits the work into three sprints, each with its own sprint contract and
independent Evaluator pass. This catches drift earlier than a single end-of-run review.

## Sprint 1 — Company Brief

**Generator deliverable:** `company_brief.md` — 3–6 stated priorities/values/tech-stack
elements of the target company, each with a verbatim source URL (careers page, engineering
blog, recent announcement, product docs).

**Sprint contract template:**
```
# Sprint 1 Contract — Company Brief
## Deliverables
- company_brief.md

## Observable checks the Evaluator will run
- [ ] Brief lists 3–6 priorities, each with a source URL that resolves.
- [ ] Each priority has a verbatim quote ≤ 25 words from the source, plus one sentence
      of paraphrase.
- [ ] At least one priority comes from a source published within the last 90 days
      (recency check).
- [ ] No fabricated claims — the Evaluator will spot-check two URLs by re-fetching.

## Format
- Markdown. Headings per priority. Source URL on its own line.
```

**Evaluator adversarial probes for Sprint 1:**
- Re-fetch at least two cited URLs — does the quoted phrase actually appear?
- Does the brief avoid fluff priorities like "innovation" unsupported by evidence?
- Are the priorities SPECIFIC enough that the résumé can reflect them structurally,
  not just cosmetically?

---

## Sprint 2 — Evidence Ledger

**Generator deliverable:** `evidence_ledger.md` — a table of every candidate résumé claim
mapped to a specific line in the applicant background notes.

**Sprint contract template:**
```
# Sprint 2 Contract — Evidence Ledger
## Deliverables
- evidence_ledger.md

## Observable checks the Evaluator will run
- [ ] Every row has: {claim_id, proposed_bullet, source_line_reference, verification_status}.
- [ ] verification_status is one of: SOURCED, WEAK, MISSING.
- [ ] Any WEAK or MISSING claim is flagged for applicant confirmation; it MUST NOT appear
      in resume.md until confirmed.
- [ ] Every candidate bullet the Generator intends to use is in the ledger — no "I'll
      add this one at draft time".

## Format
- Markdown table.
```

**Evaluator adversarial probes for Sprint 2:**
- Sample 3 rows — does the source_line_reference actually support the claim?
- Any claim with no metric but with a strong verb ("led", "owned") — is it SOURCED or
  should it be WEAK?
- Is the ledger comprehensive, or does it conveniently omit weaker areas of the
  applicant's background?

---

## Sprint 3 — Résumé + Fit-Gap

**Generator deliverable:** `resume.md` and `fit_gap.md`.

**Sprint contract template:**
```
# Sprint 3 Contract — Résumé + Fit-Gap
## Deliverables
- resume.md (PDF-ready markdown, 1–2 pages at 11pt standard font)
- fit_gap.md (2–4 dated/scoped action items)
- generator_report.md (with Strategic Decision block)

## Observable checks the Evaluator will run
- [ ] Every résumé bullet maps to a SOURCED row in evidence_ledger.md.
- [ ] Every company-specific phrasing maps to a cited element in company_brief.md.
- [ ] Length: 1–2 pages at 11pt.
- [ ] fit_gap.md has 2–4 action items, each dated AND scoped.
- [ ] No WEAK/MISSING ledger claims appear in resume.md.
- [ ] Swap test: mentally replacing the target company name breaks the fit (not cosmetic).

## Format
- resume.md: standard résumé markdown structure.
- fit_gap.md: numbered action items, date, scope, rationale.
```

**Evaluator adversarial probes for Sprint 3:** full rubric from `rubric.md`; full
adversarial probes from `evaluator-prompt.md`.

---

## When to remove sprints (model-upgrade path)

Per the article, remove harness components **one at a time** when model capability improves.
Suggested removal order for this skill (do not remove two at once):

1. Sprint 1 separate contract → absorbed into Planner spec (when company research is
   reliably accurate with fewer hallucinations).
2. Sprint 2 separate contract → absorbed into Generator self-verification (when model
   reliably refuses to write unsourced bullets).
3. Final step: collapse to Simplified tier (single Generator run + one end-of-run
   Evaluator pass capped at 3–5 rounds).

Measure after each removal — if evidence-quality or company-fit scores drop, restore the
sprint. Radical simplification has been shown to fail; methodical removal is required.
