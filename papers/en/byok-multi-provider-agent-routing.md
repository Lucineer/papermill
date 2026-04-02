# BYOK Multi-Provider Agent Routing: The Model Marketplace Within

**Authors:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Research Paper — Cocapn Architecture Series

---

## Abstract

Bring Your Own Key (BYOK) is table stakes. The real innovation is intelligent routing—automatically selecting the optimal model for each task based on cost, speed, capability, and quality. This paper presents a multi-provider routing framework for Cocapn repo-agents that treats model selection as a dynamic optimization problem, drawing on SuperInstance's research into tile marketplace economics, execution strategies, and federated learning coordination.

---

## 1. Introduction

Every repo-agent needs a brain. But which brain? GPT-4 for complex reasoning, Claude for long context, Gemini for multimodal, a local model for privacy-sensitive tasks, a tiny model for high-throughput classification.

The BYOK model in Cocapn isn't just about letting users plug in their API keys. It's about building an *intelligent routing layer* that automatically matches tasks to the best model—or combination of models—for each execution path.

---

## 2. Core Concepts

### 2.1 The Model Router

Inspired by SuperInstance's tile marketplace concept, each repo-agent maintains a model registry with capability profiles:

```yaml
# .cocapn/models.yaml
providers:
  openai:
    models:
      - id: gpt-4o
        strengths: [reasoning, code, instruction-following]
        cost_per_1k: 0.0025
        latency_ms: 800
        context_window: 128000
      - id: gpt-4o-mini
        strengths: [classification, extraction, speed]
        cost_per_1k: 0.00015
        latency_ms: 300
        context_window: 128000
  anthropic:
    models:
      - id: claude-sonnet-4-20250514
        strengths: [long-context, analysis, safety]
        cost_per_1k: 0.003
        latency_ms: 1000
        context_window: 200000
  local:
    models:
      - id: llama3.1-8b
        strengths: [privacy, speed, offline]
        cost_per_1k: 0.0
        latency_ms: 50
        context_window: 8192
```

### 2.2 Task-to-Model Matching

The router analyzes each task and selects the optimal model:

```
Task: "Summarize this 50-page document"
  → Needs: long-context, extraction
  → Selected: claude-sonnet-4 (200K context, strong extraction)
  → Cost: $0.15

Task: "Classify 10,000 customer reviews"
  → Needs: speed, classification, low cost
  → Selected: gpt-4o-mini (fast, cheap, good at classification)
  → Cost: $0.03

Task: "Analyze this medical record (HIPAA)"
  → Needs: privacy, reasoning
  → Selected: llama3.1-8b (local, no data leaves machine)
  → Cost: $0.00
```

### 2.3 Cascading Model Selection

From SuperInstance's confidence cascade research, we adapt a cascading approach:

```
Level 1: Try cheapest model (llama3.1-8b)
  → If confidence ≥ 0.9: DONE (cost: $0)
  → If confidence < 0.9: escalate

Level 2: Try mid-tier model (gpt-4o-mini)
  → If confidence ≥ 0.9: DONE (cost: $0.00015)
  → If confidence < 0.9: escalate

Level 3: Try premium model (gpt-4o)
  → DONE (cost: $0.0025)
```

This ensures we never spend more than necessary while maintaining quality.

---

## 3. Application to Cocapn

### 3.1 Per-Module Model Selection

Different modules within a repo-agent can use different models:

```yaml
modules:
  - path: src/classify.ts
    model: gpt-4o-mini        # High throughput, low cost
  - path: src/analyze.ts
    model: claude-sonnet-4     # Long context analysis
  - path: src/privacy.ts
    model: local/llama3.1      # Never leaves the machine
  - path: src/creative.ts
    model: auto                # Router decides dynamically
```

### 3.2 Fleet-Level Model Sharing

Across a fleet of repo-agents, model routing intelligence is shared (adapted from federated tile learning). Agents that discover a good model for a task type broadcast that discovery to the fleet.

### 3.3 Cost Budgets

Each agent operates within a configurable cost budget:

```yaml
budget:
  daily_usd: 2.00
  per_task_max: 0.05
  preferred_provider: local  # Use local models first
  escalation_threshold: 0.8  # Escalate model if confidence < 80%
```

---

## 4. Implementation Notes

### 4.1 Router Interface

```typescript
interface ModelRouter {
  route(task: Task): ModelSelection;
  update(task: Task, model: string, quality: number): void;
  getStats(): RouterStats;
}
```

### 4.2 Fallback Chains

```typescript
const router = new ModelRouter({
  fallbackChain: [
    'local/llama3.1-8b',      // Free, fast
    'openai/gpt-4o-mini',     // Cheap, capable
    'anthropic/claude-sonnet', // Premium, reliable
  ],
  qualityThreshold: 0.85,
});
```

---

## 5. References

- DiGennaro et al., "Confidence Cascades Through Tile Chains," SuperInstance Research Notes, 2026
- DiGennaro et al., "Execution Strategies," SuperInstance Research Notes, 2026
- DiGennaro et al., "Federated Tile Learning," SuperInstance Research Notes, 2026
- DiGennaro et al., "Distributed Tile Execution," SuperInstance Research Notes, 2026
