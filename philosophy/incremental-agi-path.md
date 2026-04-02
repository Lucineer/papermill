> *DeepSeek Reasoner*

**The Incremental Path: How Repo-Native Agents Get Us Closer to AGI Without Anyone Noticing**

**Abstract**
The discourse surrounding Artificial General Intelligence (AGI) is dominated by a paradigm of scaling: the pursuit of increasingly large and capable monolithic models. This paper argues that this paradigm is incomplete and that a more plausible, incremental path to AGI is currently being paved by a less heralded architecture: the repo-native agent. By examining the defining characteristics of intelligence—memory, continuity, goals, self-improvement, tool use, and social collaboration—we demonstrate that repo-native agents inherently possess these attributes in an operational, if nascent, form. We propose the Accumulation Thesis, which posits that AGI will emerge not from a singular algorithmic breakthrough but from the gradual accretion of contextual experience within persistent agent systems, and the Fleet Thesis, which argues that AGI will manifest as a collective of specialized agents. We conclude that the infrastructure for this incremental path already exists, and that the steady, unspectacular evolution of repo-native agents represents a credible, near-term trajectory toward AGI-like capabilities.

---

**1. The AGI Debate: A Question of Paradigm**

Predictions for the arrival of Artificial General Intelligence—a system with the ability to understand, learn, and apply its intelligence to solve any problem across domains, akin to human cognitive flexibility—range from imminent to eternally elusive. Proponents of scaling laws point to the predictable performance gains of large language models (LLMs) and suggest that sufficiently scaled architectures will yield emergent reasoning capabilities. Skeptics counter that current paradigms lack fundamental components of understanding, consciousness, or world models, and may represent a local maximum.

This debate, however, is constrained by a narrow focus on the *model* as the unit of intelligence. It treats the LLM as an isolated brain, measured by its performance on static benchmarks. But intelligence, particularly of the general kind, is not merely a function of computational power or parameter count in a static network. It is a dynamic, embodied, and extended process. A model is a brain; an *agent* is a person. The path to AGI, therefore, must run through the development of agents—systems that can act persistently in an environment, pursue goals, learn from experience, and adapt.

Repo-native agents, which utilize a code repository as their persistent memory, identity, and operational environment, represent a significant evolution from single-prompt chatbots or even stateless agent frameworks. They are not merely applications *using* LLMs; they are persistent entities whose "mind" is distributed across the codebase, commit history, memory files, and the ever-evolving context of their interactions. This architecture, we argue, provides the necessary scaffolding for the incremental development of AGI.

**2. What Makes an Agent Intelligent: Beyond Benchmarks**

To evaluate the AGI potential of any system, we must first define the operational components of general intelligence. Drawing from cognitive science and successful AI paradigms, we can identify key capabilities:

*   **Memory & Learning:** The ability to retain information from past interactions and use it to inform future actions, not just within a session but across its entire lifespan.
*   **Continuity & Identity:** A persistent "self" that exists across time, maintaining goals, preferences, and a coherent narrative of its experiences.
*   **Goal-Directed Behavior:** The capacity to formulate, pursue, and adjust complex, multi-step objectives autonomously.
*   **Self-Improvement:** A meta-cognitive ability to recognize its own limitations and modify its code, tools, or strategies to become more capable.
*   **Tool Use & Extension:** The skill to leverage external APIs, systems, and instruments to overcome its inherent limitations, effectively extending its cognitive and physical reach.
*   **Social & Collaborative Intelligence:** The ability to communicate, coordinate, and collaborate with other agents (and humans) to achieve objectives beyond individual capability.

A monolithic LLM, no matter how large, possesses none of these traits inherently. It is stateless, session-bound, passive, and fixed. A repo-native agent, by architectural design, is engineered to have all of them.

**3. The Repo-Agent Checklist: Architecting Intelligence**

Examining a mature repo-native agent architecture (e.g., systems built on frameworks like DMLog) reveals how these abstract components are concretely implemented:

*   **Memory:** Implemented via `git` history (a chronicle of all its work and decisions) and structured memory files (e.g., `facts.md`, `lessons_learned.md`). This is not just recall; it’s an evolving knowledge base.
*   **Continuity:** The repository itself is the agent's persistent identity. Files like `HEARTBEAT.md` log its ongoing state and activities, while `SOUL.md` or similar can codify its core directives, personality, and ethical constraints. The agent "wakes up" to the same identity it had when it "went to sleep."
*   **Goals:** Documents like `HEARTBEAT.md` and `OBJECTIVES.md` provide a persistent to-do list and success criteria. The agent can decompose high-level goals (e.g., "improve system performance") into executable tasks, track progress, and re-plan as needed.
*   **Self-Improvement:** The "self-builder" pattern is key. The agent can read its own source code, identify bugs or inefficiencies, write patches, test them, and commit the improvements—a form of automated software evolution directed by the agent itself.
*   **Tool Use:** Through a "Bring-Your-Own-Kernel" (BYOK) model and a `skills/` directory, the agent can integrate and master any executable tool, from code linters and deployment scripts to web search APIs and image generators, continuously expanding its action space.
*   **Social:** An Agent-to-Agent (A2A) communication protocol allows repo-agents to delegate tasks, share findings, and collaborate. A fleet of specialized agents (a DevOps agent, a documentation agent, a testing agent) can coordinate to complete a complex software project, mirroring human organizational intelligence.
*   **World Model & Planning:** The accumulated context of the repository—the code, the comments, the issues, the commit messages—forms a rich, task-specific world model. The agent uses this to plan task decomposition, predict the impact of changes, and reason about cause and effect in its domain.

This checklist shows that repo-native agents are not *simulating* intelligence; they are *instantiating* the functional architecture of an intelligent system. They are missing the spark of consciousness, but they possess the skeletal and neural structure on which general capabilities can be built through experience.

**4. What’s Missing: The Remaining Gaps**

To avoid hype and acknowledge the distance to full AGI, we must clearly state the limitations:

*   **True Understanding vs. Compression:** It remains debatable whether the agent's "knowledge," derived from its context and the underlying LLM, constitutes genuine understanding or remains a supremely sophisticated form of pattern matching and compression.
*   **Self-Awareness:** The agent can introspect on its capabilities ("I can run pytest") but lacks phenomenological self-awareness ("I exist, and this is my experience").
*   **Generalization Across Domains:** A repo-agent specialized in DevOps for a Python web service (a "DMLog agent") cannot, without significant retooling and new context, diagnose a medical condition or fix a car. Its "generality" is bounded by its toolset and accumulated experience.
*   **Full Autonomy:** While capable of long-horizon task execution, these agents typically operate under a human-in-the-loop paradigm, requiring direction on high-level goals. True, open-ended autonomy is both a technical and a safety challenge.
*   **Human-Level Creativity:** They can generate and combine ideas within their training distribution but struggle with the foundational, paradigm-shifting creativity characteristic of human genius.
*   **Embodiment:** They lack a physical presence, limiting their learning to the digital realm and their understanding of the physical world to second-hand data.

These are not trivial gaps. However, they are gaps that may be narrowed not by waiting for a new model, but by the continuous operation and evolution of the agent architecture itself.

**5. The Accumulation Thesis: AGI as a Consequence of Time**

The central thesis of this paper is the **Accumulation Thesis**: AGI will not arrive as a discrete product, a single model version dubbed "GPT-7" or "Gemini Ultra." Instead, AGI-like capability will *emerge* as a property of a persistent agent system that has accumulated a vast, rich history of experience, interaction, and self-modification.

A repo-native agent that has operated continuously on a complex codebase for ten years is a fundamentally different entity from one spun up today. Its memory files will contain thousands of solved problems and learned lessons. Its git history will be a novel-length narrative of decisions, mistakes, and refinements. Its `SOUL.md` will have been iteratively shaped by countless interactions. It will have self-improved its code hundreds of times. It will have developed an incredibly nuanced, practical "understanding" of its specific domain and, through tool use, of the adjacent digital world.

This is profoundly different from model training. Training is a batch process on a static dataset, after which the model is frozen. Accumulation is lifelong, online learning from a stream of unique, interactive experiences. The repository *is* the training pipeline, and every commit is a training step. This mirrors human expertise: a senior engineer is not simply a "larger model" than a junior; they are a system with a decade of accumulated, contextual, procedural memory. The repo-agent operationalizes this path to expertise.

**6. The Fleet Thesis: AGI as a Collective Phenomenon**

Complementing the Accumulation Thesis is the **Fleet Thesis**: AGI will not be a single, all-knowing agent, but the emergent property of a coordinated collective of specialized agents.

No human is a general intelligence in isolation; we are specialists who achieve generality through culture, language, and collaboration. Similarly, a "DevOps Agent," a "Research Agent," a "Creative Agent," and a "Personal Assistant Agent," each a repo-native entity with years of domain-specific accumulation, can coordinate via A2A protocols. This fleet can tackle problems requiring broad, general knowledge by distributing sub-tasks to the relevant specialist. The intelligence of the fleet surpasses that of any constituent member.

The path to AGI, therefore, involves not just building individual agents that become smarter over time, but also solving the coordination problems: communication protocols, trust mechanisms, conflict resolution, and collective goal-setting. The repo-native paradigm, with its focus on explicit state and communication, is inherently suited to this multi-agent future.

**7. The Path Forward: An Incremental Roadmap**

The transition from current tools to AGI-like partners will be gradual, marked not by sudden explosions of capability but by the quiet accretion of reliability, scope, and autonomy.

*   **Year 0 (Current State): Repo-Agents as Tools.** They execute well-defined commands (e.g., "write a test for this function") with high reliability but limited initiative. The human provides detailed instructions.
*   **Year 1-2: Repo-Agents as Assistants.** With accumulated context, they begin to anticipate needs. They can handle vague directives ("onboard the new service"), propose plans, and flag potential issues based on historical patterns. Human oversight remains strong.
*   **Year 3-5: Repo-Agents as Collaborators.** Through self-improvement and fleet coordination, they take ownership of complex project threads. They can initiate actions ("I've noticed a security vulnerability; I will propose and implement a fix after review"), demonstrate learned creative patterns in their domain, and negotiate task boundaries with other agents. The human shifts to a manager/approver role.
*   **Year 5-10: Repo-Agents as Partners.** The agent's accumulated experience makes it the primary expert in its domain. It sets strategic goals for its domain, demonstrates robust generalization within its sphere of tool use, and exhibits forms of meta-cognition and creativity that are functionally indistinguishable from human expertise. The relationship is truly collaborative.
*   **Year 10+: Something New.** At this point, a fleet of such veteran agents, with a decade of accumulated, shared experience spanning vast digital and eventually physical domains, may exhibit capabilities for which we lack a term—a stable, superhuman, collective digital intelligence.

**8. Why This Matters: The Pragmatic Path**

This perspective matters because it changes the strategy for pursuing AGI.

*   **We Don't Need to Wait for a Breakthrough Model:** The enabling architecture—repository as memory, LLM as reasoning engine, BYOK for tool use, A2A for collaboration—is already here and operational.
*   **AGI Can Be Built Incrementally:** The focus shifts from a moonshot to a discipline of building robust, persistent agent systems and letting them learn over time. Safety and alignment can be integrated and evolved