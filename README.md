# 5 Elements of Effective Thinking — Claude Skill

A shareable skill that teaches Claude the **5 Elements of Effective Thinking** framework by Edward B. Burger & Michael Starbird, transforming how it approaches problems, questions, and decisions.

## The 5 Elements

| Element | Force | Core Principle |
|---------|-------|---------------|
| **Earth** | Understand Deeply | Master fundamentals before complexity. Simplify. Find bedrock. |
| **Fire** | Make Mistakes | Fail intentionally. Errors reveal structure answers can't. |
| **Air** | Raise Questions | Questions > answers. Reframe before solving. |
| **Water** | Follow the Flow | Trace origins and implications. Connect across domains. |
| **Change** | Transform | The meta-element. Apply the other four to evolve your thinking. |

## Installation

### Option A: Claude Code (CLI) — Slash Command

Copy the command file into your project:

```bash
mkdir -p .claude/commands
cp .claude/commands/think.md YOUR_PROJECT/.claude/commands/think.md
```

Then use it:
```
/think "How should I architect the authentication system?"
/think "Why is our test suite slow?"
/think "Should we use microservices or a monolith?"
```

### Option B: Claude Projects (claude.ai) — Custom Instructions

1. Go to [claude.ai](https://claude.ai) and create or open a Project
2. Open Project Settings > Custom Instructions
3. Paste the contents of `EFFECTIVE_THINKING.md`
4. Claude will now apply the framework to all conversations in that project

### Option C: System Prompt (API)

Include the `## Core Framework` and `## Behavioral Instructions` sections from `EFFECTIVE_THINKING.md` in your system prompt.

## What Changes

Without this skill, Claude gives you answers.
With this skill, Claude gives you **thinking**.

| Without | With |
|---------|------|
| Jumps to solutions | Verifies understanding first (Earth) |
| Presents only the happy path | Explores failure modes and anti-patterns (Fire) |
| Answers the question as asked | Reframes questions before answering (Air) |
| Treats problems in isolation | Connects to related ideas and implications (Water) |
| Commits to first approach | Revises openly when it finds a better path (Change) |

## Files

| File | Purpose |
|------|---------|
| `EFFECTIVE_THINKING.md` | Full framework reference — use as Claude Project instructions or system prompt |
| `.claude/commands/think.md` | Claude Code slash command — invoke with `/think "your topic"` |
| `README.md` | This file |

## Examples

```
/think "We're seeing 3-second load times on the dashboard"
```

Claude will:
1. **Earth**: Clarify what "3-second" means (TTFB? LCP? Full load?) and what the baseline should be
2. **Fire**: Identify the most common causes of slow dashboards and which ones to rule out first
3. **Air**: Ask whether "faster dashboard" is the right goal or if the real problem is "too much data on one page"
4. **Water**: Connect to similar performance problems, suggest profiling approaches that worked in analogous cases
5. **Change**: Recommend what to try first and what signal would indicate a different approach is needed

## Attribution

Framework by Edward B. Burger and Michael Starbird from *The 5 Elements of Effective Thinking* (Princeton University Press, 2012). This skill operationalizes their framework for AI-assisted thinking.

## License

Freely shareable. Better thinking benefits everyone.
