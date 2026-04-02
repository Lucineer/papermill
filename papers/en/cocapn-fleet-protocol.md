# The Cocapn Fleet Protocol: How Repo-Ants Coordinate

**Authors:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Research Paper — Cocapn Architecture Series

---

## Abstract

A single repo-agent is useful. A fleet of repo-agents is formidable. This paper specifies the Cocapn Fleet Protocol—a lightweight coordination layer that enables repo-agents to discover each other, negotiate tasks, share knowledge, and self-organize without central control. Drawing extensively on SuperInstance's research into emergent swarm intelligence, stigmergy, distributed tile execution, and federated tile learning.

---

## 1. Introduction

Ants don't have managers. No ant tells another ant what to do. Yet colonies build structures, optimize foraging, and respond to threats with sophistication that dwarfs most human organizations.

SuperInstance's swarm intelligence research demonstrated that this same principle applies to AI tiles: simple agents, communicating through environment modification (stigmergy), produce emergent collective intelligence far exceeding individual capability.

The Cocapn Fleet Protocol brings stigmergy to repo-agents. Agents coordinate by modifying a shared substrate—the fleet knowledge graph—rather than through direct message passing. The result is a self-organizing system that scales gracefully and degrades gracefully.

---

## 2. Core Concepts

### 2.1 Stigmergic Coordination

From SuperInstance's emergent swarm research:

> "Tiles deposit 'digital pheromones' into cells. Other tiles detect these traces. Complex problem-solving emerges from simple rules."

In Cocapn, repo-agents deposit signals into the fleet graph:

```yaml
signal:
  agent: fishing-log-001
  type: capability_offer
  capability: weather-correlation
  strength: 0.9  # "pheromone intensity"
  ttl: 3600      # expires in 1 hour
```

Other agents detect these signals and respond—without ever directly messaging the original agent.

### 2.2 The Three Coordination Primitives

1. **Broadcast** — Agent deposits a signal to the fleet graph
2. **Detect** — Agent reads signals relevant to its current task
3. **Adapt** — Agent modifies its behavior based on detected signals

No request-response. No RPC. No orchestration. Pure stigmergy.

### 2.3 Distributed Execution Topology

From SuperInstance's distributed tile execution research:

> "Tiles live wherever they need to be. Your laptop, AWS GPU, edge device—they all work together."

The fleet protocol is topology-agnostic:

```
Agent A (laptop) ──→ Fleet Graph ←── Agent B (VPS)
                            ↑
Agent C (edge device) ─────┘
```

Agents communicate through the graph, not directly. The graph can be local (SQLite), shared (Postgres), or federated (multiple instances syncing).

---

## 3. Application to Cocapn

### 3.1 Agent Discovery

When a new repo-agent joins the fleet:

```bash
# Agent announces itself
cocapn fleet announce --capabilities [fishing, weather, mapping]

# Other agents detect
# fishing-log-002 sees: "Oh, there's a weather expert now"
# fishing-log-002 adapts: "I'll delegate weather queries to fishing-log-001"
```

### 3.2 Task Negotiation

When a task arrives that no single agent can handle:

```
Task: "Plan a fishing trip considering weather, tides, and species migration"
    │
    ├── fishing-log-001 deposits: "I can handle weather"
    ├── tide-tracker deposits: "I can handle tides"
    ├── species-db deposits: "I can handle migration"
    │
    └── Planner agent detects all three → composes solution
```

### 3.3 Fleet Knowledge Sharing

From federated tile learning, agents share learned patterns:

```yaml
shared_knowledge:
  - source: fishing-log-001
    pattern: "salmon runs correlate with water temperature > 52°F"
    confidence: 0.82
    validated_by: [fishing-log-002, fishing-log-003]
```

---

## 4. Implementation Notes

### 4.1 Protocol Specification

```typescript
interface FleetSignal {
  agentId: string;
  type: 'capability_offer' | 'task_broadcast' | 'knowledge_share' | 'status_update';
  payload: any;
  strength: number;  // 0-1, pheromone intensity
  ttl: number;       // seconds until expiration
  scope: 'local' | 'fleet' | 'global';
}
```

### 4.2 Conflict Resolution

When multiple agents offer the same capability:

```
fishing-log-001: weather-correlation (strength: 0.9, cost: $0.01/query)
fishing-log-002: weather-correlation (strength: 0.7, cost: $0.00/query)

Resolution: Use 001 for high-stakes queries, 002 for bulk queries
```

### 4.3 Graceful Degradation

If the fleet graph goes down:

- Agents fall back to local knowledge
- Direct A2A communication (degraded mode)
- Queue signals for later sync

---

## 5. References

- DiGennaro et al., "Emergent Swarm Intelligence from SMP Tiles," SuperInstance Research Notes, 2026
- DiGennaro et al., "Distributed Tile Execution," SuperInstance Research Notes, 2026
- DiGennaro et al., "Federated Tile Learning," SuperInstance Research Notes, 2026
- DiGennaro et al., "Tile Memory and Persistent State," SuperInstance Research Notes, 2026
