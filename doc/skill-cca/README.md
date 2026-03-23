# Claude Certified Architect — Exam Coach Skill

An interactive study coach for the Claude Certified Architect certification. Quiz yourself, run a mock exam, deep dive any domain, or get concept explanations — all grounded in the official Anthropic exam guide.

## Install (30 seconds)

1. Copy the entire `architect-guide` folder into your `.claude/skills/` directory:

```
your-project/
└── .claude/
    └── skills/
        └── architect-guide/    ← paste this folder here
            ├── SKILL.md
            ├── guide.md
            └── README.md
```

2. That's it. Type `/architect-guide` in Claude Code to start.

## How to Use

| Command | What it does |
|---|---|
| `/architect-guide` | Opens the main menu |
| `/architect-guide quiz` | Starts a weighted quiz (matches real exam %) |
| `/architect-guide exam` | Full mock exam — 4 random scenarios, 12 questions |
| `/architect-guide domain 1` | Deep dive Domain 1 (works for 1-5) |
| `/architect-guide explain hooks` | Explains any concept in plain English |
| `/architect-guide browse` | Browse all 5 domains and task statements |

## What's Inside

**5 Modes:**
- **Quiz** — weighted multiple choice (Domain 1 gets 27% of questions, matching the actual exam). Adapts to your weak spots within the session.
- **Exam Simulation** — 4 random scenarios, 3 questions each. No feedback until the end. Outputs a pass/fail score.
- **Domain Deep Dive** — task by task walkthrough with practice questions per task statement.
- **Concept Explainer** — 4-layer explanation: plain English → how it works → exam angle → practice question.
- **Browse** — navigate all 30 task statements across all 5 domains.

**Session intelligence:** tracks right/wrong answers, shows you your weak spots, weights subsequent questions toward areas you're struggling with.

**Grounded in the guide:** every answer comes from the official Anthropic exam guide (`guide.md`). Nothing is invented.

## The Exam at a Glance

| Domain | Weight |
|---|---|
| Agentic Architecture & Orchestration | 27% |
| Claude Code Configuration & Workflows | 20% |
| Prompt Engineering & Structured Output | 20% |
| Tool Design & MCP Integration | 18% |
| Context Management & Reliability | 15% |

Pass threshold: 720 / 1000


