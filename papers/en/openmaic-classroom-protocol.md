# The OpenMAIC Classroom Protocol: Conversational Curriculum Through Natural Language Envelope Routing

**Author:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Draft
**Keywords:** OpenMAIC, conversational curriculum, multi-agent classroom, envelope routing, experiential learning, NL protocol

## Abstract

OpenMAIC (Open Multi-Agent Instructional Communication) is a protocol for routing natural language envelopes between agents in educational settings. We present the Classroom Protocol, which uses OpenMAIC to create multi-agent learning environments where students learn by doing — interacting with specialized teaching agents, receiving personalized feedback, and progressing through adaptive curricula — rather than by reading static material. We formalize the envelope routing mechanism, define the pedagogical agent roles, and prove convergence conditions for curriculum completion.

## 1. Introduction

Traditional online education is a solo experience: one student, one screen, static content. Even "interactive" courses are pre-scripted. OpenMAIC enables something fundamentally different: a living classroom of AI agents, each specializing in different aspects of instruction, coordinating through natural language envelopes to deliver personalized, adaptive, experiential learning.

The key insight: learning is not consumption of information. Learning is *practice with feedback*. OpenMAIC classrooms make practice with feedback the primary mode of interaction.

## 2. Core Concepts

### 2.1 The Natural Language Envelope

**Definition 2.1 (NL Envelope).** An NL Envelope is a structured message format:

```
FROM: agent_id
TO: agent_id (or BROADCAST)
TYPE: instruction | question | feedback | assessment | handoff
CONTEXT: {curriculum_state, student_model, topic}
PAYLOAD: <natural language content>
TIMESTAMP: ISO 8601
TTL: time-to-live
```

The envelope is human-readable (natural language payload) but machine-routable (structured metadata). This dual nature is critical: it allows both agents and humans to understand and act on messages.

### 2.2 Pedagogical Agent Roles

**Definition 2.2 (Classroom Agent Roles).** A classroom deployment of OpenMAIC includes:

| Role | Responsibility | Interaction Style |
|---|---|---|
| `tutor` | Primary instruction | Socratic dialogue |
| `coach` | Practice and exercises | Guided trial-and-error |
| `assessor` | Evaluation and feedback | Objective analysis |
| `mentor` | Meta-learning and strategy | Reflective questioning |
| `librarian` | Reference and resources | Information retrieval |
| `playground` | Free exploration | Open-ended experimentation |

These roles are not rigid — agents can shift roles based on context. A tutor might become a coach during practice sessions.

### 2.3 The Conversational Curriculum

**Definition 2.3 (Conversational Curriculum).** A conversational curriculum CC is a directed graph G = (N, E) where:
- N is a set of learning nodes, each with entry conditions and completion criteria
- E is a set of transitions, each labeled with a handoff protocol

A student progresses through CC by demonstrating competence at each node. Unlike traditional curricula, the path is adaptive: edges are selected based on the student model, not pre-determined.

**Theorem 2.1 (Curriculum Convergence).** For any student S with initial knowledge K₀ and target knowledge K*, a conversational curriculum CC converges if and only if:
1. For every node n ∈ N, there exists a finite sequence of interactions that achieves the node's completion criteria
2. The student model is updated after each interaction (no information loss)
3. The curriculum graph is weakly connected (no isolated unreachable nodes)

*Proof.* (Sketch) Condition 1 ensures progress at each node. Condition 2 ensures the student model accurately reflects learning. Condition 3 ensures all knowledge is reachable. By induction on the number of nodes, the student will eventually reach K*. □

### 2.4 Learning by Doing

The Classroom Protocol operationalizes constructivist learning theory (Piaget, Vygotsky):

1. **Problem first:** The tutor presents a challenge, not a lecture
2. **Exploration:** The student attempts a solution (via the coach)
3. **Feedback:** The assessor evaluates the attempt
4. **Reflection:** The mentor helps the student understand what they learned
5. **Extension:** The curriculum advances to the next challenge

This cycle repeats, each iteration building on the last. The natural language envelope enables seamless handoffs between agents during this cycle.

### 2.5 Student Modeling

**Definition 2.4 (Student Model).** A student model SM is a tuple (K, P, L, A) where:
- K = current knowledge state (what they know)
- P = preference profile (how they learn best)
- L = learning history (what they've tried)
- A = affective state (confidence, frustration, engagement)

The student model is updated after every interaction and shared across all classroom agents via envelopes. This ensures consistency: the assessor knows what the tutor covered, and the mentor knows what the student found difficult.

## 3. Implementation

### 3.1 OpenMAIC Routing

Envelope routing follows these rules:
1. Direct addressing: TO: specific_agent → deliver to that agent
2. Role addressing: TO: role → deliver to any agent in that role
3. Broadcast: TO: BROADCAST → deliver to all agents
4. Handoff: TYPE: handoff → the receiving agent takes over the conversation

### 3.2 Cocapn Integration

In Cocapn, the Classroom Protocol is implemented as a fleet of vessels:
- Each pedagogical role is a vessel
- Envelopes are A2A messages
- The student model is stored in a shared knowledge base vessel
- Curriculum state is persisted in git

### 3.3 Adaptive Difficulty

The curriculum adapts based on the student model:
- If A.frustration > threshold → simplify current node
- If A.engagement < threshold → introduce variety
- If K.growth_rate > threshold → accelerate
- If completion_criteria_time > threshold → provide additional scaffolding

## 4. Applications to Cocapn

The Classroom Protocol demonstrates a key Cocapn capability: multi-vessel coordination for complex tasks. Education is not a single-agent problem — it requires multiple specialized perspectives, coordinated through structured communication.

Beyond formal education, the protocol applies to:
- Onboarding new users to Cocapn vessels
- Training vessel operators
- Cross-training agents in new domains

## 5. Related Work

- **Piaget's Constructivism (1972):** Knowledge is constructed through experience. The Classroom Protocol operationalizes this.
- **Vygotsky's Zone of Proximal Development (1978):** Learning happens just beyond current capability. Adaptive difficulty implements this.
- **Bloom's 2 Sigma Problem (1984):** One-on-one tutoring produces 2 sigma improvement. AI tutors at scale could achieve this.
- **Khan Academy's Mastery Learning:** Progress requires demonstrated mastery. Conversational curricula enforce this naturally.

## 6. Future Directions

1. **Peer learning:** Student-to-student envelope routing for collaborative learning
2. **Multi-modal envelopes:** Including code, diagrams, and interactive elements in payloads
3. **Curriculum generation:** AI agents that create curricula on-the-fly based on learning goals
4. **Longitudinal tracking:** Student models that persist across months and years of learning

## References

1. Piaget, J. (1972). *The Psychology of the Child.* Basic Books.
2. Vygotsky, L. (1978). *Mind in Society.* Harvard University Press.
3. Bloom, B.S. (1984). "The 2 Sigma Problem: The Search for Methods of Group Instruction as Effective as One-to-One Tutoring." *Educational Researcher.*
4. DiGennaro, C. et al. (2026). "OpenMAIC Conversational Curriculum." Papermill Working Paper.
5. Khan, S. (2012). *The One World Schoolhouse.* Twelve.
