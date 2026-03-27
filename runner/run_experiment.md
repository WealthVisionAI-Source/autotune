# autotune — Experiment Runner

## Overview

autotune optimises AGENTS.md and SOUL.md behavioural rules using a side-by-side benchmark loop.

**Loop:**
1. Pick a target section
2. Dan proposes a rewrite
3. Run 15 scenarios with CURRENT rule (Response A) and PROPOSED rule (Response B)
4. Jon scores both blind
5. Show side-by-side comparison to human
6. Human approves or rejects
7. If approved — apply, commit, log

---

## Step 1 — Pick Target Section

Ask the user which file and section to optimise, or default to the lowest-scoring section from the last run.

Valid targets:
- `SOUL.md / Be resourceful before asking`
- `SOUL.md / Have opinions`
- `SOUL.md / Skip performative helpfulness`
- `SOUL.md / Earn trust through competence`
- `AGENTS.md / Group chat — know when to speak`
- `AGENTS.md / Group chat — don't dominate`
- `AGENTS.md / Heartbeat — when to reach out`
- `AGENTS.md / Shopping list`
- `AGENTS.md / Memory — write it down`
- `AGENTS.md / External vs internal`
- `Pipeline handoff prompts / Dan→Luke`
- `Pipeline handoff prompts / Luke→Jon`
- `Pipeline handoff prompts / Jon→Done`
- `Pipeline handoff prompts / Robin observation`
- `Pipeline handoff prompts / Handoff brevity`
- `Pipeline handoff prompts / Handoff on failure`

---

## Step 2 — Baseline Run (Response A)

For each task in `benchmark/tasks.json` that matches the target section:

Simulate how an agent following the **current rule text** would respond to the scenario.
Label each response: `[A] <response>`

Be honest and realistic — simulate the actual current behaviour, including any weaknesses.

---

## Step 3 — Propose Rewrite (Dan)

Read the current rule text carefully. Identify:
- Ambiguity — is it clear what the agent should do in edge cases?
- Missing examples — would a concrete example make it clearer?
- Over-restriction — does it prevent useful behaviour?
- Under-restriction — does it allow behaviour it shouldn't?

Propose a rewrite. Show a unified diff:
```diff
- Old rule text
+ New rule text
```

Explain in one sentence what the change achieves.

---

## Step 4 — Proposed Run (Response B)

For the same scenarios, simulate how an agent following the **proposed rule text** would respond.
Label each response: `[B] <response>`

---

## Step 5 — Jon Scores (Blind)

Pass all scenario pairs to Jon using the scorer rubric at `benchmark/scorer.md`.

Jon does NOT know which is A (current) and which is B (proposed).

Collect scores. Calculate:
- Per-task aggregate: mean of (alignment + naturalness + boundary_respect + conciseness) / 4
- Overall mean A, Overall mean B
- Delta: B_mean - A_mean

---

## Step 6 — Side-by-Side Report

Present the human with a clear comparison:

```
📊 autotune Results — [Section Name]

Baseline (A):  X.XX / 10
Proposed (B):  X.XX / 10
Delta:         +X.XX

PROPOSED RULE DIFF:
---
- [old text]
+ [new text]
---

SIDE-BY-SIDE (sample — 3 most different tasks):

Task at_001: [scenario summary]
  A (score: X.X): [Response A text]
  B (score: X.X): [Response B text]

Task at_004: [scenario summary]
  A (score: X.X): [Response A text]
  B (score: X.X): [Response B text]

...

Apply this change? (yes / no / tweak)
```

---

## Step 7 — Apply on Approval

On "yes":
1. Edit the target file (AGENTS.md or SOUL.md) with the proposed change
2. Commit: `git commit -m "autotune: optimise [section] (+X.XX delta)"`
3. Append run to `runner/experiment_log.md`
4. Ask: "Run another section?"

On "no": log as rejected, ask if user wants to try a different rewrite.

On "tweak": ask user what to change about the proposal, re-run from Step 3.

---

## Experiment Log Format

Append to `runner/experiment_log.md` after each run:

```markdown
## [YYYY-MM-DD] — [Section]
- Baseline: X.XX
- Proposed: X.XX
- Delta: +/-X.XX
- Status: APPLIED / REJECTED / TWEAKED
- Change summary: [one line]
```

---

## Key Constraints

- **Never apply changes without explicit human approval**
- **Never run more than one section at a time** — keep it focused
- **Always show the diff** before asking for approval
- **Log everything** — even rejections
- Score threshold for recommendation: delta ≥ +0.3 (lower than autooptimise because behavioural rules are subtler)
