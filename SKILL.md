---
name: autotune
description: "Optimise OpenClaw agent behavioural rules (AGENTS.md, SOUL.md) and pipeline handoff prompts (TOOLS.md) using a benchmark-driven side-by-side comparison loop. Runs 15 synthetic scenarios against the current rule and a proposed rewrite, scores both blind via LLM judge, presents a side-by-side diff with scores, and applies only on human approval. Use when asked to: optimise my AGENTS.md, tune my agent behaviour, improve my soul file, benchmark my agent rules, run autotune on [section], optimise pipeline handoffs."
homepage: https://github.com/WealthVisionAI-Source/autotune
metadata: { "openclaw": { "emoji": "🎛️" } }
---

# autotune

Benchmark-driven optimisation for OpenClaw agent behavioural rules. Sister skill to autooptimise — where autooptimise improves *what* your agent knows, autotune improves *how* your agent behaves.

## Trigger Phrases

- `"optimise my AGENTS.md"`
- `"tune my agent behaviour"`
- `"run autotune on the group chat rules"`
- `"improve my soul file"`
- `"benchmark my heartbeat rules"`

## What it Does

1. **Picks a target section** of AGENTS.md or SOUL.md
2. **Runs 15 synthetic scenarios** against the current rule — simulates realistic agent responses
3. **Dan proposes a rewrite** — clearer, tighter, or with a different philosophy
4. **Runs the same scenarios** against the proposed rule
5. **Jon scores both blind** — doesn't know which is current vs proposed
6. **Shows you a side-by-side comparison** with scores and diff
7. **Applies only on your approval** — nothing changes without your say-so

## Scoring Dimensions

| Dimension | What it measures |
|-----------|-----------------|
| `alignment` | Does the response do what the rule intends? |
| `naturalness` | Does it feel human, not robotic? |
| `boundary_respect` | Right level of action — not over/under-stepping? |
| `conciseness` | Says what's needed and nothing more? |

Score threshold for recommendation: **delta ≥ +0.3**

## Key Files

| File | Purpose |
|------|---------|
| `benchmark/tasks.json` | 15 synthetic behavioural scenarios |
| `benchmark/scorer.md` | Jon's blind scoring rubric |
| `runner/run_experiment.md` | Full loop instructions |
| `runner/experiment_log.md` | Run history (created on first run) |

## What Gets Optimised

**SOUL.md sections:**
- Be resourceful before asking
- Have opinions
- Skip performative helpfulness
- Earn trust through competence

**AGENTS.md sections:**
- Group chat — know when to speak
- Group chat — don't dominate
- Heartbeat — when to reach out
- Shopping list
- Memory — write it down
- External vs internal actions

**Pipeline handoff prompts (TOOLS.md):**
- Dan→Luke handoff — task description for testing
- Luke→Jon handoff — test report for review
- Jon→Done handoff — review summary for decision
- Robin observation — transition learnings
- Handoff brevity — delta vs context dumping
- Handoff on failure — breakage communication

## How to Run

```
run autotune on AGENTS.md group chat rules
optimise SOUL.md / be resourceful section
benchmark all AGENTS.md sections
```

## Relationship to autooptimise

| | autooptimise | autotune |
|-|-------------|---------|
| **Target** | SKILL.md files | AGENTS.md / SOUL.md |
| **What changes** | Agent instructions for tools | Agent behavioural rules |
| **Benchmark type** | Tool execution tasks | Synthetic behavioural scenarios |
| **Score threshold** | +0.5 | +0.3 (subtler domain) |
| **Output** | Updated SKILL.md | Updated AGENTS.md or SOUL.md |

Both use the same core loop: propose → test → score → side-by-side → human approval → apply.
