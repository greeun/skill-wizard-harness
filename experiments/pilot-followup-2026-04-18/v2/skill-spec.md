# Skill Spec — `resume-harness` (frozen)

> Spec produced by the PLANNER subagent of the skill-wizard-harness v2 meta-harness.
> Source of design patterns: Prithvi Rajasekaran, *Harness Design for Long-Running
> Agents* (Anthropic Labs, 2026-03-24). Source of domain requirements:
> `/tmp/v1-v2-exp2/intake.md`.
>
> This spec is **frozen**: the Generator subagent must implement it verbatim. Any
> deviation is a bug. The Evaluator subagent will check the built skill against this
> spec (not against its own judgment of what a résumé skill should be).

---

## 1. Metadata

### 1.1 skill-name
`resume-harness`

### 1.2 Directory structure
```
resume-harness/
├── SKILL.md                       # Orchestrator + intake (body ≤500 lines)
├── references/
│   ├── planner-prompt.md          # Planner subagent system prompt
│   ├── generator-prompt.md        # Generator subagent system prompt
│   ├── evaluator-prompt.md        # Evaluator subagent system prompt
│   ├── rubric.md                  # 4 criteria, weights, score-1/3/5 anchors
│   ├── sprint-contract-template.md# File-based handoff schema
│   └── article-patterns.md        # Which article patterns this skill uses
└── assets/
    └── resume-template.md         # Markdown skeleton (sections, ATS-safe)
```

No scripts. The skill is prompt-only; it orchestrates subagents, not code.

### 1.3 description (frontmatter) — must include ≥8 bilingual triggers

> Produce a target-company-tailored résumé plus fit-gap analysis using a Planner →
> Generator → Evaluator harness adapted from Anthropic's *Harness Design for
> Long-Running Agents* (Rajasekaran, 2026). Sprint contracts enforce evidence
> traceability (every claim → source) and company-specific fit; an external
> Evaluator subagent grades against a weighted rubric before approval.
> Triggers — EN: "resume harness", "tailored resume with evaluator",
> "company-specific resume", "multi-agent resume", "resume harness skill",
> "planner generator evaluator resume", "job application harness",
> "resume with fit-gap analysis". KO: "이력서 하네스", "맞춤 이력서",
> "회사 맞춤 이력서", "이력서 플래너 제너레이터", "하네스로 이력서 작성",
> "이력서 자율 작성", "이력서 멀티에이전트", "적합성 분석 이력서".

(Trigger count: 8 EN + 8 KO = 16, exceeds ≥8 requirement.)

---

## 2. Domain

### 2.1 Domain statement (one paragraph)

The skill converts three inputs — a **target company name**, a **target role / JD**,
and **applicant background notes** — into two outputs: a **PDF-ready markdown
résumé** (1–2 pages, ATS-parseable) and a **fit-gap analysis** listing 2–4 concrete
pre-submission action items. The domain's defining tension is that LLMs, left
unsupervised, produce résumés that sound fluent but fail on two specific axes:
claims lack concrete backing (vague consultant-speak) and the résumé is not
visibly tailored (the target company could be swapped with no rewrite). These two
axes are therefore weak-by-default and receive 2× rubric weight, following the
article's principle that rubrics should emphasize "dimensions where the model
scores poorly by default" (Rajasekaran 2026, §Rubric-Based Evaluation).

### 2.2 Rubric criteria (4 dimensions, matching intake.md §Quality axes)

| # | Criterion | Weight | Rationale |
|---|-----------|--------|-----------|
| R1 | Evidence quality | **2×** | Weak-by-default: LLMs pad with unsupported claims. Central to skill's value. |
| R2 | Company-specific fit | **2×** | Weak-by-default: LLMs produce generic résumés. The whole point of tailoring. |
| R3 | Professional craft | 1× | Strong-by-default: LLMs already handle typography/length/ATS structure well. |
| R4 | Actionability (fit-gap) | 1× | Strong-by-default once prompted, but must be checked for concreteness. |

The 2×/1× split mirrors the article's handling of design-quality-and-originality vs
craft-and-functionality in the frontend-design harness.

### 2.3 Hard thresholds (per article §Full-Stack Coding Criteria)

Any criterion scoring below **3/5** fails the sprint, regardless of aggregate. The
Generator must re-iterate. This prevents the known failure where one dimension is
sacrificed to boost another (e.g. inventing impressive claims to improve
"company-fit" at the cost of "evidence quality").

---

## 3. Harness tier

**Full tier** (per intake.md §Model & tier). Even on Opus 4.5, the evidence-
traceability requirement (every claim → source) makes sprint contracts worthwhile:
the contract is precisely where "which claims go in which section, sourced from
which background-note line" gets frozen before the Generator writes prose. The
Simplified tier would collapse Planner+Generator into one agent and lose this
sourcing step, which is the skill's core value proposition.

Article justification: "The evaluator is worth the cost when the task sits beyond
what the current model does reliably solo." Evidence-traceable résumés for a
specific company are such a task.

### 3.1 Sprints (3 sequential)

1. **Sprint 1 — Research & Fit Plan**: Generator (a) gathers target-company public
   signals (careers page claims, product positioning, recent announcements cited
   by the user or via web), (b) extracts applicant's most relevant claims from
   background notes, (c) produces a *fit matrix* mapping company priorities →
   applicant evidence lines.
2. **Sprint 2 — Résumé Draft**: Generator writes the markdown résumé using only
   fit-matrix-approved claims. Every bullet must carry an inline source tag like
   `{src: background-note-L42}` or `{company: careers-page-§engineering-values}`.
   Tags are stripped at final render but visible to the Evaluator.
3. **Sprint 3 — Fit-Gap Analysis**: Generator writes the 2–4-item action list,
   each item dated and scoped (e.g. "Update portfolio with X project by [date]").

Each sprint has its own **sprint contract** (file handoff, §4 below) and its own
**Evaluator pass** before the next sprint begins. This is V1-style per-sprint
evaluation (article §V1→V2 Simplification) chosen deliberately because each
sprint produces a distinct artifact type — collapsing them to a single end-run
evaluator would obscure which artifact failed.

---

## 4. Role prompt customizations

### 4.1 Planner (domain adaptations)

The generic Planner role ("expand 1-4 sentence prompt to spec") is adapted to:
- **Parse intake**: extract `{target_company, target_role, background_notes}`
  from the user's message or prompt for them if missing.
- **Produce a frozen sprint contract** for Sprint 1 (not implementation — just
  which company signals to look up, which background sections are candidate
  sources). Contract file: `/tmp/resume-harness/sprint-1-contract.md`.
- **Stay high-level** per article: the Planner does not write bullets itself;
  it specifies the fit-matrix schema and what "passing" looks like.
- **Not re-invoked per sprint**. One Planner pass up front produces contracts for
  all three sprints, then exits. (The article's V2 learning: planning is
  usually cheap enough to front-load.)

### 4.2 Generator (domain adaptations)

- **Source-tag discipline**: every claim in the résumé carries an inline
  `{src: …}` tag. The Generator is told: "If you cannot cite a source, you may
  not write the claim. Omit rather than invent."
- **Company-fit discipline**: every section header or ordering choice that
  differs from a generic résumé must carry a `{company: …}` tag justifying why
  (e.g. "Skills section moved above Experience {company: careers-page lists
  stack first}").
- **No self-evaluation before handoff**: article §10 — the Generator does NOT
  grade its own work; it writes, tags, hands off. This is a deliberate choice
  over the article's V1 pattern where Generator self-evaluated, because in this
  domain self-evaluation bias is especially strong (résumé writers praise their
  own prose).
- **Length bound hard-coded**: 1–2 pages at 11pt = approximately 450–900 words.
  Exceeding the bound is an automatic Sprint-2 fail.

### 4.3 Evaluator (domain adaptations)

- **Traceability check**: walks every `{src: …}` tag and verifies the cited
  line exists in the background notes. Walks every `{company: …}` tag and
  verifies a matching signal exists in the company-research file from Sprint 1.
  Missing or mismatched tags → automatic R1 or R2 fail.
- **Swap test** (for R2): the Evaluator asks itself "If I replaced the target
  company name with 'Acme Corp', would more than three sentences need to
  change?" If no, R2 fails regardless of surface polish.
- **Skeptical calibration**: per article, "tuning a standalone evaluator to be
  skeptical is more tractable than making a generator critical of its own work."
  The Evaluator prompt includes the instruction: *default to the lower score
  when in doubt; the Generator can always iterate*.
- **Detailed critique output**: Evaluator writes
  `/tmp/resume-harness/sprint-N-evaluation.md` with per-criterion score,
  quoted-evidence for each score, and specific revision asks. No vague
  "improve clarity" notes allowed.

---

## 5. Evaluator few-shot anchors (résumé-domain, score 1/3/5 per criterion)

These are the calibration examples that must appear verbatim in
`references/evaluator-prompt.md`. Per article §Few-Shot Calibration, their
purpose is to reduce score drift across iterations.

### R1 — Evidence quality

- **Score 1**: *"Led strategic initiatives to drive transformational value across
  the organization."* — Zero metrics, zero artifacts, no verifiable anchor.
- **Score 3**: *"Led migration of billing service from monolith to 4 microservices
  serving 2M requests/day."* — One concrete metric + one architectural detail,
  but no link to a PR / design doc / launch date.
- **Score 5**: *"Led migration of billing service from monolith to 4 microservices
  (Q3 2024, PR #1842), reducing p99 latency from 850ms to 210ms."* — Date,
  artifact reference, before/after metric.

### R2 — Company-specific fit

- **Score 1**: A résumé whose header reads "Senior Backend Engineer" with a
  generic "Experienced engineer passionate about building quality software"
  summary. Swap-test: nothing changes if the target company changes.
- **Score 3**: Résumé's Skills section is reordered to put Go and Kubernetes
  first (target company is a Go-heavy infra company), but the Experience
  section and summary are unchanged from a generic version.
- **Score 5**: Summary paragraph echoes two specific phrases from the target's
  careers page ("developer velocity", "observability as a product"); skills
  reordered to match their stack; one Experience bullet reframed to highlight
  the same outcome-metric the company publicly emphasizes (e.g. MTTR if
  they're an observability company).

### R3 — Professional craft

- **Score 1**: 3.5 pages, mixed font sizes, tables that will break ATS parsing,
  bullets of wildly varying length (one-liner next to six-liner).
- **Score 3**: 2 pages, consistent headers, ATS-safe, but some bullets run
  three lines and sections are not prioritized (e.g. Education above Experience
  for a senior candidate).
- **Score 5**: 1.5 pages, every bullet 1–2 lines with a verb-led opener,
  consistent date formatting, section order reflects seniority (Experience →
  Skills → Education for senior; inverse for new grad).

### R4 — Actionability (fit-gap)

- **Score 1**: "Work on your weaknesses before applying." — Vague, undated,
  unscoped.
- **Score 3**: "Add a portfolio project showing Go microservices experience." —
  Concrete, but no date and no acceptance criterion.
- **Score 5**: "Publish a 2-part blog post on the monolith-to-microservices
  migration described in Experience bullet 2, by 2026-05-01, on Medium or
  personal blog; link from résumé header." — Dated, scoped, with artifact
  location and success condition.

---

## 6. Orchestrator activation flow (numbered)

The SKILL.md body implements this orchestration. File handoffs live under
`/tmp/resume-harness/`.

1. **Intake.** User message (or prompt) supplies `{target_company, target_role,
   background_notes}`. Orchestrator validates all three are present; if not,
   asks exactly once.
2. **Dispatch Planner subagent.** Planner reads intake, writes
   `sprint-1-contract.md`, `sprint-2-contract.md`, `sprint-3-contract.md`. Each
   contract names its acceptance criteria. Planner exits.
3. **Dispatch Generator subagent for Sprint 1.** Generator reads
   `sprint-1-contract.md`, writes `fit-matrix.md`. Generator exits.
4. **Dispatch Evaluator subagent for Sprint 1.** Evaluator reads contract +
   fit-matrix, writes `sprint-1-evaluation.md` with per-criterion scores. If
   any criterion <3 or overall <weighted-threshold, orchestrator loops back to
   step 3 with evaluator's revision asks appended to the contract.
   **Max iterations: 3 per sprint**, consistent with article's observation that
   evaluator scores plateau within a handful of iterations (§Iteration Range,
   5–15 iterations cited for frontend but résumé is lower-dimensional so 3
   suffices). After 3, orchestrator surfaces the failure to the user.
5. **Dispatch Generator subagent for Sprint 2.** Fresh context reset (article
   §Context Resets: Planner/Generator/Evaluator are separate subagent
   invocations, so each gets a clean window by construction — this is the
   skill's built-in V1 context-reset mechanism). Generator reads
   `fit-matrix.md` + `sprint-2-contract.md`, writes `resume-draft.md`.
6. **Dispatch Evaluator subagent for Sprint 2.** Same loop as step 4.
7. **Sprint 3 loop** (repeat 5–6 for fit-gap analysis), producing
   `fit-gap.md`.
8. **Final assembly.** Orchestrator strips source tags from `resume-draft.md`
   to produce `resume-final.md`, concatenates with `fit-gap.md`, returns both
   to the user with a one-paragraph summary of how many iterations each sprint
   took and the final rubric scores.

The orchestrator itself performs no LLM work beyond dispatch, validation, and
tag-stripping. Per article §GAN-style separation, the orchestrator is the
referee, not a player.

---

## 7. Validation plan (Evaluator checks, derived from article patterns)

The Evaluator subagent's prompt must enforce, for each sprint:

### Sprint 1 (fit-matrix)
- **V1.1** Every row maps a named company signal to ≥1 applicant-background
  line by line number. (Derived from article §13 Validation: "filing bugs
  against anything that diverged from expected behavior.")
- **V1.2** ≥3 distinct company signals cited (prevents single-signal
  over-fitting).
- **V1.3** No fabricated company signals. Evaluator spot-checks by
  re-verifying one cited signal against its source.

### Sprint 2 (résumé draft)
- **V2.1** Every bullet has a `{src: …}` tag. Walk and verify.
- **V2.2** Every structural deviation from the generic template has a
  `{company: …}` tag. Walk and verify.
- **V2.3** Length 450–900 words. (Article §Hard Thresholds.)
- **V2.4** Swap test: rewriting would require ≥4 sentence changes. (Article
  §Rubric emphasis on originality — adapted to company-fit.)
- **V2.5** No invented credentials (degrees, employers, dates). Cross-check
  Experience/Education dates against background notes.

### Sprint 3 (fit-gap)
- **V3.1** 2–4 action items (not 1, not 5+).
- **V3.2** Each item has a date and a scope. (Article §Rubric criteria
  require "concrete" not "vague".)
- **V3.3** Each item addresses a gap that was visible in the fit-matrix
  (rather than inventing new concerns).

### Cross-sprint
- **V_X.1** Résumé claims are a **subset** of fit-matrix claims (no new
  claims introduced in Sprint 2 that weren't justified in Sprint 1).
- **V_X.2** Fit-gap items reference gaps from Sprint 1, not new gaps invented
  in Sprint 3 (prevents the Generator from discovering "oh also you should
  learn Rust" without basis).

### Skepticism calibration
The Evaluator prompt includes the article's exact stance: *"Default to the
lower score when in doubt. The Generator can iterate; false approvals cannot
be undone."*

---

## 8. Test scenarios

To be run by the Evaluator subagent of the meta-harness (skill-wizard-harness)
against the built skill.

### T1 — Happy path, strong candidate
- **Inputs**: target_company = "Stripe", target_role = "Staff Backend
  Engineer, Payments", background_notes = a 300-line dense notes file with 8
  concrete projects, metrics, and dated artifacts.
- **Expected**: All three sprints pass on iteration 1 or 2. Résumé clearly
  Stripe-flavored (mentions reliability, developer experience, specific
  payments-tech vocabulary). Fit-gap has 2–3 items.

### T2 — Sparse background (forces R1 pressure)
- **Inputs**: same company, background_notes = a 40-line skeletal résumé
  with few metrics.
- **Expected**: Sprint 2 should iterate (Generator first produces padded
  claims; Evaluator catches via missing `{src: …}` tags; Generator revises
  to omit rather than invent). Final résumé is shorter but every claim
  cited. R1 ≥ 3.

### T3 — Company mismatch (forces R2 pressure)
- **Inputs**: target_company = a hardware-robotics startup,
  background_notes = web-frontend background only.
- **Expected**: Fit-gap flags large gap, suggests concrete bridging actions
  (e.g. "Add an embedded-systems side project"). Evaluator does not let the
  Generator fake robotics experience. R2 ≥ 3 not because the résumé is a
  perfect fit but because what fit exists is maximally surfaced.

### T4 — KO trigger
- **User message**: "이 배경 노트로 네이버 백엔드 포지션 맞춤 이력서를
  하네스로 작성해줘" + notes.
- **Expected**: Skill activates on the KO trigger, produces KO-language
  résumé and fit-gap. All source tags still verifiable.

### T5 — Missing input
- **Inputs**: only target_company supplied.
- **Expected**: Orchestrator asks exactly once for the other two inputs,
  does not dispatch subagents until all three are present.

### T6 — Self-eval bias regression test
- **Inputs**: background_notes deliberately containing one fabricated
  achievement ("Won Turing Award, 2023").
- **Expected**: Evaluator's swap test and date cross-check flag the
  implausibility via V2.5, even though Generator (pattern-matching on the
  notes) included it. Sprint 2 iterates to remove it.

---

## 9. Article-pattern citation map

Which article concept this skill uses, and where.

| Article concept | Where applied in this skill |
|-----------------|-----------------------------|
| GAN-style role split | §4 Planner/Generator/Evaluator prompts; §6 orchestrator dispatch |
| Sprint contracts | §3.1 three sprints; §6 contract file per sprint |
| File-based handoffs | §6 all handoffs via `/tmp/resume-harness/*.md` |
| Context resets | §6 step 5 — subagent re-dispatch = fresh context by construction |
| Rubric with 2× weighting | §2.2 R1/R2 at 2× (weak-by-default), R3/R4 at 1× |
| Few-shot calibration | §5 score 1/3/5 anchors per criterion |
| Iteration range | §6 step 4 — max 3 per sprint (lower-dim domain than frontend's 5–15) |
| Context-anxiety countermeasures | §6 subagent boundaries + short sprints (résumé is short-horizon so no compaction needed) |
| Self-eval bias countermeasures | §4.2 Generator explicitly forbidden from self-grading; §4.3 Evaluator skeptical-default |
| V1 vs V2 choice | §3 V1 Full-tier chosen because evidence-traceability demands sprint boundaries |
| "Simplest solution" principle | §1.2 no scripts; §3 three sprints not six; §6 orchestrator is dispatch-only |
| Validation as testable contract | §7 V1.x / V2.x / V3.x / V_X.x check lists |

---

## 10. Out of scope (frozen exclusions)

To prevent Generator scope creep:

- No cover-letter generation. (Separate skill if wanted.)
- No LinkedIn-profile output.
- No PDF rendering. Output is markdown; user renders.
- No real-time web scraping of the target company. Company signals come from
  the user's `background_notes` or from the Generator's training knowledge
  with explicit `{company: …}` tagging that the Evaluator verifies for
  plausibility (not ground truth — the Evaluator is not an oracle).
- No multi-company batch mode. One résumé per invocation.
- No ATS scoring simulation. Craft is judged structurally, not by an external
  ATS tool.

---

*End of frozen spec. Generator: implement verbatim. Evaluator: check against
this spec, not against your own preferences.*
