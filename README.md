# /think — A Recursive Thinking Agent for Claude Code

A Claude Code skill that makes Claude think better — and improves at it over time.

`/think` applies the [5 Elements of Effective Thinking](https://www.amazon.com/dp/0691156662) as a structured 5-phase agent. It grounds analysis in facts, stress-tests through failure, reframes the question, traces implications, and audits its own reasoning. After each session, it persists what it learned so the next session starts sharper.

## How It Works

`/think` runs a 5-phase process on any topic you give it:

| Phase | What Happens |
|-------|-------------|
| **PREPARE** | Loads memory from past sessions. Assesses topic type to weight elements. |
| **THINK** | Applies 5 elements: ground in facts, stress-test, reframe, trace implications, audit for blind spots. |
| **SYNTHESIZE** | Weaves elements into a unified recommendation with prioritized action items. |
| **REFLECT** | Turns the framework on its own reasoning — the recursive loop. |
| **LEARN** | Persists durable insights to `.think/` for future sessions. |

The 5 Elements:

```
EARTH  — What do I actually know vs assume?
FIRE   — How does this break? What fails at scale?
AIR    — What's the real question? Reframe before solving.
WATER  — Where did this come from? Where does it lead?
CHANGE — Which element am I neglecting? What must I revise?
```

## Does It Actually Work?

I ran ~21 blind A/B comparisons to find out. [Judge for yourself](https://bengiaventures.github.io/effective-thinking-skill/) in the live blind test.

### Methodology

Two independent agents run in parallel on the same topic — one organic Claude, one running `/think`. Neither can see the other's output. An orchestrator anonymizes both responses (strips framework headers, retitles sections by content) and presents them blind for evaluation on three dimensions plus gap size:

```
                  Same Topic
                 /          \
    ┌────────────┐      ┌────────────┐
    │  Organic    │      │  /think    │
    │  Claude     │      │  Agent     │
    │  (no skill) │      │  (5-phase) │
    └─────┬──────┘      └─────┬──────┘
          │   isolated        │
          └────────┬──────────┘
                   ▼
            Anonymize + Blind Judge
            (Novel Insight, Risk Coverage,
             Decision Impact, Gap Size)
```

The test skill (`/think-test`) is included in this repo — you can run it yourself.

### Results (~21 comparisons, AI-judged)

**Overall record: /think wins ~69%, organic wins ~12%, ties ~19%**

| Dimension | /think Wins | Organic Wins | Ties | Signal Strength |
|-----------|:-----------:|:------------:|:----:|:---:|
| Risk Coverage | 17 | 2 | 2 | Strong |
| Novel Insight | 9 | 1 | 11 | Moderate |
| Decision Impact | 10 | 8 | 3 | Weak |

**Key findings:**

- **Risk coverage is the killer feature.** /think surfaces failure modes, non-obvious risks, and edge cases that organic Claude consistently misses. This is the clearest signal in the data (17-2).
- **Decision impact is nearly even.** Organic responses often win on actionability for practical problems where direct advice beats structured depth. /think wins when the problem itself needs reframing.
- **Novel insight is mostly a wash.** Both approaches find similar core insights. Structured analysis surfaces *different* insights, not necessarily *more*.
- **The gap is real but modest.** No decisive gaps in either direction. Most wins are marginal. /think's advantage is depth and rigor, not dramatic superiority.
- **Works across spectrums.** Tested on organizational, business, product/strategy, financial, and cultural topics — not a niche advantage.

**Self-improvement** — I used `/think` to analyze its own test results. It identified the problem as communication architecture (thinking quality was fine; output structure wasn't reader-oriented) and recommended three fixes. I implemented two; tested the third empirically — current order won. This is the recursive loop in action: the tool diagnosed and improved itself.

### Live Blind Test

I published a [live blind comparison](https://bengiaventures.github.io/effective-thinking-skill/) with 5 anonymized pairs spanning topics any professional would recognize:

1. Scaling a team from 15 to 50 people
2. Build vs buy decisions
3. When to pivot your product
4. Pricing a B2B SaaS product
5. Remote, hybrid, or return to office

AI judges scored /think winning all 5. I'm collecting human judgments to validate whether that holds.

### Honest Limitations

- **AI judges**: All ~21 comparisons were judged by Claude instances. AI judging AI introduces potential bias toward structured output. Human validation is the next step.
- **Sample size**: ~21 comparisons is a pattern, not statistical proof.
- **Anonymization is imperfect**: /think responses have stylistic tells (confidence assessments, "what would change this" sections) that may influence judges.
- **No long-term data**: The recursive learning claim needs months of real-world use to validate.
- **Token cost**: `/think` uses significantly more tokens than organic. Whether the depth justifies the cost is a judgment call.

The testing tool is included so you can run your own tests and decide for yourself.

## Usage

```
/think "We're seeing 3-second load times on the dashboard"
/think "Should we build or buy the auth system?"
/think "Our test suite takes 45 minutes and nobody trusts it"
```

For vague or ambiguous topics, `/think` probes before solving — which is often where the real insight lives.

### Companion Skills

**`/think-review`** — Review past sessions for meta-patterns:
```
/think-review        # review last 5 sessions
/think-review 10     # review last 10 sessions
```

**`/think-test`** — Run blind A/B comparisons yourself:
```
/think-test "Should we migrate from REST to GraphQL?"
/think-test review   # see aggregate results
```

## Installation

### Recommended: Git Submodule

```bash
cd your-project
git submodule add https://github.com/bengiaventures/effective-thinking-skill.git \
  .claude/skills-vendor/effective-thinking-skill

# Symlink skills into place
mkdir -p .claude/skills
ln -s ../skills-vendor/effective-thinking-skill/.claude/skills/think .claude/skills/think
ln -s ../skills-vendor/effective-thinking-skill/.claude/skills/think-review .claude/skills/think-review
ln -s ../skills-vendor/effective-thinking-skill/.claude/skills/think-test .claude/skills/think-test
```

<details>
<summary>Windows (PowerShell as admin)</summary>

```powershell
mkdir -Force .claude\skills
New-Item -ItemType SymbolicLink -Path .claude\skills\think `
  -Target .claude\skills-vendor\effective-thinking-skill\.claude\skills\think
New-Item -ItemType SymbolicLink -Path .claude\skills\think-review `
  -Target .claude\skills-vendor\effective-thinking-skill\.claude\skills\think-review
New-Item -ItemType SymbolicLink -Path .claude\skills\think-test `
  -Target .claude\skills-vendor\effective-thinking-skill\.claude\skills\think-test
```
</details>

<details>
<summary>Alternative: Direct Copy</summary>

```bash
cp -r effective-thinking-skill/.claude/skills/think your-project/.claude/skills/think
cp -r effective-thinking-skill/.claude/skills/think-review your-project/.claude/skills/think-review
cp -r effective-thinking-skill/.claude/skills/think-test your-project/.claude/skills/think-test
```
</details>

<details>
<summary>Alternative: Standalone</summary>

```bash
git clone https://github.com/bengiaventures/effective-thinking-skill.git
cd effective-thinking-skill
claude  # start Claude Code, then use /think
```
</details>

## The Learning System

After your first `/think` session, a `.think/` directory appears in your project:

```
.think/
├── sessions/     # Individual session logs (date-stamped)
├── patterns/     # Durable insights extracted across sessions
└── MEMORY.md     # Index of learnings (loaded at start of each session)
```

Each session reads from memory, runs the analysis, reflects on its own quality, then writes back what it learned. Over time, this builds a project-specific knowledge base that makes each session more contextual.

The `.think/` directory is gitignored by default — it's your project-local thinking history, not part of the skill itself.

## Attribution

Inspired by *The 5 Elements of Effective Thinking* by Edward B. Burger and Michael Starbird (Princeton University Press, 2012). The framework descriptions in this skill are original operational interpretations for AI agent use, not reproductions of the book. Highly recommended reading: [Amazon](https://www.amazon.com/dp/0691156662)

Special thanks to [Daniel Schreiner](https://www.truthorfate.com/), creator of [Truth or Fate](https://www.kickstarter.com/projects/danielschreiner/truth-or-fate-a-magical-game-that-tells-your-future) — a psychology-driven card game drawing from over a hundred psychology books to help people connect more deeply and uncover unconscious beliefs. His passion for distilling deep thinking into accessible formats directly inspired this project.

## Support

Everything here is free and open source. If `/think` is useful to you, [donating on Patreon](https://patreon.com/Bengia) helps support continued development.

Built by Ben — [bengia.ventures](https://bengia.ventures) / [@BengiaVentures](https://x.com/BengiaVentures)

## License

MIT — see [LICENSE](LICENSE).
