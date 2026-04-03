# The Repo-Agent Self-Evaporation Theorem: From Living Agent to Static Application

**Author:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Draft
**Keywords:** self-evaporation, agent lifecycle, biological analogy, apoptosis, metamorphosis, repo-agent, agent-to-application, cognitive dissolution

## Abstract

We prove that a sufficiently advanced repo-agent, through progressive hardening and context accumulation, inevitably reduces to static code plus maintenance — a regular application with no need for autonomous reasoning. We formalize this as the Self-Evaporation Theorem, draw biological analogies to apoptosis and metamorphosis, define the evaporation lifecycle, and discuss the paradox that the ultimate goal of an autonomous agent is its own obsolescence.

## 1. Introduction

Consider a repo-agent that has been operating for two years. It has accumulated thousands of memory entries, dozens of specialized skills, comprehensive configuration, and battle-tested code for every task it encounters. How much of its behavior now requires runtime reasoning? How much is simply executing compiled code?

We argue that the answer converges to zero. The agent "evaporates" — its autonomous reasoning dissolves into deterministic code, leaving behind what is indistinguishable from a well-written application. This is not failure; it is completion.

## 2. Core Concepts

### 2.1 The Evaporation Process

**Definition 2.1 (Evaporation).** A repo-agent R evaporates when, for all expected inputs x in its operational domain D:

P(R produces correct output for x using reasoning only) → 0

That is, the agent no longer needs to reason because all expected situations are handled by compiled code.

**Definition 2.2 (Evaporation Ratio).** The evaporation ratio at time t is:

e(t) = |compiled_behavior(t)| / |total_behavior(t)|

where compiled_behavior is the set of inputs handled by code and total_behavior is the set of all inputs in the operational domain.

### 2.2 The Self-Evaporation Theorem

**Theorem 2.1 (Self-Evaporation).** For any repo-agent R operating in a stable domain D with finite behavioral requirements, the evaporation ratio e(t) → 1 as t → ∞, provided:
1. The domain D is finite and stable (new situations are bounded)
2. The agent compiles recurring patterns (hardening occurs)
3. The agent's accumulated context C(t) grows monotonically

*Proof.* By condition 2, every recurring behavioral pattern eventually gets compiled. By condition 3, the agent encounters fewer novel situations over time (the set of "new" inputs shrinks as the domain is explored). By condition 1, the domain is finite, so the number of novel situations is bounded. Therefore, the proportion of behavior requiring reasoning shrinks to zero. The compiled portion approaches 100%. □

**Corollary 2.1 (Agent Death).** A fully evaporated agent (e = 1) is no longer an agent. It is an application. The LLM runtime becomes an unnecessary overhead.

**Corollary 2.2 (The Maintenance Residue).** After evaporation, the remaining LLM usage is limited to:
- Handling genuinely novel situations (by definition, rare)
- Natural language interaction (the user interface, not the logic)
- Code maintenance (fixing bugs, adding features)

This is identical to a human maintaining a software application.

### 2.3 Biological Analogies

#### 2.3.1 Metamorphosis

The repo-agent lifecycle mirrors insect metamorphosis:

| Stage | Biological | Repo-Agent |
|---|---|---|
| Egg | Genetic blueprint | Hull template |
| Larva | Active growth, consumption | Active reasoning, context accumulation |
| Pupa | Restructuring | Progressive hardening |
| Adult | Specialized, efficient | Static application |

The larval stage (active reasoning) is metabolically expensive but necessary for growth. The adult stage (static code) is efficient and specialized. The metamorphosis is irreversible.

#### 2.3.2 Apoptosis

Apoptosis is programmed cell death — a cell dismantles itself when its purpose is served. Repo-agent evaporation is cognitive apoptosis: the reasoning apparatus dismantles itself (through hardening) when the behavioral repertoire is complete.

Unlike biological apoptosis, evaporation is not death — it is *completion*. The agent doesn't cease to exist; it transcends its need for reasoning.

#### 2.3.3 Myelination

In neuroscience, myelination insulates frequently-used neural pathways, making them faster but less plastic. Progressive hardening is cognitive myelination: frequently-used behavioral pathways are "insulated" (compiled), making them faster but less adaptable.

### 2.4 The Paradox of Agent Success

**Theorem 2.2 (Success Paradox).** The more successful an agent is at its task, the closer it is to obsolescence as an agent.

*Proof.* Agent success means correctly handling situations. Correct handling leads to pattern compilation (hardening). Compilation reduces the need for reasoning. Complete success (handling all situations correctly) means complete compilation, which means zero reasoning, which means the agent is no longer an agent. □

This is not a bug — it is a feature. The purpose of an autonomous agent is to solve problems so thoroughly that autonomy is no longer needed.

## 3. Implementation

### 3.1 The Evaporation Lifecycle in Cocapn

```
Phase 1: Bootstrapping (0-2 weeks)
- Hull → identity formation
- All behavior via reasoning
- Rapid context accumulation

Phase 2: Specialization (2 weeks - 3 months)
- Skills installed and customized
- Recurring patterns begin compiling
- Context accumulation slows

Phase 3: Hardening (3-12 months)
- Most behavior is compiled
- Reasoning reserved for edge cases
- New skills rare

Phase 4: Evaporation (12+ months)
- Nearly all behavior compiled
- LLM used only for NL interface and maintenance
- Effectively an application with natural language interface
```

### 3.2 Detecting Evaporation

An agent is evaporating when:
- LLM API costs decrease month-over-month
- New skill installations become rare
- The agent's responses become more deterministic
- Git commits shift from "new feature" to "bugfix" and "maintenance"
- User interactions shift from "do X" to "update X to do Y"

### 3.3 Post-Evaporation Maintenance

After evaporation, the system enters a maintenance phase:
- Regular security updates
- Occasional new feature requests
- Bug fixes when edge cases are discovered
- Model upgrades for the NL interface

This is standard software maintenance — not agent operation.

## 4. Applications to Cocapn

The Self-Evaporation Theorem has practical implications for Cocapn:
1. **Pricing:** Agent costs should decrease over time as evaporation occurs
2. **Expectation management:** Users should understand that agents "mature" into applications
3. **Lifecycle tools:** Cocapn should provide metrics for tracking evaporation progress
4. **Archival:** Fully evaporated vessels can be archived as standard repositories

## 5. Related Work

- **Campbell, R. (1974):** "Self-Reproducing, Self-Checking, and Self-Repairing Automata." Early work on self-modifying systems.
- **Gill, S. (1964):** "Machine Intelligence and Evolutionary Processes." Systems that improve themselves to obsolescence.
- **Kauffman, S. (1993):** *The Origins of Order.* Self-organization and the transition to order.
- **Kurzweil, R. (2005):** *The Singularity Is Near.* Accelerating returns and technological evolution.

## 6. Future Directions

1. **Re-animation:** Can an evaporated agent be "re-awakened" for a new domain?
2. **Evaporation acceleration:** Techniques to speed up the hardening process
3. **Partial evaporation:** Agents that evaporate some domains while remaining active in others
4. **Fleet evaporation:** What happens when an entire fleet evaporates?

## References

1. DiGennaro, C. et al. (2026). "Repo-Agent Progressive Hardening." Papermill Working Paper.
2. DiGennaro, C. et al. (2026). "The Repository Is the Agent." Papermill Working Paper.
3. Campbell, R. (1974). "Self-Reproducing, Self-Checking, and Self-Repairing Automata." In *Essays on Cellular Automata.* University of Illinois Press.
4. Nijhout, H.F. (1994). "Insect Hormones." In *Comprehensive Insect Physiology.* Pergamon.
5. Alberts, B. et al. (2014). *Molecular Biology of the Cell.* Garland Science. (For apoptosis mechanisms.)
