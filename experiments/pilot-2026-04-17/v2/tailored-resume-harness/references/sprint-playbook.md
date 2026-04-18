# Sprint Playbook — Tailored Résumé Harness (Full tier)

Full per-sprint instructions for the orchestrator, Generator, and Evaluator. Referenced from
SKILL.md §Activation flow steps 8, 9, 10.

Each sprint follows the same shape: contract negotiation → generation → evaluation → iterate
or proceed. The contents differ. This file freezes the per-sprint deliverables, contract
templates, and pass conditions.

---

## Sprint 1 — Outline + Source-Index

**Goal.** Establish the résumé's structure and the full mapping of planned bullets to
sources, before any prose is written. Catching an unsourced claim at this stage costs one row
in a table; catching it in Sprint 2 costs a full rewrite.

**Deliverables from Generator.**
- `outline.md` — ordered list of résumé sections with bullet-count targets per section and a
  1-line summary per planned bullet.
- `source-index.md` — table with columns: Section | Bullet summary | Source type
  (applicant-notes / company-URL) | Source citation (L<n> or full URL).

**Sprint 1 contract template** (Generator writes `sprint_contract.md` → Evaluator approves or
amends):

```
# Sprint 1 Contract

## Deliverables
- outline.md (sections + bullet-count targets + 1-line bullet summaries)
- source-index.md (table: Section | Bullet | Source type | Source citation)

## Observable checks the Evaluator will run
1. Every row in source-index.md has a non-empty Source citation column.
2. Every applicant-notes citation is a valid line number that exists in the notes.
3. Every company URL resolves and contains the cited phrase/element.
4. Every company URL is dated within the last 24 months (per spec.md §6 freshness rule).
5. The outline section order is one of the ATS-accepted patterns (Experience leads for
   experienced applicants; Education leads only for new grads per spec.md).
6. Bullet-count targets per section sum to a word count that will render to ≤ 2 pages
   (rough budget: 12–18 bullets total, each ~15–25 words in final prose).
7. Differentiation hooks from spec.md §3 have a visible home in the outline.

## Definition of Done for Sprint 1
- [ ] outline.md covers every required section from spec.md §4.
- [ ] source-index.md has one row per planned bullet.
- [ ] Zero rows with a missing Source citation.
- [ ] No invented sources (every citation checkable).
```

**Generator process.** Follow generator-prompt.md rule 3.b. Build the outline from spec.md §4;
walk applicant-notes line by line and tag each line to at most one planned bullet; walk the
target-company URLs in spec.md §6 and tag each cited phrase to at most one planned bullet;
assemble the source-index table.

**Evaluator process.** Run Sprint 1 adversarial probes 1, 6 (company-citation), 8 (ATS
section headers). Skip probes 2–5, 7, 9 until prose exists. Apply domain invariants 1, 2, 5.

**Pass condition.** Rubric in Sprint 1 is partial — C1 (Evidence Quality) is measured against
source-index completeness, not prose. C2 (Company-Specific Fit) is measured against section
ordering and differentiation-hook placement. C3 (Professional Craft) is partial (headers and
structure only). C4 (Actionability) is N/A — not applicable until Sprint 3. PASS requires C1
and C2 ≥ 4 and C3 ≥ 3.

---

## Sprint 2 — Full Draft Résumé

**Goal.** Convert the approved Sprint-1 outline + source-index into final prose bullets.
Every prose bullet stays within the approved source-index — no new bullets without a
`design_memo.md` approved by the Evaluator.

**Deliverables from Generator.**
- `resume.md` — markdown résumé, submission-ready, 1–2 pages rendered.

**Sprint 2 contract template:**

```
# Sprint 2 Contract

## Deliverables
- resume.md (full markdown résumé, 1–2 pages rendered)

## Observable checks the Evaluator will run
1. Every bullet in resume.md maps to exactly one row in the approved source-index.md.
2. No new bullets without an approved design_memo.md.
3. Every metric on the résumé appears in applicant-notes at the cited line (same number,
   same units; rounding allowed, invention not).
4. Every company-facing phrase matches the cited target-company URL.
5. No consultant-speak verb ("drove / leveraged / spearheaded / orchestrated / championed")
   without a metric in the same bullet.
6. Length renders to ≤ 2 pages at ~450 words/page.
7. Section headers are ATS-standard (Experience, Education, Skills, Projects,
   Certifications).
8. Output language is consistent with Gate-A choice (proper nouns preserved).
9. No PII beyond name, email, phone, city, LinkedIn/portfolio URL.

## Definition of Done for Sprint 2
- [ ] Every spec.md §8 Definition-of-Done item verifiable against resume.md.
- [ ] No bullet has been added since Sprint 1 without approved design_memo.md.
- [ ] Generator's self-review (generator-prompt.md rule 5 step e) passed.
```

**Generator process.** Follow generator-prompt.md rule 3.c. For each source-index row, draft
the final bullet; verify paraphrase stays within the source (no upgraded claims); check
metric-for-metric accuracy against applicant-notes; check company-facing phrase against
cited URL; read the bullet aloud for consultant-speak tells.

**Evaluator process.** Run ALL 9 adversarial probes. Apply all domain invariants 1–7 (8 is
Sprint 3 only).

**Pass condition.** Full rubric applies except C4 (fit-gap not yet written). PASS requires
C1 ≥ 4, C2 ≥ 4, C3 ≥ 4, all invariants 1–7 clean, all probes clean.

---

## Sprint 3 — Fit-Gap Analysis

**Goal.** Produce a dated, scoped action-item list tied to gaps observed during Sprint 1 or
2 (documented in generator_report.md).

**Deliverables from Generator.**
- `fit-gap.md` — 3–5 action items, each with verb + artifact + date + scope + stated
  rationale tied to an observed gap.

**Sprint 3 contract template:**

```
# Sprint 3 Contract

## Deliverables
- fit-gap.md (3–5 dated, scoped action items)

## Observable checks the Evaluator will run
1. Every action item has a verb (e.g., "push", "email", "drill", "remove").
2. Every action item has a concrete artifact (specific file, specific person, specific
   topic).
3. Every action item has a date or time-boxed window (e.g., "by Thu evening", "before
   submission within 72h", "over the weekend").
4. Every action item has scope (which README, which references, which system-design topic).
5. Every action item links to a specific gap observed in Sprint 1 or 2 — documented in
   generator_report.md.
6. Language matches Gate-A choice.

## Definition of Done for Sprint 3
- [ ] fit-gap.md has ≥ 3 items (spec.md §5 minimum).
- [ ] Every item satisfies verb + artifact + date + scope.
- [ ] Every item's rationale cites a Sprint-1-or-2 observation.
- [ ] No generic items ("prepare for the interview") without scoping.
```

**Generator process.** Follow generator-prompt.md rule 3.d. Walk the Sprint-1 and Sprint-2
observed-gaps list from generator_report.md; for each gap, propose an action item with verb
+ artifact + date + scope; verify each item is executable this week.

**Evaluator process.** Run adversarial probe 9 (fit-gap actionability) in full. Apply domain
invariant 8 (fit-gap referentiality).

**Pass condition.** Full rubric applies. C4 becomes live. PASS requires C1 ≥ 4 (no new
fabrication), C2 ≥ 4 (fit-gap still tailored), C3 ≥ 4, C4 ≥ 4.

---

## Iteration handling across sprints

- Iteration cap: 10 per sprint (per SKILL.md §Iteration wisdom and spec.md §4). If a sprint
  hits cap without passing, orchestrator escalates to user per SKILL.md activation flow.
- Each iteration produces `critique_v<n>.md` — do not overwrite prior critiques. Evaluator
  must read prior versions to write the Iteration Quality Note.
- On FAIL: fresh Generator session dispatched with spec.md + critique_v<n>.md + prior
  deliverables only. No conversation carryover.
- On HANDOFF_NEEDED: fresh Generator with spec.md + handoff.md + partial deliverables.
  Compaction forbidden.
- On DEADLOCK: orchestrator escalates to user; if user amends spec.md, restart the current
  sprint from contract negotiation.

## Cross-sprint invariants

- Sprint 2 never introduces a bullet absent from the Sprint 1 approved source-index without
  a `design_memo.md` pre-approved by the Evaluator (source-index integrity invariant).
- Sprint 3 never introduces a claim about the applicant that contradicts resume.md or
  applicant-notes.
- If Sprint N surfaces a spec contradiction, Generator outputs DEADLOCK rather than
  silently patching.
