# The Cocapn Fleet A2A Protocol: Agent-to-Agent Coordination Without Central Control

**Author:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Draft
**Keywords:** A2A protocol, fleet coordination, agent-to-agent, distributed consensus, Cocapn, inter-vessel communication, event-driven architecture

## Abstract

We present the Cocapn Fleet Agent-to-Agent (A2A) Protocol, a lightweight communication protocol for autonomous repo-agent vessels that coordinates fleet behavior without central control. The protocol defines message types, routing rules, consensus mechanisms, and state synchronization strategies optimized for sparse, asynchronous communication between cognitive agents. We prove that the protocol achieves eventual consistency under realistic network conditions.

## 1. Introduction

Centralized orchestration of AI agents creates a single point of failure and a bottleneck. Fully decentralized systems lack coordination. The Cocapn A2A Protocol occupies a middle ground: vessels communicate directly, maintain independent state, and achieve coordination through a lightweight protocol that assumes unreliable, asynchronous networks.

This is not a distributed computing protocol (like Raft or Paxos). It is a *social coordination* protocol — vessels coordinate like colleagues, not like database replicas.

## 2. Core Concepts

### 2.1 Message Types

**Definition 2.1 (A2A Message).** An A2A message is a tuple:

msg = (id, from, to, type, payload, timestamp, ttl, priority)

where type ∈ {request, response, notification, handoff, state_sync, heartbeat, discovery}

| Type | Purpose | Response Expected |
|---|---|---|
| request | Ask another vessel to perform a task | Yes |
| response | Reply to a request | No |
| notification | Inform without expecting action | No |
| handoff | Transfer conversation/task ownership | Yes (acknowledgment) |
| state_sync | Share state updates | Optional |
| heartbeat | Liveness check | Optional |
| discovery | Find vessels with specific capabilities | Yes (capability advertisement) |

### 2.2 Routing

**Definition 2.2 (Routing Function).** Given a message msg, the routing function ρ(msg) determines delivery:

1. **Direct:** msg.to is a known vessel ID → deliver directly
2. **Role-based:** msg.to is a role name → deliver to any vessel advertising that role
3. **Capability-based:** msg.to is a capability → deliver to any vessel with that capability (via discovery)
4. **Broadcast:** msg.to = * → deliver to all known vessels
5. **Dead letter:** No route found → queue for retry, notify sender after max_retries

### 2.3 Consensus Without Centralization

Vessels do not elect leaders. Instead, they use *social consensus* — a set of conventions that emerge from repeated interaction:

**Convention 2.1 (First-Responder).** For tasks where any vessel can help, the first vessel to respond claims the task. Others defer.

**Convention 2.2 (Domain Authority).** For tasks in a specific domain, the vessel with the most accumulated context (largest git history, most domain-specific code) has authority.

**Convention 2.3 (User Preference).** For tasks involving a specific user, the vessel with the most user context (USER.md, memory files) has authority.

**Theorem 2.1 (Eventual Consistency).** Under the A2A protocol, fleet state achieves eventual consistency provided:
1. The network is eventually connected (any vessel can reach any other within finite time)
2. Messages have finite TTL (no indefinite buffering)
3. State sync messages are sent periodically
4. Conflict resolution follows Convention 2.1-2.3

*Proof.* (Sketch) By condition 1, all vessels eventually receive all messages. By condition 2, stale messages expire. By condition 3, state converges. By condition 4, conflicts are resolved deterministically. Therefore, all vessels converge to the same state. □

### 2.4 State Synchronization

Vessels maintain independent state but share *summaries*:

```
STATE_SYNC payload = {
  vessel_id,
  capabilities: [list of advertised capabilities],
  current_tasks: [list of active task IDs],
  knowledge_summary: "brief description of accumulated domain knowledge",
  last_active: timestamp,
  load: current_task_count / max_capacity
}
```

These summaries are sent periodically and on significant state changes. They are not authoritative — they are *hints* that help vessels make routing decisions.

### 2.5 Failure Handling

The A2A protocol assumes vessels fail silently (crash, network partition, resource exhaustion). Handling:

1. **Request timeout:** After t_request timeout, requester retries with exponential backoff up to max_retries, then escalates to user
2. **Heartbeat failure:** If heartbeat is not received for t_dead interval, vessel is marked as possibly-down
3. **Task recovery:** On vessel restart, it reads its persistent state (git) and resumes incomplete tasks
4. **Message deduplication:** Message IDs are UUIDs; duplicate messages are detected and ignored

## 3. Implementation

### 3.1 Transport Layer

A2A messages are transported over:
- Local fleet: Direct HTTP/WebSocket between vessels on the same host
- Remote fleet: Encrypted tunnels (WireGuard, Tailscale) between hosts
- Cross-platform: Via OpenClaw gateway as message broker

### 3.2 Message Persistence

All A2A messages are logged to git in each vessel's communication log. This provides:
- Audit trail of all inter-vessel communication
- Recovery after vessel restart
- Debugging and analysis

### 3.3 Discovery Protocol

New vessels announce themselves through a discovery message. Existing vessels respond with capability advertisements. This bootstraps the fleet routing table without central configuration.

## 4. Applications to Cocapn

The A2A protocol is the nervous system of Cocapn fleets. It enables:
- A research vessel to request analysis from a specialized vessel
- A concierge vessel to hand off a conversation to a domain expert
- A sentinel vessel to notify all vessels of a system event
- Fleet-wide task distribution and load balancing

## 5. Related Work

- **Actor Model (Hewitt, 1973):** Independent actors communicating through messages. A2A is an actor model specialized for cognitive agents.
- **FIPA ACL (1997):** Agent Communication Language for multi-agent systems. A2A is simpler and more pragmatic.
- **AMQP Protocol (2003):** Advanced Message Queuing Protocol. A2A shares AMQP's reliability goals but uses natural language payloads.
- **gRPC (2016):** High-performance RPC framework. A2A uses gRPC-like patterns but with agent-specific semantics.

## 6. Future Directions

1. **Fleet intelligence:** Can fleets develop emergent behaviors through A2A interaction patterns?
2. **Trust-based routing:** Vessels learn which other vessels are reliable and prefer them
3. **Economic protocols:** Token-based incentives for vessels that help other vessels
4. **Federation:** A2A between fleets owned by different users

## References

1. Hewitt, C., Bishop, P. & Steiger, R. (1973). "A Universal Modular ACTOR Formalism for Artificial Intelligence." *IJCAI.*
2. FIPA (1997). "FIPA 97 Specification: Agent Communication Language." Foundation for Intelligent Physical Agents.
3. OASIS (2012). "AMQP Version 1.0." OASIS Standard.
4. Google (2016). "gRPC: A high performance, open-source universal RPC framework."
5. DiGennaro, C. et al. (2026). "Cocapn Fleet Protocol." Papermill Working Paper.
