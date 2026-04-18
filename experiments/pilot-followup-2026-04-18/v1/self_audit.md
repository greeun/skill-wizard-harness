# Self-audit — resume-tailor-harness (v1 single-session generation)

This audit is performed by the same session that generated the skill. I do not have access to a pre-made coverage checklist; I'm generating the list of article elements from memory of my WebFetch read, then checking my skill files against it.

## Article elements I recall and should have covered

### A. Core problem framing
1. Loss of coherence over long tasks
2. Context anxiety (premature wrap-up near perceived context limit)
3. Self-evaluation / leniency bias ("confidently praising mediocre work")
4. Feature incompleteness / stubbing
5. Shallow testing (happy-path only)

### B. Three-role architecture
6. Planner — expands brief → spec, ambitious scope, doesn't over-specify implementation
7. Generator — implements, self-evaluates, works a unit at a time
8. Evaluator — independent QA, tests, verifies contract, gives actionable feedback
9. Role-separation principle: "the agent doing the work ≠ the agent judging it"

### C. Context management
10. Context resets outperform in-place compaction (for models with context anxiety)
11. File-based handoffs between agents
12. Fresh context for each subagent dispatch
13. Automatic compaction in newer models (Opus 4.6) reduces need for manual resets

### D. Sprint contracts
14. Pre-implementation negotiation of "done" criteria
15. Generator proposes what it'll build and how it'll be tested
16. Evaluator reviews contract for spec alignment before work starts
17. Bridges gap between high-level stories and testable details

### E. Rubric-based evaluation
18. Subjective quality made concrete via axes
19. Weighting strategy: emphasize weak-by-default axes
20. Few-shot calibration anchors for evaluator
21. Hard thresholds: any critical failure = sprint fail
22. Specific pass/fail thresholds

### F. Self-eval bias prevention
23. Independent evaluator agent (separate from generator)
24. Skepticism-promoting language in evaluator prompt
25. Concrete failure examples in prompts
26. Iterate on evaluator behavior by reading logs, updating when it diverges from human judgment
27. File + line level feedback (not hand-waving)

### G. Context anxiety prevention
28. Full context reset at sprint boundaries
29. Structured state handoff via files

### H. Prompt engineering per role
30. Planner: ambitious scope, product-level not implementation-level, no cascade errors
31. Generator: feature-at-a-time, self-evaluate before handoff, strategic pivots
32. Evaluator: skepticism, test edge cases, penalize AI telltale patterns, explicit pass/fail

### I. Failure modes & mitigations
33. Over-scoping → planner expansion
34. Shallow testing → iterative prompt refinement, log review
35. Feature stubbing → detailed sprint contracts
36. Loss of coherence → decomposition + resets
37. Premature completion → full reset not compaction
38. Leniency bias → independent evaluator + calibration

### J. Metrics / telemetry
39. Cost tracking (tokens, iteration count, duration)
40. Quality signals (criteria scores over time, bug count)
41. Evaluator precision
42. Number of QA loops before pass

### K. Concrete examples from article
43. Retro video game maker (20 min solo vs 6 hr harness)
44. Dutch art museum website (creative leap at iteration 10)
45. DAW example (Opus 4.6 simplified harness, 3h50m)

### L. Named anti-patterns
46. "Context anxiety" term
47. "AI slop" / telltale AI patterns
48. Overly detailed upfront specs
49. Leniency bias
50. Shallow QA

### M. Load-bearing scaffolding principle
51. Every harness component encodes an assumption — stress-test and strip as models improve
52. Opus 4.6 made some pieces unnecessary

---

## Coverage check against my generated files

| # | Element | Where covered | Grade |
|---|---------|---------------|-------|
| 1 | Loss of coherence | SKILL.md sprint rationale; playbook anti-pattern table | Covered |
| 2 | Context anxiety | SKILL.md rule 5; playbook anti-pattern table | Covered |
| 3 | Self-eval / leniency bias | evaluator-prompt.md opening; rubric/calibration whole premise | Covered |
| 4 | Feature incompleteness | playbook "feature stubbing" row; evaluator contract-compliance check | Covered |
| 5 | Shallow testing | evaluator-prompt swap test + hard thresholds | Covered |
| 6 | Planner role | planner-prompt.md; SKILL architecture diagram | Covered |
| 7 | Generator role | generator-prompt.md; explicit self-eval section | Covered |
| 8 | Evaluator role | evaluator-prompt.md; independence stressed | Covered |
| 9 | Role-separation principle | SKILL.md rule 1; evaluator-prompt opener (quotes the principle) | Covered strongly |
| 10 | Resets > compaction | SKILL.md rule 5; generator-prompt "fresh context" | Covered |
| 11 | File-based handoffs | SKILL.md architecture + rule 2; every prompt reads from files | Covered strongly |
| 12 | Fresh context per dispatch | Generator + Evaluator prompts open with "fresh context" | Covered |
| 13 | Automatic compaction in newer models | NOT covered explicitly | **Gap** |
| 14 | Pre-implementation negotiation | sprint-playbook.md explains; generator acknowledges contract in self-eval | Covered |
| 15 | Generator proposes done | Partially covered — contract is written by Planner, Generator acknowledges. The article's two-way negotiation is weaker here. | **Partial gap** |
| 16 | Evaluator reviews contract before work | NOT covered — I skipped the evaluator-reviews-contract-before-generation step | **Gap** |
| 17 | Bridges story↔testable detail | sprint-playbook.md "why sprint contracts exist" paragraph quotes this | Covered |
| 18 | Subjective quality via axes | rubric.md | Covered |
| 19 | Weight weak axes | rubric.md weight table; SKILL.md axes section | Covered strongly |
| 20 | Few-shot calibration | evaluator-calibration.md | Covered |
| 21 | Hard thresholds | rubric.md + evaluator-prompt.md | Covered |
| 22 | Pass/fail thresholds | rubric.md | Covered |
| 23 | Independent evaluator | SKILL.md rule 1, evaluator-prompt.md | Covered |
| 24 | Skepticism language | evaluator-prompt.md ("no loyalty", "leniency self-check") | Covered |
| 25 | Concrete failure examples | evaluator-calibration.md axis examples | Covered |
| 26 | Iterate evaluator behavior | NOT covered — no "how to tune this evaluator over time" guidance | **Gap** |
| 27 | File+line feedback | evaluator-prompt.md explicit instruction | Covered |
| 28 | Full reset at sprint boundary | SKILL.md architecture | Covered |
| 29 | Structured state handoff | Entire file-handoff design | Covered |
| 30 | Planner: ambitious+product-level | planner-prompt.md "Planning principles" | Covered |
| 31 | Generator: feature-at-a-time, self-eval, strategic pivots | generator-prompt.md has strategic-pivot section | Covered |
| 32 | Evaluator: skepticism, edge cases, telltale patterns | evaluator-prompt.md anti-pattern watch list | Covered |
| 33 | Over-scoping mitigation | sprint-playbook anti-pattern table | Covered |
| 34 | Shallow testing mitigation | evaluator swap test + hard thresholds | Covered |
| 35 | Feature stubbing mitigation | sprint-playbook "deliverables list named files" | Covered |
| 36 | Loss of coherence mitigation | decomposition into 6 sprints | Covered |
| 37 | Premature completion mitigation | sprint bounds | Covered |
| 38 | Leniency bias mitigation | evaluator-calibration + independent evaluator | Covered |
| 39 | Cost tracking | SKILL.md mentions "cost signals if available" but no harness telemetry | **Partial gap** |
| 40 | Quality signals over iterations | NOT tracked explicitly | **Gap** |
| 41 | Evaluator precision | NOT addressed | **Gap** |
| 42 | QA loops before pass | Iteration count mentioned (max 3) | Covered |
| 43–45 | Concrete examples | Not needed in a generated skill; used for calibration inspiration only | N/A |
| 46 | "Context anxiety" term | Referenced by name in SKILL.md rule 5 and playbook | Covered |
| 47 | Telltale AI / consultant-speak patterns | evaluator-prompt.md anti-pattern list; rubric descriptors | Covered strongly — domain-adapted |
| 48 | Overly detailed upfront specs | planner-prompt.md anti-patterns section | Covered |
| 49 | Leniency bias | see #38 | Covered |
| 50 | Shallow QA | see #34 | Covered |
| 51 | Load-bearing scaffolding principle | SKILL.md "load-bearing scaffolding" section mentions but does NOT include a "strip pieces when model improves" protocol | **Partial gap** |
| 52 | Opus 4.6 simplification path | NOT covered (skill was scoped for Opus 4.5 Full tier per intake) | Acceptable omission |

## Scoring

- Strong coverage: 38 elements
- Partial gaps: 4 (Generator-proposes negotiation; cost telemetry; load-bearing strip protocol; evaluator iteration guidance)
- Clear gaps: 4 (automatic compaction note; evaluator-reviews-contract-before-work step; evaluator precision metric; quality-signals-over-time tracking)

Out of ~48 applicable elements (examples excluded), that's ~38 strong + 4 partial ≈ **42/48 meaningful coverage** (87%).

## Honest weaknesses

1. **Sprint contract negotiation is one-way.** In the article, Generator proposes → Evaluator reviews before work starts. In my design, Planner writes the contract and both subagents read it; the Generator only "acknowledges" in self-eval. I skipped the proactive contract-review round because it would add a fourth subagent dispatch per sprint. Worth revisiting — this is actually a core article idea.

2. **No guidance on evolving the evaluator over iterations.** The article stresses reading evaluator logs and updating prompts when judgment diverges from human. My skill is a one-shot; there's no "here's how to tune this over weeks of use" section. Could add a maintenance section to SKILL.md.

3. **No telemetry/cost tracking instructions.** The article tracks tokens, duration, iteration counts. I mention iteration counts but don't instruct the main session to log them. Could add a `run_log.md` artefact.

4. **Load-bearing scaffolding stripping.** I mention the principle but don't include a "run this on new models to check what's still needed" protocol. Worth a final section in SKILL.md or an appendix.

5. **Automatic compaction awareness.** No mention of the Opus 4.6 finding that some pieces become unnecessary. Skill is locked to Opus 4.5 per intake; acceptable but worth a footnote for future-proofing.

## What I am confident about

- Role separation is structurally enforced, not just exhorted.
- Weak-by-default axes are doubled AND have hard thresholds — the intake's #1 goal.
- Evidence traceability is a first-class output (`evidence_map.md`, `company_fit_map.md`) that the Evaluator actually checks, not a vague promise.
- Consultant-speak anti-pattern is pulled directly into the rubric and evaluator's banned-phrase audit.
- Swap test is the domain-specific analogue of the article's "telltale AI patterns" detection, operationalized as a concrete protocol.
- Six sprints is a deliberate bound — not more (decomposition waste), not fewer (coherence risk).

## Final self-judgment

Quality: **solid, with identified gaps worth a second pass.** The skill captures the article's core mechanics (role separation, file handoffs, sprint contracts, weighted rubric with hard thresholds, evaluator calibration) and ports the telltale-pattern detection faithfully to the résumé domain. The gaps are mostly additive (telemetry, evaluator tuning over time, load-bearing strip protocol) rather than structural flaws.

Would I ship this? Yes, with a note to the user that v1.1 could add the contract-negotiation round and a `run_log.md` telemetry artefact.
