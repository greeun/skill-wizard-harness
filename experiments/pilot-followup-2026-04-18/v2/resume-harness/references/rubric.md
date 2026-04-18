# Rubric — resume-harness

> The Evaluator subagent grades each sprint's artifact against this
> rubric. Weights reflect where the model scores **poorly by default**
> (per the article's rubric-design principle), not what is inherently
> more important about a résumé.

---

## The four criteria

| # | Criterion | Weight | Applies to |
|---|-----------|--------|-----------|
| R1 | Evidence quality | **2×** | All sprints (tags, citations, source-fidelity) |
| R2 | Company-specific fit | **2×** | Sprint 1 (matrix), Sprint 2 (draft) |
| R3 | Professional craft | 1× | Primarily Sprint 2 (structure, length, formatting) |
| R4 | Actionability | 1× | Sprint 3 only (fit-gap items) |

For Sprints 1 and 2, R4 is N/A and the weighted aggregate is computed
over R1–R3 only. For Sprint 3, all four are scored.

---

## R1 — Evidence quality (weight 2×)

**Definition.** Every claim the artifact makes is traceable to a source
line in `intake.md`, and every source tag resolves to a real line.
Claims without citations, inflated language, and inventions all push
the score down.

**Why 2×.** LLMs are weak-by-default here: unprompted, they pad résumés
with vague consultant-speak ("led strategic initiatives to drive value").
This is the failure mode the harness exists to prevent. The article's
principle of weighting rubric criteria where the model scores poorly by
default applies directly.

**Scoring signals the Evaluator watches for:**
- Presence of `{src: ...}` tags on every bullet (Sprint 2).
- Source lines resolve to real `intake.md` lines (walk and verify).
- Specificity: metric + artifact + date beats metric alone beats vague
  prose.
- No invented credentials (employers, degrees, dates must match
  `intake.md` verbatim).

**Hard threshold:** R1 < 3 fails the sprint regardless of aggregate.

---

## R2 — Company-specific fit (weight 2×)

**Definition.** The artifact is visibly tailored to the named target
company and role. Structural choices, emphasis, and vocabulary reflect
the fit-matrix rows, not a generic template.

**Why 2×.** The other weak-by-default axis. LLMs produce résumés that
pass the swap test trivially — replace the company name and nothing
else has to change. That is the default failure.

**Scoring signals:**
- Presence of `{company: ...}` tags on structural deviations from a
  generic résumé (Sprint 2).
- Tags reference real `fit-matrix.md` rows.
- Swap test: replacing the target company name requires rewriting ≥4
  sentences (otherwise R2 ≤ 2).
- Vocabulary alignment: the résumé echoes specific phrases or framings
  visible in the fit-matrix signal column.
- Ordering alignment: section order and bullet prominence reflect
  fit-matrix priorities.

**Hard threshold:** R2 < 3 fails the sprint.

---

## R3 — Professional craft (weight 1×)

**Definition.** Structural, typographic, and length discipline. Does
the résumé look like a professional résumé, parse through ATS, and
respect standard conventions?

**Why 1×.** Strong-by-default: LLMs handle standard résumé structure
well without special prompting. Worth checking, not worth emphasizing.

**Scoring signals:**
- Length 450–900 words (Sprint 2 hard constraint).
- Verb-led bullets, consistent date format, consistent header levels.
- ATS-safe: no tables for layout, no multi-column, no images.
- Section order reflects seniority.
- Bullets of reasonably consistent length (no one-liner next to
  six-liner).

**Hard threshold:** R3 < 3 fails the sprint.

---

## R4 — Actionability (weight 1×, Sprint 3 only)

**Definition.** The fit-gap items are concrete, dated, scoped, and
verifiable. Each addresses a real gap visible in the fit-matrix.

**Why 1×.** Strong-by-default once prompted, provided the sprint
contract enforces the 2–4-item count and dating. Still worth checking
for vague success conditions.

**Scoring signals:**
- Exactly 2, 3, or 4 items.
- ISO dates on every item.
- Artifact-location scopes (not "work on X" but "publish X to
  Medium, link from résumé header").
- Success conditions are verifiable.
- No new concerns invented outside the fit-matrix.

**Hard threshold:** R4 < 3 fails Sprint 3.

---

## Aggregate scoring

Weighted sum per sprint:

- **Sprint 1 and Sprint 2:**
  `aggregate = R1*2 + R2*2 + R3*1`
  Max possible: `5*2 + 5*2 + 5*1 = 25`.
  Pass threshold: `20/25 = 80%`, AND every criterion ≥ 3.

- **Sprint 3:**
  `aggregate = R1*2 + R2*2 + R3*1 + R4*1`
  Max possible: `5*2 + 5*2 + 5*1 + 5*1 = 30`.
  Pass threshold: `24/30 = 80%`, AND every criterion ≥ 3.

**Both conditions must hold.** A weighted 80% with one criterion at 2
is a fail — the hard threshold is not a suggestion.

---

## Rationale for hard thresholds

The article notes that in full-stack coding work, "each criterion had
a hard threshold, and if any one fell below it, the sprint failed."
The same pattern applies here: a résumé that scores 5 on craft and
1 on evidence is worse than one that scores 3 across the board. The
hard threshold prevents the Generator from discovering it can sacrifice
one dimension (e.g. evidence) to inflate another (e.g. company-fit by
fabricating a match).

---

## Rationale for the 2×/1× split

Not all criteria are equal in *how much the harness adds value*. R3
(craft) and R4 (actionability) are things a single-agent run already
does passably. R1 (evidence) and R2 (company-fit) are the dimensions
where unsupervised LLM output reliably fails. The harness's value is
concentrated in enforcing those two, so they get 2× weight. The
article's frontend-design rubric makes the analogous choice, weighting
design-quality and originality (where models fail by default) over
craft and functionality (where they don't).

---

## What the rubric does NOT measure

- **Factual accuracy of company signals** beyond plausibility. The
  Evaluator is not an oracle; it cannot verify via live web search
  that "Stripe values developer velocity" is true *today*. It checks
  that signals are either from user-supplied input or marked
  `(inferred)`.
- **Applicant suitability** for the role. The rubric measures whether
  the résumé surfaces the applicant's actual fit well, not whether
  the applicant is a strong candidate. Weak candidate + strong
  résumé-harness-output → honest fit-gap with several items.
- **Aesthetic taste** beyond ATS-safety and consistency. The skill
  does not impose a single house style.
