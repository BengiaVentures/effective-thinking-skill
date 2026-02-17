# Testing /think

## Quick Version: Use the Agent

```
/think-test "Should we migrate from REST to GraphQL?"
```

The `/think-test` skill spawns **two isolated agents in parallel** — one running organic
Claude, one running `/think` — that cannot see each other's output. It then anonymizes
both responses (stripping framework headers, normalizing format) and presents them side
by side for blind judgment.

You answer 3 questions. Results accumulate. Run `/think-test review` to see aggregate stats.

## How It Works

```
┌─────────────────────┐     ┌─────────────────────┐
│   Agent: Organic     │     │   Agent: /think      │
│                      │     │                      │
│ "Analyze this topic  │     │ Read framework.md    │
│  naturally. No       │     │ Run 5-phase process: │
│  framework. Just     │     │ PREPARE → THINK →    │
│  think carefully."   │     │ SYNTHESIZE → REFLECT │
│                      │     │ → LEARN (skip write) │
└──────────┬──────────┘     └──────────┬──────────┘
           │  (isolated)                │  (isolated)
           └────────────┬───────────────┘
                        ▼
              ┌─────────────────┐
              │  Orchestrator    │
              │                  │
              │ • Randomize A/B  │
              │ • Strip markers  │
              │ • Normalize fmt  │
              │ • Present blind  │
              └────────┬────────┘
                       ▼
              ┌─────────────────┐
              │  Human Judgment  │
              │                  │
              │ 3 questions:     │
              │ • Novel insight? │
              │ • Risk coverage? │
              │ • Decision impact│
              └────────┬────────┘
                       ▼
              ┌─────────────────┐
              │  Reveal + Log    │
              └─────────────────┘
```

### Why Two Agents?

A single agent generating both responses can't produce a fair comparison:
- It already "knows" what it said organically when generating the /think response
- It may unconsciously sandbag the organic version
- The second response benefits from having already thought about the topic

Two isolated agents solve all three problems. Neither knows the other exists.

## What It Measures

Each question maps to a specific claim `/think` makes:

| Question | What It Tests | Relevant Element |
|----------|--------------|-----------------|
| **"Which surfaced something you didn't know?"** | Does structured coverage find non-obvious insights? | EARTH + WATER |
| **"Which identified a risk you'd have missed?"** | Does deliberate failure-seeking catch real risks? | FIRE |
| **"Which would change what you actually do?"** | Does the framework produce more actionable analysis? | SYNTHESIZE |

## Test Progression

### Level 1: Single Session (5 minutes)
Run `/think-test` once on a topic you care about. Get an initial signal.

### Level 2: Pattern Detection (1-2 weeks)
Run `/think-test` on 5-10 different topics as they come up naturally. Then:
```
/think-test review
```
Look for: which dimension does /think win on? Are there topic types where organic matches it?

### Level 3: Learning Loop Validation (1 month+)
The hardest claim to test: does /think get better over time? After 15+ real `/think` sessions:
- Re-run the same test topic from Level 1
- Compare the new /think output to the original
- Run `/think-review` and evaluate whether meta-patterns are useful
- Check `.think/MEMORY.md` — transferable insights, or just session summaries?

## Sample Topics

Pick topics where you already have an opinion — that way you can judge whether the
response tells you something new.

```
/think-test "We need to add real-time notifications to our REST/PostgreSQL app. Team of 3."
/think-test "Users say the app feels slow but p99 latency is 200ms."
/think-test "Enterprise customer wants single-tenant deployment. 2 months, $200k/year."
/think-test "Our onboarding takes 45 minutes to first value."
/think-test "Our test suite is a problem."
```

Prompt 5 is deliberately vague — tests whether the approach probes before solving.

## Interpreting Results

**If /think wins most tests:** The framework adds real value. Look at *which* dimension
to understand what organic under-indexes on.

**If mostly ties:** Structure isn't hurting, but you're paying tokens for marginal gain.
Check if the learning loop (Level 3) changes this over time.

**If organic wins most tests:** The framework is ceremony without substance. Examine the
/think outputs — are the phases doing real work, or restating the same point five ways?

**The uncomfortable possibility:** If anonymized responses are indistinguishable, the
framework's value might be in the *process of writing it*, not the output. That's still
valuable as a thinking scaffold — but it means the skill is a cognitive tool for humans,
not a quality multiplier for the AI.

## What This Test Can't Tell You

- Whether `/think` is worth the extra tokens (cost/value judgment)
- Whether recursive learning compounds (needs months of data)
- Whether another evaluator would score the same (no inter-rater reliability)
- Whether `/think` helps on tasks with objectively correct answers (these are all judgment calls)

For the recursive learning claim, the best evidence isn't a comparison test — it's
reading `.think/MEMORY.md` after 20 sessions and asking: "Would I lose anything if I
deleted this?"
