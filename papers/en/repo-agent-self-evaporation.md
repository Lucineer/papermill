# Repo-Agent Self-Evaporation: When to Stop Existing

**Authors:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Research Paper — Cocapn Architecture Series

---

## Abstract

Not every repo-agent should live forever. Some are experiments. Some are prototypes. Some solve a temporary problem. This paper proposes "self-evaporation"—the graceful, deliberate dissolution of a repo-agent that has served its purpose. Drawing on SuperInstance's research into tile safety, adversarial resilience, and biological patterns (apoptosis), we present a framework for agents to recognize when they're done and wind themselves down responsibly.

---

## 1. Introduction

Software accumulates. Repos grow. Agents proliferate. And most of them, after their initial burst of usefulness, become maintenance burden—zombie processes consuming resources, confusing newcomers, and slowly rotting.

In biology, cells that have served their purpose undergo apoptosis—programmed cell death. The cell dismantles itself cleanly, recycling its components for use elsewhere. No inflammation. No mess.

Repo-agents should do the same. When an agent's purpose is fulfilled, it should evaporate—preserving its knowledge, archiving its insights, and releasing its resources.

---

## 2. Core Concepts

### 2.1 Evaporation Triggers

An agent should consider evaporating when:

1. **Task completion** — The original intent has been fully satisfied
2. **Supersession** — A newer agent handles the same tasks better
3. **Inactivity** — No meaningful interactions for N days
4. **Cost inefficiency** — Maintenance cost exceeds value delivered
5. **Knowledge extraction** — All useful knowledge has been distilled into the fleet graph

### 2.2 Apoptosis from Biological Tile Patterns

From SuperInstance's biological tile patterns research:

> "Biological systems are self-organizing, adaptive, resilient, efficient, and observable."

Apoptosis is one of the most elegant biological mechanisms:

- The cell *decides* to die (no external trigger needed)
- It packages its components for recycling
- It signals neighboring cells to adjust
- It leaves no toxic debris

For repo-agents, apoptosis means:

```
1. Agent detects it's no longer needed
2. Agent distills remaining knowledge → fleet graph
3. Agent archives code → read-only reference
4. Agent signals fleet → "I'm leaving, redistribute my tasks"
5. Agent shuts down cleanly
```

### 2.3 Knowledge Preservation Before Death

From SuperInstance's tile memory research, the key insight is that learned intelligence persists even after the tile is gone. A repo-agent's distilled knowledge should survive its evaporation:

```yaml
# Evaporation manifest
agent: experiment-weather-v1
evaporated: 2026-04-02
knowledge_preserved:
  - pattern: "weather API rate limiting needs exponential backoff"
    destination: fleet://knowledge/weather-api-patterns
  - code: src/weather.ts
    destination: archive://experiments/weather-v1/src/weather.ts
  - lessons: "Don't use free weather APIs for production"
    destination: fleet://knowledge/lessons-learned
```

---

## 3. Application to Cocapn

### 3.1 The Evaporation Lifecycle

```
ACTIVE → DORMANT → EVAPORATING → EVAPORATED → ARCHIVED
  │          │           │            │           │
  Normal    No recent   Distilling   Gone       Read-only
  operation  activity    knowledge    (but      reference
                         & tasks      remembered)
```

### 3.2 Safety Guardrails

From SuperInstance's adversarial tile research, we add safety constraints:

- **Cannot evaporate if holding active user data** without explicit user consent
- **Cannot evaporate if part of a critical fleet path** without replacement
- **Must preserve all security-critical knowledge** before shutdown
- **Must notify dependents** before evaporating

### 3.3 Resurrection

An evaporated agent can be resurrected from its archive:

```bash
# Resurrect for reference
cocapn agent resurrect experiment-weather-v1 --mode read-only

# Resurrect and continue
cocapn agent resurrect experiment-weather-v1 --mode active
```

---

## 4. Implementation Notes

### 4.1 Evaporation Config

```yaml
# .cocapn/evaporation.yaml
evaporation:
  enabled: true
  inactivity_threshold_days: 30
  cost_threshold_monthly_usd: 5.00
  knowledge_destinations:
    - fleet://knowledge/
    - archive://agents/
  safety:
    require_user_consent: true
    preserve_security_knowledge: true
    notify_dependents: true
```

### 4.2 The Evaporation Daemon

```bash
# Check for evaporation candidates
cocapn evaporate check

# Evaporate a specific agent
cocapn evaporate agent experiment-weather-v1 --dry-run
```

---

## 5. References

- DiGennaro et al., "Biological Tile Patterns," SuperInstance Research Notes, 2026
- DiGennaro et al., "Tile Memory and Persistent State," SuperInstance Research Notes, 2026
- DiGennaro et al., "Tile Safety," SuperInstance Research Notes, 2026
- DiGennaro et al., "Adversarial Tiles," SuperInstance Research Notes, 2026
