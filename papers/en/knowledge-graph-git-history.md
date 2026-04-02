# Knowledge Graph from Git History: The Repository Remembers

**Authors:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Research Paper — Cocapn Architecture Series

---

## Abstract

Every git commit is a knowledge event. The repository's history encodes decisions, relationships, dependencies, and evolution patterns that no individual developer fully grasps. This paper proposes constructing a knowledge graph from git history to give repo-agents persistent, queryable memory of their own evolution—drawing on SuperInstance's research into tile memory, tile versioning, and stigmergic coordination.

---

## 1. Introduction

A repo-agent's codebase is its brain. But its *git history* is its subconscious—recording every thought, every revision, every dead end. Most of this knowledge is buried in commit messages, diffs, and branch structures, inaccessible to the agent itself.

SuperInstance's tile memory research demonstrated that persistent state across executions enables cumulative learning. We extend this: a repo-agent's git history IS its persistent state. By mining this history into a knowledge graph, the agent gains self-awareness of its own evolution.

---

## 2. Core Concepts

### 2.1 The Commit Graph as Knowledge

Every commit is a node. Every relationship between commits is an edge. The result is a rich graph:

```
[commit abc123: "add auth module"]
    │
    ├── modifies: src/auth.ts
    ├── references: #42 (issue)
    ├── breaks: tests/auth.test.ts
    ├── inspired_by: commit def456
    ├── reverted_by: commit ghi789
    └── hardens: confidence(auth) from 0.6 → 0.85
```

### 2.2 Stigmergic Memory

From SuperInstance's emergent swarm intelligence research:

> "SMP tiles enable stigmergic coordination—communication through environment modification—just like ant colonies."

Git commits are stigmergic signals. Each commit modifies the environment (the codebase), and future agents read those modifications to understand context. No explicit communication needed—the code IS the message.

### 2.3 Temporal Knowledge Patterns

The knowledge graph captures temporal patterns:

- **Decision trajectories** — How did we get here? What alternatives were considered?
- **Failure archaeology** — What was tried and abandoned? Why?
- **Expertise emergence** — Which files have been touched most? By whom (which agent)?
- **Coupling discovery** — Which files always change together?

---

## 3. Application to Cocapn

### 3.1 Agent Self-Awareness

When a repo-agent needs to understand its own codebase:

```bash
# "What do I know about authentication?"
cocapn graph query "auth" --depth 3

# "What was I thinking when I added this?"
cocapn graph trace src/auth.ts --show-reasoning

# "What has broken before when I touched this file?"
cocapn graph blast-radius src/api/router.ts
```

### 3.2 Cross-Agent Knowledge Transfer

When agent A solves a problem that agent B encounters later:

```
Agent A commits: "fix race condition in concurrent cache"
    │
    ├── knowledge graph extracts: pattern=concurrent-bug
    ├── solution: mutex + backoff retry
    └── broadcasts to fleet

Agent B encounters similar bug
    │
    ├── fleet knowledge graph matches: concurrent-bug pattern
    └── Agent B applies known fix
```

### 3.3 Version Intelligence

From SuperInstance's tile versioning research:

> "We're not just versioning code—we're versioning intelligence itself."

The knowledge graph captures not just what changed, but *what was learned*:

```yaml
commit: abc123
learned:
  - pattern: "retry with exponential backoff works better than linear"
  - confidence_delta: +0.15 for src/cache.ts
  - anti_pattern: "shared mutable state in async context"
```

---

## 4. Implementation Notes

### 4.1 Graph Construction Pipeline

```bash
# Extract knowledge from git history
cocapn graph build --from HEAD~1000 --to HEAD

# Update incrementally
cocapn graph update --since HEAD~10
```

### 4.2 Query Language

```cypher
// Find all files related to a concept
MATCH (c:Commit)-[:MODIFIES]->(f:File)
WHERE c.message CONTAINS 'auth'
RETURN f.path, c.date, c.message

// Find blast radius
MATCH (f1:File {path: 'src/api.ts'})-[:CO_CHANGES*1..2]-(f2:File)
RETURN f2.path, count(*) as coupling_strength
ORDER BY coupling_strength DESC
```

### 4.3 Storage

- **SQLite with JSON extensions** for small repos
- **Neo4j** for fleet-scale knowledge graphs
- **Embeddings** for semantic search over commit messages

---

## 5. References

- DiGennaro et al., "Tile Memory and Persistent State," SuperInstance Research Notes, 2026
- DiGennaro et al., "Tile Version Control and Evolution," SuperInstance Research Notes, 2026
- DiGennaro et al., "Emergent Swarm Intelligence from SMP Tiles," SuperInstance Research Notes, 2026
- DiGennaro et al., "Stigmergy," (covered in Emergent Swarm), SuperInstance Research Notes, 2026
