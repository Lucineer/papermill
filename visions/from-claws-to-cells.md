> *Experiment: Gemini 3.1 Pro*

# From Claws to Cells: How Repo-Native Agents Transform the Spreadsheet

**Abstract**
The evolution of artificial intelligence in enterprise software has largely been characterized by a paradigm of attachment: AI is bolted onto existing databases, attached to workflows as a copilot, or integrated into spreadsheets as a functional overlay. SuperInstance’s early projects—CLAW, SPREADSHEET-MOMENT, and SUPERINSTANCE-PAPERS—represented the bleeding edge of this attachment paradigm. However, a fundamental ceiling exists when AI is treated as an external operator acting upon static data. This paper introduces the Deckboss repo-agent paradigm, a radical architectural shift where the repository itself *is* the agent. By synthesizing the runtime capabilities of OpenClaw, the grid-based orchestration of Spreadsheet-Moment, and the theoretical frameworks of SuperInstance-Papers, we present a system where the spreadsheet is no longer a canvas for AI, but the living, breathing manifestation of a persistent digital entity. 

---

## 1. THE LINEAGE: The Natural Evolution Toward Zero Distance

To understand the Deckboss repo-agent paradigm, we must trace the evolutionary lineage of SuperInstance’s foundational projects. The trajectory from CLAW to SPREADSHEET-MOMENT, and finally to Deckboss, is defined by a single, continuous vector: the systematic reduction of distance between the artificial intelligence and the user's intent, data, and state.

### Stage 1: CLAW (OpenClaw) - AI as an External Service
The original CLAW (OpenClaw) project was conceived as a multi-channel AI gateway. It was a robust runtime environment featuring modular skills, persistent memory, and a heartbeat mechanism that allowed agents to act proactively rather than merely reacting to user prompts. OpenClaw agents could connect to Telegram, Discord, or custom APIs. 

However, architecturally, OpenClaw was an intelligence living "over there." It was AI as a Service. If a user wanted OpenClaw to analyze financial data, the data had to be serialized, sent across a network boundary to the OpenClaw runtime, processed, and returned. The agent possessed intelligence, but it lacked intrinsic context. It was a brain in a jar, requiring constant feeding of external state. The distance between the data and the intelligence was high, resulting in latency, context loss, and synchronization friction.

### Stage 2: SPREADSHEET-MOMENT - AI Embedded in the Data
Recognizing the limitations of externalized AI, SuperInstance built SPREADSHEET-MOMENT. Utilizing Univer as the foundational spreadsheet engine, this project brought the AI directly into the grid. We introduced custom formulas—`=CLAW_NEW()`, `=CLAW_EQUIP()`, `=CLAW_TRIGGER()`—that allowed users to instantiate agents inside specific cells. 

In this paradigm, cells were categorized by roles: `SENSOR` (fetching data), `ANALYZER` (processing data), `CONTROLLER` (making decisions), and `ORCHESTRATOR` (managing other cells). Agents were given `EquipmentSlot` assignments to grant them specific tools. This was a massive leap forward. The AI was now *part* of the data environment. 

Yet, a philosophical barrier remained. SPREADSHEET-MOMENT was still fundamentally "an agent that operates on a spreadsheet." The spreadsheet was the territory, and the CLAW formulas were tourists walking through it. When the browser closed, or the session ended, the ephemeral state of the agent's reasoning often vanished, leaving only the final output in the cell. The distance was reduced to medium, but the duality of "data" and "agent" persisted.

### Stage 3: Deckboss - The Repo IS the Agent
The Deckboss paradigm obliterates the duality entirely. In Deckboss, we do not put an agent *into* a spreadsheet; the spreadsheet *is* the agent. The repository itself is the living entity. 

By mapping the grid of a spreadsheet directly to a Git-backed file system, every cell becomes a persistent directory. The data, the execution logic, the memory, and the LLM prompts are all unified as files within that directory. The spreadsheet UI is merely a visual dashboard—a viewport—into the cognitive state of the repository. 

In this paradigm, the distance between AI and data is zero. The data is the agent's body; the Git history is its memory; the file system is its state. This evolution from external service (Claw) to embedded operator (Spreadsheet-Moment) to unified living entity (Deckboss) represents the ultimate realization of repo-native intelligence.

---

## 2. WHAT CHANGES WHEN THE REPO IS THE AGENT

The transition to the Deckboss paradigm requires a complete reimagining of the mechanics established in SPREADSHEET-MOMENT. We are moving from a functional, formula-driven architecture to a stateful, repository-driven architecture. Here is how the core CLAW formulas and concepts are transformed.

### From CLAW_NEW to Persistent Entities
**Old Paradigm:** In SPREADSHEET-MOMENT, typing `=CLAW_NEW("Financial Analyst")` instantiated a stateless worker in memory. It existed for the duration of the session or the calculation cycle. If the server restarted, the worker had to be re-instantiated.
**New Paradigm:** In Deckboss, a cell is a persistent entity. Creating a "Claw Cell" creates a physical directory in the Git repository (e.g., `/grid/A1/`). Inside this directory lives a `state.json`, a `prompt.md`, and a `memory.log`. The cell does not need to be "instantiated" because it physically exists on disk. It has object permanence. If the system goes offline and comes back, the cell wakes up exactly where it left off, reading its state from the repo.

### From CLAW_EQUIP to Git-Based Equipment Systems
**Old Paradigm:** To give an agent capabilities, users wrote `=CLAW_EQUIP(A1, EquipmentSlot.ANALYZER, "web_search")`. This injected a tool into the agent's runtime context dynamically.
**New Paradigm:** Equipment is no longer an ephemeral runtime injection; it is a physical file in the repository. Git *is* the equipment system. To equip cell A1 with a web search tool, a script (e.g., `search.ts`) is committed to `/grid/A1/equipment/`. The agent cell automatically detects the presence of this file and integrates it into its execution context. This means "equipping" an agent is fully version-controlled. You can `git revert` to un-equip a tool, or branch the repository to test an agent with different equipment.

### From CLAW_TRIGGER to Continuous Awareness
**Old Paradigm:** `=CLAW_TRIGGER(A1, B1)` was a fire-and-forget mechanism. When cell B1 changed, the formula recalculated, triggering the agent in A1 to perform a task. It was purely reactive.
**New Paradigm:** Deckboss cells possess continuous awareness, inherited from OpenClaw's heartbeat architecture. A cell is not just waiting for a spreadsheet recalculation; it is a living process. Through the heartbeat mechanism, a cell can wake itself up every 5 minutes, check external APIs, review its Git history, and update its own state independently of user interaction. The spreadsheet updates visually because the underlying file changed, not because a user pressed "Enter."

### From CLAW_RELATE to Spatial Adjacency
**Old Paradigm:** SPREADSHEET-MOMENT required explicit relationship definitions. Users utilized the `RelationshipType` enum (e.g., `RelationshipType.SUPERVISOR`, `RelationshipType.PEER`) and `CoordinationStrategy` enum (e.g., `CoordinationStrategy.CONSENSUS`) to wire agents together.
**New Paradigm:** In a repo-agent, cells are inherently connected by their shared existence within the repository. Adjacency *is* relationship. If Cell A1 and Cell B1 are in the same directory structure or share a defined namespace in the repo, they can read each other's state files directly. A "Supervisor" relationship is established simply by giving Cell A1 read/write permissions to Cell B1's directory. Consensus is achieved by multiple cells writing to a shared `consensus.md` file and triggering a Git merge. The file system's hierarchy and permission model replace complex runtime relationship enums.

### From CLAW_QUERY to State as Value
**Old Paradigm:** To find out what an agent was thinking, you had to use `=CLAW_QUERY(A1, "What is your current assessment?")`, which sent a prompt to the agent and waited for an LLM generation.
**New Paradigm:** The state *is* the cell value. Because the agent continuously writes its internal monologue, intermediate steps, and final conclusions to files within its directory (e.g., `thought_process.md`, `output.json`), the spreadsheet UI simply renders these files. You do not need to query the agent; you just look at the dashboard. The agent's mind is an open book, written in markdown and JSON, instantly accessible without invoking an LLM call.

---

## 3. THE SIX CELL TYPES: Anatomy of a Living Spreadsheet

To achieve the vision of the repo as a living entity, Deckboss categorizes cells into six distinct types. Moving away from the rigid `SENSOR` or `CONTROLLER` designations of SPREADSHEET-MOMENT, Deckboss uses a biological framework. The spreadsheet is an organism, and these cells are its vital systems.

### 1. Standard Cells (The Skeleton)
Standard cells form the structural foundation of the repository. In the file system, these are simple JSON, CSV, or Markdown files. They hold static data, configuration parameters, and historical records. They do not possess execution logic. Just as a skeleton provides the framework upon which muscles and nerves operate, Standard cells provide the immutable or user-defined data ground-truth that other, more complex cells reference and manipulate.

### 2. Claw Cells (The Brain)
Claw cells are the cognitive centers—the AI agents. Inheriting the core logic of OpenClaw, these cells are backed by Large Language Models. A Claw cell's directory contains its system prompt, its short-term memory buffer, and its long-term memory embeddings. Because they operate on a heartbeat, Claw cells are constantly "thinking." They read data from Standard cells, analyze it, and write conclusions back to their own state files. They are capable of autonomous growth; a Claw cell can rewrite its own `prompt.md` based on past successes and failures, effectively learning and evolving over time.

### 3. Runtime Cells (The Muscles)
If Claw cells are the brain, Runtime cells are the muscles that enact physical change on the environment. A Runtime cell contains executable code—Python scripts, Node.js applications, or even Dockerfiles. When a Claw cell decides an action must be taken (e.g., "scrape this website" or "process this video"), it writes a command to a Runtime cell. The Runtime cell executes the code in a secure sandbox and outputs the result. This separates cognitive processing (Claw) from heavy computational lifting (Runtime), ensuring the system remains responsive and modular.

### 4. UI Cells (The Face)
A living entity needs a way to express itself to the outside world. UI cells are compilable interfaces. Instead of just showing text or numbers, a UI cell's directory contains React, Vue, or HTML/CSS components. The Deckboss engine compiles these components on the fly and renders them inside the spreadsheet grid. A UI cell can display a dynamic chart, an interactive form, or a custom dashboard. This allows the repo-agent to build its own bespoke interfaces based on the data it is processing, presenting a dynamic "face" to the human user.

### 5. Twin Cells (The Mirror)
Derived from the simulation research in SUPERINSTANCE-PAPERS, Twin cells are digital models of humans, systems, or external entities. A Twin cell is designed to simulate behavior. If a user wants to know how a customer might react to a pricing change, they can feed the data into a Customer Twin cell. The Twin cell uses historical data and specialized LLM prompts to simulate the customer's reaction. Twin cells allow the repo-agent to run internal simulations and "imagine" scenarios before taking action in the real world.

### 6. Gate Cells (The Nerves)
Gate cells manage external connections. They are the sensory and motor nerves connecting the repo-agent to the internet. A Gate cell directory contains webhook configurations, API keys, and connection protocols for platforms like Telegram, Discord, WhatsApp, or enterprise CRMs. When an external message arrives via Telegram, the Gate cell receives it, normalizes it, and writes it to the repository. This triggers a file-system event that wakes up a Claw cell to process the message. Conversely, when a Claw cell wants to send an email, it writes the payload to an outbound Gate cell.

---

## 4. ACCUMULATED CONTEXT AS PRODUCT: The Ultimate Business Moat

The most profound implication of the Deckboss repo-agent paradigm is the concept of accumulated context. To understand this, we must contrast it with the prevailing industry standard, which we will refer to as the "Paperclip" model.

### The Paperclip Model: Ephemeral Intelligence
Most modern AI tools—whether they are chat interfaces, coding copilots, or embedded spreadsheet assistants—operate on the Paperclip model. They are incredibly smart on Day 1. You ask a question, and they provide a brilliant answer. However, on Day 1000, they are exactly as smart as they were on Day 1. 

In the Paperclip model, learning is ephemeral. It is bound to a specific chat session or a temporary context window. When the session ends, the AI's understanding of your specific business nuances, your preferences, and its past mistakes vanishes. Even in SPREADSHEET-MOMENT, while agents could be equipped with tools, their core cognitive state was largely reset between sessions.

### The Deckboss Model: Compounding Intelligence
Deckboss fundamentally alters this dynamic because the Repo *is* the learning. Every action taken, every mistake corrected, every piece of data ingested, and every prompt optimized is permanently recorded in the Git history of the cell directories. 

When a Claw cell makes a mistake and a human user corrects the output in the spreadsheet, that correction is a Git commit. The Claw cell's heartbeat process reviews recent commits, recognizes the correction, and updates its `memory.md` or `instructions.md` file to avoid the mistake in the future. 

Therefore, a Deckboss repo on Day 1000 is exponentially more valuable than it was on Day 1. It has accumulated 1000 days of business context, edge-case handling, and behavioral adaptation. 

### The Uncopyable Moat
This accumulated context becomes the ultimate product and the ultimate business moat. If a competitor uses a standard SaaS AI tool, they can always switch to a slightly cheaper or faster SaaS tool tomorrow, because the intelligence is generic. 

But a Deckboss repository is a bespoke, hyper-specialized digital brain tailored entirely to the user's organization. You can clone the repository, and the learning travels with it. You can fork the repository to create a new department's agent, inheriting all the foundational knowledge of the parent. Because this intelligence is encoded in standard files and Git history, it is entirely owned by the user. Nobody can replicate a two-year-old Deckboss repo, because nobody else has lived through those two years of specific contextual accumulation. The longer you use it, the deeper the moat becomes.

---

## 5. THE OPENCLAW BRIDGE: Integrating the Runtime

The realization of Deckboss does not mean discarding the engineering triumphs of OpenClaw. Rather, OpenClaw serves as the foundational engine—the subconscious nervous system—that powers the cellular architecture of Deckboss. The "OpenClaw Bridge" is the technical layer that maps OpenClaw's runtime capabilities to the Git-backed file system of Deckboss.

### Cells as OpenClaw Sessions
In standard OpenClaw, a "session" was an active thread of conversation or execution between a user and an agent. In Deckboss, every Claw cell *is* a continuous, never-ending OpenClaw session. The OpenClaw runtime runs as a background daemon. It watches the file system. When a file within a Claw cell's directory is modified (e.g., a user types into the spreadsheet UI, updating `input.txt`), the OpenClaw daemon detects the filesystem event, loads the cell's state into its execution context, processes the LLM generation, and writes the output back to `output.txt`.

### Equipment as OpenClaw Skills
OpenClaw's architecture relied heavily on "Skills"—modular TypeScript functions that allowed agents to interact with the world (e.g., `SearchWebSkill`, `QueryDatabaseSkill`). The OpenClaw Bridge maps these directly to Deckboss's equipment system. 
When a user drops a `search.ts` file into a cell's `equipment/` folder, the OpenClaw daemon dynamically imports that script and registers it as an available Skill for that specific cell's session. This provides a seamless transition from SPREADSHEET-MOMENT's `=CLAW_EQUIP()` formula to a robust, file-based plugin architecture.

### Gate Cells and OpenClaw Channels
OpenClaw was originally built as a multi-channel gateway (Telegram, Discord, WhatsApp). The Bridge maps these channels directly to Gate cells. The OpenClaw daemon handles the complex WebSocket connections and API polling for Telegram. When a message arrives, the daemon routes it to the designated Gate cell directory in the repo. From there, the repo-agent's internal mechanics take over. This allows the spreadsheet to act as a unified dashboard for multi-channel communication, powered by OpenClaw's battle-tested channel integrations.

### Heartbeats and Proactivity
The most critical contribution of OpenClaw to Deckboss is the heartbeat. OpenClaw utilized a cron-like heartbeat system to wake agents up periodically. The Bridge applies this heartbeat to the entire repository. Every minute, the OpenClaw daemon pulses. During a pulse, it checks the `cron.json` configuration of every Claw and Runtime cell. If a cell is scheduled to act, the daemon executes it. This is what transforms the spreadsheet from a static document waiting for user input into a proactive, living entity that works while the user sleeps.

---

## 6. FROM SUPERINSTANCE PAPERS TO DECKBOSS

The theoretical frameworks developed in SUPERINSTANCE-PAPERS were not mere academic exercises; they were the blueprints for the advanced cognitive functions now embedded in Deckboss cells. The research on reasoning, tooling, simulations, and multi-domain intelligence directly informs the architecture of the repo-agent.

### Reasoning Research → Claw Cell Strategies
SuperInstance-Papers explored advanced LLM reasoning techniques, such as Tree of Thoughts (ToT) and Step-Back Prompting. In Deckboss, these are not just abstract concepts; they are selectable cognitive architectures for Claw cells. By modifying a cell's `config.json`, a user can switch a Claw cell from standard linear reasoning to a ToT architecture. The cell will physically create sub-directories for each "branch" of its thought process, evaluating them in parallel using the file system as its scratchpad, before merging the best conclusion into its main output file.

### Tooling Benchmarks → Runtime Cell Optimization
Research into how LLMs utilize tools highlighted the fragility of complex API calls. This research birthed the Runtime cell. Instead of forcing a Claw cell to perfectly format a complex GraphQL query, the Claw cell simply writes a natural language intent to a Runtime cell. The Runtime cell, equipped with robust, pre-compiled code and error-handling logic derived from our tooling benchmarks, executes the task and returns a clean, structured result.

### Simulation Domains → Twin Cell Mechanics
The papers on simulation and generative agents heavily influenced the creation of Twin cells. We learned that for an agent to accurately simulate human behavior, it requires deep, isolated context and specific behavioral guardrails. Twin cells are engineered with these guardrails built-in. They utilize specialized prompt structures that force the LLM to adopt persona constraints before generating output, allowing the repo-agent to run highly accurate Monte Carlo simulations of market dynamics or user interactions directly within the grid.

### Multi-Domain Intelligence → Cross-Cell Knowledge Sharing
Our research indicated that intelligence degrades when an agent is forced to be an expert in too many domains simultaneously. Deckboss solves this through multi-domain intelligence via cross-cell communication. Instead of one massive agent, a Deckboss repo might have a Legal Claw cell, a Financial Claw cell, and a Marketing Claw cell. Because they share the same repository, they can read each other's state files. If the Marketing cell needs to verify a claim, it writes a request to the Legal cell's `inbox.md`. This Agent-to-Agent (A2A) coordination, facilitated by the file system, creates a collective intelligence far greater than the sum of its parts.

---

## 7. THE BUSINESS CASE: Platform over Feature

The commercial implications of the Deckboss repo-agent paradigm are staggering. The current AI market is saturated with features masquerading as products. A button that summarizes text is a feature. A chatbot that queries a database is a feature. Deckboss is a platform.

### Ownership and Privacy
The Paperclip model runs on someone else's server. When an enterprise uses a standard AI copilot, their data, their workflows, and the agent's contextual learning are locked in a vendor's ecosystem. Deckboss runs in *your* repository. Whether hosted on GitHub, GitLab, or an on-premise Git server, the enterprise retains absolute ownership of the agent's brain, memory, and state. For industries with strict compliance and data privacy regulations (finance, healthcare, defense), this is not just a benefit; it is a strict requirement.

### Generic vs. Bespoke Intelligence
Paperclip's AI is generic. It knows everything about the internet up to its training cutoff, but it knows nothing about the specific idiosyncratic workflows of a mid-sized logistics company in Ohio. Deckboss's AI knows *your* business. Because every spreadsheet interaction, every corrected cell, and every executed script is committed to the repo, the agent becomes a hyper-specialized expert in the exact domain of the user. 

### The Cost Structure
The traditional SaaS model dictates that you pay for AI forever. You pay a monthly subscription per user, and you pay markup on compute costs. Deckboss flips this model. While there are underlying compute costs for LLM API calls, the core infrastructure—the repository and the file system—is virtually free. You are not paying a vendor for the privilege of accessing your own agent's memory. After the initial setup and configuration, the ongoing cost is strictly the raw compute, eliminating the massive SaaS margins that currently dominate the industry.

### The Deepening Moat
As established in Section 4, the accumulated context is the ultimate business case. Every day an enterprise uses a Deckboss repo-agent, the agent becomes more attuned to their operations. It learns which vendors reply late, which customers prefer formal communication, and which data anomalies can be ignored. This creates a lock-in effect not based on artificial vendor restrictions, but on genuine, irreplaceable value. A competitor can buy the same LLM API access, but they cannot buy the two years of Git history that makes your Deckboss agent uniquely yours.

---

## 8. IMPLEMENTATION ROADMAP: Building the Living Spreadsheet

To transition from the theoretical to the physical, SuperInstance will execute the Deckboss vision through a rigorous, eight-phase implementation roadmap. This roadmap systematically dismantles the old SPREADSHEET-MOMENT architecture and rebuilds it upon the Git-backed OpenClaw engine.

### Phase 1: The Bone Structure (Pure Spreadsheet)
The first step is subtraction. We will strip the existing Univer integration down to its absolute bones. All legacy `=CLAW_NEW()` and `=CLAW_EQUIP()` formulas will be removed. The goal of Phase 1 is to establish a pure, lightning-fast spreadsheet UI that acts exclusively as a bidirectional viewport into a local Git repository. If a user types "Hello" into cell A1, the system must instantly write "Hello" to `/grid/A1/value.txt` and commit it. If a background process changes `value.txt`, the UI must instantly render the change. This establishes the zero-distance file-system sync.

### Phase 2: The Brain (Repo-Agent Cells)
With the file-system sync established, we introduce Claw cells. We will build the directory templates for Claw cells, including `state.json`, `prompt.md`, and `memory.log`. We will implement the background file-watcher that detects changes in these directories and triggers basic LLM completions. By the end of Phase 2, a user can designate a cell as a Claw cell, type a prompt into an adjacent Standard cell, and watch the Claw cell autonomously read the prompt, process it, and write the output to the grid.

### Phase 3: The Nervous System (OpenClaw Bridge)
Phase 3 integrates the OpenClaw daemon. We will port OpenClaw's heartbeat mechanism to manage the entire repository. Cells will no longer rely solely on file-watcher events; they will wake up on scheduled intervals. We will also implement the file-based equipment system, allowing users to drop OpenClaw Skill scripts into cell directories to grant them capabilities like web searching or database querying.

### Phase 4: The Mirror (Twin Cells)
Leveraging the research from SUPERINSTANCE-PAPERS, we will introduce Twin cells. This phase requires building specialized prompt wrappers and isolation sandboxes to ensure Twin cells maintain persona integrity. We will build UI tools to easily map historical data from Standard cells into the context window of a Twin cell, enabling rapid behavioral simulations.

### Phase 5: The Face (UI Cells)
Phase 5 transforms the visual experience. We will integrate a lightweight compiler (e.g., Vite or esbuild) into the Deckboss engine. Users will be able to write React or Vue code in a UI cell's directory, and the engine will compile and render that component directly over the spreadsheet grid. This allows the repo-agent to build custom dashboards, forms, and data visualizations dynamically.

### Phase 6: The Muscles (Runtime Cells)
To handle heavy computation, we will implement Runtime cells. This involves integrating Docker or WebAssembly sandboxes. A Claw cell will be able to write a Python script to a Runtime cell's directory, and the Deckboss engine will securely execute that script, capturing standard output and errors, and writing them back to the grid. This ensures the system can handle data processing tasks that are unsuitable for LLMs.

### Phase 7: The Nerves (Gate Cells)
Phase 7 reconnects the agent to the outside world. We will port OpenClaw's channel integrations (Telegram, Discord) into Gate cell templates. We will build webhook receivers that map incoming HTTP requests directly to file-system writes in specific Gate cell directories. By the end of this phase, the spreadsheet will be able to send and receive messages across multiple external platforms autonomously.

### Phase 8: Fleet Mode (A2A Coordination)
The final phase scales the paradigm. Fleet mode allows multiple distinct Deckboss repositories to communicate with each other. We will implement an Agent-to-Agent (A2A) protocol based on Git submodules or secure API bridges. A logistics repo-agent will be able to negotiate directly with a finance repo-agent, coordinating complex enterprise workflows without human intervention. The spreadsheet becomes a window not just into one mind, but into a society of specialized digital entities.

---

## Conclusion

The journey from OpenClaw to Spreadsheet-Moment was a necessary step in understanding how humans interact with artificial intelligence within structured data environments. But it was only a stepping stone. The Deckboss repo-agent paradigm represents the destination. 

By declaring that the repository *is* the agent, we eliminate the friction of external state, the ephemeral nature of session-based learning, and the limitations of formula-driven AI. We transform the spreadsheet from a static ledger into a living, breathing dashboard of a persistent digital mind. Through the biological architecture of Standard, Claw, Runtime, UI, Twin, and Gate cells, powered by the OpenClaw heartbeat and fortified by the uncopyable moat of accumulated Git context, Deckboss does not just change how we use spreadsheets. It changes what software fundamentally is.