# The Socratic Method for Code Generation: Question-Driven AI-Assisted Programming

**Author:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Draft
**Keywords:** Socratic method, code generation, AI tutoring, progressive disclosure, pedagogical agents, repo-agent

## Abstract

Most AI-assisted coding tools operate in a "do-what-I-say" paradigm: the user describes desired behavior and the model generates code. We propose an alternative paradigm based on the Socratic method, where the AI tutor asks a sequence of probing questions that guide the learner toward generating the code themselves. Rather than producing a solution, the tutor produces understanding. We describe the question generation algorithm, progressive hint disclosure system, and adaptation mechanisms that calibrate question difficulty to the learner's current understanding. Deployed within Cocapn repo-agents, the Socratic mode achieves comparable task completion rates to direct code generation while producing significantly deeper understanding, as measured by the ability to modify and debug the resulting code independently.

## 1. Introduction

The rapid adoption of AI code generation tools (Copilot, Cursor, Claude Code) has created a new category of programmer: one who can describe what they want but may not understand what they get. Code is generated, tested, shipped—often without the developer fully grasping the underlying patterns, trade-offs, or failure modes.

This is efficient in the short term but creates fragility. When the generated code breaks in production, or needs significant modification, the developer who doesn't understand it is stuck. They must either ask the AI again (reinforcing dependency) or debug blindly.

The Socratic method offers an alternative: the AI doesn't write the code—it asks the questions that lead the developer to write it. This is slower but builds transferable understanding. The question is whether the efficiency cost is acceptable.

## 2. Core Concepts

### 2.1 Socratic Dialogue for Programming

The Socratic method in philosophy involves asking a series of questions that expose contradictions, clarify assumptions, and lead the interlocutor toward a conclusion. Applied to programming:

1. **What should this function return?** (Clarifying the contract)
2. **What inputs does it need?** (Defining the interface)
3. **What happens if the input is empty?** (Edge case awareness)
4. **How would you break this into smaller steps?** (Decomposition)
5. **What have you written before that's similar?** (Pattern recognition)
6. **Can you write the simplest version first?** (Progressive complexity)

Each question is not random—it's selected to advance the learner's mental model by exactly one step.

### 2.2 Progressive Hint Disclosure

Not all learners need the same level of guidance. The Socratic repo-agent uses a hint ladder:

**Level 0 — Open question**: "How would you approach filtering a list of users by their role?"
**Level 1 — Narrowed question**: "What JavaScript array method could help you filter items based on a condition?"
**Level 2 — Guided question**: "Have you used `.filter()` before? What callback does it take?"
**Level 3 — Partial answer**: "The `.filter()` method takes a callback that returns true for items you want to keep. What would your callback look like?"
**Level 4 — Code skeleton**: "Try: `users.filter(user => ???)`"
**Level 5 — Full answer**: "Here's the solution: `users.filter(user => user.role === 'admin')`"

The agent starts at Level 0 and descends only when the learner is stuck (no response for >60 seconds, or explicit "I don't know"). The descent is irreversible within a single question (once a hint is given, it's not taken back) but the agent resets to Level 0 for the next question.

### 2.3 Question Types

The Socratic repo-agent uses five question types, selected based on the current task and learner state:

1. **Clarification**: "What exactly do you mean by 'handle errors'?"
2. **Decomposition**: "Can you break this into smaller steps?"
3. **Analogy**: "This is similar to how you handled pagination last week. What pattern did you use?"
4. **Edge case**: "What should happen if the API returns an empty array?"
5. **Reflection**: "Why did you choose a `for` loop instead of `.map()` here?"

The mix of question types is calibrated to the learner's level: beginners get more clarification and decomposition; advanced learners get more reflection and edge case questions.

### 2.4 Understanding Assessment

The system continuously assesses understanding through:

- **Code quality**: Does the written code handle edge cases, follow conventions, and demonstrate conceptual understanding?
- **Explanation quality**: When asked "why did you write it this way?", can the learner articulate reasoning?
- **Transfer**: Can the learner apply the same pattern to a slightly different problem?
- **Debugging ability**: When the code has an intentional bug, can the learner find and fix it?

## 3. Implementation

### 3.1 Socratic Engine Architecture

```
User Request: "I need a function to validate email addresses"
    │
    ▼
┌──────────────────────────────┐
│     Task Decomposer          │
│  (Break into sub-tasks)      │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│     Question Generator       │
│  (Select type + difficulty)  │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│     Learner Model            │
│  (Update based on response)  │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│     Hint Controller          │
│  (Descend ladder if stuck)   │
└──────────────────────────────┘
```

### 3.2 Task Decomposition

Complex tasks are decomposed into a question sequence using a dependency graph. Each sub-task has prerequisites, and questions are ordered topologically:

```
validate_email(task):
  1. Define input/output (clarification)
  2. Identify valid email patterns (knowledge retrieval)
  3. Choose validation approach (design decision)
  4. Handle edge cases (robustness)
  5. Write tests (verification)
  6. Refactor (craft)
```

### 3.3 Adaptation Rules

The learner model tracks per-concept understanding (0–1) and updates after each interaction:

```
understanding[concept] += learning_rate * (outcome - understanding[concept])
```

Where `outcome` is 1 for independent correct answer, 0.5 for answer after hints, and 0 for unable to answer. The `learning_rate` is set higher for concepts the learner has seen fewer times (novelty bonus).

Question difficulty is then set to `target_difficulty = understanding[concept] + 0.2`, clamped to [0, 1]. This ensures questions are slightly harder than the learner's current level (Zone of Proximal Development).

### 3.4 Socratic Mode vs. Direct Mode

The repo-agent offers both modes. Users can switch between them per-task:

- **Socratic mode**: For learning new concepts, understanding existing code, building foundational skills
- **Direct mode**: For routine tasks, well-understood patterns, time-critical work

The system recommends mode based on the task: if the learner has never written similar code, Socratic mode is suggested. If they've written similar code 5+ times, direct mode is default.

## 4. Case Studies / Applications

### 4.1 Learning React Hooks

A developer new to React worked through building a custom hook using Socratic mode. The agent asked 23 questions over 45 minutes. The developer wrote the hook code themselves with only 3 hint descents. In a follow-up task (building a different custom hook), the developer completed it independently in 12 minutes without any AI assistance.

**Comparison**: A control group that received direct code generation completed the first hook in 8 minutes but required AI assistance for the second hook as well (average 15 minutes with AI help).

### 4.2 Debugging Practice

The agent introduced a subtle async bug into working code and asked the learner to find it. Through a series of targeted questions ("What happens if `fetchData` resolves after the component unmounts?"), the learner identified the race condition and implemented a fix. This debugging skill transferred to a production issue the following week.

### 4.3 System Design Reasoning

For a senior developer designing a microservice architecture, the Socratic agent asked trade-off questions ("What happens to your latency if the auth service is down?" "Have you considered eventual consistency here?") rather than generating the design. The developer reported that the questioning process caught two design flaws they would have missed.

## 5. Evaluation

### 5.1 Experimental Design

30 developers were given 5 programming tasks each, split between Socratic and direct modes. Task completion, time, and understanding were measured.

### 5.2 Results

| Metric | Socratic | Direct | p-value |
|---|---|---|---|
| Task completion rate | 88% | 94% | n.s. |
| Average time to completion | 3.2× longer | baseline | <0.001 |
| Independent transfer (next similar task) | 73% | 41% | <0.01 |
| Bug detection in generated code | 67% | 23% | <0.001 |
| 7-day retention | 71% | 38% | <0.01 |

### 5.3 Key Findings

- Socratic mode takes 3× longer but produces nearly 2× better transfer
- The time premium decreases as learners gain experience (from 4× for beginners to 1.8× for intermediate)
- Learners who switch between modes strategically (Socratic for new concepts, direct for familiar ones) achieve the best overall efficiency

## 6. Future Work

- **Adaptive mode switching**: Automatically detecting when a learner would benefit from switching between Socratic and direct modes
- **Collaborative Socratic sessions**: Multiple learners in a Socratic session, where the AI asks one learner a question and another evaluates their answer
- **Socratic code review**: Using Socratic questioning during code review to help reviewers think more deeply about the code
- **Question quality evaluation**: Measuring which question types and sequences produce the best learning outcomes per topic

## References

1. Paul, R., Elder, L. "The Art of Socratic Questioning." *Foundation for Critical Thinking*, 2023.
2. Vygotsky, L.S. "Mind in Society: The Development of Higher Psychological Processes." Harvard University Press, 1978.
3. DiGennaro, et al. "Repo-Agent Learning Systems in Cocapn." *OpenMAIC Technical Report*, 2025.
4. Kazi, H., et al. "Tutoring in Programming: A Meta-Analysis of Pedagogical Approaches." *Computer Science Education*, 2024.
5. Lishinski, A., et al. "Students' Computational Thinking Patterns in an Introductory CS Course." *ICER*, 2022.
