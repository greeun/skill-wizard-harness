# Rubric Design Guide

How to design effective evaluation rubrics for harness-powered skills.

## Core Principle: Weight What Claude Lacks

From the article: Claude already scores well on technical correctness, structure, and basic functionality by default. What it lacks without pressure is **originality, depth, specificity, and domain taste**.

Weight 2× the criteria where Claude is weakest. Weight 1× where it's already adequate.

## The Style Magnet Warning

"The wording of the criteria steered the generator in ways I didn't fully anticipate. Including phrases like 'the best designs are museum quality' pushed designs toward a particular visual convergence."

**Rule**: Write criteria as **qualities** (coherence, depth, specificity), not **references** (museum-quality, Apple-like, McKinsey-level). References belong in `spec.md` design intent, not in the rubric.

## Domain Rubric Templates

### Content (blog, report, whitepaper)

| Criterion | Weight | Measures |
|-----------|--------|----------|
| Depth & Specificity | 2× | Are claims backed by concrete evidence, examples, data? Or generic filler? |
| Voice & Originality | 2× | Does this read like it has a specific author with a perspective? Or could any AI have written it? |
| Structure & Flow | 1× | Logical progression, clear transitions, appropriate section lengths |
| Accuracy & Sourcing | 1× | Factual claims correct, sources cited where needed |

### Strategy (business plan, marketing, GTM)

| Criterion | Weight | Measures |
|-----------|--------|----------|
| Actionability | 2× | Could someone execute this plan this week? Specific timelines, owners, budgets? |
| Evidence Quality | 2× | Are market claims backed by real data, named competitors, cited sources? Or consultant-speak? |
| Internal Consistency | 1× | Do financials match strategy? Does timeline match resources? |
| Completeness | 1× | All required sections present with substantive content |

### Code / App (website, app, feature)

| Criterion | Weight | Measures |
|-----------|--------|----------|
| Design Quality | 2× | Coherent visual identity? Or collection of unrelated parts? |
| Originality | 2× | Deliberate custom choices? Or template defaults + AI slop patterns? |
| Craft | 1× | Typography, spacing, color harmony, contrast, responsive behavior |
| Functionality | 1× | Does the app let users accomplish what it promises? End-to-end, not stubs? |

### Creative (branding, UX, campaign)

| Criterion | Weight | Measures |
|-----------|--------|----------|
| Distinctive Identity | 2× | Would you recognize this as a specific brand? Or generic output? |
| Strategic Alignment | 2× | Does every creative choice serve the stated brand/campaign goals? |
| Execution Quality | 1× | Professional polish, consistent application across touchpoints |
| Versatility | 1× | Does it work across required formats and contexts? |

### Research / Analysis

| Criterion | Weight | Measures |
|-----------|--------|----------|
| Insight Depth | 2× | Are findings non-obvious? Does analysis go beyond surface-level patterns? |
| Methodological Rigor | 2× | Are conclusions justified by the evidence? Are limitations acknowledged? |
| Clarity | 1× | Can a non-expert follow the reasoning? |
| Completeness | 1× | All research questions addressed, data gaps identified |

## Scoring Guide

| Score | Meaning |
|-------|---------|
| 5 | Exceeds expectations — a domain expert would be impressed |
| 4 | Meets expectations — solid, professional quality |
| 3 | Acceptable but noticeably weak — needs improvement |
| 2 | Below expectations — significant gaps |
| 1 | Unacceptable — fundamental problems |

## Verdict Logic

```
All criteria ≥ 4 AND adversarial probes clean → PASS
Any 2×-weighted criterion < 4              → FAIL
Any 1×-weighted criterion < 3              → FAIL
Any Definition-of-Done item unverified     → FAIL
```

## Calibration Checkpoint

After scoring, if every category is ≥ 4, apply these secondary checks:

1. **Domain expert lens**: "What would a picky senior [domain expert] catch?"
2. **Adversary lens**: "What would a competitor point out as weak?"
3. **User lens**: "Would the target audience actually find this useful/compelling?"

Add any findings from these checks to the critique.
