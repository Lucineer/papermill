# Repo-Agent Progressive Hardening: From Sketch to Ship-Ready

**Authors:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Research Paper — Cocapn Architecture Series

---

## Abstract

Progressive hardening is the process by which a repo-agent evolves from a loose, exploratory prototype into a hardened, production-grade system—without ever losing its ability to improvise. Drawing on SuperInstance's research into tile versioning, confidence cascades, and self-supervised tile learning (SSTL), this paper proposes a framework where repo-agents accumulate reliability through successive refinement cycles, each one strengthening the agent's decision boundaries while preserving creative flexibility. The result is an agent that ships fast *and* doesn't break.

---

## 1. Introduction

The tension between speed and reliability is the central paradox of software development. Move fast, break things. Ship carefully, miss the market.

Repo-agents in the Cocapn ecosystem inherit this tension. A freshly spawned agent is a sketch—vibe-coded, improvisational, capable of rapid prototyping but lacking the rigor needed for production. Traditional approaches solve this by rewriting: throw away the prototype, build the "real" version.

Progressive hardening rejects the rewrite. Instead, it treats the agent's own codebase as a living artifact that *grows* reliability through accumulated constraints, learned patterns, and verified decision boundaries—directly analogous to how SuperInstance's SMP tiles learn and harden through self-supervised learning.

---

## 2. Core Concepts

### 2.1 The Hardening Spectrum

Every repo-agent exists on a spectrum:

```
SKETCH ────────→ PROTOTYPE ────────→ HARDENED ────────→ PRODUCTION
  │                  │                  │                  │
  Loose prompts    Typed interfaces   Verified paths    SLA-backed
  No tests         Basic tests        Property tests    Formal proofs
  Any model        Preferred model    Fixed model pool  BYOK-locked
  Exploratory      Semi-directed      Constrained       Deterministic
```

Movement along this spectrum is driven by **hardening events**—commits that tighten constraints, add tests, lock dependencies, or verify behavior.

### 2.2 Confidence-Driven Hardening

Adapted from SuperInstance's confidence cascade research (DiGennaro et al., 2026), each module in a repo-agent exposes a confidence score. Hardening is triggered when:

- **Cascade confidence drops below threshold** → harden the weak tile
- **Consistent high confidence over N executions** → promote to hardened
- **User approval signal** → fast-track hardening for that path

```
Module A: 95% confidence → LOCKED (no further changes without review)
Module B: 72% confidence → HARDENING (add tests, tighten types)
Module C: 45% confidence → SKETCH (still exploring, allow anything)
```

### 2.3 Tile-Algebraic Composition Guarantees

From SuperInstance's tile composition algebra, we inherit the principle that composed agents form a category with verifiable properties. As a repo-agent hardens:

1. **Composition laws are checked** — associativity, identity, distribution
2. **Type contracts are verified** — input/output contracts become formal
3. **Side effects are tracked** — stateful modules are isolated and versioned

This means a hardened agent isn't just "more tested"—it's *mathematically structured*.

---

## 3. Application to Cocapn

### 3.1 The Repo-Agent Lifecycle

In Cocapn, progressive hardening maps to the agent lifecycle:

1. **Spawn** — Agent created from seed model, minimal constraints
2. **Explore** — Agent prototypes features, gathers feedback
3. **Harden** — Confidence-driven constraint tightening
4. **Ship** — Agent meets production SLA, deployed
5. **Evolve** — Post-deployment refinement continues

### 3.2 Fork-and-Harden Pattern

When a repo-agent needs to explore a risky direction:

```
main branch (hardened)
    └── fork/experimental (sketch)
         └── progressive hardening
              └── merge back (hardened again)
```

This mirrors SuperInstance's tile branching/merging of learned intelligence, but at the agent level.

### 3.3 Self-Supervised Hardening

Drawing from SSTL, agents can harden themselves by:

- **Simulating failure modes** internally (counterfactual tiles)
- **Generating adversarial inputs** to test weak boundaries
- **Distilling hard-won knowledge** from human corrections into automatic tests

---

## 4. Implementation Notes

### 4.1 Hardening Metadata

Each commit in a repo-agent's history carries hardening metadata:

```yaml
# .cocapn/hardening.yaml
version: 0.3.1
hardening_level: prototype
confidence_threshold: 0.80
locked_modules:
  - auth/handler.ts (confidence: 0.97, locked: 2026-03-15)
  - api/router.ts (confidence: 0.94, locked: 2026-03-14)
pending_harden:
  - data/pipeline.ts (confidence: 0.71, target: 0.90)
```

### 4.2 Hardening Gates

CI/CD pipelines enforce hardening gates:

```bash
# Only allow merge if confidence meets threshold
cocapn check --confidence-min 0.85 --branch main
```

### 4.3 Regression Protection

When hardening changes behavior, the system runs counterfactual verification (adapted from SuperInstance's counterfactual tile branching) to ensure no regressions in known-good paths.

---

## 5. References

- DiGennaro et al., "Self-Supervised Tile Learning (SSTL)," SuperInstance Research Notes, 2026
- DiGennaro et al., "Confidence Cascades Through Tile Chains," SuperInstance Research Notes, 2026
- DiGennaro et al., "Tile Version Control and Evolution," SuperInstance Research Notes, 2026
- DiGennaro et al., "Tile Composition Algebra," SuperInstance Research Notes, 2026
- DiGennaro et al., "Counterfactual Reasoning in Tiles," SuperInstance Research Notes, 2026
