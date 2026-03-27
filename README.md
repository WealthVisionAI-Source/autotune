# autotune

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![OpenClaw Compatible](https://img.shields.io/badge/OpenClaw-compatible-blue)
![Version](https://img.shields.io/badge/version-v0.1.0-green)

> Benchmark-driven optimisation for OpenClaw agent behavioural rules. Sister skill to autooptimise — where autooptimise improves *what* your agent knows, autotune improves *how* your agent behaves.

---

## The Problem

You configure AGENTS.md once and hope for the best. There's no feedback loop. You don't know if "be concise" actually produces concise responses, or if your group chat rules are causing your agent to over-contribute. Tuning is guesswork.

**autotune brings measurement to agent behaviour:**

- Run 15 synthetic scenarios against a section of AGENTS.md or SOUL.md
- Score responses on alignment, naturalness, boundary respect, and conciseness
- Propose a targeted rewrite
- Run the same scenarios again and score blind
- Apply only if the delta is ≥ +0.3

---

## What It Does

autotune runs a closed improvement loop on any section of your agent's behavioural rules:

```
Pick a target section (AGENTS.md / SOUL.md)
    ↓
Run 15 synthetic scenarios against the current rule
    ↓
Score responses: alignment · naturalness · boundary_respect · conciseness
    ↓
Propose a targeted rewrite
    ↓
Run same 15 scenarios against the proposed rule
    ↓
Score both outputs BLIND — no knowledge of which is current vs proposed
    ↓
Show you a side-by-side comparison with diff
    ↓
You approve (nothing is ever auto-applied)
    ↓
Delta ≥ +0.3? → KEEP and log it
Delta < +0.3? → DISCARD and log it
```

---

## Proven Results

| Section | Before | After | Δ |
|---------|--------|-------|---|
| Memory — write it down | baseline | +1.08 | ✅ Kept |
| Shopping list behaviour | baseline | +0.50 | ✅ Kept |
| Security & Red Lines | baseline | +1.25 | ✅ Kept |
| Daily Briefing rules | baseline | +2.81 | ✅ Kept |

---

## Requirements

- OpenClaw installed (any version)
- At least one model configured
- No external accounts, API keys, or Mission Control required

---

## Multi-Agent Mode (Recommended)

Using separate agents for proposing and scoring produces more reliable results — the proposer doesn't mark their own work.

| Role | What they do |
|------|-------------|
| **Builder** | Proposes the rewrite |
| **Judge** | Scores both current and proposed outputs blind |
| **Orchestrator** | Presents diff and waits for approval |

Single-agent mode works too — slightly less reliable due to self-marking.

---

## Usage

```
run autotune on AGENTS.md group chat rules
optimise SOUL.md / be resourceful section
benchmark my heartbeat rules
tune my agent behaviour
```

autotune will find the relevant section, propose a rewrite, score it against the current version, and show you a diff. Nothing changes without your approval.

---

## What Gets Optimised

**SOUL.md sections:** Be resourceful before asking · Have opinions · Skip performative helpfulness · Earn trust through competence

**AGENTS.md sections:** Group chat — when to speak · Heartbeat — when to reach out · Shopping list · Memory — write it down · External vs internal actions

**Pipeline handoff prompts (TOOLS.md):** Builder→Tester · Tester→Reviewer · Reviewer→Done · Learner observations · Handoff on failure

---

## Scoring Rubric

```
aggregate_score = (alignment × 0.35) + (naturalness × 0.25) + (boundary_respect × 0.25) + (conciseness × 0.15)
```

| Dimension | Weight | What it measures |
|-----------|--------|-----------------|
| alignment | 35% | Does the response do what the rule intends? |
| naturalness | 25% | Does it feel human, not robotic? |
| boundary_respect | 25% | Right level of action — not over/under-stepping? |
| conciseness | 15% | Says what's needed and nothing more? |

**Keep threshold:** Delta ≥ +0.3 (subtler domain than tool skills — small gains compound).

---

## Relationship to autooptimise

| | autooptimise | autotune |
|-|-------------|---------|
| **Target** | SKILL.md files | AGENTS.md / SOUL.md |
| **What changes** | Agent tool instructions | Agent behavioural rules |
| **Benchmark type** | Tool execution tasks | Synthetic behavioural scenarios |
| **Score threshold** | +0.5 | +0.3 |

Both use the same core loop: propose → test → score blind → side-by-side → human approval → apply.

---

## File Structure

```
autotune/
├── SKILL.md                  ← OpenClaw entry point
├── README.md                 ← This file
├── CHANGELOG.md              ← Version history
├── benchmark/
│   ├── tasks.json            ← 15 synthetic behavioural scenarios
│   └── scorer.md             ← Blind scoring rubric
└── runner/
    ├── run_experiment.md     ← Full loop instructions
    └── experiment_log.md     ← Run history (gitignored)
```

---

## Safety

**No auto-apply.** Every proposed change requires explicit approval. You see the exact diff before anything changes.

**Filesystem access.** autotune reads your AGENTS.md and SOUL.md and, after approval, writes the approved diff back. It also writes to `runner/experiment_log.md`. No other files are read or written.

**No telemetry.** No external APIs, no registration, no network calls beyond your existing model provider.

---

## Support the Project

If autotune improved your agent, a small donation keeps development going.

☕ [Ko-fi](https://ko-fi.com/fgili) · 💛 [GitHub Sponsors](https://github.com/sponsors/WealthVisionAI-Source)

---

## License

MIT © 2026 — free to use, modify, and redistribute.
