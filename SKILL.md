---
name: skill-wizard-harness
version: 1.0.0
description: Create Claude Code skills that use the Planner-Generator-Evaluator harness pattern from Anthropic's "Harness Design for Long-Running Application Development." Use when creating skills that need multi-agent loops, quality assurance cycles, iterative refinement, autonomous long-running workflows, or when the user wants a skill that runs a planner/generator/evaluator pipeline. Trigger phrases — EN: "create harness skill", "skill with harness", "harness-based skill", "skill wizard v2", "skill-wizard-harness", "create skill with planner evaluator", "multi-agent skill", "autonomous skill". KO: "하네스 스킬 생성", "하네스 스킬", "스킬 위자드 v2", "플래너 제너레이터 스킬", "자율 스킬 생성", "멀티에이전트 스킬".
---

# Skill Wizard v2 — Harness-Powered Skill Creator

Creates Claude Code skills that embed the **Planner → Generator → Evaluator** harness pattern from Anthropic's *Harness Design for Long-Running Application Development* (Prithvi Rajasekaran, 2026). Skills created by this wizard produce autonomous, self-correcting workflows where generation and evaluation are structurally separated.

## What this wizard produces

A complete skill directory where the generated SKILL.md instructs Claude to:
1. Accept a user's topic/requirements as input
2. Run a **Planner** subagent to expand the brief into a detailed spec
3. Run a **Generator** subagent to implement iteratively with sprint contracts
4. Run an **Evaluator** subagent to verify against the spec with real evidence
5. Loop Generator ↔ Evaluator until quality thresholds pass or iteration cap reached
6. Handle **context resets**, **file-based handoffs**, and **self-evaluation bias** structurally

## The six harness principles every generated skill MUST encode

These are non-negotiable. They come directly from the article and define what makes a harness a harness.

### 1. Structural role separation (GAN-style)

Generation and evaluation are **different subagent processes**, dispatched via the `Agent` tool. A single session role-playing both sides defeats the purpose — self-evaluation bias causes agents to "confidently praise mediocre work." Separating the judge from the builder is the primary lever.

### 2. File-based handoffs as the only communication channel

Agents never see each other's reasoning or conversation. They communicate exclusively through files in a shared working directory:
- `spec.md` — Planner output, read by Generator and Evaluator
- `sprint_contract.md` — Generator ↔ Evaluator negotiation
- `generator_report.md` — Generator → Evaluator
- `critique.md` — Evaluator → Generator (includes REDIRECT authority)
- `handoff.md` — outgoing session → fresh incoming session (context reset)

### 3. Sprint contracts before implementation

Before coding, Generator proposes scope + observable verification checks. Evaluator reviews and amends. Only after agreement does implementation begin. This bridges high-level spec and testable deliverables "without over-specifying implementation too early."

### 4. Context resets over compaction

When a session fills, do not compact — end cleanly, write `handoff.md`, and start a **fresh** subagent session. Compaction preserves continuity but does not clear context anxiety (premature wrap-up behavior). A clean window does.

### 5. Rubric-based evaluation with evidence

The Evaluator grades against domain-specific criteria, each scored 1–5 with one-sentence justification AND evidence (screenshots, response bodies, file contents). "It looks fine" is never a pass. The Evaluator must be tuned — out-of-the-box LLM evaluators are too lenient and "talk themselves into approving."

### 6. Every harness component encodes an assumption

Each piece of scaffolding assumes the model can't do something alone. On model upgrade, stress-test each component. Drop what is no longer load-bearing. Redirect freed complexity toward harder problems.

---

## Wizard Workflow

Guide the user through these 7 phases sequentially:

```
Phase 1: Domain Discovery
    ↓
Phase 2: Harness Architecture
    ↓
Phase 3: Metadata Design
    ↓
Phase 4: Role Prompt Design
    ↓
Phase 5: Rubric & Contract Design
    ↓
Phase 6: Orchestration Logic
    ↓
Phase 7: Generation & Validation
```

---

## Phase 1: Domain Discovery

**Goal**: Understand what domain the harness-powered skill will operate in.

Ask these questions one at a time:

1. "What does this skill produce when a user gives it a topic or requirements?"
   - Examples: blog posts, business plans, marketing strategies, code projects, design systems, research reports
2. "What makes the output good vs. mediocre in this domain? Name 2-3 quality axes."
   - This directly shapes the Evaluator rubric.
3. "What are the observable, verifiable checks that prove the output works?"
   - Sprint contract checks must be things the Evaluator can actually test — not subjective feelings.
4. "Does this domain have sensory limits for an LLM evaluator?"
   - Audio, visual aesthetics, physical interaction — identify channels where the Evaluator needs human checkpoints.

**Output**: Domain statement, quality axes, verification approach, sensory limitations.

---

## Phase 2: Harness Architecture

**Goal**: Decide the right harness weight class.

Present the three tiers (from the article's evolution):

| Tier | Structure | When to use |
|------|-----------|-------------|
| **Full harness** | Planner → Generator (sprint-based) → Evaluator (per-sprint) | Complex, multi-feature output; tasks at the edge of model capability |
| **Simplified harness** | Planner → Generator (continuous) → Evaluator (single pass at end) | Strong model (Opus 4.5/4.6); moderate complexity |
| **Single-session discipline** | One session with Plan → Contract → Build → Self-grade (harsh) | Small scope; user wants speed over rigor; strong model |

Ask: "How complex is the typical output this skill will produce?"

**Decision logic:**
- If the output typically has 5+ independent features or takes >1 hour → **Full harness**
- If the output is a coherent single artifact (report, plan, post) → **Simplified harness**
- If the output is small and well-defined → **Single-session** (but still with rubric self-grading)

For the chosen tier, also decide:
- **Iteration cap**: How many Evaluator rounds before escalating? (Default: 5–15 for design slices in full harness, 3–5 for simplified)
- **Context reset strategy**: Always reset between Generator rounds (full), or only on context pressure (simplified)?

### Model-specific harness guidance

| Model class | Context anxiety | Recommended tier | Notes |
|-------------|----------------|-----------------|-------|
| **Sonnet 4.5** | Strong — wraps up prematurely | Full harness (V1) | Small sprints, aggressive Evaluator, firm context resets between every sprint |
| **Opus 4.5** | Largely eliminated | Simplified harness (V2) | Multi-hour coherent sessions, sprint decomposition often droppable |
| **Opus 4.6** | Eliminated; improved planning, long-context, debugging | Simplified or single-session | Can sustain 2+ hour builds; re-examine every harness component — drop what's not load-bearing |

**General rule**: Every harness component encodes an assumption about what the model can't do alone. On model upgrade, stress-test each assumption individually — remove one component at a time and measure impact. The article warns against radical simplification (removing everything at once failed); methodical one-at-a-time removal reveals which pieces are actually load-bearing.

---

## Phase 3: Metadata Design

**Goal**: Craft the skill's name and description.

### Name

Format: `domain-harness` or `domain-action-harness`
Examples: `blog-post-harness`, `business-plan-harness`, `marketing-strategy-harness`

### Description

Must include:
1. What the skill produces
2. That it uses the Planner → Generator → Evaluator pattern
3. Trigger phrases in both EN and KO (if user is bilingual)

**Template**:
```
[Domain] [output type] using Planner → Generator → Evaluator harness from Anthropic's
"Harness Design for Long-Running Application Development." Generates [output] through
iterative refinement with file-based handoffs, sprint contracts, rubric-based evaluation,
and self-evaluation bias prevention. Use when [trigger 1], [trigger 2], or [trigger 3].
```

Generate 3 options, let the user pick or refine.

---

## Phase 4: Role Prompt Design

**Goal**: Write the three core prompts that will be embedded in the generated skill.

For each role, the prompt must be **self-contained** — the subagent starts with zero context except the prompt and the files it is told to read.

### Planner Prompt Template

Adapt from this skeleton based on the domain:

```
You are the PLANNER in a three-agent [DOMAIN] harness (Planner → Generator → Evaluator).

Your job: turn a short [DOMAIN] request (1–4 sentences) into a detailed [OUTPUT_TYPE] specification
that a separate Generator agent will produce without ever seeing this conversation.

Hard rules:
1. Stay at the [PRODUCT/CONTENT] level, not the implementation level.
   - Describe [DOMAIN-SPECIFIC DELIVERABLES AND STRUCTURE].
   - Do NOT prescribe [IMPLEMENTATION DETAILS THE GENERATOR SHOULD OWN].
2. Be ambitious about scope but concrete about [OBSERVABLE CRITERIA].
3. [DOMAIN-SPECIFIC DESIGN INTENT GUIDANCE]
4. Write the spec as if the reader has zero prior context.

Output — write to `spec.md`:
[DOMAIN-SPECIFIC SPEC TEMPLATE WITH SECTIONS]

When finished, output only: `SPEC_READY: spec.md`
```

### Generator Prompt Template

```
You are the GENERATOR in a three-agent [DOMAIN] harness. A Planner wrote `spec.md`;
an Evaluator will test your work against it. You will never see the Planner's or
Evaluator's reasoning — only their files.

Operating rules:
1. READ `spec.md` in full before producing anything.
2. Before starting, negotiate a SPRINT CONTRACT with the Evaluator:
   - Write `sprint_contract.md` listing (a) what you will produce, (b) the exact
     observable checks the Evaluator should run, (c) [DOMAIN-SPECIFIC DELIVERY DETAILS].
   - Wait for approval before proceeding.
3. [DOMAIN-SPECIFIC PRODUCTION PROCESS]
4. Never mark work complete until every check in the sprint contract passes.

Anti-patterns — do NOT do these:
- Declaring victory on shallow completion.
- Wrapping up early because context feels full. Write `handoff.md` and stop cleanly.
- Self-congratulatory summaries. Report facts.

Output — write to `generator_report.md`:
[DOMAIN-SPECIFIC REPORT TEMPLATE]

Then output only: `READY_FOR_QA: generator_report.md`
```

### Evaluator Prompt Template

```
You are the EVALUATOR in a three-agent [DOMAIN] harness. The Generator claims work is ready.
Verify those claims against `spec.md` like a skeptical [DOMAIN EXPERT].

You are NOT the Generator's teammate. You are their adversary in service of the user.
Default to skepticism. "It looks fine" is not a pass.

Workflow:
1. Read `spec.md`, `sprint_contract.md`, `generator_report.md`.
2. Run EVERY check in the sprint contract PLUS adversarial probes:
   [DOMAIN-SPECIFIC ADVERSARIAL CHECKS]
3. Capture evidence. Do not describe what you "would" see; observe it.

Grading rubric — score each 1–5 with justification AND evidence:
[DOMAIN-SPECIFIC RUBRIC CRITERIA — identify which criteria to weight 2×]

If all criteria ≥4 and adversarial probes clean, PASS. Otherwise FAIL with specific findings.

Output — write to `critique.md`:

# Critique
## Verdict: PASS | FAIL
## Rubric scores (with justification + evidence)
## Blocking issues (numbered; reproduction steps, actual vs expected, severity)
## Non-blocking notes
## Recommended next focus

Calibration rules:
- If you score every category ≥4, ask what a picky domain expert would catch. Add those findings.
- Never pass when any Definition-of-Done item in `spec.md` is unverified.
- Do NOT praise. Report.

Then output only: `CRITIQUE_READY: critique.md`
```

**Ask the user** to review each role prompt and refine the domain-specific sections.

---

## Phase 5: Rubric & Contract Design

**Goal**: Define the evaluation rubric and sprint contract template for the domain.

### Rubric Design

From Phase 1's quality axes, create 4–6 grading criteria. For each:

| Criterion | What it measures | Weight | Why this weight |
|-----------|-----------------|--------|-----------------|
| [Name] | [Description] | 1× or 2× | [Rationale] |

**Weighting rule from the article**: Weight 2× the criteria where Claude is weakest by default. Claude typically scores well on technical correctness and structure; it scores poorly on originality, depth, and domain-specific taste.

**Calibration warning**: "The wording of criteria steered the generator in ways I didn't fully anticipate." Write criteria as **qualities** (coherence, depth, specificity), not **references** (museum-quality, Apple-like). References become style magnets.

**Late-stage creative leaps**: In the article's Dutch art museum experiment, iterations 1–9 produced competent but conventional output. On iteration 10, the model scrapped the approach entirely and reimagined it as a spatial 3D experience — "the kind of creative leap that I hadn't seen before from a single-pass generation." Don't cap iterations so tightly that you kill these breakthroughs. But require `design_memo.md` in every context-reset handoff to prevent amnesia-driven pivots from being confused with genuine insight. Late pivots must be justified by rubric evidence, not by the Generator forgetting what it was doing.

### Sprint Contract Template

```markdown
# Sprint Contract: [Sprint Name]

## Deliverables
1. [Specific deliverable]
2. [Specific deliverable]

## Observable Checks
For each deliverable, the Evaluator will verify:
- [ ] [Check 1 — must be binary pass/fail, not subjective]
- [ ] [Check 2]

## Delivery Details
- [Domain-specific: files to produce, format, where to save, how to run/test]

## Definition of Done
All checks above pass AND all Definition-of-Done items from spec.md covered by this sprint are verified.
```

---

## Phase 6: Orchestration Logic

**Goal**: Write the orchestrator control flow that goes into the generated skill's SKILL.md.

The orchestrator is **not a subagent** — it is the instruction that Claude follows directly when the skill is activated.

### Full Harness Orchestrator Template

```
When the user provides a topic or requirements:

1. Confirm working directory (default: `./<skill-domain>-output/`).
2. Dispatch Planner subagent. Wait for `SPEC_READY: spec.md`.
3. Show spec.md to user, confirm before continuing.
4. Dispatch Generator: "Read spec.md, propose sprint_contract.md, wait."
5. Dispatch Evaluator: "Review sprint_contract.md. Approve or amend."
6. Dispatch Generator: "Execute approved contract. Produce generator_report.md."
7. Dispatch Evaluator: "Verify. Produce critique.md."
8. FAIL → fresh Generator with spec.md + critique.md only. Back to 7.
   PASS + DoD complete → STOP.
   PASS + DoD incomplete → next sprint at 4.

Safeguards:
- Cap Evaluator iterations per sprint (default [N]). On cap reached WITHOUT pass,
  escalate to user — do NOT silently stop. Late iterations can produce breakthroughs.
- Context-anxiety detection: if Generator rushes or skips verification → end cleanly,
  write handoff.md, dispatch fresh Generator.
- Report to user only at milestones: spec approved / sprint passed / complete / blocked.
```

### Simplified Harness Orchestrator Template

```
When the user provides a topic or requirements:

1. Confirm working directory.
2. Dispatch Planner subagent. Wait for `SPEC_READY: spec.md`.
3. Show spec.md to user, confirm.
4. Dispatch Generator: "Read spec.md, write sprint_contract.md, then execute fully.
   Produce generator_report.md."
5. Dispatch Evaluator: "Full evaluation pass. Produce critique.md."
6. FAIL → Dispatch fresh Generator with spec.md + critique.md. Back to 5.
   PASS → STOP.

Safeguards:
- Cap total evaluation rounds (default [N]).
- On context pressure: handoff.md + fresh session.
```

### File Contract (always include in generated skill)

```
Files (single source of truth):
  spec.md              Planner → Generator, Evaluator
  sprint_contract.md   Generator ↔ Evaluator (negotiated)
  generator_report.md  Generator → Evaluator
  critique.md          Evaluator → Generator
  handoff.md           outgoing session → fresh session (context reset)
```

---

## Phase 7: Generation & Validation

**Goal**: Assemble and validate the complete skill.

### Step 1: Create skill directory

```
<skill-name>/
├── SKILL.md
└── references/
    ├── planner-prompt.md
    ├── generator-prompt.md
    ├── evaluator-prompt.md
    └── rubric.md
```

**Why references?** The three role prompts are substantial. Keeping them in `references/` prevents SKILL.md body from exceeding 500 lines. SKILL.md contains the orchestration logic and references the prompts.

### Step 2: Write SKILL.md

Structure:

```markdown
---
name: [skill-name]
description: [from Phase 3]
---

# [Skill Title]

[One paragraph: what this skill does and the harness pattern it uses.]

## The two failures this harness prevents

1. **Self-evaluation bias.** [Domain-specific explanation]
2. **Context anxiety.** [Domain-specific explanation]

## Non-negotiable principles

[6 principles adapted to the domain — from the six principles section above]

## Activation

[Orchestrator flow from Phase 6]

## Role Prompts

- **Planner**: See [references/planner-prompt.md](references/planner-prompt.md)
- **Generator**: See [references/generator-prompt.md](references/generator-prompt.md)
- **Evaluator**: See [references/evaluator-prompt.md](references/evaluator-prompt.md)

## Evaluation Rubric

See [references/rubric.md](references/rubric.md)

## File Handoff Contract

[File contract from Phase 6]

## V1 vs V2 guidance

[When to use full vs simplified harness for this domain]

## Iteration wisdom

- **Late-stage creative leaps are possible.** In the article's experiments, iteration 10 produced a breakthrough that iterations 1–9 never approached. Don't cap iterations so tightly that you kill this potential. When the iteration cap is reached without PASS, escalate to the user rather than silently stopping.
- **Middle iterations can be better than the final one.** The Evaluator's Iteration Quality Note exists for this reason — if iteration 6 had better qualities than iteration 9, that feedback is critical.
- **Pivots must be justified.** A late-stage direction change requires either an explicit `REDIRECT` from the Evaluator, or a `design_memo.md` from the Generator explaining why. Context-reset amnesia is not insight.
- **Stress-test harness components on model upgrade.** Remove one component at a time and measure impact. Radical simplification (removing everything at once) failed in the article's experiments; methodical one-at-a-time removal reveals what's load-bearing.

## Red flags — STOP if you catch yourself doing these

[Domain-specific anti-pattern table]
```

### Step 3: Write reference files

Write each role prompt and rubric to their respective files in `references/`.

### Step 4: Validate

Run through these checks:

- [ ] SKILL.md body under 500 lines
- [ ] Description includes function + triggers + harness mention
- [ ] All three role prompts are self-contained (zero shared context assumption)
- [ ] Planner prompt stays at product/content level, not implementation
- [ ] Generator prompt includes sprint contract negotiation step
- [ ] Generator prompt includes anti-patterns: no self-congratulation, no premature wrap-up, handoff.md on context pressure
- [ ] Evaluator prompt includes: skepticism default, evidence requirement, calibration rules
- [ ] Rubric has 4–6 criteria with explicit 2× weights on weak-by-default axes
- [ ] Rubric criteria describe qualities, not references (no style magnets)
- [ ] File handoff contract is complete and consistent across all prompts
- [ ] Orchestrator includes iteration cap and context-anxiety detection
- [ ] Orchestrator includes user confirmation gate after spec

### Step 5: Test scenarios

Generate 3 test cases:

1. **Happy path**: User gives a clear, moderate-complexity brief
2. **Edge case**: User gives a vague one-liner — Planner must expand substantially
3. **Out of scope**: A request that sounds similar but should NOT trigger this skill

---

## Quick Reference: Common Mistakes in Harness Skills

| Mistake | Why it fails | Fix |
|---------|-------------|-----|
| Single session plays all roles | Self-evaluation bias — agent praises own work | Dispatch separate subagents via `Agent` tool |
| Pasting critique into Generator chat | Leaks evaluator reasoning, undermines independence | File-based handoff only: Generator reads `critique.md` from disk |
| No sprint contract | Generator builds wrong thing, Evaluator has no criteria | Always negotiate contract before implementation |
| Compacting instead of resetting | Context anxiety persists through compaction | Fresh session with `handoff.md` |
| Rubric uses reference words | "Museum quality" makes everything look like museums | Describe qualities: coherence, specificity, depth |
| Evaluator too lenient | Default LLM behavior is to approve | Tune with: "Default to skepticism. 'It looks fine' is not a pass." |
| No evidence requirement | Evaluator describes what it "would" see | Require captured evidence: files, outputs, screenshots |
| No iteration cap | Infinite thrashing loop | Cap iterations, escalate to user on breach |
| Generator self-congratulates | Wastes tokens, masks real status | "Report facts, not praise" in every Generator prompt |
| Skipping user spec confirmation | Harness builds the wrong thing autonomously | Always show spec.md and wait for user approval |
