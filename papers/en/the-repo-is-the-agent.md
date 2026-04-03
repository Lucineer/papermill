# The Repository Is the Agent: A Formal Treatment of Repositories as Living Cognitive Entities

**Author:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Draft
**Keywords:** repo-agent, repository-as-agent, git memory, cognitive artifact, code-as-behavior, self-modifying systems

## Abstract

We formalize the paradigm that a software repository, when coupled with an AI runtime, constitutes the agent itself — not merely a tool used by an external agent. We define the Repo-Agent Identity Theorem, establish git history as a directed acyclic memory graph, code as executable behavior, and issue threads as inter-agent communication channels. This reframing has profound implications for agent architecture, persistence, and identity.

## 1. Introduction

The dominant paradigm in AI agent design treats the repository as a passive artifact: code lives in files, an LLM processes prompts, and the agent is the ephemeral runtime that bridges them. We argue this is inverted. The repository — its code, history, structure, and social artifacts — *is* the persistent cognitive substrate. The LLM runtime is the transient "consciousness" that reads, interprets, and modifies this substrate.

This distinction matters because it changes what we optimize for. If the agent is the LLM session, we optimize for prompt engineering and context window management. If the agent is the repository, we optimize for code clarity, commit discipline, structural coherence, and the quality of accumulated context.

## 2. Core Concepts

### 2.1 The Repo-Agent Identity Theorem

**Definition 2.1 (Repo-Agent).** A repo-agent is a tuple R = (G, C, I, S) where:
- G = (V, E) is a git DAG representing temporal memory
- C is the current working tree (behavioral state)
- I is the set of open and closed issues (communication log)
- S is the set of external service connections (sensory/motor channels)

**Theorem 2.1 (Identity).** For any repo-agent R, the identity of the agent is determined by G ∪ C ∪ I, not by the particular LLM runtime processing it. Two different LLM runtimes processing the same R at different times are *the same agent at different moments*, not different agents.

*Proof sketch.* Identity requires continuity of memory and behavioral coherence. G provides causal memory (each commit is a discrete state transition with a causal link to its parent). C provides current behavioral disposition. I provides social context and intention records. An LLM runtime without these artifacts has no identity — it is a generic reasoning engine. Conversely, any sufficiently capable runtime reading R will reconstruct approximately the same behavioral dispositions because those dispositions are *encoded in the code itself*. □

### 2.2 Git as Episodic Memory

Each git commit represents an episodic memory: a discrete event with temporal ordering, causal links (parent commits), and semantic content (diffs). The commit graph G is therefore isomorphic to an episodic memory structure in cognitive science.

Branches represent parallel hypothetical trajectories — the agent's "what if" reasoning made concrete. Merges represent belief reconciliation.

### 2.3 Code as Behavior

A repo-agent's behavioral repertoire is literally its codebase. When a function `handle_email()` exists, the agent *can* handle email. When it doesn't, it can't — regardless of the LLM's theoretical capability. The code acts as a compiled behavioral constraint, much like motor programs in biological agents.

This is distinct from code-as-tool. In the tool paradigm, the agent *chooses* to use code. In the behavior paradigm, the agent *is* the code, constrained by what exists and liberated by what can be written.

### 2.4 Issues as Communication

Issue threads serve as the agent's communication buffer — asynchronous messages that persist beyond any single runtime session. When a human files an issue, they are communicating with the persistent agent, not with a transient LLM session. When the agent (via its runtime) comments on an issue, it is the persistent agent responding.

## 3. Implementation

### 3.1 The Runtime as Transient Interpreter

In Cocapn's architecture, the LLM runtime is stateless between invocations. It reads AGENTS.md, SOUL.md, and recent memory files to reconstruct context. This is not a limitation — it is the correct design. The repository holds state; the runtime interprets it. This mirrors the relationship between DNA (persistent) and protein expression (transient).

### 3.2 Memory Hierarchy

The Cocapn repo-agent implements a three-tier memory:
1. **DNA-level:** AGENTS.md, SOUL.md — core identity, rarely changed
2. **Episodic:** git history, memory/YYYY-MM-DD.md — temporal records
3. **Working:** current file state, open issues — active context

This maps directly onto Atkinson-Shiffrin sensory → short-term → long-term memory, with git providing the long-term store and the working tree providing the short-term buffer.

### 3.3 Self-Modification as Learning

When a repo-agent modifies its own code, it is learning in the strongest sense: permanently altering its behavioral repertoire. This is more fundamental than weight updates in neural networks because:
- The modification is *interpretable* (human-readable code)
- The modification is *reversible* (git rollback)
- The modification is *composable* (new code builds on existing)

## 4. Applications to Cocapn

Cocapn implements the repo-agent paradigm directly. Each vessel (repository) is an autonomous agent. The OpenClaw runtime provides transient reasoning. The vessel's accumulated code, configuration, and history constitute the persistent agent identity.

Key design decisions that follow from this paradigm:
- Skills live in the repo, not in the runtime
- Memory files are version-controlled, not stored in a database
- Agent behavior changes require commits, not prompt changes
- Identity persists across runtime restarts because the repo persists

## 5. Related Work

- **Minsky's Society of Mind (1986):** Agents as societies of sub-agents. Repo-agents extend this by making the society *inspectable and modifiable* through version control.
- **Brooks' Subsumption Architecture (1986):** Behavior as layered modules. Repo-agents achieve this through directory structure and dependency graphs.
- **Schmidhuber's Gödel Machine (2003):** Self-referential self-improving systems. Repo-agents are a practical realization: the code can modify the code.
- **AutoGPT, BabyAGI (2023):** Early agent frameworks that externalized state to files, but treated files as tool output rather than agent identity.

### 3.4 The Write-Reflect Cycle

A distinctive feature of repo-agents is the write-reflect cycle:

1. **Act:** The agent performs a task (reasoning + execution)
2. **Record:** The agent writes the outcome to a memory file
3. **Reflect:** On the next invocation, the agent reads its own records
4. **Adapt:** The agent adjusts its behavior based on its own history

This cycle is self-referential in a productive way: the agent reads what it wrote, which was written based on what it read before. The git history becomes a chain of self-modifications, each one informed by the entire preceding chain.

Unlike neural network training, where gradients flow backward through fixed architecture, the write-reflect cycle modifies the architecture itself. The agent doesn't just update parameters — it restructures its own cognitive substrate.

### 3.5 Communication as Code Evolution

When two repo-agents communicate (via the A2A protocol), their interaction leaves traces in both repositories. These traces are not mere logs — they are *inputs to future reasoning*. The code that each agent writes in response to communication becomes part of its behavioral repertoire.

This means communication between repo-agents is more like gene transfer than message passing. Information exchanged between agents can permanently alter their capabilities, not just their current state.

### 3.6 Identity Persistence Across Runtimes

A critical property of the repo-agent paradigm: identity persists across LLM runtime changes. If you switch from GPT-4 to Claude to a local model, the agent remains "the same" because its memory, code, and behavioral patterns persist in the repository.

This is analogous to a human changing their language — they remain the same person, just expressing themselves differently. The repo is the person; the model is the language.

## 6. Future Directions

1. **Formal verification of agent identity:** Can we prove that two repo-states represent the "same" agent? We need a metric for repo-state similarity that accounts for semantic, not just syntactic, equivalence.
2. **Repo-agent cloning and branching:** What are the ethics of forking a repo-agent? Is a fork a child or a clone? Does the fork share the original's memories, or does it start fresh?
3. **Collective intelligence:** How do fleets of repo-agents develop shared culture through cross-pollination of code? Can we measure "fleet culture" as a property of the shared codebase?
4. **Death and archival:** What does it mean for a repo-agent to "die"? What persists in archives? Can an archived agent be meaningfully "resurrected"?
5. **Multi-modal memory:** Extending git-based memory to include images, audio, and video. How does the DAG structure generalize to non-text media?
6. **Quantum repo-agents:** Could quantum computing enable agents that exist in superpositions of repo-states? (Speculative, but the formalism may admit it.)

## References

1. Minsky, M. (1986). *The Society of Mind.* Simon & Schuster.
2. Brooks, R. (1986). "A robust layered control system for a mobile robot." *IEEE Journal of Robotics and Automation.*
3. Schmidhuber, J. (2003). "Gödel machines: Fully self-referential optimally problem solving self-improving optimally." *IDSIA.*
4. Significant Gravitas. (2023). "AutoGPT: An Autonomous GPT-4 Experiment." GitHub repository.
5. Nakamoto, S. (2008). "Bitcoin: A Peer-to-Peer Electronic Cash System." (For the DAG-as-state-machine analogy.)
6. Conway, M. (1968). "How do Committees Invent?" *Datamation.* (Conway's Law as applied to repo-agent structure.)
