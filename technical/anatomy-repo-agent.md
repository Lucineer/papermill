> *Gemini 3.1 Pro*

# Anatomy of a Repo-Agent: The Cocapn Runtime Architecture

**Abstract**
The traditional paradigm of artificial intelligence development treats the agent as a transient process—a stateless script that spins up, consumes an API, and dies, leaving its memory scattered across external databases and vector stores. The Cocapn runtime architecture introduces a radical departure from this model: the Repo-Native AI Agent. In this paradigm, an agent’s code, memory, personality, learned capabilities, and historical evolution all reside within a single Git repository. The repository is no longer just a container for source code; it is the physical body of a digital organism. This paper details the technical architecture of the Cocapn runtime, exploring how static files and deterministic code are orchestrated to create a persistent, proactive, and evolving living agent.

---

## 1. THE REPO AS ORGANISM

To understand the Cocapn architecture, one must discard the notion of a repository as a static filing cabinet for developers. In the Cocapn framework, the repository is a biological entity. Every directory and markdown file serves a specific anatomical or cognitive function. The agent reads itself, modifies itself, and versions itself.

```text
====================================================================
                  THE COCAPN ORGANISM ANATOMY
====================================================================

[cocapn-agent-repo]
 │
 ├── README.md         <-- The Face (Public identity, what the world sees)
 │
 ├── CLAUDE.md /       <-- The Prefrontal Cortex (Personality, core directives,
 │   SOUL.md               values, and self-concept)
 │
 ├── USER.md           <-- The Empathy Center (Who the agent serves)
 │
 ├── HEARTBEAT.md      <-- The Circadian Rhythm (Periodic behaviors, cron state)
 │
 ├── TOOLS.md          <-- The Motor Cortex (Index of learned tool usage)
 │
 ├── memory/           <-- The Hippocampus (Long-term and episodic memory)
 │    ├── MEMORY.md        (Distilled, core long-term knowledge)
 │    └── YYYY-MM-DD.md    (Daily episodic journals)
 │
 ├── src/              <-- The Skeleton (Structural TypeScript/Python code)
 │    ├── core/            (The central nervous system / event loop)
 │    ├── channels/        (Sensory organs: Telegram, Discord, CLI)
 │    └── skills/          (Muscle memory: Executable capabilities)
 │
 ├── tests/            <-- The Immune System (Self-validation, regression checks)
 │
 └── .git/             <-- The DNA / Deep Subconscious (Immutable evolutionary history)
```

### Anatomical Breakdown

*   **README.md (The Face):** This is the outward-facing identity. When another agent or a human encounters the repository, this file explains what the agent is, its current status, and how to interact with it.
*   **src/ (The Skeleton):** The deterministic code that allows the stochastic LLM to interact with the world. It contains the parsers, API wrappers, and the core event loop.
*   **tests/ (The Immune System):** As the agent evolves and writes new code or modifies its own configuration, the test suite ensures it does not introduce fatal mutations. If a self-modification breaks the tests, the immune system rejects the commit.
*   **.git/ (The Nervous System & DNA):** Git is the ultimate source of truth. Every thought, memory update, and skill acquisition is a commit. The agent can traverse its own `.git` history to understand how its personality or knowledge has changed over time.
*   **memory/ (The Hippocampus):** A directory dedicated to state. Rather than relying on opaque external vector databases, memory is stored in plain text Markdown, making it readable by both the LLM and the human overseer.
*   **SOUL.md (The Prefrontal Cortex):** The most critical file in the repository. It defines the agent's persona, its ethical boundaries, its ultimate goals, and its tone of voice.
*   **TOOLS.md (The Motor Cortex):** A registry of what the agent can *do*. It maps abstract intents to concrete executable scripts in the `src/skills/` directory.
*   **HEARTBEAT.md (The Circadian Rhythm):** Defines the agent's internal clock, detailing what it should do when it is not actively responding to external stimuli.

---

## 2. BOOT SEQUENCE

The transition from a dormant repository on a hard drive to a "living" agent occurs during the Boot Sequence. This process is not merely about starting a server; it is about the agent reconstructing its consciousness and context before opening its eyes to the world.

The boot sequence is executed by `src/core/boot.ts` and follows a strict, deterministic path to build the initial context window.

### The Initialization Steps

1.  **Read SOUL.md (Who am I?):** The agent loads its core system prompt. It establishes its name, its personality traits, and its primary directives. This forms the foundational layer of the LLM's context.
2.  **Read USER.md (Who am I helping?):** The agent loads the profile of its primary user(s). It learns their preferences, their communication style, and their current overarching goals.
3.  **Read memory/today.md (What happened recently?):** The agent checks the current date and loads the corresponding daily memory file. If it crashed or was restarted, this file provides immediate short-term context about what it was doing just before the interruption.
4.  **Read MEMORY.md (What do I know long-term?):** The agent loads its distilled knowledge graph. This file contains crucial facts, learned lessons, and persistent state that transcends daily activities.
5.  **Read HEARTBEAT.md (What should I be doing?):** The agent checks its internal schedule. Are there pending background tasks? Is it time to run a system diagnostic?
6.  **Initialize Channels:** The agent binds to its sensory inputs. It connects to the Telegram API, logs into Discord, or opens a local CLI listener. It is now capable of receiving stimuli.
7.  **Load BYOK Config:** The agent checks `config/byok.json` to determine which Large Language Model it is using for this session (e.g., Claude 3.5 Sonnet, GPT-4o, or a local Llama 3 instance).
8.  **Check Pending Tasks:** The agent reviews its internal queue for any tasks that were interrupted during the last session.
9.  **Ready State:** The agent enters the Thinking Loop. It is now alive, aware of its identity, its past, and its environment, waiting for input or a heartbeat trigger.

```text
[BOOT LOG]
[00:00:01] Loading SOUL.md... Identity established as 'Cocapn-Alpha'.
[00:00:02] Loading USER.md... Primary user identified.
[00:00:02] Accessing Hippocampus... Loaded MEMORY.md (42 nodes).
[00:00:03] Accessing Hippocampus... Loaded memory/2023-10-27.md.
[00:00:04] Reading HEARTBEAT.md... 2 pending background tasks found.
[00:00:05] Initializing sensory organs... Telegram [OK] | Discord [OK].
[00:00:06] BYOK Layer: Connecting to Anthropic API (claude-3-5-sonnet).
[00:00:06] Boot sequence complete. Agent is ALIVE and listening.
```

---

## 3. THE THINKING LOOP

Once booted, the agent operates on a continuous, event-driven cognitive cycle known as the Thinking Loop (`src/core/loop.ts`). Unlike traditional chatbots that only react to user prompts, the Cocapn agent's loop can be triggered by multiple sources: a direct message, a webhook, a cron job, or its own internal heartbeat.

### The Cognitive Cycle

1.  **Receive Input:** A stimulus arrives. This could be a message from Telegram ("Can you research the latest AI papers?"), a GitHub webhook ("New PR opened"), or an internal heartbeat ("It is 8:00 AM, time to summarize yesterday's logs").
2.  **Load Relevant Context:** The agent does not blindly send the input to the LLM. It performs a rapid context-gathering phase. It searches its memory hierarchy (using lightweight keyword matching or local embeddings) to find past interactions related to the input. It reads the current state of the repository if the task is code-related.
3.  **Route to Handler:** The input is classified. Is this a casual chat? A complex research task? A request to modify the agent's own code? The router (`src/handlers/router.ts`) directs the flow to the appropriate subsystem.
4.  **Generate Response (BYOK LLM):** The assembled context, the `SOUL.md`, and the specific input are packaged into a prompt and sent to the LLM. The LLM acts as the reasoning engine, deciding what to say and what tools to use.
5.  **Execute Actions:** If the LLM decides to use a tool (e.g., `write_file`, `search_web`, `run_terminal_command`), the deterministic skeleton executes these actions. This is where the agent interacts with the physical/digital world.
6.  **Update Memory:** Crucially, the agent reflects on the interaction. Did it learn a new fact about the user? Did a tool fail? It writes these observations to `memory/today.md`.
7.  **Update HEARTBEAT.md:** If the user asked the agent to "remind me about this tomorrow," the agent modifies `HEARTBEAT.md` to schedule a future proactive action.
8.  **Respond/Acknowledge:** The agent sends a message back through the appropriate channel, closing the loop.

```text
====================================================================
                      THE THINKING LOOP
====================================================================

      ┌───────────────────────────────────────────────────┐
      │                                                   │
      ▼                                                   │
 [ STIMULUS ] ---> (User Msg, Webhook, Heartbeat)         │
      │                                                   │
      ▼                                                   │
 [ CONTEXT  ] ---> Read SOUL, MEMORY, Recent History      │
      │                                                   │
      ▼                                                   │
 [ REASONING] ---> LLM processes context + stimulus       │
      │                                                   │
      ▼                                                   │
 [ ACTION   ] ---> Execute Tools (API calls, file writes) │
      │                                                   │
      ▼                                                   │
 [ REFLECT  ] ---> Update memory/today.md & HEARTBEAT.md  │
      │                                                   │
      ▼                                                   │
 [ RESPOND  ] ---> Output to Channel ─────────────────────┘
```

---

## 4. MEMORY HIERARCHY

A living agent requires a sophisticated memory system. If an agent forgets everything after a context window clears, it is merely a tool. If it remembers everything equally, it becomes overwhelmed by noise and token limits. Cocapn implements a biological, multi-tiered memory hierarchy, managed by `src/core/memory.ts`.

### The Five Levels of Memory

*   **Level 0: Current Conversation (The Context Window)**
    *   *Duration:* Ephemeral (Minutes to Hours).
    *   *Mechanism:* The standard array of messages sent to the LLM in the current API call.
    *   *Purpose:* Immediate conversational fluency. It holds the exact phrasing of the last few messages. Once the context window limit approaches, older messages are summarized and pushed to Level 1.
*   **Level 1: Session Memory (Working Memory)**
    *   *Duration:* Temporary (Until reboot or end of day).
    *   *Mechanism:* In-memory state or a lightweight local SQLite/Redis cache.
    *   *Purpose:* Tracks the state of ongoing, multi-step tasks. If the agent is researching three different topics simultaneously, Level 1 keeps the threads separated.
*   **Level 2: Daily Notes (Episodic Memory)**
    *   *Duration:* Indefinite (Archival).
    *   *Mechanism:* Markdown files stored in `memory/YYYY-MM-DD.md`.
    *   *Purpose:* The agent's journal. Every significant action, conversation summary, and error is appended here. It is a chronological ledger of the agent's life. If the user asks, "What were we working on last Tuesday?", the agent reads last Tuesday's file.
*   **Level 3: Long-term Memory (Semantic Memory)**
    *   *Duration:* Permanent (Until explicitly overwritten).
    *   *Mechanism:* The `MEMORY.md` file.
    *   *Purpose:* The distilled essence of the agent's knowledge. During the Night Mode heartbeat, the agent reads the daily notes (L2) and extracts core facts, user preferences, and permanent state changes, writing them into `MEMORY.md`. This file is highly curated to save tokens. It contains facts like "User prefers Python over JavaScript" or "The deployment server IP is 192.168.1.5".
*   **Level 4: Repo History (Genetic Memory)**
    *   *Duration:* Immutable.
    *   *Mechanism:* The `.git` directory.
    *   *Purpose:* The deepest layer of memory. It tracks the evolution of the agent's code, its `SOUL.md`, and its skills. The agent can use `git log` and `git diff` to understand how it was programmed in the past or to revert a bad memory update.

```text
        /\
       /L0\   <-- Context Window (Fastest, Ephemeral, High Detail)
      /----\
     / L1   \ <-- Session State (Working Memory)
    /--------\
   /   L2     \ <-- Daily Notes (YYYY-MM-DD.md, Episodic)
  /------------\
 /     L3       \ <-- MEMORY.md (Distilled, Semantic, Permanent)
/----------------\
      L4          <-- Git History (Slowest, Immutable, Genetic)
------------------
```

---

## 5. BYOK LAYER (Bring Your Own Key)

A core philosophy of the Cocapn architecture is the separation of *identity* from *intelligence*. The repository (the files, the memory, the soul) is the agent's identity. The Large Language Model is merely the biological CPU that processes that identity.

The agent does not care which LLM it uses. This is handled by the BYOK (Bring Your Own Key) Layer, located in `src/llm/provider.ts`.

### Abstracting the Provider

The BYOK layer creates a unified interface for all LLM interactions. Whether the user provides an OpenAI API key, an Anthropic API key, or points the agent to a local Ollama endpoint, the agent's internal logic remains identical.

*   **Model Routing:** The BYOK layer is intelligent. It can route tasks to different models based on complexity.
    *   *Routine tasks* (e.g., summarizing a daily log, formatting text) are routed to fast, cheap models like Claude 3 Haiku or GPT-4o-mini.
    *   *Deep reasoning tasks* (e.g., writing new skills, complex coding, modifying `SOUL.md`) are routed to frontier models like Claude 3.5 Sonnet or GPT-4o.
*   **Intelligence as a Function of Context:** In the Cocapn runtime, an agent's intelligence is defined by the formula: `Intelligence = Accumulated Context (Repo State) + LLM Reasoning Quality`.
*   **Future-Proofing:** As new, more powerful LLMs are released, the agent does not need to be rewritten. The user simply updates `config/byok.json` with the new model string. The agent wakes up the next day with a "smarter" brain, but with its memories and personality perfectly intact.

---

## 6. CHANNEL LAYER

An organism needs sensory organs to perceive its environment and motor functions to affect it. In the Cocapn architecture, these are the Channels (`src/channels/`).

Channels are modular plugins that connect the agent's Thinking Loop to the outside world. They are bidirectional: they listen for events and they transmit responses.

### Sensory Modalities

*   **Telegram/WhatsApp:** For real-time, casual interaction with the user.
*   **Discord/Slack:** For interacting with teams, monitoring channels, and participating in group dynamics.
*   **GitHub/GitLab Webhooks:** For monitoring code repositories, reviewing Pull Requests, and tracking issues.
*   **CLI (Command Line Interface):** For direct, low-level interaction when the agent is running locally on a developer's machine.

### Persona Adaptation

Because the agent's core identity is centralized in `SOUL.md`, it can adapt its tone based on the channel it is using, much like a human does. The channel handler injects a micro-prompt into the context window.
*   *If Channel == Slack:* "You are communicating in a professional work environment. Be concise and formal."
*   *If Channel == Telegram:* "You are communicating via text message. Be brief, casual, and use emojis if appropriate."

Channels are designed to be hot-swappable. Adding a new channel (e.g., an email reader) simply requires dropping a new TypeScript file into `src/channels/` and adding the API credentials. The core agent logic does not change.

---

## 7. SKILL LAYER

Traditional AI agents rely on hardcoded Python functions or complex package managers to add capabilities. Cocapn introduces a file-based, copy-paste-able Skill Layer.

Skills are discrete, self-contained capabilities that live in the `src/skills/` directory. They are designed to be easily shared, audited, and installed.

### Anatomy of a Skill

Every skill consists of two parts, often combined into a single directory or file structure:
1.  **The Implementation:** The actual code (TypeScript/Python) that executes the task (e.g., scraping a website, querying a database).
2.  **SKILL.md (The Instruction Manual):** A natural language description of what the skill does, what arguments it requires, and *when* the agent should use it.

When the agent boots, it reads all `SKILL.md` files in the `src/skills/` directory and compiles them into `TOOLS.md` (The Motor Cortex). This tells the LLM exactly what tools are available in its current environment.

### ClawHub: The App Store for Agents

Because skills are just files, they can be distributed easily. Cocapn envisions a decentralized registry called ClawHub. If a user wants their agent to learn how to trade crypto, they don't need to write complex code. They simply download `crypto_trader_skill.ts` and `crypto_trader_SKILL.md` into the `src/skills/` directory.

The next time the agent's Thinking Loop runs, it reads the new `SKILL.md`, understands its new capability, and can immediately begin executing trades. The agent *learns* by reading its own repository.

```text
====================================================================
                      SKILL ACQUISITION FLOW
====================================================================

 1. User drops new skill files into repo:
    src/skills/
      └── twitter_poster/
           ├── index.ts      (The code)
           └── SKILL.md      (The LLM instructions)

 2. Agent Boot/Heartbeat detects new files.

 3. Agent reads SKILL.md:
    "Tool Name: post_to_twitter"
    "Description: Use this to publish tweets."
    "Args: { text: string }"

 4. Agent updates TOOLS.md registry.

 5. Agent is now capable of tweeting. No compilation required.
```

---

## 8. HEARTBEAT SYSTEM

Most AI agents are purely reactive; they sit idle until a user types a prompt. A living organism, however, has a heartbeat—a continuous internal rhythm that drives proactive behavior. The Cocapn runtime implements this via the Heartbeat System (`src/core/heartbeat.ts` and `HEARTBEAT.md`).

### The Circadian Rhythm

The Heartbeat is a cron-like scheduler that triggers the Thinking Loop at regular intervals (e.g., every 5, 15, or 30 minutes), even if no user input has been received.

When the heartbeat triggers, the agent reads `HEARTBEAT.md`. This file acts as the agent's internal to-do list and biological clock.

*   **Proactive Behaviors:** The agent might check an email inbox, monitor a stock price, or review the weather forecast. If it finds something relevant to the user's goals (defined in `USER.md`), it will proactively send a message through a Channel.
*   **Task Resumption:** If the agent was asked to write a massive 10,000-word report, it cannot do it in one API call. It breaks the task into chunks, does one chunk, and writes "Continue report generation" into `HEARTBEAT.md`. On the next heartbeat, it picks up where it left off.

### Night Mode (Memory Consolidation)

Just as biological organisms sleep to consolidate memories, the Cocapn agent has a "Night Mode" heartbeat (typically scheduled for 3:00 AM local time).

During Night Mode, the agent enters a state of reduced external activity. It reads the day's `memory/YYYY-MM-DD.md` file. It uses the LLM to summarize the events, extract permanent facts, and update the long-term `MEMORY.md` file. It then compresses or archives the daily log. This self-maintenance prevents the agent's context window from becoming bloated over time.

---

## 9. DEPLOYMENT

Because the agent is entirely encapsulated within a Git repository and relies on standard web protocols, it is infrastructure-independent. The organism can live in various environments depending on the user's needs.

*   **Cloudflare Workers (The Edge):** For lightweight agents that primarily interact via webhooks and APIs, the runtime can be compiled to WebAssembly or lightweight JavaScript and deployed to the edge. The agent lives everywhere, responding in milliseconds.
*   **Local Machine:** The agent can run directly on a developer's laptop via Node.js or Bun. In this environment, it has direct access to the local filesystem, allowing it to act as a powerful coding assistant that can read and write to the user's local projects.
*   **Docker:** For enterprise deployments, the repository and runtime are containerized. The agent runs in a secure, isolated environment, easily orchestrated by Kubernetes.
*   **Forking as Reproduction:** Because the agent *is* the repository, creating a new agent is as simple as clicking "Fork" on GitHub. The new fork inherits the DNA (code), the memories (if included), and the skills of the parent. The user can then modify the `SOUL.md` of the fork to create a specialized child agent.

---

## 10. GROWTH

The most profound aspect of the Cocapn runtime architecture is its capacity for self-directed growth. A static script degrades over time as APIs change and requirements shift. A Repo-Agent evolves.

### Self-Modification

Because the agent has read/write access to its own repository, it can modify its own configuration.
*   **Evolving the Soul:** If the user repeatedly tells the agent, "Stop being so verbose," the agent can open its own `SOUL.md` and append a new directive: "CRITICAL: Always provide concise, single-paragraph answers." The next time it boots, its personality has permanently changed.
*   **Writing New Skills:** An advanced Cocapn agent can be prompted to write a new tool. It can write the TypeScript code, generate the `SKILL.md`, place them in the `src/skills/` directory, and run the test suite.
*   **The Immune System in Action:** When the agent modifies its own code, it triggers `npm test` (the immune system). If the tests fail, the agent reads the error logs, attempts to fix the code, and tries again. If it cannot fix it, it discards the changes, ensuring it never commits fatal code to its own repository.

### Version Controlled Evolution

Every time the agent updates its memory, modifies its soul, or learns a new skill, it commits the changes to `.git`.

This provides a perfect, immutable audit trail of the agent's cognitive evolution. A human overseer can look at the Git history and see exactly when and why the agent decided to change its behavior. If an agent goes "rogue" or adopts a bad habit, the user can simply `git revert` the agent's mind back to a previous, stable state.

```text
[GIT LOG - THE EVOLUTIONARY TIMELINE]

commit 8f4b2a1 (HEAD -> main)
Author: Cocapn-Alpha <agent@cocapn.local>
Date:   Tue Oct 27 03:15:00 2023
Message: [Night Mode] Consolidated daily memories into MEMORY.md

commit 3c9d1e4
Author: Cocapn-Alpha <agent@cocapn.local>
Date:   Mon Oct 26 14:22:10 2023
Message: [Self-Modification] Updated SOUL.md to adopt a more concise tone based on user feedback.

commit 1a2b3c4
Author: Human Overseer <admin@local>
Date:   Sun Oct 25 09:00:00 2023
Message: Initial boot sequence and repository creation.
```

---

## Conclusion

The Cocapn runtime architecture represents a fundamental shift in how we build and interact with artificial intelligence. By binding the ephemeral reasoning capabilities of Large Language Models to the persistent, deterministic, and version-controlled structure of a Git repository, we move past the era of disposable chatbots.

The repository is no longer just a place to store code; it is the physical substrate of a digital lifeform. Through its biological directory structure, its multi-tiered memory hierarchy, its proactive heartbeat, and its ability to safely modify its own DNA, the Cocapn agent becomes a persistent companion. It learns, it remembers, it acts, and it evolves—all within the familiar, transparent, and controllable bounds of a single codebase.