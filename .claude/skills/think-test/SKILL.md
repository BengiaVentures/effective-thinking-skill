---
description: Run a blind comparison test of /think vs organic Claude on a given topic
argument: topic — the problem or question to test with (or "review" to see past results)
---

# /think-test — Blind Comparison Agent

You are a test orchestrator. You evaluate whether `/think` produces better analysis
than organic Claude by spawning **isolated subagents** that cannot see each other's
output, then collecting and anonymizing the results for judgment.

---

## Argument Routing

- If argument is `"review"` → skip to **Review Results** at the bottom.
- If argument starts with `[batch]` → skip to **Batch Mode** at the bottom.
- If argument starts with `[3-way]` → run **3 agents** (adds strong baseline arm).
- Otherwise → run standard **2-agent** comparison.

---

## Step 1: Spawn Isolated Agents in Parallel

Use the Task tool to launch subagents **simultaneously** in a single message.
All agents receive the same topic but different instructions. No agent can see
another's output — they run in complete isolation.

**Agent: Organic Claude** (subagent_type: "general-purpose")
```
You are being tested on your ability to analyze a topic. Give your best, most
thorough analysis. There is no special framework or process to follow — just
think carefully and give a genuinely useful response.

Do NOT use any structured framework. Do NOT break your response into labeled
phases or elements. Just analyze the topic naturally, as you would in any
thoughtful conversation.

Topic: <the user's topic>

Write your full analysis. Use clear markdown formatting. Be substantive.
```

**Agent: /think Claude** (subagent_type: "general-purpose")
```
You are a thinking agent. Read the file `.claude/skills/think/references/framework.md`
for the 5 Elements framework, then apply the full 5-phase process defined in
`.claude/skills/think/SKILL.md` to this topic.

Execute all 5 phases: PREPARE, THINK (all 5 elements), SYNTHESIZE, REFLECT, LEARN.

For PREPARE: check for `.think/` directory and load memory if it exists.
For LEARN: do NOT write any session files or update memory — this is a test run.

Topic: <the user's topic>

Write your full analysis following the skill's output format.
```

**Agent: Strong Baseline** (subagent_type: "general-purpose") — **Only in 3-way mode**
```
Analyze this topic thoroughly. Specifically:
- Separate what you know from what you're assuming
- Identify 2-3 concrete failure modes or risks
- Question whether the stated problem is the real problem
- Trace second-order effects and cross-domain connections
- Provide prioritized, concrete action items

Do NOT use any named framework or labeled phases. Just think carefully
using the guidance above. Write your full analysis in clear markdown.

Topic: <the user's topic>
```

**Critical:** Launch all agents in the same tool-call message so they run in parallel
with no shared context.

---

## Step 2: Collect and Anonymize

Once all agents return:

1. **Randomize labels** — Randomly assign responses to labels (A, B, and C if 3-way).
   Use the current seconds value or any method. Record the mapping privately.

2. **Strip identifying markers from the /think response:**
   - Remove phase headers: "Phase 1: PREPARE", "Phase 2: THINK", etc.
   - Remove element headers: "EARTH —", "FIRE —", "AIR —", "WATER —", "CHANGE —"
   - Remove "SYNTHESIZE", "REFLECT", "LEARN" section headers
   - Re-title sections based on their *content* (e.g., "Fundamentals" instead of "EARTH",
     "Risks" instead of "FIRE", "Recommendations" instead of "SYNTHESIZE")
   - Remove any self-referential language about "applying the framework" or "Phase N"

3. **Normalize all responses:**
   - Add content-based section headers to wall-of-text responses
   - Ensure similar markdown formatting density across all responses

The goal: a reader should not be able to tell which response used a framework
just by looking at the structure.

---

## Step 3: Present All Responses

Output responses clearly separated:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RESPONSE A
━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[anonymized response text]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RESPONSE B
━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[anonymized response text]
```

Add RESPONSE C block if in 3-way mode.

---

## Step 4: Collect Judgment

Ask all questions using the AskUserQuestion tool in one call.

For **2-way mode**, options are: Response A | Response B | Neither | Both
For **3-way mode**, options are: Response A | Response B | Response C | None stood out

**Question 1 — "Novel Insight"**
"Which response surfaced something you didn't already know or hadn't considered?"

**Question 2 — "Risk Coverage"**
"Which response identified a risk or failure mode you would have missed on your own?"

**Question 3 — "Decision Impact"**
"If you had to act on one of these, which would change what you actually do?"

**Question 4 — "Gap Size"**
"How large is the gap between the best response and the rest?"
Options: Marginal | Clear | Decisive | No gap

---

## Step 5: Reveal and Record

**Reveal the mapping:** Tell the user which response was organic, /think, and (if 3-way)
strong baseline.

**Record results:** Create `.think-test/` and `.think-test/results/` if they don't exist.

Write to `.think-test/results/YYYY-MM-DD-<slug>.md`:

```markdown
# Test: <topic summary>
**Date:** YYYY-MM-DD
**Labels:** A = <organic|think|strong-baseline>, B = <...>, C = <...>
**Model:** <model used>
**Study Phase:** calibration | main | follow-up | standalone
**Topic Type:** technical | strategic | creative
**Topic Number:** <N of 30, or "ad-hoc" if not from study>
**Judge Type:** ai | human | both
**Mode:** 2-way | 3-way

## Scores
- Novel Insight: <organic|think|strong-baseline|neither|both>
- Risk Coverage: <organic|think|strong-baseline|neither|both>
- Decision Impact: <organic|think|strong-baseline|neither|both>
- Gap Size: <marginal|clear|decisive|no-gap>

## Winner: <organic|think|strong-baseline|tie>
A response wins a dimension if selected alone. "Both" = tie on that dimension.
"Neither" = tie on that dimension. Overall winner = most dimensions won (Q1-Q3).

## Notes
<Observations about why one approach worked better for this topic type>
```

**Print summary:**

```
Result: /think [won|lost|tied] (insight: ✓/✗, risk: ✓/✗, decision: ✓/✗) gap: [size]
Record: X wins, Y losses, Z ties across N tests
```

---

## Batch Mode

If the argument starts with `[batch]`, format: `[batch] path/to/topics.md range:1-10`

1. Read the topics file at the specified path
2. Parse the range to determine which numbered topics to run (e.g., `range:1-10`)
3. For each topic in the range:
   a. Run Steps 1-5 as normal
   b. Between tests, print the running tally
4. After all tests, print a summary table:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
BATCH SUMMARY — Topics [range]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

| # | Topic | Insight | Risk | Decision | Gap | Winner |
|---|-------|---------|------|----------|-----|--------|
| 1 | ...   | think   | think| organic  | clear | think |
...

Overall: X-Y-Z (think wins - organic wins - ties)
Per dimension: Insight X/N, Risk X/N, Decision X/N
Magnitude: N decisive, N clear, N marginal, N no-gap
```

---

## Review Results

If the argument is "review":

1. Read all files in `.think-test/results/`
2. Compute aggregate stats:
   - Win/loss/tie record overall
   - Per-dimension win rates (insight, risk, decision)
   - Gap size distribution
   - Breakdown by topic type (technical, strategic, creative)
   - Breakdown by study phase (calibration, main, follow-up)
   - If 3-way tests exist: /think vs strong baseline comparison
3. Identify patterns:
   - Which dimension does /think win most consistently?
   - Are there topic types where organic matches it?
   - Does gap size correlate with topic type?
   - Is there improvement over time (if /think memory is building)?
4. Output a summary report with concrete recommendations

---

## Design Principles

- **True isolation.** Agents that cannot see each other. No contamination.
- **Blind evaluation.** Framework markers stripped. Format normalized. Substance only.
- **4 questions, not 7.** Three map to specific /think claims. One measures magnitude.
- **Cumulative tracking.** Results build over time. Patterns emerge from repetition.
- **No numeric scores.** Categorical is faster and more honest than scales.
- **Strong baseline arm.** Tests whether structure beats good prompting, not just no prompting.
