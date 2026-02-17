# Pre-Registration: Blind Evaluation of /think Structured Analysis

**Date registered:** 2026-02-17
**Status:** Pre-registered (no study tests run yet)
**Repository:** bengiaventures/effective-thinking-skill

---

## Hypothesis

The `/think` skill — a 5-phase structured thinking agent applying the 5 Elements of Effective Thinking — produces higher-quality analysis than unstructured Claude on three dimensions: novel insight, risk coverage, and decision impact. We also test whether it outperforms a "strong baseline" (unstructured Claude given explicit analytical guidance).

## Study Design

### Arms

| Arm | Description |
|-----|-------------|
| **Organic** | Claude with no framework. Instructed to "think carefully and give a genuinely useful response." |
| **/think** | Claude running the full 5-phase `/think` process (PREPARE → THINK → SYNTHESIZE → REFLECT → LEARN). |
| **Strong Baseline** | Claude with no framework but explicit guidance: "separate knowns from assumptions, identify failure modes, question the stated problem, trace second-order effects, provide prioritized actions." |

The strong baseline tests whether `/think`'s value comes from its structured process or whether equivalent prompting achieves the same result.

### Architecture

Each test spawns **isolated parallel agents** that cannot see each other's output. An orchestrator anonymizes all responses (strips framework headers, normalizes formatting) before evaluation.

```
                    Same Topic
                   /     |     \
     ┌──────────┐  ┌──────────┐  ┌──────────┐
     │ Organic  │  │  /think  │  │ Strong   │
     │          │  │          │  │ Baseline │
     └────┬─────┘  └────┬─────┘  └────┬─────┘
          │  isolated    │  isolated   │
          └──────────┬───┴─────────────┘
                     ▼
              Anonymize + Judge
```

### Topics

30 topics sourced from external discussions (Stack Overflow, Hacker News, engineering blogs), not curated by the study author. See `STUDY_TOPICS.md` for the full list with source URLs.

**Stratification:**
- Topics 1-10: Technical (architecture, performance, debugging)
- Topics 11-20: Strategic (build/buy, team, product decisions)
- Topics 21-30: Creative (design, UX, developer experience)

### Evaluation

Four categorical questions per test:

| # | Question | Options | What It Tests |
|---|----------|---------|---------------|
| 1 | "Which surfaced something you didn't already know?" | A / B / C / Neither / Both | Novel insight discovery |
| 2 | "Which identified a risk you would have missed?" | A / B / C / Neither / Both | Failure mode coverage |
| 3 | "Which would change what you actually do?" | A / B / C / Neither / Both | Decision impact / actionability |
| 4 | "How large is the gap between best and rest?" | Marginal / Clear / Decisive / No gap | Effect magnitude |

No numeric scales. Categorical judgments are faster and more honest.

### Phases

#### Phase 1: Calibration (Tests 1-10)

- Topics 1-10 (technical)
- Each test judged by **both** an AI judge and a human judge
- Purpose: determine if AI judges agree with human judges (target: 80%+ agreement)
- If calibrated: AI judges are trustworthy for the remaining tests
- If not calibrated: all remaining tests require human judges

#### Phase 2: Main Study (Tests 11-30)

- Topics 11-20 (strategic) and 21-30 (creative)
- Judge type determined by Phase 1 calibration results
- All three arms (organic, /think, strong baseline)

#### Phase 3: Learning Loop (Follow-up)

Separate longitudinal design to test "/think improves over time":
1. Run 5 topics with empty `.think/` memory
2. Run 15+ normal `/think` sessions on unrelated topics (building memory)
3. Re-run the same 5 topics
4. Blind-compare before vs after pairs

## Analysis Plan

All analysis decisions are specified here before data collection begins.

### Primary Analysis

**Win rate:** For each dimension (insight, risk, decision), compute the proportion of tests won by each arm. A response "wins" a dimension if selected alone. "Both" and "Neither" count as ties.

**Binomial test:** For the primary comparison (/think vs organic), test whether /think's win rate significantly exceeds 50% (chance) using a two-sided exact binomial test. With 30 tests, a 75% win rate yields p < 0.05.

### Secondary Analyses

**Per-topic-type breakdown:** Compute win rates separately for technical, strategic, and creative topics. Report as descriptive (no statistical test — sample too small per stratum).

**Magnitude distribution:** Report the frequency of Marginal / Clear / Decisive / No gap judgments. A tool that wins 20 tests marginally is less impressive than one that wins 15 decisively.

**Strong baseline comparison:** If /think wins against organic but ties with the strong baseline, the framework's value is convenience (packaging good prompting), not structural. If /think beats both, the structured process adds value beyond the analytical directions alone.

**Calibration agreement:** Compute Cohen's kappa between AI and human judges on the 10 calibration tests. Report per-dimension and overall.

### What We Will NOT Do

- No cherry-picking: all 30 tests are reported regardless of outcome
- No post-hoc dimension weighting: all 4 dimensions are reported equally
- No re-running tests that produce unfavorable results
- No modifying the /think skill between Phase 1 and Phase 2
- No modifying the analysis plan after data collection begins

## Model

All agents (organic, /think, strong baseline) and AI judges run on the same model for a given test. Model is recorded per test.

## Reporting

Final results will be published as `STUDY_RESULTS.md` in this repository with:
- Raw results table (all 30 tests)
- Aggregate statistics per the analysis plan above
- Calibration agreement data
- Discussion of limitations
- Raw anonymized response pairs (so others can re-judge)

## Limitations (Acknowledged in Advance)

- **Sample size:** 30 tests provides basic statistical power but cannot detect small effect sizes.
- **AI judges:** Even if calibrated against humans on 10 tests, systematic bias may exist on topics outside the calibration set.
- **Single evaluator:** Human judgments come from one person (the project author), introducing individual bias. Mitigated by blind evaluation but not eliminated.
- **Topic sourcing:** While topics come from external sources, the selection of which external sources to draw from is a form of curation.
- **Self-evaluation:** The study author built the tool being tested. This is disclosed, not eliminated.
- **No cost analysis:** Token usage is not tracked. A tool that wins by using 5x the tokens may not be worth it.
