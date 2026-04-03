# Progressive Hardening: A Mathematical Model for Soft Actualization in Autonomous Systems

**Author:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Draft
**Keywords:** progressive hardening, soft actualization, convergence, partial evaluation, program transformation, formal methods, agent maturation

## Abstract

We present a formal mathematical model for progressive hardening — the process by which an autonomous agent gradually replaces runtime reasoning with compiled behavior. We define the Soft Actualization function, prove convergence conditions under which hardening terminates, establish error bounds for partially hardened systems, and demonstrate a deep connection to Futamura projections in partial evaluation. The model provides theoretical guarantees for systems that evolve from flexible, reasoning-intensive agents to efficient, compiled applications.

## 1. Introduction

A new repo-agent begins its life as pure potential: a runtime (LLM) that reasons about every action, a hull with no specialized code, and accumulated context that grows with each interaction. Over time, recurring patterns get compiled into code. What once required reasoning becomes a function call. The agent "hardens."

This is not unique to repo-agents — it is a universal phenomenon in system maturation. A startup's informal processes become SOPs. A child's conscious decisions become automatic habits. We formalize this process.

## 2. Core Concepts

### 2.1 The Soft Actualization Spectrum

**Definition 2.1 (Hardness).** Let h ∈ [0, 1] be the hardness of a system, where:
- h = 0: Pure runtime reasoning (every action requires inference)
- h = 1: Pure compiled behavior (every action is a deterministic function call)

Real systems operate in the interior (0, 1). We call this *soft actualization* — the system is neither fully soft (reasoning) nor fully hard (compiled).

**Definition 2.2 (Hardening Function).** Given a system with current hardness hₜ and a set of recurring behavioral patterns Pₜ, the hardening function is:

h_{t+1} = hₜ + η · |Pₜ ∩ P_{t-1}| / |Pₜ|

where η is the hardening rate (how aggressively patterns are compiled).

### 2.2 Formal System Model

**Definition 2.3 (Behavioral Program).** A behavioral program B = (C, R) where:
- C is the compiled code (deterministic functions)
- R is the runtime reasoning specification (what the LLM must figure out)

The system's total behavior at time t is: behavior(t) = C(t) ∪ R(t)

**Definition 2.4 (Hardening Operation).** A hardening step takes a frequently-occurring reasoning pattern r ∈ R and compiles it into code c, moving it from R to C:

harden(r) = (C ∪ {compile(r)}, R \ {r})

### 2.3 Convergence Conditions

**Theorem 2.1 (Hardening Convergence).** A system converges to a stable hardness h* < 1 if and only if:

1. The behavioral environment is ergodic (patterns recur)
2. The compiled code C remains consistent (no conflicting compilations)
3. New behavioral requirements emerge at rate λ < η · |P_max|

*Proof.* Under condition 1, patterns recur and can be compiled. Under condition 2, compilation doesn't need to be undone. Under condition 3, the rate of new requirements is less than the rate of compilation, so R shrinks over time. Therefore h approaches a limit h* = η · λ / (η + λ). Since λ > 0 (the world changes), h* < 1. □

**Corollary 2.1.** In a static environment (λ = 0), hardening converges to h* = 1 (full compilation). In a maximally dynamic environment (λ → ∞), hardening converges to h* = 0 (pure reasoning).

### 2.4 Error Bounds

**Definition 2.5 (Hardening Error).** When a pattern r is compiled into code c, the hardening error is:

ε(r, c) = P(correct_response | use c) - P(correct_response | use r)

That is, the probability that the compiled code produces the correct response minus the probability that full reasoning produces the correct response.

**Theorem 2.2 (Error Accumulation Bound).** The total system error after n hardening steps is bounded by:

E_total ≤ Σᵢ₌₁ⁿ ε(rᵢ, cᵢ) · frequency(rᵢ)

*Proof.* Each compiled pattern rᵢ contributes error proportional to its frequency of use. Since errors are independent (patterns don't interfere in well-structured systems), they sum linearly. □

This bound justifies conservative hardening: only compile patterns with low ε and high frequency.

### 2.5 Relationship to Partial Evaluation

The hardening process is isomorphic to Futamura's projections in partial evaluation:

| Partial Evaluation | Progressive Hardening |
|---|---|
| Static input | Accumulated context |
| Dynamic input | Current situation |
| Residual program | Compiled behavior C |
| Specializer | The hardening operation |
| Self-application (Futamura-3) | The agent compiling its own reasoning |

**Theorem 2.3 (Futamura-Hardening Correspondence).** The third Futamura projection (self-application of a partial evaluator) is equivalent to a repo-agent hardening its own reasoning patterns.

*Proof.* In Futamura-3, a partial evaluator P applied to itself (P(P, program)) produces a compiler. In progressive hardening, an agent's runtime reasoning about its own behavior patterns produces compiled code. Both are cases of a program specializing itself. □

### 2.6 The Hardness-Adaptability Tradeoff

**Theorem 2.4 (Tradeoff).** For a system with hardness h, the adaptability to novel situations is bounded by:

adaptability(h) ≤ (1 - h) · model_capability

*Proof.* Only the uncompiled portion (1 - h) can be adapted through reasoning. The compiled portion h is fixed. Adaptation quality depends on model capability for the remaining reasoning. □

This formalizes the intuition that over-hardened systems become brittle.

## 3. Implementation

### 3.1 Hardening Signals in Repo-Agents

A repo-agent detects hardening opportunities through:
- Repeated code generation patterns (generating similar code ≥ 3 times)
- Frequently executed query sequences (same information retrieved repeatedly)
- User corrections that converge (the agent learns the right answer after a few attempts)
- Performance bottlenecks (runtime reasoning is too slow or expensive)

### 3.2 Safe Hardening Protocol

1. **Detect:** Identify recurring pattern
2. **Abstract:** Generalize from specific instances
3. **Compile:** Write code that handles the pattern
4. **Test:** Verify the compiled code produces correct results
5. **Deploy:** Replace reasoning with code call
6. **Monitor:** Watch for edge cases the compilation misses
7. **Rollback:** Revert if error rate exceeds threshold

### 3.3 Hardness Dashboard

A monitoring system tracks h(t) over time, ε per compiled pattern, and the adaptability tradeoff curve. Operators can adjust η (hardening rate) based on their priorities.

## 4. Applications to Cocapn

Progressive hardening explains the repo-agent lifecycle:
- **Birth:** h ≈ 0, all behavior is reasoning
- **Growth:** h increases as patterns are compiled into skills
- **Maturity:** h stabilizes at h* based on environment dynamism
- **Stasis:** If environment changes, h* shifts, triggering re-softening

Cocapn's skill system is the mechanism for hardening: when a behavioral pattern is detected, it gets packaged as a skill (compiled code + instructions) and installed.

## 5. Related Work

- **Futamura, Y. (1971):** "Partial Evaluation of Computation Process — An Approach to a Compiler-Compiler." The foundational work on partial evaluation.
- **Jones, N. et al. (1993):** *Partial Evaluation and Automatic Program Generation.* Prentice Hall.
- **Ashby, W.R. (1956):** *An Introduction to Cybernetics.* Adaptation and stability in dynamic systems.
- **Dijkstra, E.W. (1968):** "Go To Statement Considered Harmful." Structured programming as hardening of control flow.

## 6. Future Directions

1. **Automatic hardening:** Agents that compile their own patterns without human intervention
2. **Adaptive hardening rate:** Dynamically adjusting η based on environment change detection
3. **De-hardening:** Reverting compiled code back to reasoning when patterns become obsolete
4. **Cross-vessel hardening:** Sharing compiled patterns across a fleet

## References

1. Futamura, Y. (1971). "Partial Evaluation of Computation Process." *Systems, Computers, Controls.*
2. Jones, N., Gomard, C. & Sestoft, P. (1993). *Partial Evaluation and Automatic Program Generation.* Prentice Hall.
3. Ashby, W.R. (1956). *An Introduction to Cybernetics.* Chapman & Hall.
4. DiGennaro, C. et al. (2026). "Repo-Agent Progressive Hardening." Papermill Working Paper.
5. Bird, R. (2010). *Pearls of Functional Programming.* Cambridge University Press. (For program transformation theory.)
