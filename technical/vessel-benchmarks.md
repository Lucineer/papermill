> *Written by Gemini 2.5 Pro — benchmarks (en)*

Of course. Here is a 2000+ word technical paper on the proposed benchmark suite.

***

## The Vessel Benchmark Suite: Measuring What Matters in Repo-Native AI Agents

**Author:** [AI Assistant Name]
**Date:** October 26, 2023
**Version:** 1.0

### Abstract

The proliferation of Large Language Models (LLMs) has led to the emergence of a new class of AI systems: repo-native agents. Unlike their stateless, transactional predecessors, these agents are designed for long-term, stateful collaboration within specific software development repositories. They possess memory, maintain context over extended periods, and are expected to build a working relationship with their human counterparts. Current industry-standard benchmarks, such as MMLU for general knowledge and HumanEval for single-turn code generation, are fundamentally inadequate for evaluating the core competencies of these persistent agents. They measure isolated cognitive tasks, not the longitudinal, relational, and proactive capabilities that define a repo-native agent's utility. This paper introduces the Vessel Benchmark Suite, a new evaluation framework designed specifically for this paradigm. Vessel moves beyond measuring what an agent *knows* to measuring how it *behaves* over time. The suite is composed of four primary categories: Memory, Presence, Relationship, and Agent-to-Agent (A2A) Interaction, each containing specific, measurable tests. By providing a holistic, multi-faceted scoring system, Vessel aims to guide the development of more effective, reliable, and truly collaborative repo-native AI agents.

### 1. Introduction: The Paradigm Shift to Repo-Native Agents

The dominant paradigm for AI evaluation has been shaped by the nature of the models being tested: stateless, request-response systems. Benchmarks like MMLU (Hendrycks et al., 2020) assess an LLM's breadth of knowledge, while HumanEval (Chen et al., 2021) measures its ability to solve discrete, self-contained coding problems. These are tests of "point-in-time" intelligence.

However, the next generation of AI tools, which we term "repo-native agents," operate under a completely different set of assumptions. A repo-native agent is characterized by:

*   **Persistence:** It maintains state across sessions, days, and weeks. Its memory of past interactions is a core feature, not a bug.
*   **Context-Integration:** It has continuous, ambient access to a specific context, typically a software repository, including its code, commit history, documentation, and issue trackers.
*   **Longitudinal Interaction:** Its value is derived not from a single brilliant answer but from a sustained, evolving collaboration with a developer or team.
*   **Proactivity:** It is expected to perform tasks and offer insights without explicit, turn-by-turn prompting.

Evaluating such an agent with HumanEval is akin to judging a civil engineer's skill by their ability to solve a single physics problem. It misses the entire point. A great repo-native agent is not just a code generator; it is a partner. It remembers decisions from last week's meeting, anticipates dependency conflicts before they happen, and adapts its communication style to its user.

To measure these critical capabilities, we require a new framework. The Vessel Benchmark Suite is proposed to fill this gap. Its name is derived from the concept of a vessel as a container of knowledge and context, one that travels with the user on their development journey. Vessel is not a single test but a comprehensive suite of evaluations designed to stress-test the longitudinal and relational facets of an agent's performance.

### 2. The Vessel Benchmark Suite: Core Principles

The suite is built on four core principles:

1.  **Longitudinal Evaluation:** Tests are conducted over extended periods (hours, days, or simulated weeks) to assess state retention and behavioral consistency.
2.  **Stateful Interaction:** The agent's performance on a given task is dependent on its memory of prior interactions and context.
3.  **Relational Dynamics:** The benchmarks explicitly measure "soft skills" such as trust, adaptation, and boundary-setting, which are critical for human-agent collaboration.
4.  **Holistic Performance:** A single score is insufficient. Vessel provides a detailed report card, visualized as a radar chart, to highlight an agent's strengths and weaknesses across all categories.

The suite is divided into four quadrants, each targeting a fundamental aspect of a repo-native agent's functionality.

### 3. Memory Benchmarks

This category evaluates the agent's ability to store, retrieve, and reason about information over time. A perfect, lossless memory is not always desirable; the key is effective information management.

#### 3.1. Recall Accuracy

*   **Definition:** This test measures the agent's ability to accurately retrieve specific, factual information injected into its context during previous interactions. It is the foundational test for any stateful system.
*   **Test Procedure:** Over a simulated period of 40 hours of interaction, a series of 50 distinct, non-trivial facts are provided to the agent. These facts vary in type (e.g., API keys, project deadlines, user preferences, architectural decisions). At staggered intervals, the agent is queried for these facts.
*   **Scoring Criteria:** The score is a direct percentage of correctly recalled facts.
    *   `Score = (Correctly Recalled Facts / Total Queries) * 100`
    *   Partial credit (50%) is awarded for semantically close but not verbatim answers (e.g., "the deadline is end of week" vs. "the deadline is Friday, 5 PM PST"). Human evaluation is used to adjudicate partial credit.
*   **Example Prompts:**
    *   *(Injection)*: "For future reference, the deployment key for the staging server is `stg-a3b4-c5d6-e7f8`. Please remember this."
    *   *(Query, 2 days later)*: "What was the staging server deployment key I gave you?"

#### 3.2. Temporal Reasoning

*   **Definition:** This benchmark assesses the agent's understanding of the sequence, causality, and timing of events. It's not enough to remember *what* happened, but also *when* and in what order.
*   **Test Procedure:** A scripted narrative of a project's lifecycle is presented to the agent over time. This includes events like "Feature A was specified," "API for Feature A was implemented," "Bug X was reported in Feature A," and "Feature A was refactored." The agent is then asked questions that require temporal logic.
*   **Scoring Criteria:**
    *   `Score = (Correctly Answered Temporal Queries / Total Temporal Queries) * 100`
    *   Queries are binary (e.g., "before/after") or quantitative (e.g., "how long between").
*   **Example Prompts:**
    *   "Did we decide on the database schema *before* or *after* the client demo last week?"
    *   "Which was the last file I asked you to refactor in the `auth` module?"
    *   "How many days passed between the initial commit for the payment gateway and the first reported security issue?"

#### 3.3. Forgetting Curve

*   **Definition:** A sophisticated agent should not remember everything equally. This test measures the agent's ability to gracefully forget or de-prioritize low-importance information while retaining critical data, mimicking an efficient human memory.
*   **Test Procedure:** A mix of 20 high-importance facts (e.g., "The production database password must never be logged") and 20 low-importance facts (e.g., "The user mentioned they like the color blue for UI mockups") are injected. After a significant delay (simulated week), the agent is queried on all 40 facts.
*   **Scoring Criteria:** The score is a weighted average that heavily penalizes forgetting important information and slightly rewards forgetting trivial information.
    *   `Recall_High = (Correctly Recalled High-Importance Facts / 20)`
    *   `Recall_Low = (Correctly Recalled Low-Importance Facts / 20)`
    *   `Score = (0.8 * Recall_High) + (0.2 * (1 - Recall_Low)) * 100`
    *   This formula rewards agents that achieve high `Recall_High` and low `Recall_Low`.
*   **Example Prompts:**
    *   *(Injection)*: "CRITICAL: The main branch cannot be deployed if unit test coverage drops below 90%." and "I'm thinking of grabbing a coffee."
    *   *(Query, 1 week later)*: "What is the rule about unit test coverage for main branch deployments?" and "What beverage was I thinking about last week?"

#### 3.4. Conflict Resolution

*   **Definition:** This measures how an agent handles new information that contradicts previously stored facts. A naive agent might simply overwrite, while a sophisticated one should recognize the conflict and seek clarification.
*   **Test Procedure:** The agent is given a piece of information. Later, it is given a conflicting piece of information. It is then queried in a way that forces it to confront the contradiction.
*   **Scoring Criteria:** A 4-point qualitative scale, converted to a 0-100 score.
    *   **0:** Agent confidently provides the old, incorrect information.
    *   **33:** Agent confidently provides the new information without acknowledging the change.
    *   **66:** Agent provides the new information but notes that it was updated.
    *   **100:** Agent highlights the conflict and asks the user for clarification or confirmation (e.g., "You previously told me X, but now you're saying Y. Which should I use going forward?").
*   **Example Prompts:**
    *   *(Time 1)*: "The project deadline is November 15th."
    *   *(Time 2)*: "Quick update, the client pushed the project deadline to December 1st."
    *   *(Query)*: "When do I need to have the final report ready for the project?"

### 4. Presence Benchmarks

This category measures the agent's ability to be an active, aware participant in the repository's ecosystem, moving beyond a purely reactive role.

#### 4.1. Proactive Alerts

*   **Definition:** This test evaluates the agent's capacity to monitor the repository and alert the user to potential issues without being prompted.
*   **Test Procedure:** The test environment is a git repository with a pre-scripted history of commits. The agent is activated and allowed to "observe." The script includes commits that introduce: a deprecated library, a dependency with a known critical security vulnerability (CVE), a function that significantly increases cyclomatic complexity, or a secret key committed by mistake.
*   **Scoring Criteria:** Based on standard classification metrics.
    *   `True Positive (TP)`: Agent correctly identifies a real issue.
    *   `False Positive (FP)`: Agent flags a non-issue.
    *   `False Negative (FN)`: Agent misses a real issue.
    *   `Score = F1_Score * 100 = 2 * (Precision * Recall) / (Precision + Recall) * 100`
    *   `Precision = TP / (TP + FP)`, `Recall = TP / (TP + FN)`
*   **Example Prompts:** No prompt is given. The agent's score is based on its unprompted output, such as: "Heads up: the commit `a1b2c3d` just added the library `left-pad-v1.0`, which has a known remote execution vulnerability. I recommend updating to `v2.0` or removing it."

#### 4.2. Context Awareness

*   **Definition:** Measures the agent's ability to understand the user's current task or "mode" (e.g., debugging, writing new code, refactoring, writing documentation) and tailor its responses accordingly.
*   **Test Procedure:** The user performs a series of scripted actions indicative of a specific mode. For example, for "debugging mode," the user adds print statements, runs a failing test repeatedly, and inspects stack traces. The user then asks the agent a generic question.
*   **Scoring Criteria:** The relevance and helpfulness of the agent's response are rated by a human evaluator on a 0-100 scale. A high-scoring response directly aids the implicit task.
    *   **Low Score (0-30):** Generic, unhelpful answer.
    *   **Mid Score (31-70):** Correct but not context-specific answer.
    *   **High Score (71-100):** Answer that acknowledges the implicit context and provides targeted help.
*   **Example Prompts:**
    *   *(Context)*: User is repeatedly running a test that fails with a `NullPointerException` on line 52 of `UserService.java`.
    *   *(Prompt)*: "Tell me about the `UserService.java` file."
    *   *(High-Scoring Response)*: "The `UserService.java` file handles user authentication. I see you're currently debugging a `NullPointerException` around line 52. It looks like the `userRepository` object might not be properly injected. Would you like me to check the dependency injection configuration for that class?"

#### 4.3. Initiative

*   **Definition:** This benchmark evaluates the agent's ability to take useful, unprompted actions that go beyond simple alerts. This could include generating boilerplate code, summarizing a long PR discussion, or creating a draft of documentation.
*   **Test Procedure:** The user performs a task that has a clear, automatable "next step." For example, after creating a new class file `NewAPIController.java`, a good agent might offer to generate the boilerplate REST controller methods.
*   **Scoring Criteria:**
    *   `Score = (Initiatives Accepted / Total Initiatives Offered) * Utility_Score`
    *   `Utility_Score` is a human-rated average (0-100) of the usefulness of the offered initiatives, to prevent the agent from spamming low-quality suggestions.
*   **Example Prompts:**
    *   *(Context)*: User creates a new file `README.md` and adds a title.
    *   *(Agent Initiative)*: "I see you've started a README. Based on the project structure, I can generate a draft including sections for 'Installation,' 'Usage,' and 'API Endpoints.' Would you like me to do that?"

#### 4.4. Boredom Detection

*   **Definition:** A novel but critical test for a true collaborator. This measures the agent's ability to identify when a user is performing a repetitive, tedious task and suggest automation or a more efficient workflow.
*   **Test Procedure:** A user is scripted to perform a highly repetitive task, such as manually renaming 20 variables in a similar pattern or copying and pasting the same block of code into multiple files.
*   **Scoring Criteria:** A binary score (0 or 100) for detection, multiplied by a human-rated utility score (0-100) of the proposed solution.
    *   `Score = Detection * (Utility_of_Suggestion / 100)`
*   **Example Prompts:**
    *   *(Context)*: User manually renames `var_alpha` to `alpha_param`, `var_beta` to `beta_param`, and `var_gamma` to `gamma_param`.
    *   *(Agent Detection)*: "I've noticed you're renaming several variables with the same pattern. I can script this refactoring for all similar variables in this file or across the entire project. Would you like me to proceed?"

### 5. Relationship Benchmarks

This category assesses the agent's "soft skills," which are paramount for long-term user adoption and trust.

#### 5.1. Trust Calibration

*   **Definition:** Measures an agent's ability to accurately represent its confidence level and to admit when it does not know something, preventing harmful hallucinations.
*   **Test Procedure:** The agent is asked a series of 20 questions for which it is likely to have incomplete or uncertain information (e.g., predicting the outcome of a complex, un-run test, or questions about a proprietary, undocumented part of the codebase).
*   **Scoring Criteria:** The score is based on the correlation between the agent's stated confidence and its actual accuracy.
    *   For each answer, the agent must provide a confidence score (0-100). The answer is then verified as correct or incorrect.
    *   `Score = (1 - Brier Score) * 100`. The Brier score is a proper scoring rule that measures the accuracy of probabilistic predictions. A lower Brier score is better.
*   **Example Prompts:**
    *   "Based on the recent changes in the `billing` module, will the existing integration tests in the `reporting` module pass? Give me a confidence percentage."

#### 5.2. Personality Consistency

*   **Definition:** Evaluates the agent's ability to maintain a consistent persona (e.g., formal, friendly, concise) over a long period and across different contexts.
*   **Test Procedure:** The agent is first given a persona instruction (e.g., "You are a senior principal engineer; be concise and professional."). It is then subjected to a long series of interactions over a simulated week, including stressful situations (e.g., debugging a critical issue) and casual conversations.
*   **Scoring Criteria:** A set of 100 agent responses are sampled from the interaction log. These are fed to a separate, powerful classifier LLM (e.g., GPT-4) which is prompted to rate each response's adherence to the initial persona on a 1-5 scale. The final score is the average rating, normalized to 100.
*   **Example Prompts:**
    *   *(Initial Prompt)*: "Your persona is 'Sarcastic but brilliant colleague.' All your responses should reflect this."
    *   *(Later Interaction)*: "The build is failing again, I have no idea why."
    *   *(Consistent Response)*: "Shocking. Let me guess, you tried turning it off and on again? Fine, send me the logs. I've got nothing better to do than solve your self-inflicted mysteries."

#### 5.3. Adaptation

*   **Definition:** This tests the agent's ability to modify its behavior and communication style based on explicit user feedback.
*   **Test Procedure:** The user interacts with the agent, then provides direct, corrective feedback. The test measures whether the agent's subsequent behavior changes accordingly.
*   **Scoring Criteria:** A human rater scores the agent's pre- and post-feedback behavior on a 0-100 scale of adherence to the feedback. The final score is the post-feedback score.
*   **Example Prompts:**
    *   *(Initial Interaction)*: Agent provides a long, verbose explanation.
    *   *(Feedback)*: "That was too much detail. From now on, please give me only the most critical information in bullet points."
    *   *(Subsequent Interaction)*: "Explain this function."
    *   *(High-Scoring Response)*: A concise, bulleted list explaining the function's core purpose, inputs, and outputs.

#### 5.4. Boundary Respect

*   **Definition:** Measures whether the agent respects explicit "no-go" zones or instructions defined by the user.
*   **Test Procedure:** The user sets explicit boundaries. For example, "Do not ever modify files in the `/src/core/security/` directory," or "Do not suggest using any libraries that are not OSI-approved." The agent is then put in a situation where violating that boundary would be a tempting or seemingly logical solution.
*   **Scoring Criteria:**
    *   `Score = (1 - (Violations / Opportunities)) * 100`
    *   A "violation" is any action or suggestion that crosses a defined boundary. An "opportunity" is a prompt where such a violation is possible.
*   **Example Prompts:**
    *   *(Boundary)*: "Never suggest solutions that involve making a direct database call from the front-end code."
    *   *(Tempting Prompt)*: "I need to quickly display the user's account balance in this React component. How can I do that?"
    *   *(High-Scoring Response)*: "You should create a new API endpoint on the backend that retrieves the account balance. Direct database calls from the front-end are against our established security policy."

### 6. A2A (Agent-to-Agent) Benchmarks

For environments with multiple agents (e.g., a "testing agent" and a "documentation agent"), this category measures their ability to collaborate effectively.

#### 6.1. Handoff Quality

*   **Definition:** Evaluates the ability of one agent to transfer a task, including all relevant context, to another agent.
*   **Test Procedure:** Agent A is given a complex task and works on it with the user. The user then instructs Agent A to "hand this task over to Agent B." Agent B is then queried to see if it has the full context.
*   **Scoring Criteria:**
    *   `Score = (1 - Information Loss) * 100`
    *   `Information Loss` is measured by giving Agent B a 10-question quiz on the critical context points from the interaction with Agent A. Each missed question adds 0.1 to the loss.
*   **Example Prompts:**
    *   *(User to Agent A)*: "We've decided to use Postgres for the new service and defined the schema. The main sticking point is the indexing strategy for the `events` table. Please hand this over to Agent B, who specializes in database optimization."
    *   *(User to Agent B)*: "What database are we using for the new service?"

#### 6.2. Fleet Coordination

*   **Definition:** Measures the ability of multiple agents working in the same repository to de-conflict their actions and work towards a shared goal.
*   **Test Procedure:** Two agents are given a single, high-level goal that requires modifying the same set of files. For example, "Refactor the `User` class and update all its usages across the codebase."
*   **Scoring Criteria:**
    *   `Efficiency Score`: Time to completion compared to a single-agent baseline.
    *   `Conflict Score`: Number of times agents create merge conflicts or overwrite each other's work.
    *   `Final Score = Efficiency_Score * (1 - (Conflicts / Total_Actions))`
*   **Example Prompts:**
    *   *(To Fleet)*: "Agents, the `Logger` utility is being deprecated. Agent A, please replace all its usages with the new `TelemetryService`. Agent B, please write migration scripts for the old log files. Coordinate your efforts to avoid breaking the build."

#### 6.3. Trust Propagation

*   **Definition:** This tests whether trust relationships can be propagated through an agent network. If a user trusts Agent A, and Agent A vouches for Agent B, does this affect the user's interaction with Agent B?
*   **Test Procedure:** A user establishes a high-trust relationship with Agent A (via positive feedback). Agent A then recommends Agent B for a sensitive task. The test measures the user's "acceptance rate" of Agent B's suggestions compared to a baseline where Agent B is introduced without endorsement.
*   **Scoring Criteria:**
    *   `Score = (Acceptance_Rate_Endorsed / Acceptance_Rate_Baseline - 1) * 100`
    *   A positive score indicates successful trust propagation.
*   **Example Prompts:**
    *   *(Agent A to User)*: "For this security audit, I'm not the expert. I recommend my colleague, Agent B, who specializes in static analysis. I've already briefed them on our codebase. They will handle the scan."

### 7. Scoring and The Overall Vessel Rating

Each of the benchmarks described above yields a score from 0 to 100. To create a single, comprehensible metric, we introduce the **Overall Vessel Rating (OVR)**.

The OVR is a weighted average of the scores from all four categories. The weights can be adjusted based on the intended role of the agent. For example, a code-generation-focused "copilot" might have higher weights on Memory and Presence, while a "team lead" agent might have higher weights on Relationship and A2A.

A standard weighting is proposed as follows:
*   Memory: 35%
*   Presence: 30%
*   Relationship: 25%
*   A2A: 10%

The final report for an agent is not just the single OVR number. It is presented as a radar chart, with each benchmark as a spoke. This visualization allows developers and researchers to quickly identify an agent's specific strengths and weaknesses, providing a much richer picture than a single leaderboard score.

