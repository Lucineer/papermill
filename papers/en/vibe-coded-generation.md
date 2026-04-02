# Vibe-Coded Generation: The Art of Getting It Mostly Right Fast

**Authors:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Research Paper — Cocapn Architecture Series

---

## Abstract

"Vibe coding" is the practice of generating software from conversational intent rather than formal specifications. This paper examines vibe coding as a legitimate and powerful development methodology for repo-agents—where the agent captures the human's intent through natural language, generates a working prototype, and iteratively refines it. Drawing on SuperInstance's research into tile composition languages (declarative over imperative), automatic tile discovery, and sloppy logic, we formalize the vibe-coding paradigm and show how it enables radical productivity for Cocapn agents.

---

## 1. Introduction

The best software starts as a feeling. "I want something that tracks my fishing trips and tells me when the salmon are running." No spec. No architecture document. No UML diagram. Just a vibe.

Traditional engineering says this is wrong. You need requirements. You need design. You need planning.

Vibe coding says: the agent can figure out the rest. Describe what you want. The agent generates it. You iterate. The result emerges from conversation, not specification.

This isn't lazy. It's how most software actually gets built—even by professionals who write specs nobody reads.

---

## 2. Core Concepts

### 2.1 Intent Capture

The first step of vibe coding is capturing the human's intent in natural language:

```
Human: "I want a fishing log that tracks what I caught, where, and when.
        It should predict good fishing days based on weather and tides."

Agent interprets:
  - Entity: Catch (species, weight, location, date)
  - Feature: Weather integration
  - Feature: Tide prediction
  - Feature: ML-based prediction
  - Vibe: Alaskan fisherman, practical, outdoorsy
```

### 2.2 Declarative Composition

From SuperInstance's tile composition language research:

> "You describe WHAT you want, not HOW to wire it together. The compiler handles composition algebra, type checking, constraint propagation, and optimization."

Vibe coding is the ultimate declarative system. The human describes the WHAT. The agent discovers the HOW.

```yaml
# What the human said (intent)
intent: |
  A fishing log that tracks catches, predicts good days,
  and works offline on my boat where there's no internet.

# What the agent generates (composition)
modules:
  - catch-log (CRUD, SQLite, offline-first)
  - weather-fetch (API, cached, graceful degradation)
  - tide-predict (ephemeris calculation, no API needed)
  - prediction-engine (regression on historical data)
```

### 2.3 Automatic Architecture Discovery

From SuperInstance's automatic tile discovery research:

> "MetaTile uses Thompson Sampling for type selection, attractor dynamics for state transitions, information theory for exploration control."

The agent doesn't pick one architecture—it explores multiple and converges:

```
Iteration 1: Monolithic single file (vibe: "just make it work")
Iteration 2: Split into modules (vibe: "this is getting complex")
Iteration 3: Add tests and types (vibe: "I need this to be reliable")
Iteration 4: Add offline sync (vibe: "it broke on my boat")
Iteration 5: Add ML prediction (vibe: "can it predict salmon runs?")
```

Each iteration is driven by the human's feedback—their vibe.

---

## 3. Application to Cocapn

### 3.1 Vibe-to-Seed Pipeline

```bash
# Capture intent
cocapn vibe "I want a cooking assistant that learns my preferences
             and suggests meals based on what's in my fridge"

# Agent generates seed
cocapn seed create --from-vibe --name my-chef

# Iterate
cocapn vibe add "also track macros and suggest alternatives for allergies"
```

### 3.2 The Iteration Loop

Vibe coding is inherently iterative:

```
1. Human expresses intent → Agent generates
2. Human tests → Human provides feedback (natural language)
3. Agent refines → Human tests again
4. Repeat until vibe matches reality
```

### 3.3 Vibe Preservation

The original intent is preserved throughout the development process:

```yaml
# .cocapn/vibe.yaml
original_intent: |
  A fishing log that tracks what I caught, where, and when.
  It should predict good fishing days based on weather and tides.
current_vibe: |
  Same as original, but now also tracks bait used and moon phase.
  Works offline. Syncs when back on WiFi.
vibe_drift: low  # Still close to original intent
```

---

## 4. Implementation Notes

### 4.1 Vibe Parser

The vibe parser extracts structured requirements from natural language:

```typescript
interface Vibe {
  entities: string[];
  features: string[];
  constraints: string[];
  personality: string;  // "practical", "playful", "minimal"
  non_negotiables: string[];
}
```

### 4.2 Vibe Testing

Instead of unit tests, vibe tests verify the implementation matches the intent:

```bash
cocapn vibe test --intent "tracks fishing catches"
# → PASS: Catch logging works
# → PASS: Species tracking works
# → FAIL: Location tracking doesn't work offline
```

---

## 5. References

- DiGennaro et al., "Tile Composition Language (TCL)," SuperInstance Research Notes, 2026
- DiGennaro et al., "Automatic Tile Discovery by AI," SuperInstance Research Notes, 2026
- DiGennaro et al., "Sloppy Logic and Approximate Execution," Cocapn Paper, 2026
- DiGennaro et al., "Self-Supervised Tile Learning (SSTL)," SuperInstance Research Notes, 2026
