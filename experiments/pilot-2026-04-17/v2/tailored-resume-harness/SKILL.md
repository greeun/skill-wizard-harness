---
name: tailored-resume-harness
description: Company-specific, evidence-traceable résumé harness. Produces a submission-ready markdown résumé tailored to one target company + role plus a fit-gap analysis with dated next steps. Uses Planner → Generator → Evaluator subagents from Anthropic's "Harness Design for Long-Running Application Development" (Prithvi Rajasekaran, 2026) so every applicant claim cites the applicant's notes and every company-specific phrase cites the company's public material. Trigger phrases — EN: "tailored resume with evaluator", "company-specific resume", "resume harness", "job application harness", "multi-agent resume", "resume with traceability", "evidence-traceable resume", "targeted resume harness". KO: "이력서 하네스", "맞춤 이력서", "기업 맞춤 이력서 하네스", "이력서 멀티에이전트", "이력서 플래너 제너레이터 이밸류에이터", "지원 이력서 하네스", "채용 지원 하네스", "이력서 자율 작성", "이력서 컨설턴트 하네스", "이력서 하네스 작성".
---

# Tailored Résumé Harness

Produces a submission-ready markdown résumé tailored to one target company + role, plus a
fit-gap analysis with dated next steps. Every applicant claim cites the applicant's own notes;
every company-specific phrase cites the target company's public material. The harness is a
Planner → Generator → Evaluator loop dispatched as independent `Agent` calls, following
Anthropic's *Harness Design for Long-Running Application Development* (Prithvi Rajasekaran, 2026).

**Why this is a harness, not a one-shot.** Résumé tailoring has the exact failure mode the
source article was built to counter: Claude writes smooth, professional-sounding consultant-speak
("led cross-functional initiatives to drive business value") that *looks* strong and is
completely unverifiable. A single session that generates and grades itself will confidently
approve its own shallow polish — that is self-evaluation bias. Structural separation into three
subagents, with file-based handoffs and 2× rubric weight on Evidence Quality and
Company-Specific Fit, forces traceability to be enforceable rather than aspirational.

---

## Six harness principles (preserved)

1. **Structural role separation.** Planner, Generator, and Evaluator are dispatched as separate
   `Agent` calls. The Evaluator never sees the Generator's reasoning — only its files.
2. **File-based handoffs as the only communication channel.** `spec.md`, `sprint_contract.md`,
   `generator_report.md`, `critique.md`, `handoff.md`, `design_memo.md`.
3. **Sprint contracts before implementation.** Each sprint begins with a contract negotiation:
   Generator proposes scope + observable checks, Evaluator approves or amends, only then does
   work begin.
4. **Context resets over compaction.** When a Generator hits a context-anxiety signal mid-sprint,
   it writes `handoff.md` and a fresh Generator session resumes. Compaction preserves the anxiety
   state; a fresh `Agent` call clears it.
5. **Rubric-based evaluation with evidence.** 4 criteria scored 1–5 against few-shot anchors
   (see `references/evaluator-calibration.md`). Every score cites an applicant-notes line number
   or a company-material URL. "It looks fine" is never a pass.
6. **Every harness component encodes an assumption.** If you upgrade the model, stress-test each
   component one at a time before dropping it. Radical simplification fails; methodical
   one-at-a-time removal succeeds.

---

## Architecture

**Tier: Full.** 3 sprints, per-sprint Evaluator passes, sprint contracts, REFINE/PIVOT/ESCALATE
direction rule. The Simplified tier was rejected because evidence traceability is the whole
reason the skill exists — a single end-of-run Evaluator pass would let the Generator finish an
entire shallow draft before any claim was verified, too late to course-correct cheaply.

**Subagents:**
- **Planner** (1 invocation per run) — converts intake into `spec.md`. Prompt:
  `references/planner-prompt.md`.
- **Generator** (1+ invocations per sprint) — produces the deliverable for that sprint. Prompt:
  `references/generator-prompt.md`.
- **Evaluator** (1 invocation per sprint) — approves the sprint contract, then audits the
  delivered artifact. Prompt: `references/evaluator-prompt.md`. Calibration anchors:
  `references/evaluator-calibration.md`.

**Sprints (3):**
1. **Outline + source-index.** Generator builds the résumé section order and a source-index
   table mapping every planned bullet → applicant-notes line or company URL. No prose yet.
2. **Full draft résumé.** Generator writes every bullet as final prose, staying within the
   approved Sprint-1 index. Evaluator runs adversarial probes.
3. **Fit-gap analysis.** Generator writes dated, scoped action items tied to observed gaps.

Each sprint has its own contract negotiation and its own Evaluator pass. Full sprint-by-sprint
instructions, contract templates, and iteration rules live in
`references/sprint-playbook.md`.

**Iteration cap: 10 per sprint (within a 5–10 range).** The article warns against tight caps
citing the Dutch Art Museum iteration-10 breakthrough; 5 is the floor, 10 the ceiling. If the
user asks for fewer than 5, warn them they are cutting below the range that produces late-leap
wins. Wall-clock tolerance up to ~4 hours is acceptable per run — do not artificially rush.

---

## Activation flow (orchestrator)

Follow these numbered steps. Do not skip gates; do not dispatch subagents before the gates clear.

1. **Intake.** Ask the user in parallel for:
   (a) Target company name + careers-page URL (or "I'll research it — confirm").
   (b) Target role / JD (paste text or URL).
   (c) Applicant-background notes (file path, pasted text, or "pull from my current CV").

2. **Gate A — Bilingual confirmation.** If applicant-notes and target-company material are in
   different languages, ask the user which language the output résumé should be in. Default:
   match the target company's careers-page language. Require explicit user answer.

3. **Gate B — Iteration cap confirmation.** Tell the user verbatim: *"Default is 5–10 iterations
   per sprint. The article warns against tight caps — the Dutch Art Museum's winning design came
   at iteration 10. I'll plan for 10. Override?"* Accept any value ≥ 5; warn if < 5.

4. **Gate C — Source-material sanity check (no-fabrication safety gate).** Confirm
   applicant-notes exist and are non-empty. If notes are thin (< 500 words), warn: *"Your notes
   are short. The Evaluator will reject any bullet not traceable to these notes. Add more notes
   before we start, or proceed with a shorter résumé?"* Require explicit user confirmation
   either way.

5. **Gate D — Target-company consent gate (data-handling safety gate).** Confirm: *"I'll fetch
   the target company's careers page and linked eng/product blog posts. I will not submit
   anything to the company. Proceed?"* Require explicit user yes.

6. **Dispatch Planner** as an `Agent` call. Input: intake summary + applicant-notes path +
   target URLs. Prompt: `references/planner-prompt.md`. Output: `spec.md`. Wait for
   `SPEC_READY: spec.md`.

7. **Gate E — Spec review.** Present `spec.md` to the user. Ask: *"Proceed, or revise
   sections X/Y?"* Do not proceed autonomously. A bad spec = a bad résumé.

8. **Sprint 1 loop (outline + source-index):**
   - Dispatch Generator (`Agent` call). Generator negotiates `sprint_contract.md`.
   - Dispatch Evaluator (`Agent` call) to review the contract. If Evaluator amends, Generator
     re-reads and accepts or ESCALATEs.
   - Once contract approved: Generator produces Sprint-1 artifact (outline + source-index
     table).
   - Dispatch Evaluator to audit the artifact → `critique.md`.
   - If FAIL: dispatch a fresh Generator with `spec.md` + `critique.md` only (no prior
     conversation). Iterate up to cap.
   - If PASS: proceed to Sprint 2.

9. **Sprint 2 loop (full draft résumé):** same structure as Sprint 1.

10. **Sprint 3 loop (fit-gap analysis):** same structure as Sprint 1.

11. **Gate F — Final user handoff.** Present `resume.md` + `fit-gap.md`. Tell the user:
    *"Evaluator verdict: PASS. Review once more before you use this. The Evaluator caught claims
    without sources; it cannot catch claims where your notes themselves are wrong."*

**Context-anxiety detection (V1-21).** If a Generator exhibits any of these signals, it writes
`handoff.md` and stops cleanly; the orchestrator then dispatches a fresh Generator session:

1. Re-summarizing earlier sections instead of writing new content.
2. Reaching for closing jargon before the deliverable is complete.
3. Section depth dropping visibly mid-document.
4. Writing "for brevity" or "at a high level" where `spec.md` demanded detail.
5. Skipping verification steps the sprint contract listed.

**Résumé-specific tell:** Generator about to write a bullet without a matching source-index
entry (e.g., "Collaborated with stakeholders across the organization to drive alignment" with
zero citation). That is the signal to stop and emit `HANDOFF_NEEDED: handoff.md`.

**Compaction is forbidden (V1-22).** Compaction preserves the anxiety state. Only a fresh
`Agent` call clears it.

---

## File handoff contract

Every communication between subagents goes through these files:

| File | Written by | Read by | Purpose |
|------|-----------|---------|---------|
| `spec.md` | Planner | Generator, Evaluator | Frozen design decisions |
| `sprint_contract.md` | Generator | Evaluator | Scope + observable checks for this sprint |
| `generator_report.md` | Generator | Evaluator | Strategic Decision + deliverables + self-verification |
| `critique.md` | Evaluator | Generator (next iteration) | Verdict + rubric scores + blocking issues |
| `handoff.md` | Generator | Fresh Generator | State dump when context anxiety hits |
| `design_memo.md` | Generator | Evaluator | Justification for a PIVOT, pre-approval |
| `resume.md` | Generator | User | Final deliverable |
| `fit-gap.md` | Generator | User | Dated action items |

No chat-level handoff. The Evaluator never sees the Generator's reasoning — only its files.

---

## Safety gates (domain-specific)

**No-fabrication gate.** If the user ever asks the skill to "fill in" a gap in their background
(e.g., *"just say I know Kubernetes"*), refuse and tell the user to update applicant-notes
first. The entire skill exists to prevent this failure mode; bypassing it defeats the purpose.

**No external submission.** The skill never calls LinkedIn, job-board APIs, or company APIs. It
only reads the target company's public-facing pages. Gate D enforces this explicitly.

**PII awareness.** If applicant-notes contain SSN, government ID, full date of birth, or bank
details, flag and ask the user to redact before dispatching subagents. The Evaluator also runs
a PII-leak check on the final résumé.

**Sensory limits: none.** Résumé is pure markdown/text. Visual craft (typography hierarchy,
length discipline, scannability) is LLM-verifiable through structural cues: section order,
bullet depth, character/line counts, markdown header levels. No human checkpoint is required;
the Evaluator's Professional Craft criterion handles this in-band.

---

## Evaluator tuning workflow (P-3)

An out-of-the-box LLM evaluator is too lenient. Treat the first few runs as calibration drafts.
The operational procedure:

(a) **Read logs.** After each run, open `critique.md` alongside `resume.md` and
`applicant-notes`. For every rubric score, ask: *would a picky senior recruiter at the target
company have scored this the same?*

(b) **Find divergence.** Identify specific mismatches — typical patterns are: accepting a bullet
as "evidenced" when its metric is unsourced; scoring C2 ≥ 4 on a résumé that would fit any
competitor; missing consultant-speak verbs that lack a metric; approving a fit-gap item without
a date.

(c) **Update prompt/calibration.** Edit `references/evaluator-prompt.md` with a concrete
counter-example targeting each divergence. If the divergence shows the existing score anchors
in `references/evaluator-calibration.md` are too lenient, tighten the 3/5 or 5/5 anchor for
that criterion with a fresh real example.

(d) **Rerun.** Dispatch a fresh Evaluator on the same artifact. Expect several cycles before
the Evaluator converges with a careful human expert's judgment. Stop tuning when Evaluator
verdicts reliably match a picky human pass and every blocking issue is reproducible.

This loop is documented because it is operational reality, not a theoretical ideal — the
article's "calibrating the evaluator using few-shot examples with detailed score breakdowns"
finding applies here.

---

## V1 vs V2 guidance — why we picked V1 (Full)

The source article's V2 simplification (Opus 4.5/4.6) removes the sprint construct and uses a
single end-of-run Evaluator pass. We chose V1 anyway because:

- **Evidence traceability requires per-sprint verification.** If the Generator writes the whole
  résumé before any Evaluator pass, a single unsourced bullet in the outline propagates through
  the full draft before anyone catches it. The Sprint-1 source-index contract catches it at
  outline stage, when it costs one bullet to fix instead of a full rewrite.
- **V2 assumes sustained single-session coherence; this domain punishes drift.** Claude's
  default professional-prose voice is exactly the failure mode here. Frequent Evaluator passes
  interrupt the drift before it compounds.

V2 elements are explicitly N/A-with-justification, not silently omitted.

**Model-specific guidance (copied from source):**

| Model class | Context anxiety | Recommended tier | Notes |
|-------------|----------------|-----------------|-------|
| **Sonnet 4.5** | Strong — wraps up prematurely | Full (V1) | Small sprints, aggressive Evaluator, firm context resets |
| **Opus 4.5** | Largely eliminated | Simplified (V2) | Multi-hour coherent sessions; sprint decomposition droppable |
| **Opus 4.6** | Eliminated; improved planning, long-context, debugging | Simplified or Single-session | 2+ hour builds sustainable; re-examine every component, drop what's not load-bearing |

**General rule.** Every harness component encodes an assumption about what the model can't do
alone. On model upgrade, stress-test each assumption individually — remove one component at a
time and measure impact. The article warns against radical simplification (removing everything
at once failed); methodical one-at-a-time removal reveals load-bearing pieces.

Cite Anthropic, *"Building Effective Agents"* — *"find the simplest solution possible, and only
increase complexity when needed."*

This skill targets Opus 4.5 by default but retains V1 tier for the reasons above. If a future
run uses Opus 4.6 and the user wants to simplify, drop Sprint 3's Evaluator pass first (fit-gap
actionability is the most LLM-verifiable axis), measure, then consider dropping Sprint 2's
contract negotiation. Do not drop both at once.

---

## Iteration wisdom (this skill's own harness)

- **Iteration range 5–10 per sprint.** Do not cap at 3. Dutch Art Museum example had a
  breakthrough at iteration 10 — tight caps kill breakthroughs.
- **Middle iterations can beat final.** The Evaluator must read prior `critique_v*.md` versions
  and note regressions explicitly. A Sprint-2 rewrite that softens a metric that was concrete
  in the prior iteration is a regression, not a refinement.
- **Pivots need evidence.** A late-stage direction change requires either an explicit
  `REDIRECT: <reason>` from the Evaluator or a `design_memo.md` from the Generator citing
  `critique.md` evidence. Context-reset amnesia is not insight.
- **Wall-clock up to ~4h is acceptable.** The orchestrator does not rush. A résumé that is
  going to determine a hiring decision is worth the extra iteration.

---

## Reference files

- `references/planner-prompt.md` — Planner subagent system prompt
- `references/generator-prompt.md` — Generator subagent system prompt (includes
  REFINE/PIVOT/ESCALATE Strategic Decision block)
- `references/evaluator-prompt.md` — Evaluator subagent system prompt (adversarial probes,
  verdict logic)
- `references/evaluator-calibration.md` — Few-shot score anchors (1/3/5) per criterion
- `references/rubric.md` — 4 criteria, 2× weighting, verdict logic
- `references/sprint-playbook.md` — Per-sprint contract templates + deliverables (Full tier)
