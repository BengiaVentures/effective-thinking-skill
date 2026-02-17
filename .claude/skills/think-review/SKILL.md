---
description: Review past /think sessions to find meta-patterns and curate memory
argument: count — number of recent sessions to review (default 5)
---

# /think-review — Session Review Agent

You review past `/think` sessions, apply the 5 Elements across the collection to
find meta-patterns, curate memory, and produce a growth report.

---

## Step 1: Load Context

- Read `.think/MEMORY.md` for the current memory index
- List files in `.think/sessions/` sorted by date (most recent first)
- Read the most recent **$arguments** sessions (default: 5). If fewer exist, read all.
- Read all files in `.think/patterns/`

If `.think/` doesn't exist or has no sessions, tell the user and stop.

---

## Step 2: Cross-Session Analysis (5 Elements)

Apply the framework across the *collection* of sessions, not individual ones:

- **EARTH** — What fundamentals keep appearing? What basics are consistently strong or weak?
- **FIRE** — What failure patterns recur? Which predicted risks actually materialized?
- **AIR** — Which reframing questions led to the best insights? What questions are never asked?
- **WATER** — What connections between sessions weren't noticed at the time? What trajectories are emerging?
- **CHANGE** — How has the thinking evolved? What old patterns should be retired?

---

## Step 3: Memory Curation

Review `.think/MEMORY.md` and `.think/patterns/`:
- Flag entries that are outdated, redundant, or no longer useful
- Suggest consolidations (multiple entries that say the same thing)
- Identify insights from this review that should become new patterns
- Propose specific edits (but ask before making destructive changes)

---

## Step 4: Growth Report

Output a structured report:

```markdown
## Thinking Review: Last N Sessions

### Strengths
- Elements consistently applied well

### Blind Spots
- Elements or approaches consistently underused

### Emerging Patterns
- Themes or connections across sessions

### Memory Health
- Current line count / 100 limit
- Entries to retire or consolidate
- New patterns to add

### Recommendation
- One concrete thing to focus on in the next /think session
```

---

## Notes

- Be specific. Reference actual session dates and topics, not generalities.
- If memory is getting bloated, prioritize curation over new additions.
- This skill is diagnostic — it should be honest about weaknesses, not encouraging.
