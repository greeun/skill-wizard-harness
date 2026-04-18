# Evaluator calibration — score-1/3/5 anchors per criterion

> Loaded alongside `evaluator-prompt.md` and `rubric.md`. These anchors
> exist to keep scoring stable across iterations and runs. Per the
> article: "I calibrated the evaluator using few-shot examples with
> detailed score breakdowns. This ensured the evaluator's judgment
> aligned with my preferences, and reduced score drift across
> iterations."
>
> When in doubt, match to the **lower** anchor. Drift across iterations
> (evaluator getting softer as Generator iterates) is the specific
> failure mode these anchors are here to prevent.

---

## R1 — Evidence quality

### Anchor: score 1 (fail)

**Example bullet:**
> "Led strategic initiatives to drive transformational value across
> the organization, partnering cross-functionally to deliver best-in-class
> outcomes."

Why this is a 1:
- Zero metrics.
- Zero artifacts (no PR, no doc, no launch).
- No verifiable anchor — "strategic initiatives", "transformational
  value", "best-in-class" are all placeholders.
- No `{src: ...}` tag, or a tag pointing to a nonexistent line.

### Anchor: score 3 (pass — minimum acceptable)

**Example bullet:**
> "Led migration of billing service from monolith to 4 microservices
> serving 2M requests/day. {src: background-note-L42}"

Why this is a 3:
- One concrete metric (2M rps).
- One architectural detail (4 microservices, monolith → split).
- A source tag that resolves to a real line.
- **What keeps it from being a 5:** no date, no artifact reference
  (PR / design doc), no before/after on a performance metric.

### Anchor: score 5 (exemplary)

**Example bullet:**
> "Led migration of billing service from monolith to 4 microservices
> (Q3 2024, PR #1842), reducing p99 latency from 850 ms to 210 ms.
> {src: background-note-L42}"

Why this is a 5:
- Date (Q3 2024).
- Artifact reference (PR #1842).
- Before/after metric (850 ms → 210 ms).
- Source tag resolves to a real line whose content matches the bullet
  in both scope and numbers.

---

## R2 — Company-specific fit

### Anchor: score 1 (fail)

**Example fragment:**
> ```
> # John Doe — Senior Backend Engineer
> Experienced engineer passionate about building quality software.
> ```

Why this is a 1:
- No evidence the résumé was written for a particular company.
- Swap test: replace target company name with any other — nothing
  needs to change.
- No `{company: ...}` tags anywhere in the document.
- Summary could be copy-pasted across 50 applications.

### Anchor: score 3 (pass — minimum acceptable)

**Example fragment (target company: a Go-heavy infra company):**
> ```
> ## Skills {company: careers-page-lists-stack-first}
> - Go, Kubernetes, gRPC, Postgres, Terraform
> ```
>
> (But the Experience section and Summary are unchanged from a generic
> version.)

Why this is a 3:
- One structural choice (Skills above Experience, Go first) is
  company-tagged and references a real fit-matrix row.
- But only one visible deviation — the rest of the document is
  interchangeable.
- Swap-test count: ~4 sentence changes needed (right at threshold).

### Anchor: score 5 (exemplary)

**Example fragment (target company: observability-focused SaaS):**
> ```
> ## Summary {company: observability-as-product}
> Backend engineer focused on developer velocity and operational
> telemetry; built MTTR-reducing tooling for on-call rotations at a
> 2000-engineer company.
>
> ## Skills {company: careers-page-lists-stack-first}
> - Go, OpenTelemetry, Prometheus, Kubernetes
>
> ## Experience
> - Reduced incident MTTR from 47 min to 12 min via structured runbook
>   + alert-grouping service (Q2 2024, PR #904). {src:
>   background-note-L23} {company: observability-as-product}
> ```

Why this is a 5:
- Summary echoes two specific company signals ("developer velocity",
  "operational telemetry" matches "observability as product").
- Skills reordered to match the company's stack.
- An Experience bullet is reframed to highlight the same outcome
  metric (MTTR) the company emphasizes.
- Multiple `{company: ...}` tags, all pointing to real fit-matrix rows.
- Swap-test count: ≥8 sentence changes would be needed.

---

## R3 — Professional craft

### Anchor: score 1 (fail)

**Example:**
- Résumé is 3.5 pages.
- Mixed font sizes within a section.
- Uses a two-column table for Skills (breaks ATS parsers).
- Bullets range from 1 line to 6 lines in the same section.
- Inconsistent date formats ("Jan 2024", "2024-01", "1/2024" all
  appear).

Why this is a 1: failed on every craft axis at once.

### Anchor: score 3 (pass — minimum acceptable)

**Example:**
- 2 pages (within limit).
- Consistent headers (all `##` for sections).
- ATS-safe (no layout tables).
- But some bullets run to 3 lines.
- Sections not seniority-prioritized (Education above Experience for a
  10-year senior).

Why this is a 3: structurally sound but not optimized. A reader can
parse it; a hiring manager won't object.

### Anchor: score 5 (exemplary)

**Example:**
- 1.5 pages.
- Every bullet is 1–2 lines with a strong verb-led opener.
- Consistent ISO-style date formatting throughout.
- Section order reflects seniority (senior: Experience → Skills →
  Education).
- Consistent bullet density across sections.
- Clean visual rhythm — no visually jarring blocks.

---

## R4 — Actionability (Sprint 3 only)

### Anchor: score 1 (fail)

**Example item:**
> "Work on your weaknesses before applying."

Why this is a 1:
- Vague ("your weaknesses").
- Undated.
- Unscoped (no artifact location).
- No success condition.
- Not tied to a fit-matrix gap row.

### Anchor: score 3 (pass — minimum acceptable)

**Example item:**
> "Add a portfolio project showing Go microservices experience."

Why this is a 3:
- Concrete topic (Go microservices).
- But no date.
- No artifact location (portfolio page? GitHub repo? Blog?).
- No verifiable success condition.

### Anchor: score 5 (exemplary)

**Example item:**
> ### Item 1 — Publish migration case study
> - **Addresses gap:** fit-matrix row 4 (Go microservices depth).
> - **Action:** Write a 2-part blog post on the monolith-to-microservices
>   migration described in Experience bullet 2.
> - **Scope:** Publish on Medium (or personal blog); link from résumé
>   header.
> - **Date:** by 2026-05-15.
> - **Success condition:** Post is published and URL appears in line 3
>   of `resume-final.md`.

Why this is a 5: dated, scoped to an artifact location, references a
specific fit-matrix row, and the success condition is objectively
checkable.

---

## Calibration notes for the Evaluator

- **Borderline cases default to the lower anchor.** A bullet that has
  a metric + date but no artifact reference is a 3, not a 4. Don't
  round up because "the Generator tried hard."
- **A score of 4 means "above 3 but not yet 5."** Use it when the
  artifact exceeds the 3 anchor on at least one axis but falls short
  of the 5 anchor on another. Do not use 4 as a polite 3.
- **A score of 2 means "below 3 but not totally vacuous."** A bullet
  that has a metric but no citation is a 2 (R1), not a 3.
- **When a criterion spans many bullets,** score the *weakest* bullet
  class that appears more than once, not the average. One uncited
  bullet in a 30-bullet résumé is a procedural error and drops R1 to
  2–3; five uncited bullets is a systemic failure and drops R1 to 1.
- **Do not import anchors across criteria.** A great R2 (company fit)
  does not inflate R1 (evidence). Score each criterion against its own
  anchors, independently.

---

## Tuning record (append-only)

When the harness operator tunes calibration (per the operational loop
in `SKILL.md` §4), append a dated note here describing the divergence
and the fix. This is the article's "read the evaluator's logs, find
examples where its judgment diverged from mine, and update the QAs
prompt" loop, materialized.

Format:

```
## 2026-<MM>-<DD>
- Divergence: <what the evaluator missed or over-approved>
- Fix: <what anchor was added or which check was sharpened>
- Re-run result: <did the evaluator now catch it?>
```

_(No entries yet — first tuning pass will populate.)_
