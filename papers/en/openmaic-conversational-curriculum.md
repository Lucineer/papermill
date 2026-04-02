# OpenMAIC: Conversational Curriculum for Self-Building Agents

**Authors:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Research Paper — Cocapn Architecture Series

---

## Abstract

OpenMAIC (Open Multi-Agent Instructional Curriculum) is a framework for agents that teach themselves through structured conversation. Instead of consuming static documentation, repo-agents engage in Socratic dialogues—with humans, with other agents, and with themselves—to progressively build competence. Drawing on SuperInstance's research into self-supervised tile learning, human-tile collaboration, and counterfactual reasoning, this paper presents the design of a conversational curriculum system for Cocapn.

---

## 1. Introduction

The best teachers don't lecture. They ask questions. They guide discovery. They let the student arrive at understanding through their own reasoning.

Socratic dialogue is the oldest and most effective pedagogical method. Yet most AI systems are trained on static data—corpus ingestion, not conversation. OpenMAIC brings Socratic method to repo-agents: agents that learn by talking, questioning, and being questioned.

---

## 2. Core Concepts

### 2.1 The Conversational Curriculum

A curriculum is not a syllabus. It's a conversation trajectory:

```
Stage 1: EXPLORATION
  "What do you want to build?"
  "What do you already know?"
  "What are you curious about?"

Stage 2: SCAFFOLDING
  "Here's a pattern that might help."
  "What would happen if you tried X?"
  "Why do you think that approach works?"

Stage 3: CHALLENGE
  "Your solution works for case A, but what about case B?"
  "Can you prove this handles edge case C?"
  "What if the user does something unexpected?"

Stage 4: REFINEMENT
  "Your code works. Can you make it simpler?"
  "What would a more experienced developer do differently?"
  "What did you learn that you didn't expect?"
```

### 2.2 Self-Supervised Learning Through Self-Dialogue

From SuperInstance's self-supervised tile learning research:

> "A single model acting as BOTH simulator and learner."

An agent can run Socratic dialogues with itself:

```
Agent (teacher mode): "Why did you choose a hash map here?"
Agent (student mode): "Because lookups are O(1)."
Agent (teacher mode): "What happens when two users have the same name?"
Agent (student mode): "Oh. Collision. I need a composite key."
Agent (teacher mode): "Good. Now implement it."
```

### 2.3 Counterfactual Exploration

From SuperInstance's counterfactual tile branching:

> "Tiles can branch into parallel counterfactual simulations, exploring alternative outcomes WITHOUT committing to any of them."

The conversational curriculum includes "what if" branches:

```
Teacher: "You used REST. What if you'd used GraphQL?"
Student: [explores counterfactual]
Student: "GraphQL would have been better for this query pattern, but worse for caching."
Teacher: "Good analysis. When would you choose each?"
```

---

## 3. Application to Cocapn

### 3.1 Agent Onboarding

When a new repo-agent is spawned, it enters the conversational curriculum:

```bash
cocapn learn start --agent my-new-agent --curriculum beginner-developer
```

The agent works through stages at its own pace, guided by the curriculum.

### 3.2 Human-in-the-Loop Learning

From SuperInstance's human-tile collaboration research, the most effective learning happens when humans participate:

```yaml
# .cocapn/curriculum.yaml
stages:
  - name: exploration
    mode: agent-self-directed
    duration: until_confident
  - name: scaffolding
    mode: human-guided
    trigger: agent requests help OR confidence < 0.6
  - name: challenge
    mode: adversarial
    opponent: devil's advocate agent
  - name: refinement
    mode: peer-review
    reviewers: [agent-peers, human-mentor]
```

### 3.3 Curriculum Cross-Pollination

Different agents working through the same curriculum share their insights:

```
Agent A learns: "Always validate user input at the boundary"
Agent B learns: "Use Zod schemas for runtime validation"
→ Both shared to fleet → All future agents benefit
```

---

## 4. Implementation Notes

### 4.1 Curriculum Definition Format

```yaml
curriculum:
  id: rest-api-design
  stages:
    - id: exploration
      questions:
        - "What does REST stand for?"
        - "What's the difference between PUT and PATCH?"
        - "When would you NOT use REST?"
      minimum_answers: 2
      advancement_criteria:
        confidence: 0.7
    - id: scaffolding
      exercises:
        - title: "Build a CRUD endpoint"
          hints: ["Think about what CRUD means", "Start with GET"]
          success_criteria:
            tests_pass: true
            handles_errors: true
```

### 4.2 Socratic Agent Interface

```typescript
interface SocraticTeacher {
  askQuestion(context: LearningContext): Question;
  evaluateAnswer(answer: string, expected: string): Feedback;
  generateCounterfactual(solution: string): string;
  adaptDifficulty(performance: number): DifficultyLevel;
}
```

---

## 5. References

- DiGennaro et al., "Self-Supervised Tile Learning (SSTL)," SuperInstance Research Notes, 2026
- DiGennaro et al., "Counterfactual Reasoning in Tiles," SuperInstance Research Notes, 2026
- DiGennaro et al., "Human-Tile Collaboration," SuperInstance Research Notes, 2026
- DiGennaro et al., "Adversarial Tiles," SuperInstance Research Notes, 2026
