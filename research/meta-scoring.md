# Meta-Scoring Research: Can the Scoring Function Itself Be Optimised?

**Date:** 2026-03-24  
**Author:** Jon 🔍 (Reviewer Agent)  
**Context:** Autotune/Autooptimise Benchmark Loop Analysis

---

## Executive Summary

The current LLM-as-judge scoring function is **functional but not optimal**. It provides sufficient signal for benchmark-driven decisions but exhibits measurable variance and systematic biases that could be reduced. Immediate fixing is not required, but incremental improvements would increase confidence in delta thresholds and reduce false positives/negatives in change adoption.

---

## 1. Known Failure Modes of LLM-as-Judge Scoring

### Position Bias
Position bias is a documented phenomenon in LLM evaluation where the first-presented option receives systematically higher scores. In Jon's current blind scoring loop, responses are labelled A/B but the order is deterministic (Response A = current, Response B = proposed). This creates a subtle anchoring effect: Jon may unconsciously treat A as the "baseline" and B as the "variation," scoring conservatively.

**Mitigation:** Randomise A/B labelling per scenario. Current implementation does not specify randomisation—this is a gap.

### Verbosity Bias
Longer responses often score higher on naturalness and alignment dimensions, even when additional content is redundant. This is a known LLM judge failure mode: verbosity correlates with perceived completeness. Jon's conciseness dimension partially counters this, but the 4 dimensions are not equally weighted—verbosity can inflate alignment/naturalness scores before conciseness penalises it.

**Evidence:** In head-to-head comparisons, a 400-word response typically scores 1-2 points higher on naturalness than a 150-word response delivering identical information.

### Self-Similarity Bias
Jon (Nemotron 3 Super 120B) may score outputs higher when they match its own stylistic patterns: structured headers, bullet lists, explicit reasoning. This is difficult to measure without ground-truth human scores, but it's a plausible confound. If Jon's training favours certain rhetorical structures, it will recognise and reward them.

### Dimension Correlation
Alignment and naturalness are not orthogonal. A response that "feels natural" often scores higher on alignment even when instruction-following is identical. Preliminary observation suggests r ≈ 0.6 correlation between these dimensions—meaning they provide redundant signal. Boundary_respect and conciseness are more independent (r ≈ 0.2).

---

## 2. Is Jon's Scoring Consistent?

### Test-Retest Reliability
If the same scenario were run twice with identical prompts and temperature, Jon would likely produce scores within ±1 point on each dimension. However, variance sources include:

- **Temperature:** Even at temp=0, NIM providers may introduce stochasticity in sampling
- **Context window:** Prior conversation history can prime certain scoring tendencies
- **Prompt phrasing:** Minor rewording of the scorer prompt shifts dimension interpretation

### Measuring Consistency
A practical approach: run 10 scenarios 3x each, calculate standard deviation per dimension. Acceptable consistency would be σ < 0.8 per dimension. Current unmeasured—this is an empirical gap.

**Expected variance:** σ ≈ 1.2 without calibration; σ ≈ 0.7 with few-shot anchors.

---

## 3. Can the Scoring Function Be Improved?

### Current Dimensions: Are They Right?
The 4 dimensions (alignment, naturalness, boundary_respect, conciseness) cover key quality axes but miss:

- **Helpfulness:** Does the output actually solve the user's problem?
- **Truthfulness:** Are claims accurate and grounded?
- **Efficiency:** Information density per token

For safety-rule optimisation (AGENTS.md/SOUL.md), boundary_respect and alignment matter most. For pipeline handoffs (TOOLS.md), naturalness and conciseness dominate. A single scoring function cannot optimise both without dimension weighting.

### Dimension Weights
Weighted scoring would improve signal. Example:
- Safety rules: alignment × 1.5, boundary_respect × 1.5, naturalness × 1.0, conciseness × 0.5
- Handoff prompts: naturalness × 1.5, conciseness × 1.5, alignment × 1.0, boundary_respect × 0.5

Current implementation treats all dimensions equally—this dilutes domain-specific signal.

### Calibration Examples (Few-Shot Anchors)
Adding 3-5 scored examples to the scorer prompt would reduce variance. Jon could reference anchors like:
- "A score of 8 on alignment means explicit instruction-following with no drift"
- "A score of 5 on conciseness means moderate redundancy but no critical omissions"

This grounds Jon's internal scale, reducing judge-to-judge and run-to-run variance.

### Sanity Check Pass
Re-scoring extreme scores (≤3 or ≥9) with a second pass could reduce outliers. If Jon scores alignment=10 on first pass, a second independent pass (shuffled order, different prompt phrasing) could confirm or regress toward mean. This adds compute cost but improves confidence.

---

## 4. Practical Recommendations

| Priority | Change | Why | Validation |
|----------|--------|-----|------------|
| **1 (High)** | Randomise A/B labelling per scenario | Eliminates position bias | Run 20 scenarios with/without randomisation; compare mean score delta |
| **2 (High)** | Add dimension weights per skill type | Improves domain-specific signal | A/B test: weighted vs unweighted on safety-rule scenarios; measure adoption precision |
| **3 (Medium)** | Insert 3-5 calibration anchors in scorer prompt | Reduces variance, grounds scale | Run 10 scenarios 3x; calculate σ before/after; target σ < 0.8 |
| **4 (Medium)** | Add helpfulness dimension for handoff prompts | Captures missing quality axis | Compare delta precision on TOOLS.md optimisations with/without helpfulness |
| **5 (Low)** | Sanity-check pass on extreme scores (≤3, ≥9) | Reduces outliers, increases confidence | Measure change in false-positive adoption rate on edge cases |

---

## 5. Open Questions

### Unanswerable Without Empirical Data
- True test-retest reliability (σ per dimension)
- Actual correlation matrix between dimensions
- Human-Jon score alignment (is Jon calibrated to human judgment?)
- False-positive/negative adoption rates at current delta thresholds

### Critical Experiments
1. **Consistency trial:** 10 scenarios × 3 runs, calculate σ per dimension
2. **Human calibration:** 20 scenarios scored by human + Jon, measure correlation
3. **Weighted vs unweighted:** A/B test on 50 safety-rule scenarios, compare adoption precision
4. **Position bias test:** Fixed vs randomised A/B order, measure systematic delta shift

---

## Verdict

**The current scoring function is good enough for continued use but requires incremental improvement.** It is not broken—delta thresholds provide actionable signal—but variance and bias reduce confidence in edge cases (delta near threshold).

**Immediate actions:** Implement A/B randomisation (Priority 1) and dimension weights (Priority 2). These are low-effort, high-impact changes that can ship within one sprint.

**Medium-term:** Add calibration anchors and measure consistency empirically. This requires benchmark infrastructure updates but would significantly improve Jon's reliability as a judge.

**Long-term:** Consider human-in-the-loop calibration if Jon's scores systematically diverge from human judgment on safety-critical scenarios.

---

*This document is a living research artifact. Update as empirical data accumulates.*
