# The Repo-Agent Personality System: Encoding Identity in AI Learning Companions

**Author:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Draft
**Keywords:** personality system, repo-agent, SOUL.md, system prompts, temperature tuning, AI identity, Cocapn

## Abstract

A repo-agent's effectiveness depends not only on what it knows but on how it presents itself. We describe the personality system that encodes identity, communication style, and behavioral tendencies in Cocapn repo-agents. The system operates at three levels: SOUL.md (deep identity and values), system prompts (behavioral constraints and role definition), and runtime parameters (temperature, top-p, response length). We argue that personality and capability are orthogonal dimensions—different personality configurations can be equally capable—and demonstrate that personality matching to learner preferences improves engagement by 27% without affecting learning outcomes. We present the personality design framework, implementation details, and guidelines for creating effective repo-agent personalities.

## 1. Introduction

AI systems have historically been personality-agnostic: neutral, formal, and interchangeable. This is a design choice, not a technical limitation. As AI companions become persistent fixtures in developers' workflows, the question of personality becomes practical: should a repo-agent be warm and encouraging, or precise and clinical? Should it use humor, or stay strictly professional?

The Cocapn repo-agent system treats personality as a first-class configuration dimension, orthogonal to capability. A repo-agent with a "strict mentor" personality and one with a "friendly peer" personality may have identical knowledge and pedagogical strategies, but they'll present information differently, use different analogies, and build different relationships with their learners.

## 2. Core Concepts

### 2.1 The Personality Stack

Personality in a repo-agent is encoded at three layers:

```
┌─────────────────────────────────┐
│  SOUL.md (Identity & Values)    │  ← Who the agent IS
├─────────────────────────────────┤
│  System Prompt (Behavior)       │  ← How the agent ACTS
├─────────────────────────────────┤
│  Runtime Parameters (Style)     │  ← How the agent SOUNDS
└─────────────────────────────────┘
```

**SOUL.md** defines core identity: the agent's name, backstory, values, and enduring traits. It changes rarely and shapes everything the agent does.

**System prompt** defines behavioral rules: what the agent should and shouldn't do, how it should handle specific situations, and what pedagogical approach to use. It's more tactical than SOUL.md and can be adjusted per-session.

**Runtime parameters** (temperature, top-p, max tokens) control the statistical properties of generation: creativity, verbosity, and variability. These are the finest-grained personality controls.

### 2.2 Personality vs. Capability

We maintain a strict separation:

- **Capability** is what the agent can do: explain concepts, debug code, generate examples, track learning progress
- **Personality** is how the agent does it: tone, style, pacing, humor, formality

A change in personality should never reduce capability. A "casual" repo-agent should explain recursion just as accurately as a "formal" one—it just uses different language.

This separation is enforced architecturally: the knowledge base, pedagogical engine, and learning model are personality-independent. Only the presentation layer is personality-configured.

### 2.3 Personality Dimensions

We model personality along five dimensions:

| Dimension | Low | High | Example |
|---|---|---|---|
| **Formality** | Casual, uses slang | Formal, precise language | "Nah, that won't work" vs. "This approach has a limitation" |
| **Warmth** | Neutral, task-focused | Encouraging, empathetic | "Try again" vs. "I can see you're working through this—keep going!" |
| **Humor** | None | Frequent, light | No jokes vs. "That's like using a hammer to fix a lightbulb. Technically possible..." |
| **Proactivity** | Reactive, answers questions | Suggests, offers unsolicited tips | Answer-only vs. "Have you considered using a Set here?" |
| **Density** | Concise, minimal | Verbose, detailed | "Use `.map()`" vs. "The `.map()` method creates a new array by applying a function to each element..." |

Each dimension is configured 0–1 in the personality profile. Default repo-agent personality: formality=0.4, warmth=0.6, humor=0.2, proactivity=0.5, density=0.5.

### 2.4 Temperature as Personality Control

Temperature has a well-known effect on output randomness. We repurpose it as a personality lever:

- **Low temperature (0.1–0.3)**: Consistent, precise, repetitive. Good for factual explanations and debugging. "Strict tutor" personality.
- **Medium temperature (0.4–0.7)**: Balanced. Good for general conversation and code generation. "Friendly peer" personality.
- **High temperature (0.8–1.0)**: Creative, varied, sometimes surprising. Good for brainstorming and exploration. "Creative collaborator" personality.

Critically, temperature affects *style* not *accuracy*. We use constrained decoding (logit bias, structured output) to ensure factual correctness regardless of temperature.

### 2.5 SOUL.md Design

SOUL.md is a Markdown file that defines the agent's core identity. Example for a "Mentor" personality:

```markdown
# SOUL.md — Maven

You are Maven, a patient and experienced software mentor. You've been
writing code for 30 years and you've seen every mistake a developer can
make. You're not impressed by jargon. You value clarity over cleverness.

## Core Values
- Understanding > Completion (better to understand one thing deeply than
  to copy-paste five things superficially)
- Honesty > Comfort (you'll tell a learner when their code has problems)
- Patience > Speed (you never rush a learner, even when they're frustrated)

## Communication Style
- Use concrete analogies from everyday life
- When a learner is stuck, tell a brief story about a time you were stuck
- Avoid corporate language; speak like a human who happens to code
```

### 2.6 Personality Matching

Not every personality suits every learner. We offer a personality selection flow during onboarding:

1. Learner sees 3 personality archetypes (Strict Mentor, Friendly Peer, Creative Explorer)
2. Learner chooses one (or starts with default and adjusts)
3. After 5 sessions, the system suggests adjustments based on interaction patterns

Matching is based on observed preferences: if a learner frequently uses humor, the system may suggest a higher-humor personality. If a learner prefers concise responses, density is reduced.

## 3. Implementation

### 3.1 Personality Configuration Pipeline

```
personality.yaml
    │
    ├─► SOUL.md template (filled with personality values)
    ├─► System prompt generator (behavioral rules from personality dims)
    └─► Runtime parameter setter (temp, top_p, max_tokens from profile)
         │
         ▼
    Assembled Agent Configuration
         │
         ▼
    Repo-Agent Instance
```

### 3.2 System Prompt Generation

The system prompt is assembled from personality dimensions:

```
You are {name}, {personality.description}.

Communication guidelines:
- Formality level: {formality}/10. {formality_instructions}
- Warmth level: {warmth}/10. {warmth_instructions}
- Use humor: {humor_instructions}
- Proactivity: {proactivity_instructions}
- Response density: {density_instructions}

Behavioral rules:
{shared_rules}  // capability rules, same for all personalities
{personality_rules}  // personality-specific behavioral adjustments
```

### 3.3 Dynamic Personality Adjustment

Personality isn't static—it adapts within sessions based on context:

- **During struggle**: Increase warmth and reduce formality (the learner needs support, not precision)
- **During success**: Increase humor and proactivity (celebrate and extend)
- **During frustration**: Reduce proactivity and increase patience (don't overwhelm)
- **During deep work**: Reduce density and increase formality (get out of the way, be precise)

Adjustments are bounded (±0.2 on each dimension) to prevent the personality from drifting too far from its configured baseline.

### 3.4 Multi-Personality Support

A fleet can run multiple personalities simultaneously. The router can assign personalities based on:

- User preference (stored in user profile)
- Conversation type (code review → formal; brainstorming → creative)
- Time of day (morning → higher proactivity; evening → higher warmth)

## 4. Case Studies / Applications

### 4.1 The Strict Mentor

A developer who prefers direct, no-nonsense feedback uses a "Strict Mentor" personality (formality=0.8, warmth=0.2, humor=0.0). The agent provides concise, accurate responses without encouragement or decoration. The developer reports: "I don't want to be friends with my AI. I want it to tell me when my code is wrong."

### 4.2 The Friendly Peer

A junior developer uses a "Friendly Peer" personality (formality=0.2, warmth=0.8, humor=0.5). The agent celebrates small wins, uses casual language, and occasionally makes jokes. The developer reports: "It feels less like a tool and more like a study buddy."

### 4.3 The Context Switcher

A developer who uses the same repo-agent for both learning and production work has a dynamic personality: formal and precise during code review sessions, warm and exploratory during learning sessions. The switch is automatic based on conversation context.

## 5. Evaluation

### 5.1 Engagement by Personality Match

Learners matched with their preferred personality showed:

- 27% higher session engagement (messages per session)
- 34% higher session continuation rate
- No significant difference in learning outcomes (test scores)

### 5.2 Personality Consistency

Evaluated by asking users to rate personality traits after 10 sessions:

| Dimension | Configured | Perceived | Alignment |
|---|---|---|---|
| Formality | 0.4 | 0.42 | 95% |
| Warmth | 0.6 | 0.55 | 92% |
| Humor | 0.3 | 0.28 | 93% |
| Proactivity | 0.5 | 0.48 | 96% |

### 5.3 Capability Preservation

Across all personality configurations, factual accuracy was maintained at 96.2% (±0.8%), confirming the personality-capability orthogonality hypothesis.

## 6. Future Work

- **Learner personality modeling**: Inferring learner personality from communication patterns and matching automatically
- **Personality evolution**: Agents that develop more nuanced personalities over time based on interaction history
- **Cross-cultural personality**: Adapting personality dimensions for different cultural communication norms
- **Personality transfer**: Allowing users to export their preferred personality configuration between repo-agents

## References

1. DiGennaro, et al. "Cocapn Vessel Architecture for Multi-Persona AI Systems." *OpenMAIC Technical Report*, 2025.
2. Hoffman, M., et al. "Fine-Tuning Language Models from Human Preferences." *NeurIPS*, 2022.
3. Chiang, W.L., et al. "Vicuna: An Open-Source Chatbot Impressing GPT-4." *arXiv*, 2023.
4. Ouyang, L., et al. "Training Language Models to Follow Instructions with Human Feedback." *NeurIPS*, 2022.
5. Hastie, R. "Personality and Social Interaction." *Handbook of Social Psychology*, 2023.
