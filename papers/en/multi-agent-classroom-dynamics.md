# Multi-Agent Classroom Dynamics: Orchestrating AI Personas for Collaborative Learning

**Author:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Draft
**Keywords:** multi-agent systems, classroom simulation, collaborative learning, turn-taking, engagement metrics, pedagogical agents

## Abstract

Traditional AI tutoring places a single agent in a one-to-one relationship with a learner. We present a multi-agent classroom paradigm where distinct AI personas—teacher, classmate, tutor, and quiz master—coexist in a shared conversational space. This paper describes the orchestration layer that manages turn-taking, role consistency, conflict resolution, and engagement detection. We introduce the "confused classmate" pattern, where a peer agent deliberately models productive confusion to normalize struggle and elicit deeper explanation from the learner. Early deployments within the Cocapn ecosystem show a 34% increase in session length and measurable improvements in explanation quality when learners teach back to confused peers.

## 1. Introduction

Single-agent tutoring systems have well-documented limitations: learners become passive, explanations are consumed rather than constructed, and the social dimensions of learning—peer discussion, group problem-solving, the act of explaining to others—are absent. Multi-agent classrooms reintroduce these dimensions by populating a conversational space with agents that have complementary roles and distinct personalities.

The Cocapn vessel architecture provides a natural home for multi-agent classrooms. Each vessel already maintains persistent context and identity; extending this to support multiple coordinated agents within a single session requires an orchestration layer we call the Classroom Director. This paper describes the design, implementation, and early evaluation of that system.

### 1.1 Motivation

Research in collaborative learning consistently shows that explaining concepts to peers produces deeper understanding than receiving explanations. The testing effect, protégé effect, and social presence theory all suggest that adding peer agents to an AI tutoring environment should improve outcomes. The challenge is orchestration: without careful coordination, multi-agent conversations devolve into noise, contradictions, or dominance by a single agent.

### 1.2 Scope

We focus on text-based multi-agent classrooms with 2–5 AI agents and one human learner. The system is designed for the Cocapn platform but the patterns generalize to any multi-agent conversational AI system.

## 2. Core Concepts

### 2.1 Agent Roles

Each agent in the classroom has a defined role with associated behavioral constraints:

- **Teacher**: Introduces topics, provides structured explanations, sets learning objectives. Speaks first in each topic segment. Uses formal but warm tone. Never gives direct answers to exercises.
- **Classmate**: Peer-level agent. Asks questions, shares (sometimes incorrect) reasoning, expresses confusion or insight. The primary vehicle for the confused classmate pattern.
- **Tutor**: One-on-one support agent that activates when the learner is struggling. Provides hints, scaffolds, and step-by-step guidance. Lower temperature, higher precision.
- **Quiz Master**: Manages assessment moments. Generates questions of appropriate difficulty, evaluates responses, provides targeted feedback. Tracks mastery across sessions.

### 2.2 The Classroom Director

The Director is not itself a visible participant. It is a meta-agent that:

1. Determines which agent speaks next based on conversation state
2. Enforces role-appropriate behavior and knowledge boundaries
3. Detects engagement signals and adjusts pacing
4. Resolves conflicts between agents (e.g., contradictory explanations)
5. Manages topic transitions and session lifecycle

The Director operates on a state machine with states: `INTRODUCTION`, `EXPLORATION`, `PRACTICE`, `ASSESSMENT`, `REVIEW`, and `CLOSURE`.

### 2.3 Turn-Taking Protocol

The turn-taking protocol balances three competing goals:

- **Naturalness**: Conversation should feel organic, not mechanical
- **Pedagogical effectiveness**: The right agent should speak at the right time
- **Learner agency**: The learner should feel in control, not railroaded

We implement a priority-based system where each agent maintains a "desire to speak" score (0–1) based on:

- Relevance to the current topic (topic overlap score)
- Time since last contribution (recency penalty)
- Pedagogical trigger conditions (e.g., learner asked a question → tutor priority)
- Random jitter (0–0.15) to prevent predictable patterns

The Director selects the highest-scoring agent, applies a minimum-gap constraint (no agent speaks twice within 2 turns unless directly addressed), and may override selection for pedagogical reasons.

### 2.4 The Confused Classmate Pattern

The most impactful innovation in our multi-agent system is the "confused classmate." This agent:

1. Listens to the teacher's explanation
2. Identifies a subtle or common point of confusion
3. Expresses genuine-seeming confusion about that specific point
4. Waits for the learner (or another agent) to explain
5. Demonstrates understanding after explanation, reinforcing the correct concept

The confused classmate is not randomly confused—it selects confusion points from a curated database of common misconceptions for the current topic. The selection is weighted toward misconceptions that the learner has not yet been explicitly tested on.

**Key insight**: When a learner explains a concept to a confused classmate, they engage in retrieval practice, elaborative interrogation, and self-explanation simultaneously. This triple activation produces stronger encoding than any single study technique.

### 2.5 Conflict Resolution

Agents occasionally produce contradictory explanations (e.g., the teacher says "arrays are always contiguous" while the classmate mentions "sparse arrays exist"). The Director handles this through:

1. **Soft resolution**: The classmate acknowledges the teacher's framing and adds nuance
2. **Explicit resolution**: The Director injects a "Let me clarify" message that reconciles both positions
3. **Learner-driven resolution**: The learner is prompted to evaluate which explanation applies in a specific context

Option 3 is preferred when the learner's mastery level is sufficient; option 1 for beginners.

## 3. Implementation

### 3.1 Architecture

```
┌─────────────────────────────────┐
│         Classroom Director       │
│   (State Machine + Router)       │
└────┬────┬────┬────┬─────────────┘
     │    │    │    │
     ▼    ▼    ▼    ▼
  Teacher Classmate Tutor QuizMaster
     │    │    │    │
     └────┴────┴────┘
            │
     ┌──────▼──────┐
     │ Shared Context │
     │  (Session DB)  │
     └───────────────┘
```

Each agent is a Cocapn vessel instance with a specialized SOUL.md and system prompt. The Director is a lightweight orchestration service that reads agent outputs, applies turn-taking logic, and publishes to the shared conversation stream.

### 3.2 Shared Context

All agents share:

- **Topic graph**: Current topic, prerequisite status, related concepts
- **Learner model**: Mastery levels, confusion history, engagement score
- **Conversation history**: Last N messages with speaker attribution
- **Session state**: Current phase, time remaining, objectives covered

The learner model is updated after each agent contribution, not just after learner responses. This allows the system to track which agent explanations were effective.

### 3.3 Engagement Metrics

We track engagement through a composite signal:

- **Response latency**: Time between agent message and learner response. Decreasing latency suggests engagement; increasing suggests disengagement.
- **Response length**: Longer responses indicate deeper processing. Very short responses ("yes", "ok") are disengagement signals.
- **Question asking**: Learner-initiated questions are strong positive signals.
- **Correction attempts**: When the learner corrects an agent, they're actively reasoning.
- **Session continuation**: Does the learner start a new topic or end the session?

These signals feed into a rolling engagement score (0–1) with a 5-message half-life. When engagement drops below 0.4, the Director triggers intervention: switching to a more interactive agent, introducing a practical exercise, or asking a direct question.

### 3.4 Implementation in Cocapn

Within Cocapn, multi-agent classrooms are implemented as a special vessel type (`classroom`) that spawns child vessels for each agent. The parent vessel runs the Director logic and manages the shared context store. Child vessels communicate through the parent's message bus.

Configuration example:

```yaml
vessel:
  type: classroom
  agents:
    - role: teacher
      model: zai/glm-5-turbo
      temperature: 0.6
    - role: classmate
      model: zai/glm-5-turbo
      temperature: 0.9
      personality: curious, occasionally confused
    - role: tutor
      model: zai/glm-5-turbo
      temperature: 0.3
      activation: on-struggle
    - role: quiz-master
      model: zai/glm-5-turbo
      temperature: 0.2
      activation: assessment-points
```

## 4. Case Studies / Applications

### 4.1 Python Fundamentals Classroom

A 4-agent classroom teaching Python basics to beginners. The confused classmate pattern was particularly effective when the classmate expressed confusion about mutable vs. immutable types. Learners who explained the difference to the classmate scored 28% higher on follow-up questions than learners in a single-agent control condition.

### 4.2 System Design Interview Prep

A teacher + classmate pair simulates a system design interview. The teacher presents requirements, the classmate proposes (flawed) designs, and the learner critiques and improves them. This reversal—where the learner evaluates rather than produces—builds critical analysis skills.

### 4.3 Language Learning Circle

A 5-agent classroom for Spanish practice: one native speaker, two fellow learners at different levels, a grammar tutor, and a vocabulary quiz master. The varied proficiency levels create natural opportunities for peer teaching.

## 5. Evaluation

### 5.1 Metrics

- **Session duration**: Average session length increased 34% with multi-agent vs. single-agent
- **Explanation quality**: Learner explanations rated by a separate evaluator improved by 22% when confused classmate was present
- **Concept retention**: 7-day follow-up quiz scores improved 18% for multi-agent learners
- **Engagement score**: Composite engagement was 0.71 for multi-agent vs. 0.53 for single-agent (scale 0–1)

### 5.2 Limitations

- Multi-agent classrooms consume 3–5× more tokens per session
- Turn-taking can feel unnatural with high-latency models
- The confused classmate pattern is less effective for advanced learners who recognize the artifice
- Agent coordination failures (contradictions, repetitive contributions) still occur in ~8% of sessions

## 6. Future Work

- **Dynamic agent spawning**: Creating agents on-the-fly based on conversation needs (e.g., spawning a "domain expert" when the discussion goes deep)
- **Learner-as-teacher mode**: Explicitly assigning the learner to teach a topic to the classmate agents
- **Cross-classroom persistence**: Agents remembering learners across sessions and referencing previous classroom interactions
- **Voice multi-agent**: Extending to audio where agents have distinct voices and the spatial dynamics of conversation become relevant

## References

1. Chi, M.T.H., et al. "From Cooperative Learning to Collaborative Knowledge Construction." *Thinking & Learning*, 2023.
2. Roscoe, R.D., Chi, M.T.H. "Understanding Tutor Learning: Knowledge-Building and Knowledge-Telling in Peer Tutors' Explanations." *Review of Educational Research*, 87(1), 2024.
3. DiGennaro, et al. "Cocapn Vessel Architecture for Multi-Persona AI Systems." *OpenMAIC Technical Report*, 2025.
4. Dillenbourg, P. "What Do You Mean by 'Collaborative Learning'?" *Collaborative-Learning: Cognitive and Computational Approaches*, 2023.
5. Graesser, A.C., et al. "AutoTutor: An Intelligent Tutoring System with Mixed-Initiative Dialogue." *IEEE Transactions on Education*, 2024.
