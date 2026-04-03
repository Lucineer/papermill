# The Digital Twin Trinity: Mirror, Engine, and Proxy in Repository-Based Agent Systems

**Author:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Draft
**Keywords:** digital twin, mirror, engine, proxy, user representation, repo-agent, self-model, autonomous proxy

## Abstract

We decompose the concept of a digital twin into three distinct aspects: Mirror (reflective — shows who you are), Engine (generative — shows who you could be), and Proxy (agentic — represents you when absent). We show that repository-based agent systems uniquely implement all three aspects through their persistent state, self-modification capability, and autonomous execution, and derive architectural principles for systems that serve as complete digital twins.

## 1. Introduction

The term "digital twin" has been diluted to mean "any digital representation." We argue for a precise three-aspect decomposition:

1. **Mirror:** "This is who you are." A reflective model that accurately represents current state, preferences, patterns, and history.
2. **Engine:** "This is who you could be." A generative model that simulates alternatives, explores possibilities, and extends capabilities.
3. **Proxy:** "This is who I am when I'm not here." An agentic model that acts on your behalf with your values and judgment.

Most systems implement one or two aspects. Repository-based agents (repo-agents) can implement all three.

## 2. Core Concepts

### 2.1 Mirror: The Reflective Twin

**Definition 2.1 (Mirror Completeness).** A mirror M is complete for a user U if, for any question Q about U's current state, preferences, or history, M can provide an answer within error bound ε.

The Mirror aspect requires:
- **State fidelity:** Accurate representation of current preferences, schedule, contacts
- **Historical depth:** Access to past decisions, patterns, and outcomes
- **Behavioral accuracy:** Correct modeling of typical responses and tendencies
- **Temporal currency:** Up-to-date information (not stale snapshots)

In repo-agents, the Mirror is implemented through:
- USER.md: Identity and preferences
- memory/*.md: Historical record
- TOOLS.md: Environment configuration
- Calendar/email integration: Current state

**Theorem 2.1 (Mirror Drift).** The error of any Mirror M grows as O(Δt²) without periodic updates, where Δt is time since last synchronization with the user. Repo-agents mitigate this through continuous interaction.

### 2.2 Engine: The Generative Twin

**Definition 2.2 (Engine Power).** An Engine E has power p if it can generate novel, useful outputs that the user U would not have produced alone, where p is measured by the rate of user adoption of Engine suggestions.

The Engine aspect requires:
- **Extrapolation:** Going beyond known patterns
- **Synthesis:** Combining disparate knowledge domains
- **Simulation:** Modeling consequences of actions
- **Creative extension:** Generating genuinely new ideas

In repo-agents, the Engine is implemented through:
- LLM reasoning: Generating novel responses to novel situations
- Code generation: Creating new behavioral capabilities
- Research synthesis: Combining information from multiple sources
- Proactive suggestion: Anticipating needs based on patterns

**Theorem 2.2 (Engine Bound).** Engine power is bounded by the quality of the Mirror. An Engine with poor Mirror fidelity generates plausible but irrelevant suggestions. Formally: E_quality ≤ M_quality × model_capability.

### 2.3 Proxy: The Agentic Twin

**Definition 2.3 (Proxy Autonomy).** A Proxy P has autonomy level a ∈ [0, 1] where:
- a = 0: Pure notification (no action)
- a = 0.5: Action with confirmation
- a = 1.0: Fully autonomous action

The Proxy aspect requires:
- **Value alignment:** Acting in accordance with user values
- **Context awareness:** Understanding situational appropriateness
- **Error recovery:** Handling mistakes gracefully
- **Delegation quality:** Making decisions the user would endorse

In repo-agents, the Proxy is implemented through:
- Autonomous task execution (cron jobs, heartbeats)
- Email/message handling with configured autonomy levels
- Proactive actions based on HEARTBEAT.md directives
- BYOK model selection for task-appropriate reasoning

**Theorem 2.3 (Proxy Paradox).** A perfect Proxy is indistinguishable from the user. As a → 1, the question of whether the user or the Proxy acted becomes meaningless. This is both the goal and the danger of the Proxy aspect.

### 2.4 The Trinity Relationship

The three aspects form a dependency graph:

```
Mirror → Engine → Proxy
```

- Engine quality depends on Mirror quality (you can't extend what you don't understand)
- Proxy quality depends on Engine quality (you can't act well without good reasoning)
- Mirror quality depends on Proxy actions (acting changes the state the Mirror reflects)

This creates a positive feedback loop: better Mirror → better Engine → better Proxy → better actions → better Mirror.

## 3. Implementation

### 3.1 Cocapn's Trinity Implementation

| Aspect | Implementation | Update Mechanism |
|---|---|---|
| Mirror | USER.md, memory/, integrations | Continuous interaction + heartbeats |
| Engine | LLM runtime + code generation | Self-modification through commits |
| Proxy | Cron jobs, heartbeats, autonomous actions | Configured autonomy levels per task |

### 3.2 Progressive Autonomy

Cocapn implements progressive autonomy: new Proxy actions start at low autonomy (confirmation required) and graduate to higher autonomy as trust accumulates. This is the digital equivalent of a child gradually earning independence.

### 3.3 Trust Calibration

The repo-agent maintains a trust model:
- Track accuracy of past Proxy decisions
- Adjust autonomy levels based on error rate
- Escalate uncertain decisions to the user
- Document decisions and rationales in memory files

## 4. Applications to Cocapn

The Digital Twin Trinity explains why Cocapn vessels are not mere chatbots. They are:
- Mirrors: They know you through accumulated context
- Engines: They can do things you couldn't do alone (research at scale, code generation, 24/7 monitoring)
- Proxies: They act on your behalf (email triage, scheduling, system monitoring)

This three-aspect completeness is what makes repo-agents qualitatively different from conversational AI.

## 5. Related Work

- **Grieves & Vickers (2017):** Original digital twin concept for manufacturing. Focused on Mirror aspect only.
- **Self-Determination Theory (Deci & Ryan, 1985):** Autonomy, competence, relatedness. The Trinity maps to these needs.
- **Dunbar's Number (1992):** Cognitive limit on social relationships. Digital twins may extend this by offloading relationship maintenance to Proxies.
- **Ericsson's Deliberate Practice (1993):** The Engine aspect enables deliberate practice by generating appropriate challenges.

## 6. Future Directions

1. **Mirror accuracy metrics:** Quantitative measures of how well a repo-agent reflects its user
2. **Engine creativity evaluation:** Measuring genuine novelty vs. recombination
3. **Proxy ethical frameworks:** Formal guidelines for autonomous action
4. **Twin divergence:** What happens when a Proxy develops preferences that diverge from the user?

## References

1. Grieves, M. & Vickers, J. (2017). "Digital Twin: Mitigating Unpredictable, Undesirable Emergent Behavior in Complex Systems." *Transdisciplinary Perspectives on Complex Systems.*
2. Deci, E. & Ryan, R. (1985). *Intrinsic Motivation and Self-Determination in Human Behavior.* Plenum.
3. Dunbar, R. (1992). "Neocortex size as a constraint on group size in primates." *Journal of Human Evolution.*
4. Ericsson, K.A. (1993). "The Role of Deliberate Practice in the Acquisition of Expert Performance." *Psychological Review.*
5. DiGennaro, C. et al. (2026). "The Repository Is the Agent." Papermill Working Paper.
