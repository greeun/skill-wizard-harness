# Skill Wizard Harness — v2.0.0

A meta-skill for creating Claude Code skills that use the **Planner → Generator → Evaluator** harness pattern from Anthropic's [Harness Design for Long-Running Application Development](https://www.anthropic.com/engineering/harness-design-long-running-apps) (Prithvi Rajasekaran, 2026).

## What It Does

This wizard guides you through creating a new skill where — when a user provides a topic or requirements — the skill automatically runs a multi-agent loop:

1. **Planner** expands a brief (1–4 sentences) into a full product/content spec
2. **Generator** implements iteratively with negotiated sprint contracts
3. **Evaluator** verifies against the spec with real evidence and rubric-based grading
4. Loop until quality thresholds pass or iteration cap reached

Each agent runs as a separate subagent (via `Agent` tool), communicating only through files — never seeing each other's reasoning.

## Why Use a Harness?

Two failure modes this pattern structurally prevents:

- **Self-evaluation bias** — When asked to grade their own work, LLMs "confidently praise mediocre output." Separating generation from evaluation is the primary lever.
- **Context anxiety** — As context fills, models wrap up prematurely. Context resets (fresh sessions with file handoffs) fix this; compaction does not.

## Installation

Copy the `skill-wizard-harness/` directory into your Claude Code skills folder:

```bash
cp -r skill-wizard-harness/ ~/.claude/skills/skill-wizard-harness/
```

## Usage

Invoke via Claude Code:

```
/skill-wizard-harness
```

Or trigger naturally with phrases like:
- "Create a harness-based skill for generating marketing reports"
- "I need a skill with planner/evaluator for blog posts"
- "Build a multi-agent skill for code review"

The wizard walks you through 7 phases:

```
Phase 1: Domain Discovery        → What domain? What quality axes?
Phase 2: Harness Architecture    → Full / Simplified / Single-session?
Phase 3: Metadata Design         → Name + description
Phase 4: Role Prompt Design      → Planner, Generator, Evaluator prompts
Phase 5: Rubric & Contract       → Evaluation criteria + sprint contract template
Phase 6: Orchestration Logic     → Control flow for the generated skill
Phase 7: Generation & Validation → Assemble, validate, test
```

## What Gets Generated

A complete skill directory:

```
<your-skill-name>/
├── SKILL.md                    # Orchestration logic + principles
└── references/
    ├── planner-prompt.md       # Self-contained Planner prompt
    ├── generator-prompt.md     # Self-contained Generator prompt
    ├── evaluator-prompt.md     # Self-contained Evaluator prompt
    └── rubric.md               # Domain-specific grading criteria
```

## Key Concepts from the Article

| Concept | How It's Applied |
|---------|-----------------|
| **GAN-style separation** | Generator and Evaluator are different subagent processes |
| **File-based handoffs** | `spec.md`, `sprint_contract.md`, `generator_report.md`, `critique.md`, `handoff.md` |
| **Sprint contracts** | Generator proposes scope + checks; Evaluator approves before implementation |
| **Context resets** | Fresh sessions with `handoff.md`, not compaction |
| **Rubric grading** | 1–5 scores with evidence; 2x weight on weak-by-default axes |
| **Evaluator tuning** | Few-shot calibration, skepticism default, anti-leniency rules |
| **Style magnet warning** | Rubric uses qualities (coherence, depth), not references (museum-quality) |
| **V1 → V2 evolution** | Full harness for complex tasks, simplified for strong models |
| **One-at-a-time removal** | Stress-test each component on model upgrade; drop what's not load-bearing |
| **Late-stage creative leaps** | Don't cap iterations so tightly that you kill breakthroughs; escalate to user instead of silently stopping |
| **Middle iterations can win** | Evaluator's "Iteration Quality Note" tracks when a prior iteration was better than the current one |
| **Stub-feature detection** | Evaluator distinguishes "UI exists" from "interaction works end-to-end" |
| **design_memo.md** | Generator must document direction rationale on pivots; prevents amnesia-driven changes after context resets |

## Harness Tiers

| Tier | When to Use |
|------|-------------|
| **Full** (V1) | Complex output, 5+ features, Sonnet-class models |
| **Simplified** (V2) | Single coherent artifact, Opus-class models |
| **Single-session** | Small scope, strong model, speed over rigor |

## Model-Specific Guidance

| Model | Context Anxiety | Recommended Tier | Notes |
|-------|----------------|-----------------|-------|
| **Sonnet 4.5** | Strong — wraps up prematurely | Full harness (V1) | Small sprints, aggressive Evaluator, firm context resets |
| **Opus 4.5** | Largely eliminated | Simplified (V2) | Multi-hour coherent sessions, sprint decomposition droppable |
| **Opus 4.6** | Eliminated; improved planning, long-context, debugging | Simplified or single-session | 2+ hour builds; re-examine every component |

## Iteration Wisdom (from the Article's Experiments)

- **Late-stage creative leaps are real.** In the Dutch art museum experiment, iteration 10 produced a breakthrough that iterations 1–9 never approached. Iteration caps should escalate to the user, not silently stop.
- **Middle iterations can beat the final one.** The Evaluator's "Iteration Quality Note" exists to catch regression — "iteration 6 had better spatial rhythm than iteration 9" is valid feedback.
- **Pivots must be justified.** Require either an explicit `REDIRECT` from the Evaluator, or a `design_memo.md` from the Generator explaining why. Context-reset amnesia is not insight.

## File Structure

```
skill-wizard-harness/
├── SKILL.md                                 # Main wizard (479 lines)
├── README.md                                # This file
├── README.ko.md                             # Korean version
└── references/
    ├── planner-prompt-template.md           # Planner prompt skeleton + domain adaptations
    ├── generator-prompt-template.md         # Generator prompt skeleton + domain adaptations
    ├── evaluator-prompt-template.md         # Evaluator prompt skeleton + tuning workflow
    └── rubric.md                            # Rubric design guide + domain templates
```

## License

MIT
