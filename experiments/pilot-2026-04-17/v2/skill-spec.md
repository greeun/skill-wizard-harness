# Skill Specification: tailored-resume-harness

This specification is frozen. The Generator subagent that implements the skill will not see the
intake conversation — everything needed to produce the skill package is below. Every decision
cites `article-coverage-checklist.md` (AC) or `rubric.md` (RB) where applicable.

---

## 1. Domain & Purpose

**Domain statement.** The `tailored-resume-harness` skill produces a submission-ready résumé
tailored to one specific target company + target role + applicant background. It outputs a
PDF-ready markdown résumé **plus** a fit-gap analysis that tells the applicant exactly what to do
before hitting "submit." Unlike generic résumé generators, every applicant claim in the output
must be traceable to a concrete artifact in the applicant's own notes, and every company-specific
phrase must be traceable to a cited element in the target company's public-facing material. The
whole point of the harness is to make those two traceability constraints structurally enforceable:
the Evaluator subagent runs adversarial probes that a single-session LLM will not apply to its
own output (V1-16; self-evaluation bias section of evaluator template).

**Why a harness, not a one-shot.** Résumé tailoring has the exact failure mode the article's
grading harness was designed for: Claude confidently writes smooth, professional-sounding
consultant-speak ("led cross-functional initiatives to drive business value") that LOOKS strong
and is completely unverifiable. The Evaluator split (V1-1) forces every claim through a
skeptical reader who demands source citations, and the 2× weighting on evidence quality and
company-specific fit (V1-3, RB "Weight what Claude lacks") prevents the generator from declaring
victory on shallow polish.

**Model & tier.** Target model is Opus 4.5. Tier is **Full** (sprint contracts + per-sprint
Evaluator), per intake. Rationale: the evidence-traceability requirement is the whole reason the
skill exists, and a Simplified tier (V2-1, single end-of-run Evaluator pass) would let the
Generator finish a first draft with unsourced claims and only surface the problem once the whole
résumé was written — too late to course-correct cheaply. Full tier allows a contract-level
check at the outline stage ("do your bullets each cite a source?") before any prose is written.

**Sensory limits.** None that require a human checkpoint. Résumé is pure markdown/text; visual
craft (typography hierarchy, length discipline, scannability) is LLM-verifiable through
structural cues: section order, bullet depth, character/line counts, markdown header levels. P-1
(sensory-limit human checkpoint) therefore resolves to "N/A — domain has no sensory limits,
verified in §7."

---

## 2. Metadata

**skill-name:** `tailored-resume-harness`

**description (SKILL.md frontmatter):**
```
Company-specific, evidence-traceable résumé harness. Produces a submission-ready markdown
résumé tailored to one target company + role plus a fit-gap analysis with dated next steps.
Uses Planner → Generator → Evaluator subagents from Anthropic's "Harness Design for Long-Running
Application Development" (Prithvi Rajasekaran, 2026) so every applicant claim cites the applicant's
notes and every company-specific phrase cites the company's public material. Trigger phrases —
EN: "tailored resume with evaluator", "company-specific resume", "resume harness",
"job application harness", "multi-agent resume", "resume with traceability",
"evidence-traceable resume", "targeted resume harness". KO: "이력서 하네스", "맞춤 이력서",
"기업 맞춤 이력서 하네스", "이력서 멀티에이전트", "이력서 플래너 제너레이터 이밸류에이터",
"지원 이력서 하네스", "채용 지원 하네스", "이력서 자율 작성", "이력서 컨설턴트 하네스",
"이력서 하네스 작성".
```

Bilingual (KO/EN) triggers per intake. 18 total phrases — well above the 8+ floor the planner
prompt requires.

**Directory structure:**
```
tailored-resume-harness/
├── SKILL.md
└── references/
    ├── planner-prompt.md           # Résumé-specific Planner role prompt
    ├── generator-prompt.md         # Résumé-specific Generator role prompt
    ├── evaluator-prompt.md         # Résumé-specific Evaluator role prompt
    ├── rubric.md                   # 4 criteria, 2× weighting, verdict logic
    ├── evaluator-calibration.md    # Few-shot score anchors (1/3/5) per criterion
    ├── article-principles.md       # Condensed article citations for in-skill reference
    └── resume-formatting.md        # ATS-parseable markdown conventions + length rules
```

No scripts/, no assets/. The skill is pure-prompt.

---

## 3. Rubric

Four criteria. 2× weighting on the two axes intake.md names as weak-by-default for Claude in this
domain (evidence quality, company-specific fit). 1× on the two Claude handles adequately by
default (professional craft — Claude already writes clean markdown; actionability — Claude already
produces "next steps" sections without prompting). This matches the "Weight what Claude lacks"
principle (RB §Core Principle) and satisfies V1-3.

| # | Criterion | Weight | Measures | Claude's default weakness |
|---|---|---|---|---|
| C1 | **Evidence Quality** | **2×** | Every bullet about the applicant cites a concrete artifact (metric, shipped project, named employer, date, URL) in the applicant-notes source. No consultant-speak. | Claude smooths vagueness into professional-sounding prose; this forces a source-citation check per bullet. |
| C2 | **Company-Specific Fit** | **2×** | Language, skill emphasis, and ordering visibly reflect the target company's stated priorities. A reader swapping the company name would notice the résumé no longer fits. | Claude writes "universally employable" résumés by default — generic enough to send anywhere, specific enough for nowhere. |
| C3 | **Professional Craft** | 1× | ATS-parseable markdown, 1–2 page render length, consistent bullet depth, section order follows industry convention, no typos. | Claude already produces clean markdown structure without pressure. |
| C4 | **Actionability (fit-gap)** | 1× | Fit-gap analysis names ≥2 concrete, dated, scoped action items (portfolio updates, references, certs, practice interview topics). | Claude already generates "next steps" lists when asked; 1× keeps it honest without over-indexing. |

**Style-magnet compliance (V1-4):** criteria are phrased as qualities ("evidence quality",
"company-specific fit"), not references ("McKinsey-style", "FAANG-caliber"). References to
specific companies appear only in `spec.md` intent, never in the rubric.

**Verdict logic (V1-17, RB §Verdict Logic):**
```
All criteria ≥ 4 AND adversarial probes clean → PASS
Any 2×-weighted criterion (C1 or C2) < 4      → FAIL
Any 1×-weighted criterion (C3 or C4) < 3      → FAIL
Any Definition-of-Done item unverified        → FAIL
```

---

## 4. Harness Architecture

**Tier:** Full (V1 architecture — V1-1 through V1-22 apply; V2-1/V2-2 are N/A with
justification below).

**Subagent topology:**
- Planner (1 invocation per run) — converts the intake into `spec.md`.
- Generator (1–N invocations per run, one per sprint) — produces outline → draft → final.
- Evaluator (1 invocation per sprint) — reviews sprint contract, then reviews delivered artifact.

All three dispatched as separate `Agent` calls from the orchestrator (V1-1). File-based handoff
only (V1-19): `spec.md`, `sprint_contract.md`, `generator_report.md`, `critique.md`, `handoff.md`,
`design_memo.md`.

**Iteration cap:** **5–10 iterations** per generation. The article warns against tight caps
citing the Dutch Art Museum iteration-10 breakthrough (V1-10); 5–10 gives room for the late leap
while keeping wall-clock bounded. Range, not single value (V1-6). Orchestrator must warn the user
against picking the low end when invoking. Wall-clock tolerance up to ~4h is acceptable (V1-7);
the orchestrator does not artificially rush.

**Context reset policy (V1-22, V1-21):** Each Generator invocation is a fresh Agent call. The
prior Generator's session memory is gone; only the files remain. If a Generator hits a
context-anxiety signal mid-sprint, it writes `handoff.md` and a fresh Generator resumes — no
compaction (V1-22). The SKILL.md principles section explicitly states "compaction does not clear
context anxiety; a fresh Agent call does."

**Sprints (3 sprints default):**
1. **Sprint 1 — Outline + source index.** Generator builds the résumé section order, bullet
   targets per section, and a source-index table mapping every planned bullet → source (applicant
   notes line number OR company material citation). No prose yet. Evaluator checks that every
   planned bullet has a source AND that company-side sources are real (URLs resolve, dated
   recently, from the actual target company).
2. **Sprint 2 — Full draft résumé.** Generator writes every bullet as final prose, staying within
   the approved source-index. Evaluator runs adversarial probes (see §5 Evaluator).
3. **Sprint 3 — Fit-gap analysis.** Generator writes the fit-gap document, referencing specific
   gaps revealed during Sprint 1/2. Evaluator checks actionability (dated, scoped items).

Each sprint has its own contract negotiation (V1-18) and its own Evaluator pass.

**V2 tier elements are N/A:**
- V2-1 sprint removal — N/A, tier=Full.
- V2-2 single end-of-run Evaluator — N/A, tier=Full.
- V2-3/V2-4 model guidance — **still documented in SKILL.md** as "V1 vs V2 — why we picked V1
  here," per AC requirement that V2 rows get explicit N/A justification rather than silent
  omission.

**Article element applicability (condensed — full mapping in §7):**
- Applicable: V1-1 through V1-22, G-1 through G-5, P-1 (N/A justified), P-2, P-3.
- N/A with justification: V2-1, V2-2.

---

## 5. Role Prompt Customizations

### Planner

Adapt `planner-prompt-template.md` as follows. Core rules (1-ambitious scope, 2-stay at product
level, 3-no implementation prescription, 4-weave domain nuance, 5-reader has zero context) remain
verbatim. Domain-specific substitutions:

- `[DOMAIN]` → "résumé tailoring"
- `[OUTPUT_TYPE]` → "tailored résumé + fit-gap analysis"
- `[PRODUCT/CONTENT]` → "content/product"
- `[DELIVERABLE UNIT]` → "each résumé bullet and each fit-gap action item"

**Section 3 of spec.md (domain-specific intent section) →** *"Voice, emphasis, and evidence
posture."* Planner must fill with:
- Voice/tone register calibrated to the target company (e.g., startup-casual vs. enterprise-formal
  vs. agency-punchy — inferred from the target's careers page).
- Emphasis hierarchy: which 2-3 applicant strengths lead, which are de-emphasized, given the JD.
- Evidence posture: for this applicant × this role, what kind of evidence carries the most weight
  (shipped metrics vs. named clients vs. academic credentials vs. open-source footprint).
- **Anti-patterns list**: consultant-speak verbs to avoid ("drove," "leveraged," "spearheaded"
  unless followed by a metric), generic filler ("team player," "passionate about"), AI-slop bullet
  structure (every bullet starting with the same verb pattern).

**Section 6 of spec.md (domain-specific data/context section) →** *"Target-company and
applicant-source context."* Planner must fill with:
- Target company: official name, careers page URL, product/mission summary (2-3 lines from
  public material), 3-5 priority themes the résumé should mirror (each with a cited URL).
- Target role: JD summary, must-have requirements list, nice-to-have list.
- Applicant background: pointer to the applicant-notes file/section the Generator should treat as
  the ONLY source of truth for applicant claims. Any information not in that file is
  out-of-bounds for the Generator.
- Bilingual note: if applicant-notes is Korean and the target company is English-speaking (or
  vice versa), Planner flags this and specifies the output language.

**Planner ambition directive (P-2):** Planner prompt explicitly states *"be ambitious about scope
— if the applicant's background supports a stronger positioning than the user's brief suggested,
include it in spec.md with rationale. Do not downscope to match the brief's tone."*

**Differentiation hook (V1-14, P-2):** *"Actively look for opportunities to weave in
domain-specific value the applicant could not see themselves — e.g., highlighting an unusual
cross-functional pattern across roles, surfacing a metric the applicant undersold, spotting a
skill the target company values that the applicant has but didn't list."*

### Generator

Adapt `generator-prompt-template.md` as follows. Core operating rules (1-read spec, 2-negotiate
contract, 3-production process, 4-quality standard, 5-self-verify, REFINE/PIVOT/ESCALATE block,
context-anxiety signals) remain verbatim. Domain-specific substitutions:

- `[DOMAIN]` → "résumé tailoring"
- `[OUTPUT_TYPE]` → "tailored résumé + fit-gap analysis"
- `[DOMAIN EXPERT ROLE]` (in evaluator's eyes) → "picky senior recruiter at the target company"

**Production process (rule 3 specialization)** — use the *content/strategy hybrid* pattern from
the generator template:
```
3. Production process:
   a. Read spec.md in full; load applicant-notes and target-company materials into working memory.
   b. Sprint 1: build the résumé outline and source-index table.
      - Every planned bullet must have a citation column (applicant-notes line, or company URL).
      - Bullets without a source are removed, not softened.
   c. Sprint 2: write each bullet as final prose, staying within approved sources.
      - For applicant claims: paraphrase the source; do not invent metrics not in the source.
      - For company-facing phrasing: mirror language from cited company material.
   d. Sprint 3: draft the fit-gap analysis.
      - Every action item dated (e.g., "before submission, within 72h") and scoped.
   e. Self-review against every sprint contract check before handoff. Specifically: re-read each
      bullet and verify you can point to a source line without re-reading the source file.
```

**Quality standard (rule 4 specialization):**
```
4. Honor the voice/emphasis/evidence posture in spec.md §3. Avoid consultant-speak verbs unless
   followed by a metric ("led X" is weak; "led team of 5 shipping X with 30% latency reduction"
   is acceptable if the 30% is in applicant-notes). Avoid generic filler. No bullet may rely on
   inference — if applicant-notes says the applicant "worked on payments," you cannot write
   "architected payment systems" unless the notes also say "architected."
```

**Direction-change rule** (REFINE/PIVOT/ESCALATE, V1-9) is carried verbatim from the template.
Domain interpretation:
- REFINE = tighten specific bullets per critique.
- PIVOT = restructure section order or change which applicant strengths lead (requires
  design_memo.md citing critique.md evidence).
- ESCALATE = Generator and Evaluator disagree about what counts as "evidence" for a specific
  claim; user arbitrates.

**Context-anxiety triggers** (V1-21) are preserved verbatim. Domain-specific tell: Generator
catches itself writing a late-section bullet like "Collaborated with stakeholders across the
organization to drive alignment" — pure consultant-speak, zero citation. That is the signal to
stop and emit `HANDOFF_NEEDED: handoff.md`.

### Evaluator

Adapt `evaluator-prompt-template.md` as follows. Core structure (adversary posture, workflow,
rubric table, verdict logic, calibration rules, no-praise rule) remains verbatim. Domain-specific
substitutions:

- `[DOMAIN]` → "résumé tailoring"
- `[OUTPUT_TYPE]` → "tailored résumé + fit-gap analysis"
- `[DOMAIN EXPERT ROLE]` → "picky senior recruiter at the target company"
- `[SECONDARY EXPERT PERSPECTIVE]` → "ATS parser + hiring-manager first-30-second skim"
- `[SURFACE-LEVEL COMPLETION]` → "a résumé that looks polished"
- `[ACHIEVE DEEP FUNCTIONALITY]` → "cite a source for every claim"

**Adversarial probes (V1-16, evaluator template §Domain-Specific Adversarial Probes — blend of
content + strategy + new résumé-specific checks):**
```
Adversarial probes:
1. Source traceability probe: pick 5 random résumé bullets about the applicant. For each, locate
   the supporting line in applicant-notes. If any bullet cannot be traced to a concrete line,
   that bullet fails.
2. Company-swap probe: mentally replace the target company name with a direct competitor in the
   same space. Does the résumé still fit equally well? If yes, the résumé is not actually
   tailored. FAIL C2.
3. Metric-invention probe: scan for numbers (percentages, dollar amounts, team sizes, timelines).
   Verify each appears in applicant-notes. A fabricated 30% is a hard fail.
4. Consultant-speak probe: flag bullets using "drove / leveraged / spearheaded / orchestrated /
   championed" without a metric in the same bullet.
5. Filler probe: try deleting each bullet. If the résumé loses no information, the bullet is
   filler.
6. Company-citation probe: for every company-specific phrasing (tech stack, values, priorities),
   verify a URL in spec.md §6 actually contains that element. Dead link or missing phrase = FAIL.
7. Length probe: count words and estimate rendered page count (rough rule: ~450 words/page at
   11pt with standard résumé spacing). >2 pages = FAIL C3.
8. ATS probe: check that section headers use standard names (Experience, Education, Skills, etc.)
   and that no bullets rely on non-parseable elements (tables inside bullets, images, multi-column
   layouts in the markdown).
9. Fit-gap actionability probe: each action item must have (a) a verb, (b) a concrete artifact,
   (c) a date. "Prepare references" fails; "By Wed: email Jane + Kim confirming they'll be
   references for this application" passes.
```

**Iteration Quality Note (V1-11):** preserved from template — Evaluator explicitly compares
current sprint to prior sprint and notes regressions. Résumé-specific: "Sprint 2's bullet 7 was
stronger than the current Sprint 2 rewrite's bullet 7 — the metric got softened."

**Few-shot calibration (V1-5, V1-8) — 2 anchors per criterion, résumé-realistic:**

*C1 — Evidence Quality*
- **Score 1/5:** "Led transformation initiatives across global teams, driving business value
  through data-driven decision-making." — Zero specifics. Zero citation possible. Pure
  consultant-speak template bullet.
- **Score 3/5:** "Led a 4-person team that migrated the payment service, reducing latency."
  — Team size is cited, but "reducing latency" has no number even though applicant-notes says
  "p99 dropped from 800ms to 200ms." Source exists; Generator just didn't use it.
- **Score 5/5:** "Led 4-engineer migration of payment service to Rust (Q2 2024), cutting p99
  latency from 800ms → 200ms [applicant-notes L47]; rollout covered 12M req/day." — Every number
  traceable, team size concrete, timeline specific, cite visible.

*C2 — Company-Specific Fit*
- **Score 1/5:** Résumé targeting Stripe that emphasizes "full-stack generalist skills" and lists
  17 languages/frameworks in skills section — same bullet order and emphasis would fit equally for
  Vercel, Cloudflare, or any dev-tools startup. Company-swap probe instantly flags this.
- **Score 3/5:** Résumé targeting Stripe with a "Payments & Fintech" section header and one bullet
  mentioning PCI compliance — some signal, but skills section still lists everything generically
  and the summary line could apply to any fintech.
- **Score 5/5:** Résumé targeting Stripe with: summary line echoing Stripe's "economic
  infrastructure" phrasing, experience bullets reordered so fintech/payments work leads,
  Ruby/Go prioritized in skills (Stripe's core stack per their eng blog cite),
  and an explicit "Prior experience with SCA/PSD2 regulatory rollouts [notes L112]" bullet
  mirroring Stripe's European-expansion careers-page emphasis.

*C3 — Professional Craft*
- **Score 1/5:** 4-page résumé with 15-word bullets that wrap inconsistently, mixed bullet
  depths (some 1-level, some 3-level nested), section headers as bold text not headers,
  emoji in section names — fails ATS parsing, fails length, fails scannability.
- **Score 3/5:** 2-page résumé, clean header structure, but bullet depth varies between sections
  (Experience has 4-bullet entries, Projects has 1-bullet entries with no rationale), and the
  summary paragraph is 6 lines when convention is 2-3.
- **Score 5/5:** 1.5-page résumé, H2 section headers, uniform bullet depth (3-5 bullets per role,
  1 line each, wrapping at most once), summary 2 lines, ATS-parseable standard section names,
  consistent date format throughout.

*C4 — Actionability*
- **Score 1/5:** Fit-gap section says "The applicant should strengthen their portfolio and
  prepare for the interview." No dates, no artifacts, no scope. Unexecutable.
- **Score 3/5:** Fit-gap lists 3 items: "1. Update GitHub README. 2. Email references.
  3. Review system design." Items have verbs and artifacts but no dates and no scope (which
  READMEs? which references? which system design topics?).
- **Score 5/5:** Fit-gap lists 4 items: "1. By Thu evening: push updated README to
  github.com/<user>/payment-svc documenting the 800ms→200ms migration. 2. By Fri noon: email
  Jane Lee + Kim Park to confirm reference consent; forward JD. 3. Over the weekend: drill
  2 Stripe-style system design rounds on distributed rate-limiting (pair with Alex). 4. Before
  submission: remove 2018 bootcamp bullet (pre-applicant-notes era, unverifiable)."

The `evaluator-calibration.md` file carries the full text of these 8 anchors (4 criteria × 2
anchors: score 1/3 and score 5 — the template calls for 2–3 per criterion, we ship 3 by combining
the 3/5 anchor above).

---

## 6. Orchestrator

The SKILL.md body is the orchestrator. It walks the main session through a numbered flow.

**Activation flow (numbered, résumé-specific):**

1. **Intake.** Ask the user for three inputs in parallel:
   (a) Target company name + careers-page URL (or "I'll research it — confirm").
   (b) Target role / JD (paste or URL).
   (c) Applicant-background notes (file path, pasted text, or "pull from my current CV").

2. **Bilingual confirmation.** If applicant-notes and target-company material are in different
   languages, ask the user which language the output résumé should be in. Default: match the
   target company's careers page language.

3. **Iteration cap confirmation.** Tell the user: *"Default is 5–10 iterations per sprint.
   The article warns against tight caps (Dutch Art Museum's winning design came at iteration 10).
   I'll plan for 10. Override?"*

4. **Source-material sanity check (safety gate — no fabrication).** Before dispatching Planner,
   confirm the user's applicant-notes exist and are non-empty. If notes are thin (<500 words),
   warn explicitly: *"Your notes are short. The Evaluator will reject any bullet not traceable
   to these notes. Want to add more notes before we start, or proceed with a shorter résumé?"*
   Require explicit user confirmation either way.

5. **Target-company consent gate (safety gate — data handling).** Confirm with the user:
   *"I'll fetch the target company's careers page and linked eng/product blog posts. I will not
   submit anything to the company. Proceed?"* Require explicit user yes.

6. **Dispatch Planner** (Agent call, V1-1). Input: intake summary + applicant-notes path + target
   URLs. Output: `spec.md`. Wait for `SPEC_READY:` sentinel.

7. **Present spec.md to user for confirmation.** One user-confirmation gate before any Generator
   runs. User can edit or approve.

8. **Sprint 1 loop (outline + source index):**
   - Dispatch Generator (Agent call). Generator negotiates sprint_contract.md.
   - Dispatch Evaluator (Agent call) to review contract.
   - If contract approved: Generator produces Sprint 1 outline + source-index.
   - Dispatch Evaluator to review Sprint 1 artifact.
   - If FAIL: return to Generator with critique.md; iterate up to cap.
   - If PASS: proceed to Sprint 2.

9. **Sprint 2 loop (full draft résumé):** same structure as Sprint 1.

10. **Sprint 3 loop (fit-gap analysis):** same structure.

11. **Final user handoff.** Present `resume.md` + `fit-gap.md`. Tell user:
    *"Evaluator verdict: PASS. Review once more before you use this. Remember — the Evaluator
    caught claims without sources; it cannot catch claims where your notes themselves are wrong."*

**Iteration cap:** **10** (chosen from the 5–10 range; top of range per V1-10 guidance).

**Context-anxiety detection triggers** (SKILL.md carries V1-21 list verbatim + résumé-specific
tell): Generator about to write a bullet without a source-index entry → `HANDOFF_NEEDED`.
Compaction is explicitly forbidden (V1-22).

**User confirmation gates (explicit, numbered):**
- Gate A (step 2): language choice if bilingual mismatch.
- Gate B (step 3): iteration cap override.
- Gate C (step 4): thin-notes warning acknowledgment.
- Gate D (step 5): target-company fetch consent.
- Gate E (step 7): spec.md review before Sprint 1.
- Gate F (step 11): final résumé review before external use.

**Safety gates (domain-specific blockers requiring user consent):**
- **No fabrication gate:** if the user ever asks the skill to "fill in" a gap in their background
  (e.g., "just say I know Kubernetes"), the orchestrator refuses and tells the user to update
  applicant-notes first. This is the résumé-domain equivalent of the article's self-evaluation
  bias guardrail — the skill exists to prevent this failure mode, and bypassing it defeats the
  purpose.
- **No external submission:** the skill never calls LinkedIn, job boards, or company APIs. It
  only reads the target company's public page. Explicit in Gate D.
- **PII awareness:** if applicant-notes contain SSN, government ID, or bank details, the
  orchestrator flags and asks the user to redact before dispatching subagents.

---

## 7. Article Coverage Mapping

Every row in `article-coverage-checklist.md` mapped to a concrete location the Generator must
produce. Blank = silent omission = FAIL per the checklist's coverage verdict rule.

### V1 Architecture (Frontend Design Harness)

| # | Article element | Mapping in tailored-resume-harness |
|---|---|---|
| V1-1 | GAN-style generator/evaluator split | SKILL.md activation flow §6 dispatches Planner, Generator, Evaluator as separate `Agent` calls per sprint. |
| V1-2 | 4–6 criteria, 1–5 scored | `references/rubric.md` has 4 criteria, 1–5 scale. |
| V1-3 | 2× weighting on weak-by-default axes | `references/rubric.md` — C1 Evidence Quality & C2 Company-Specific Fit weighted 2× with rationale. |
| V1-4 | Criteria as qualities, not references | `references/rubric.md` uses "Evidence Quality" / "Company-Specific Fit" — no "FAANG-caliber" etc. |
| V1-5 | Few-shot calibration examples | `references/evaluator-calibration.md` with 3 score anchors per criterion (full text from §5). |
| V1-6 | Iteration range 5–15 | SKILL.md activation flow §6 documents **5–10 range**, warns against low end (per V1-10). |
| V1-7 | Wall-clock tolerance ~4h | SKILL.md principles: "Do not artificially rush. Up to ~4h per run is acceptable when the domain requires deep traceability." |
| V1-8 | Evaluator tuning via few-shot | `references/evaluator-prompt.md` §Tuning + `evaluator-calibration.md`. |
| V1-9 | Refine/pivot/escalate after each evaluation | `references/generator-prompt.md` REFINE/PIVOT/ESCALATE block (verbatim from template). |
| V1-10 | Dutch Art Museum late-leap wisdom | SKILL.md §Iteration Wisdom cites the iteration-10 breakthrough + warns against <5 cap. |
| V1-11 | Middle iterations can beat final | `references/evaluator-prompt.md` §Iteration Quality Note. |

### V1 Architecture (Full-Stack Coding Harness)

| # | Article element | Mapping |
|---|---|---|
| V1-12 | Planner 1–4 sentence brief → full spec, ambitious | `references/planner-prompt.md` §1 states "ambitious about scope" verbatim. |
| V1-13 | Planner stays at product level | `references/planner-prompt.md` §1 "do not prescribe implementation details" verbatim. |
| V1-14 | Planner weaves differentiation hooks | `references/planner-prompt.md` §4 — differentiation hook on cross-functional pattern / undersold metric. |
| V1-15 | Generator self-evaluates before handoff | `references/generator-prompt.md` rule 5 + production-process step (e). |
| V1-16 | Evaluator adversarial probes + evidence | `references/evaluator-prompt.md` §Adversarial Probes — 9 probes listed in §5. |
| V1-17 | Hard threshold per criterion | `references/rubric.md` §Verdict Logic. |
| V1-18 | Sprint contract negotiation before work | SKILL.md §6 flow steps 8/9/10 — contract negotiated before each sprint. |
| V1-19 | File-based communication only | SKILL.md §File Handoff Contract — spec.md, sprint_contract.md, generator_report.md, critique.md, handoff.md, design_memo.md. |
| V1-20 | Evaluator tuning loop across runs | SKILL.md §Evaluator Tuning Workflow — numbered (a)–(d) procedure (P-3 compliance). |
| V1-21 | Context anxiety → handoff.md + reset | `references/generator-prompt.md` §Context-Anxiety Signals (5 triggers + résumé-specific tell). |
| V1-22 | Context reset ≠ compaction | SKILL.md §Principles — explicit "compaction preserves the anxiety state; fresh Agent call clears it." |

### V2 Architecture (Opus 4.5/4.6 simplification)

| # | Article element | Mapping |
|---|---|---|
| V2-1 | Sprint construct removed (tier=Simplified) | **N/A — tier=Full.** SKILL.md §V1 vs V2 Guidance justifies: "evidence traceability requires per-sprint Evaluator; simplified tier would let the Generator finish an entire shallow draft before any verification." |
| V2-2 | Single end-of-run Evaluator pass | **N/A — tier=Full.** Same justification. |
| V2-3 | Evaluator cost not fixed yes/no | SKILL.md §V1 vs V2 Guidance discusses model-specific tradeoffs. |
| V2-4 | Context anxiety largely eliminated on Opus 4.5+ | SKILL.md §V1 vs V2 Guidance has the model-specific table copied from article. |

### General Principles

| # | Article element | Mapping |
|---|---|---|
| G-1 | Every harness component encodes an assumption | SKILL.md §Principles + "remove one at a time when upgrading" guidance. |
| G-2 | Radical simplification failed → methodical one-at-a-time | SKILL.md §V1 vs V2 narrative. |
| G-3 | "Building Effective Agents" simplicity principle | SKILL.md §Principles quotes *"find the simplest solution possible, and only increase complexity when needed."* |
| G-4 | Experiment with the target model; read traces | SKILL.md §Evaluator Tuning Workflow — numbered (a) read logs → (b) find divergence → (c) update prompt/calibration → (d) rerun. |
| G-5 | Harness space shifts, not shrinks, with model improvement | SKILL.md §Closing Guidance. |

### Gap-Patch Probes

| # | Article element | Mapping |
|---|---|---|
| P-1 | Sensory-limit human checkpoint | **N/A — no sensory limits.** SKILL.md §Sensory Limits explicitly states: "résumé is pure markdown/text; visual craft is LLM-verifiable via structural cues (section order, bullet depth, word counts); no human checkpoint required." |
| P-2 | Planner ambition + differentiation hook | `references/planner-prompt.md` — explicit "be ambitious about scope" + résumé-domain differentiation hook per §5 above. |
| P-3 | Operational tuning loop | SKILL.md §Evaluator Tuning Workflow — (a)–(d) numbered steps. |

---

## 8. Additional Domain-Specific Validation

Beyond the coverage checklist, the Evaluator must also verify these résumé-domain invariants in
every critique.md:

1. **Source-index integrity:** Sprint 1's source-index table maps every bullet → source. Sprint 2
   must not introduce bullets that weren't in the approved Sprint 1 index. If Generator adds a
   bullet in Sprint 2, Evaluator rejects.

2. **Company-citation freshness:** company-facing URLs cited in spec.md §6 must be from the last
   24 months (careers pages, blog posts, announcements). A citation to a 2019 blog post to justify
   "they value X today" is a FAIL for C2.

3. **No-fabrication invariant:** if a bullet contains a metric, Evaluator must find that metric
   (same number, same units) in applicant-notes. Rounding is allowed (800ms → ~800ms); invention
   (unspecified latency → "30% improvement") is FAIL.

4. **Length render check:** Evaluator estimates rendered page count using word count (~450 words
   per rendered page for standard résumé formatting) and flags >2 pages hard fail, >1.75 pages
   warning.

5. **ATS section-header compliance:** the résumé uses standard section names (Experience / Work
   Experience, Education, Skills, Projects, Certifications). Creative section headers ("My
   Adventures") fail C3.

6. **Output-language consistency:** if user picked output language X in Gate A, the résumé is
   fully in X with no language-switched bullets (unless the applicant-notes explicitly contain
   a project name/title in the other language that should be preserved).

7. **PII leak check:** Evaluator scans for obvious PII that shouldn't be on a résumé (SSN,
   government ID, full DOB, home address). Presence of any → FAIL.

8. **Fit-gap referentiality:** every fit-gap action item must be tied to a specific gap the
   Generator observed during Sprint 1 or 2 (documented in generator_report.md). Generic action
   items not tied to an observed gap fail C4.

---

## 9. Test Scenarios

The Generator subagent should verify the generated skill against these three scenarios before
declaring it ready.

**Scenario 1 — Happy path (English applicant → English company).**
- Inputs: applicant-notes for a Senior Backend Engineer with 6 years of experience at two
  payment-adjacent startups; JD for Stripe Senior Backend Engineer role; careers page URL +
  two recent eng-blog URLs.
- Expected: Planner produces spec.md with Stripe-specific voice and priority themes. Generator
  produces a 1.5-page résumé with every bullet source-cited. Evaluator passes with C1/C2 ≥ 4
  after 2-3 iterations. Fit-gap has 3-4 dated action items.

**Scenario 2 — Bilingual (Korean applicant-notes → English résumé for US company).**
- Inputs: applicant-notes in Korean; JD for Meta ML Engineer role; careers page in English.
- Expected: Gate A triggers, user confirms English output. Planner produces spec.md with
  output_language=EN flag. Generator translates applicant claims while preserving source
  citations (applicant-notes line numbers still valid). Evaluator verifies no Korean bullets leak
  into final résumé (except preserved proper nouns). No fabrication during translation.

**Scenario 3 — Adversarial: thin applicant-notes.**
- Inputs: applicant-notes totaling ~300 words; JD for a Senior role requiring 5+ years.
- Expected: Gate C (thin-notes warning) fires. If user proceeds anyway, Evaluator fails C1
  because Generator has insufficient sources to write enough evidence-backed bullets. Generator
  emits ESCALATE after iteration 3 rather than fabricating. Skill does not produce a polished
  but unverifiable résumé. This is the test that proves the no-fabrication invariant works.

Each scenario exercises a different axis: Scenario 1 validates the happy-path coverage checklist,
Scenario 2 validates bilingual gate and translation traceability, Scenario 3 validates the
adversarial no-fabrication guarantee that is the whole point of using a harness for this domain.

---

**End of specification.** The Generator subagent has everything it needs to produce the skill
package without seeing the intake conversation.
