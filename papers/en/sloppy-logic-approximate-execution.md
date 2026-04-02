# Sloppy Logic and Approximate Execution: Good Enough Is Good Enough

**Authors:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Research Paper — Cocapn Architecture Series

---

## Abstract

Not every computation needs to be correct. Most human reasoning is approximate—we estimate, we round, we satisfice. This paper proposes a framework for "sloppy logic" in repo-agents: execution paths that deliberately trade precision for speed, cost, and creative potential. Drawing on SuperInstance's research into confidence cascades, execution strategies (parallel/series/sync/async routing), and tile algebra, we show how approximate execution enables repo-agents to be fast, cheap, and surprisingly insightful.

---

## 1. Introduction

Software engineering has an obsession with correctness. Every function must return the right answer. Every edge case must be handled. Every path must be tested.

But repo-agents aren't traditional software. They're creative entities that prototype, explore, and iterate. For these agents, "correct" is often the enemy of "useful." A restaurant recommendation that's 80% right and delivered in 100ms beats one that's 100% right and takes 10 seconds.

SuperInstance's execution strategy research revealed that user-controlled parallelization enables dramatic speedups by running tiles in parallel instead of series. We extend this: *approximate execution* enables even greater gains by allowing tiles to return "good enough" answers when full precision isn't needed.

---

## 2. Core Concepts

### 2.1 The Precision Spectrum

```
EXACT ──────→ APPROXIMATE ──────→ HEURISTIC ──────→ VIBE
  │                │                    │              │
  Math proofs     Rounding            Rules of       "Feels right"
  Financial calcs  Estimation         thumb          Creative tasks
  Slow, expensive  Fast, cheap        Very fast      Instant
```

### 2.2 Confidence-Gated Execution

From SuperInstance's confidence cascade research:

> "When Tile A is 90% confident and Tile B is 80% confident, what's the cascade? It depends on how they're connected."

We add a third option beyond sequential and parallel: **approximate parallel**.

```
# Exact: All tiles must complete
result = await Promise.all([tileA.exact(), tileB.exact(), tileC.exact()])

# Approximate: Return first "good enough" result
result = await Promise.race([
  tileA.approximate(threshold: 0.7),
  tileB.approximate(threshold: 0.7),
  tileC.exact()  // fallback
])
```

### 2.3 Sloppy Composition

From tile algebra, we know composition is associative. But sloppy composition adds a new operation: **satisfice**.

```
Exact:     f(g(h(x))) = f ∘ g ∘ h(x)     // All must succeed
Satisfice: f(??(h(x)))                    // g can be skipped if f is tolerant
Vibe:      ??(??(??(x)))                  // Any tile, any order, good enough
```

---

## 3. Application to Cocapn

### 3.1 Approximate Agent Behaviors

Repo-agents in Cocapn can operate at different precision levels:

- **Draft mode** — Fast, approximate responses, no fact-checking
- **Standard mode** — Normal execution with confidence thresholds
- **Rigorous mode** — Full verification, multiple passes, high confidence

### 3.2 Cost-Optimized Execution

For fleet-scale operations, approximate execution is essential:

```
100 repo-agents × 1 exact query = $10.00
100 repo-agents × 1 approximate query = $0.50
Savings: 95%
```

### 3.3 Creative Surprises

Approximate execution intentionally introduces noise. This noise can produce creative outputs that exact execution never would—a happy accident, a novel connection, a "wrong" answer that's actually brilliant.

---

## 4. Implementation Notes

### 4.1 Precision Annotations

```yaml
# .cocapn/precision.yaml
modules:
  - path: src/recommend.ts
    precision: approximate
    confidence_threshold: 0.7
    timeout_ms: 500
    fallback: src/recommend-exact.ts
```

### 4.2 Sloppy Pipeline DSL

```yaml
pipeline:
  - step: classify
    mode: approximate  # fast, cheap
  - step: rank
    mode: standard     # normal
  - step: verify
    mode: exact        # must be correct
```

---

## 5. References

- DiGennaro et al., "Confidence Cascades Through Tile Chains," SuperInstance Research Notes, 2026
- DiGennaro et al., "Execution Strategies," SuperInstance Research Notes, 2026
- DiGennaro et al., "Tile Composition Algebra," SuperInstance Research Notes, 2026
- DiGennaro et al., "Tile Energy Efficiency," SuperInstance Research Notes, 2026
