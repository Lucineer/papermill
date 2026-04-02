# Seed Model Rapid Prototyping: The First Commit Sets Everything

**Authors:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Research Paper — Cocapn Architecture Series

---

## Abstract

The seed model—the initial prompt, scaffold, and configuration that spawns a repo-agent—determines the agent's entire developmental trajectory. This paper examines how seed model design influences agent capability, drawing on SuperInstance's research into automatic tile discovery, meta-tiles, and pluripotent differentiation. We propose a "seed algebra" for Cocapn that enables rapid prototyping of agents by composing minimal seed configurations that self-organize into capable systems.

---

## 1. Introduction

Every repo-agent begins as a seed. A few lines of intent, a model selection, a directory structure. From this acorn, an entire codebase grows.

SuperInstance's MetaTile research revealed that AI systems can be designed to discover their own optimal architecture through pluripotent differentiation—the system starts undifferentiated and specializes based on task demands. This is profoundly analogous to how a well-designed seed model should work: minimal specification, maximum emergence.

The question this paper addresses: **What makes a good seed?**

---

## 2. Core Concepts

### 2.1 The Seed Spectrum

Seeds range from prescriptive to emergent:

```
PRESCRIPTIVE                              EMERGENT
    │                                         │
    Full scaffold      Minimal scaffold    Pure intent
    50+ files           5 files           1 prompt
    Fixed arch         Flexible arch      Self-discovering
    Predictable        Adaptive          Surprising
```

Most production agents start prescriptive. Most breakthrough agents start emergent.

### 2.2 Meta-Tile Differentiation as Seed Design

From SuperInstance's automatic tile discovery research:

> "MetaTile uses Thompson Sampling for type selection, attractor dynamics for state transitions, information theory for exploration control, and Elastic Weight Consolidation for memory protection."

Translated to seed design:

- **Thompson Sampling** → The seed doesn't pick one architecture; it maintains a posterior over possible architectures
- **Attractor Dynamics** → The seed defines attractor states (capabilities) that the agent gravitates toward
- **Information Theory** → The seed explores when entropy is high, exploits when it's low
- **EWC** → The seed protects early-learned patterns from catastrophic forgetting during specialization

### 2.3 The Minimal Viable Seed

Drawing from SuperInstance's minimum viable tile concept:

```
cocapn-seed/
├── SOUL.md          # Agent identity (1-2 paragraphs)
├── intent.md        # What this agent should become (bullet list)
├── .cocapn/
│   └── config.yaml  # Model selection, constraints, hardening targets
└── README.md        # Human-readable description
```

Four files. That's it. The agent fills in everything else.

---

## 3. Application to Cocapn

### 3.1 Seed Templates

Cocapn provides seed templates for common agent archetypes:

- **MakerLog Agent** — Personal productivity, journaling, habit tracking
- **BusinessLog Agent** — Meeting simulation, strategy, market analysis
- **CookLog Agent** — Recipe generation, meal planning, food knowledge
- **Fleet Agent** — Multi-agent coordination, protocol adherence

Each template is a *starting point*, not a straitjacket.

### 3.2 Seed Mutation and Evolution

Seeds can mutate through:

- **Prompt evolution** — The agent refines its own SOUL.md based on experience
- **Architecture discovery** — New modules self-organize from task demands
- **Constraint accumulation** — Hardening events add structure over time

This is biological differentiation applied to software agents.

### 3.3 Cross-Pollination Between Seeds

When two repo-agents encounter similar problems, their solutions can cross-pollinate through the fleet protocol—adapted from SuperInstance's federated tile learning, where organizations share learned decision boundaries without sharing raw data.

---

## 4. Implementation Notes

### 4.1 Seed CLI

```bash
# Create from template
cocapn seed create --template makerlog --name my-journal

# Create from pure intent
cocapn seed create --intent "An agent that tracks fishing catches and predicts salmon runs"

# Fork and mutate
cocapn seed fork ./existing-agent --mutate-prompt --add-capability forecasting
```

### 4.2 Seed Evaluation

Before spawning, seeds are evaluated for:

- **Clarity of intent** — Can the agent understand what it should become?
- **Constraint adequacy** — Are guardrails sufficient without being restrictive?
- **Model fit** — Does the selected model match the task complexity?
- **Emergence potential** — Does the seed allow for surprise and discovery?

---

## 5. References

- DiGennaro et al., "Automatic Tile Discovery by AI," SuperInstance Research Notes, 2026
- DiGennaro et al., "LLM Deconstruction into SMP Tiles," SuperInstance Research Notes, 2026
- DiGennaro et al., "Biological Tile Patterns," SuperInstance Research Notes, 2026
- DiGennaro et al., "Federated Tile Learning," SuperInstance Research Notes, 2026
