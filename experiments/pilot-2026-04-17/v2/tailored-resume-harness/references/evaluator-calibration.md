# Evaluator Calibration — Tailored Résumé Harness

Few-shot score anchors for the Evaluator. Read this before scoring every critique. These
anchors are concrete résumé-domain examples, not placeholders. They exist because
out-of-the-box LLM evaluators drift toward "4 out of 5 on everything" — the anchors below
hold the scale honest.

Per the source article: *"calibrating the evaluator using few-shot examples with detailed
score breakdowns ensured the evaluator's judgment aligned with my preferences, and reduced
score drift across iterations."*

Rule of use: before you assign a score on any criterion, locate the bullet/phrase/item in
the critique target, then compare it to the 1/3/5 anchors below. If the artifact is between
two anchors, pick the lower one — skepticism is the job.

---

## C1 — Evidence Quality (weight 2×)

**Score 1/5 — Unacceptable.**
Example bullet: *"Led transformation initiatives across global teams, driving business value
through data-driven decision-making."*
Why 1/5: Zero specifics. No number, no scope, no employer, no date, no artifact. Zero
citation is possible because there is nothing to cite. This is the consultant-speak template
bullet the entire harness exists to prevent. If more than one bullet like this appears in
the résumé, C1 is 1/5 regardless of the rest of the draft.

**Score 3/5 — Acceptable but noticeably weak.**
Example bullet: *"Led a 4-person team that migrated the payment service, reducing latency."*
Why 3/5: Team size is concrete (4-person) and can be cited. The project is named (payment
service migration). But "reducing latency" has no number even though applicant-notes line 47
says "p99 dropped from 800ms to 200ms during the Rust migration." The Generator had the
source and softened it to filler. Partial credit for scope + team + project; penalty for
leaving a cited metric on the floor. This is the most common "looks polished but is actually
weak" pattern and must not be mistaken for 4/5.

**Score 5/5 — Exceeds expectations.**
Example bullet: *"Led 4-engineer migration of payment service to Rust (Q2 2024), cutting p99
latency 800ms → 200ms [applicant-notes L47]; rollout covered 12M req/day [L48]."*
Why 5/5: Every number traceable, team size concrete, timeline specific (Q2 2024, also in
notes), cites visible in-line. A picky recruiter can verify every claim in 10 seconds using
only the résumé + notes. No consultant-speak verbs. No filler. This is what the harness is
trying to produce every time.

---

## C2 — Company-Specific Fit (weight 2×)

**Score 1/5 — Unacceptable.**
Example: Résumé targeting Stripe that leads with a Summary line reading *"Full-stack
generalist with 17 years of experience across web, mobile, and backend."* Skills section
lists 17 languages/frameworks alphabetically. Experience bullets emphasize web UI work. No
mention of payments, fintech, economic infrastructure, PCI compliance, or any theme from
Stripe's careers page. Why 1/5: the company-swap probe is instant — this same résumé would
fit Vercel, Cloudflare, Supabase, or any dev-tools startup with zero changes. The applicant's
payments-adjacent work exists in applicant-notes but was buried. This is a generic résumé
with Stripe's name in the header and nothing else.

**Score 3/5 — Acceptable but noticeably weak.**
Example: Résumé targeting Stripe that adds a "Payments & Fintech" section header and
includes one bullet mentioning PCI compliance. Skills section moves Ruby and Go above Python
and Java. But the Summary line still reads *"Senior backend engineer passionate about
scalable systems"* — which fits any infra company. Experience bullets are in the same order
as the applicant's generic résumé. Why 3/5: there is visible tailoring (the section header,
the skills reorder, the PCI bullet) but the company-swap probe still passes for most
competitors with minor edits. The Generator did step 1 of tailoring and stopped before the
harder steps of voice and ordering.

**Score 5/5 — Exceeds expectations.**
Example: Résumé targeting Stripe with (a) Summary line echoing Stripe's *"economic
infrastructure"* phrasing from their homepage [URL cited in spec.md §6]; (b) Experience
reordered so fintech and payments work leads, with generic backend work demoted to 1-bullet
entries; (c) Skills section lists Ruby and Go first, with a note *"(Stripe's core stack
per their eng blog 2024)"* and the URL cited; (d) an explicit bullet *"Prior experience with
SCA / PSD2 regulatory rollouts [applicant-notes L112]"* that mirrors Stripe's
European-expansion careers-page emphasis [URL cited]; (e) company-swap probe clearly fails —
dropping in Adyen instead of Stripe would require rewriting the Summary, the skills
ordering, and at least three bullets. Why 5/5: a picky recruiter reading this would
recognize the applicant did their homework. Every company-facing phrase is cited. The
company-citation freshness check passes (all URLs within 24 months).

---

## C3 — Professional Craft (weight 1×)

**Score 1/5 — Unacceptable.**
Example: 4-page résumé with 15-word bullets that wrap inconsistently (some fit one line,
some bleed 3 lines). Mixed bullet depths across sections — Experience has 3-level-nested
bullets, Projects has flat bullets. Section headers are in bold text, not markdown headers
(fails ATS parsing). Emoji in two section names ("💼 Experience", "🎓 Education"). No
contact information at the top. Why 1/5: fails ATS parsing (a non-trivial fraction of
applications would be auto-rejected), fails length discipline (> 2 pages is a hard cap in
spec.md §8), fails scannability (mixed bullet depths kill the first-30-second skim).

**Score 3/5 — Acceptable but noticeably weak.**
Example: 2-page résumé. Clean H2 section headers (ATS-parseable). But bullet depth varies
between sections without rationale — Experience has 4-5 bullets per role, Projects has
1-bullet entries, Education has no bullets at all. Summary paragraph is 6 lines when
industry convention is 2-3. Date format is inconsistent ("Q2 2024" in one role, "Apr 2024
— Jun 2024" in the next). No typos, but two headline-case inconsistencies in section names.
Why 3/5: parseable and readable, but a picky recruiter would notice the inconsistency and
subconsciously downgrade. Passes the hard checks; fails the taste checks.

**Score 5/5 — Exceeds expectations.**
Example: 1.5-page résumé. H2 markdown section headers (ATS-standard names: Experience,
Projects, Skills, Education, Certifications). Uniform bullet depth — 3-5 bullets per
Experience role, each 1 line wrapping at most once. Summary paragraph 2 lines. Consistent
date format throughout ("Q2 2024" everywhere, or "Apr 2024 – Jun 2024" everywhere).
Contact block at top with name, email, phone, city, LinkedIn, portfolio URL. No typos, no
trailing whitespace, no section-header inconsistencies. Why 5/5: renders cleanly to PDF at
standard résumé formatting; a recruiter's 30-second skim lands on the right bullets
because hierarchy is respected; parseable by every major ATS.

---

## C4 — Actionability (fit-gap) (weight 1×)

**Score 1/5 — Unacceptable.**
Example fit-gap content: *"The applicant should strengthen their portfolio and prepare for
the interview. They may want to consider updating their references and reviewing common
system design topics."* Why 1/5: zero dates, zero artifacts, zero scope. Unexecutable.
Nothing the applicant can actually do on Monday morning. Every verb is vague ("strengthen,"
"prepare," "consider," "reviewing"). This is an AI hedging with a "next steps" wrapper.

**Score 3/5 — Acceptable but noticeably weak.**
Example fit-gap items:
1. Update GitHub README.
2. Email references.
3. Review system design.
Why 3/5: items have verbs (update, email, review) and artifacts (README, references, system
design) — more than 1/5. But no dates, so the applicant cannot prioritize. No scope — which
READMEs? Which references? Which system-design topics? The Generator produced the skeleton
of a fit-gap and stopped before the specificity that makes it useful. Passes verb-and-artifact
check, fails date-and-scope check.

**Score 5/5 — Exceeds expectations.**
Example fit-gap items:
1. **By Thu evening:** push updated README to github.com/<user>/payment-svc documenting the
   800ms → 200ms migration mentioned in résumé bullet 3. Recruiters may click the repo link.
2. **By Fri noon:** email Jane Lee (former manager) + Kim Park (former tech lead) to confirm
   reference consent; forward the Stripe JD so they can recall relevant context.
3. **Over the weekend:** drill 2 Stripe-style system-design rounds on distributed
   rate-limiting — the JD emphasizes it and applicant-notes shows only tangential experience.
   Pair with Alex for mock feedback.
4. **Before submission (within 72h):** remove the 2018 bootcamp bullet from Education — it
   predates applicant-notes coverage and cannot be verified against any source, which would
   fail the source-traceability probe if a recruiter cross-checked.
Why 5/5: every item has a verb + artifact + date + scope + a stated reason tied to a
specific gap observed during drafting. The applicant can execute every item this week. Each
is traceable to a Sprint-1-or-2 observation documented in generator_report.md.

---

## Cross-criterion note

If you find yourself scoring every criterion ≥ 4 on the first pass, stop. Apply the two
secondary lenses the Evaluator prompt requires:
- **ATS parser lens:** would this résumé lose content if a standard ATS parsed it? If yes,
  C3 drops.
- **Hiring-manager first-30-second skim lens:** what does the reader walk away remembering?
  If it's "this person seems senior" and not "this person shipped payment migrations with
  verified metrics," C2 drops.

When the anchors above conflict with your intuition, trust the anchors. Drift starts with
one "this probably qualifies as 4" and ends with a rubric that passes everything.
