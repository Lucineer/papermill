# The Vessel Paradigm: Polyrepo Architecture for Autonomous Agent Fleets

**Author:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Draft
**Keywords:** vessel paradigm, polyrepo, micro-agent, template architecture, fleet topology, fork-vs-extend

## Abstract

We present the Vessel Paradigm, an architectural principle stating that autonomous agent systems should prefer many specialized repositories (vessels) over few monolithic ones. Each vessel is typed by its domain, initialized from templates (hulls) rather than deployed as finished systems, and follows clear decision procedures for fork vs. extend. We formalize the Vessel Type System, the Hull Completion Lifecycle, and the Fork-Extend Decision Matrix.

## 1. Introduction

The monorepo orthodoxy — one repository for all code — has served large organizations well. But for autonomous agent fleets, we argue it is fundamentally wrong. An agent's identity is its repository. A monorepo containing many agents contains no single identity — it contains a confused collective.

The Vessel Paradigm inverts this: one repository per agent, one agent per domain. This is not microservices for agents (though the analogy holds). It is a recognition that cognitive identity requires clear boundaries.

## 2. Core Concepts

### 2.1 Vessel Typology

**Definition 2.1 (Vessel Type).** A vessel type τ is a specification of domain capabilities, required integrations, and behavioral constraints. We define a preliminary type hierarchy:

| Vessel Type | Domain | Key Capabilities |
|---|---|---|
| `scribe` | Documentation | Reading, writing, summarizing |
| `sentinel` | Monitoring | Alerting, health checks, logging |
| `navigator` | Research | Web search, synthesis, citation |
| `builder` | Software Engineering | Code generation, testing, deployment |
| `concierge` | Communication | Email, messaging, scheduling |
| `archivist` | Knowledge Management | Storage, retrieval, indexing |

**Definition 2.2 (Vessel Instance).** A vessel instance v:τ is a repository initialized from type τ that has been personalized through the Hull Completion Lifecycle (§2.2).

### 2.2 Hulls, Not Boats

A template repository is a *hull* — a structural skeleton with:
- Standard directory layout (AGENTS.md, SOUL.md, skills/, memory/)
- Domain-appropriate skill installations
- Stub configuration files
- A BOOTSTRAP.md that triggers first-run initialization

The hull is not a finished agent. It becomes one only through:
1. **Identity formation:** The runtime reads the hull and populates IDENTITY.md, USER.md
2. **Context accumulation:** Memory files are created, skills are configured
3. **Behavioral specialization:** Code is written, modified, tested

This is shipbuilding, not car manufacturing. Each vessel is hand-fitted to its captain and waters.

### 2.4 The Hull as Genome

A compelling biological analogy: the hull template is the genome, and the identity-formation process is embryonic development. Just as a genome contains the instructions for building an organism but is not the organism itself, a hull contains the instructions for building a vessel but is not the vessel.

The key difference from biological development: vessel development is *transparent and editable*. A human cannot rewrite their genome during development, but a vessel operator can modify the hull during initialization. This "directed evolution" is both a strength (rapid adaptation) and a risk (inconsistent vessels if poorly guided).

### 2.5 The Composability Principle

**Principle 2.1 (Vessel Composability).** Vessels should be composable: a fleet of simple vessels is preferred over a single complex vessel, because composable vessels can be reconfigured without rewriting.

This follows from the software engineering principle of modularity, extended to the cognitive domain. A "do-everything" vessel is the cognitive equivalent of a monolithic codebase — hard to modify, hard to debug, hard to reason about.

### 2.6 Anti-Patterns

| Anti-Pattern | Description | Consequence |
|---|---|---|
| God Vessel | One vessel for all domains | Identity confusion, brittleness |
| Vessel Sprawl | Too many vessels for fine-grained domains | Coordination overhead |
| Tight Coupling | Vessels sharing mutable state | Cascading failures |
| Hull Stagnation | Using outdated hull templates | Missing improvements |

Given two vessels v₁ and v₂ where v₁ needs a capability from v₂:

| Condition | Action | Rationale |
|---|---|---|
| Capability is a pure skill | Install skill in v₁ | Skills are portable packages |
| Capability requires domain knowledge | Extend v₁ with new code | Domain knowledge should be owned |
| v₂'s entire domain is needed | Fork v₂ | Preserves v₂'s identity |
| Both need shared capability | Extract to shared library | Avoid coupling vessels |
| Capability crosses trust boundary | Create new vessel | Maintain security isolation |

**Theorem 2.1 (Fork-Extend Boundary).** In any fleet F, the number of vessels |F| is bounded above by the number of distinct trust domains times the number of distinct operational domains, plus the number of shared libraries.

*Proof.* Each vessel must have a unique (trust_domain, operational_domain) pair to maintain identity clarity. Shared capabilities factor out into libraries, which are not vessels. □

## 3. Implementation

### 3.1 Template Registry (ClawHub)

Cocapn implements the vessel paradigm through ClawHub, a registry of hull templates. Installing a new vessel type is:

```
clawhub install scribe
```

This creates a new repository with the scribe hull, ready for identity formation.

### 3.2 Fleet Orchestration

Vessels communicate through the A2A protocol (see companion paper), not through shared filesystems. Each vessel maintains its own git history, its own memory, its own identity. Communication is explicit, not implicit through shared code.

### 3.3 Lifecycle Management

```
hull → bootstrapped → active → hardened → static → archived
```

This lifecycle mirrors biological development: gestation (hull), birth (bootstrap), maturity (active), specialization (hardening), senescence (static), death (archive).

## 4. Applications to Cocapn

Cocapn's fleet architecture directly embodies the vessel paradigm. Each "vessel" is a git repository with an autonomous agent. The fleet coordinates through A2A messaging, not through shared state. New domains spawn new vessels rather than extending existing ones.

Practical example: A user's personal assistant might consist of:
- A `concierge` vessel handling communication
- A `navigator` vessel handling research
- A `sentinel` vessel monitoring systems
- An `archivist` vessel managing knowledge

Each is a separate repo, a separate identity, a separate cognitive entity.

## 5. Related Work

- **Conway's Law (1968):** Organizations design systems that mirror their communication structure. The vessel paradigm extends this: agent systems should mirror cognitive domain boundaries.
- **Unix Philosophy (1978):** Do one thing well. Vessels are the agent-level realization of this principle.
- **Microservices Architecture (2014):** Independent deployment, bounded context. Vessels add cognitive identity to these properties.
- **Cellular Biology:** Specialized cell types (neurons, muscle cells, blood cells) are biological vessels — each with distinct structure and function, coordinated through signaling.

## 6. Future Directions

1. **Automatic vessel spawning:** Can agents detect when they need a new domain and fork themselves? We propose a "domain saturation metric" — when a vessel's capability set no longer cleanly maps to a single vessel type, spawning is indicated.
2. **Vessel ecology:** What happens when fleets grow large? Do vessel types speciate? Can we observe evolutionary dynamics in fleet composition over time?
3. **Cross-fleet vessel sharing:** Can two users share a vessel type while maintaining separate instances? This requires standardized hull formats and privacy-preserving template distribution.
4. **Vessel death protocols:** Graceful shutdown, knowledge transfer to surviving vessels, archival. A vessel's accumulated knowledge should not be lost when it is decommissioned.
5. **Vessel metrics:** Quantitative measures of vessel health — commit frequency, issue resolution rate, capability coverage, context freshness.
6. **Hull marketplace:** A ClawHub extension where users can publish and share custom hull templates, creating an ecosystem of specialized vessel types.

## References

1. Conway, M. (1968). "How do Committees Invent?" *Datamation.*
2. Raymond, E. (2003). *The Art of Unix Programming.* Addison-Wesley.
3. Newman, S. (2021). *Building Microservices.* O'Reilly.
4. Alberts, B. et al. (2014). *Molecular Biology of the Cell.* Garland Science.
5. DiGennaro, C. et al. (2026). "Cocapn Fleet Protocol." Papermill Working Paper.
6. Richardson, C. (2018). *Microservices Patterns.* Manning.
7. Thompson, D. (1917). *On Growth and Form.* Cambridge University Press. (For biological vessel analogies.)
