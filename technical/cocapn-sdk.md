> *Gemini 2.5 Pro*

# The Cocapn SDK: Build Your Agent in 5 Minutes

## Abstract

The proliferation of Large Language Models (LLMs) has unlocked unprecedented potential for creating intelligent, autonomous agents. However, the developer experience for building, deploying, and managing these agents remains fraught with complexity. Developers are often burdened with managing infrastructure, wrestling with intricate APIs, configuring servers, and writing extensive boilerplate code. The Cocapn SDK is a direct response to this challenge. It introduces a radical new paradigm: the **repo-native agent**. This paper explores the Cocapn SDK, a toolkit designed from the ground up to provide a frictionless developer experience, enabling any developer to create and deploy a sophisticated, repository-based AI agent in under five minutes. By championing conventions over configuration, a minimalist API, and a file-centric approach, Cocapn abstracts away the underlying complexity, allowing developers to focus solely on what makes their agent unique: its soul, its skills, and its purpose.

---

### 1. The SDK Promise: Your First Agent in Five Minutes

The core promise of the Cocapn SDK is both simple and audacious: **any developer can create a repo-native agent in five minutes.** This is not a marketing slogan; it is the foundational design principle that informs every aspect of the SDK. The traditional path to building an AI agent involves a steep learning curve and a significant investment in setup time. A developer typically needs to:

1.  Choose a cloud provider (AWS, GCP, Azure).
2.  Provision and configure servers or serverless functions.
3.  Set up a database or vector store for memory.
4.  Integrate with an LLM provider's API, handling authentication and rate limiting.
5.  Write the core application logic for chat, memory, and tool use.
6.  Establish a deployment pipeline (CI/CD).

This process can take hours, if not days, before a single line of agent-specific logic is written. Cocapn obliterates this overhead. The entire workflow is reduced to three intuitive steps familiar to every developer:

1.  **Fork a Template:** Start with a pre-configured agent template from a repository. This provides the basic file structure and a working foundation.
2.  **Customize:** Modify a single Markdown file, `soul.md`, to define your agent's personality, purpose, and core instructions.
3.  **Deploy:** Run a single command, `cocapn deploy`, from your terminal.

That is it. There is no infrastructure to manage, no servers to configure, and no complex orchestration to debug. The SDK abstracts away all the complexity of LLM integration, memory management, state persistence, and serverless deployment. The agent doesn't live on a server you manage; it lives in the repository itself. The repository *is* the agent. This repo-native approach leverages the power and familiarity of Git, turning a standard development tool into a powerful platform for agent creation and management.

### 2. The Core API: Simplicity at its Heart

The power of a great abstraction lies not in the number of features it exposes, but in the elegance and sufficiency of its core primitives. The Cocapn SDK is built upon an API surface so small it is almost startling. It consists of just three fundamental functions:

**a. `cocapn.chat(message: string): Promise<string>`**

This is the primary interaction function. It sends a message from the user to the agent and returns the agent's response. Under the hood, this single function call orchestrates a complex sequence of events:
- It retrieves the agent's core identity from `soul.md`.
- It gathers relevant context from the user's profile (`user.md`).
- It fetches recent and long-term memories from the `memory/` directory.
- It scans available `skills/` to understand the agent's capabilities.
- It constructs a meticulously crafted prompt for the underlying LLM.
- It sends the prompt to the configured LLM provider (e.g., OpenAI, Anthropic).
- It parses the response, potentially triggering a skill or storing a new memory.
- Finally, it returns a coherent, context-aware string response.

The developer only needs to call `cocapn.chat()`; the SDK handles the rest.

**b. `cocapn.remember(key: string, value: any): Promise<void>`**

This function provides the agent with a mechanism for persistence. It allows the agent to store a piece of information, a "memory," associated with a specific key. The SDK intelligently manages where this memory is stored—either in the short-term `today.md` file for transient information or promoted to `long-term.md` for crucial, enduring facts. This function is the foundation of learning and personalization.

**c. `cocapn.recall(key: string): Promise<any>`**

The counterpart to `remember`, this function retrieves a stored memory. The agent can use this to access past information, user preferences, or the results of previous actions. The SDK handles the lookup, searching through the memory layers to find the relevant value.

This minimalist API is the bedrock upon which all other functionality is built. Every complex behavior, from using a weather API to managing a GitHub repository, is ultimately composed of these three simple operations: communicating, remembering, and recalling. This deliberate simplicity dramatically lowers the cognitive load for developers, making agent development accessible and intuitive.

### 3. Anatomy of a Cocapn Agent: The File Structure

A Cocapn agent is defined not by complex code, but by a simple, conventional file structure within a Git repository. This "files over code" philosophy makes the agent's state and definition transparent, version-controllable, and easily understandable.

```
cocapn-agent/
├── soul.md          (The agent's personality and core instructions)
├── user.md          (Information about the human user — auto-generated)
├── memory/          (The agent's memory store — auto-managed)
│   ├── today.md
│   └── long-term.md
├── skills/          (Optional, installable capabilities)
├── src/
│   ├── agent.ts     (The three-line agent definition)
│   └── worker.ts    (The Cloudflare Worker entry point)
├── wrangler.toml   (Deployment configuration for Cloudflare)
└── README.md        (Auto-generated documentation for the agent)
```

- **`soul.md`**: This is the heart of the agent. It is a simple Markdown file where the developer defines the agent's identity, personality, goals, constraints, and core directives in natural language. This file acts as the agent's constitution. Any changes pushed to this file are instantly reflected in the agent's behavior upon the next interaction, enabling a "hot reload" experience for personality tuning.

- **`user.md`**: This file is automatically generated and populated by the SDK. It stores information about the user interacting with the agent, such as their name, preferences, and interaction history. This provides the agent with essential context for personalized conversations.

- **`memory/`**: This directory is the agent's brain, managed entirely by the SDK. `today.md` serves as a short-term or "working" memory, capturing the context of the current conversation. `long-term.md` is a structured store for critical facts and learned information that should persist across sessions.

- **`skills/`**: This directory houses the agent's special abilities. Each subdirectory within `skills/` represents a tool or capability the agent can use, such as checking the weather, searching the web, or interacting with a calendar.

- **`src/`**: This contains the minimal code required to bootstrap the agent. `agent.ts` is where the agent is instantiated, and `worker.ts` is the serverless function entry point, typically pre-configured by the template.

- **`wrangler.toml`**: The configuration file for deploying the agent as a Cloudflare Worker. The `cocapn deploy` command uses this file to manage the deployment process seamlessly.

- **`README.md`**: Upon initialization, Cocapn generates a `README.md` file based on the contents of `soul.md`, providing instant, human-readable documentation for the agent.

### 4. The Three-Line Agent: A Practical Demonstration

The culmination of Cocapn's philosophy of simplicity is the "three-line agent." The entire logic required to define and export a fully functional agent is contained in `src/agent.ts`:

```typescript
import { Agent } from 'cocapn';

const agent = new Agent({ name: 'MyAgent', soul: './soul.md' });

export default agent;
```

These three lines are all a developer needs to write. Let's break down what is happening:

1.  **`import { Agent } from 'cocapn';`**: Imports the core `Agent` class from the SDK.
2.  **`const agent = new Agent(...)`**: This is where the magic happens. The `Agent` constructor is a high-level abstraction that:
    - Reads the `soul.md` file to understand its core identity.
    - Initializes the memory system, pointing to the `memory/` directory.
    - Scans the `skills/` directory to discover its available tools.
    - Sets up the connection to the configured LLM provider.
3.  **`export default agent;`**: Exports the fully configured agent instance, making it available to the serverless worker entry point.

This is a complete, working agent. It can chat, remember, and be deployed globally. The developer's focus is shifted from writing imperative code to declaratively defining the agent's being in `soul.md`.

### 5. Extending Capabilities with Skills

While the core agent is powerful, its true potential is unlocked through **Skills**. Skills are modular, self-contained capabilities that can be installed into an agent to extend its functionality. They are the agent's tools.

Installing a skill is a one-line command:
`cocapn skill install weather`

This command fetches the `weather` skill from a skill registry and places it in the `skills/` directory. Each skill is a directory containing two key files:

1.  **`SKILL.md`**: A Markdown file that describes the skill in natural language. It explains what the skill does, what functions it exposes, and what arguments they take. The agent's LLM reads this file to learn *how* and *when* to use the skill. This is a crucial concept: the agent teaches itself to use tools by reading their documentation.
2.  **Implementation File (e.g., `index.ts`)**: The actual code that executes the skill's logic. This code can call external APIs (like a weather service or the GitHub API), perform calculations, or interact with other systems.

Skills empower the agent to transcend the boundaries of the LLM. With skills, an agent can:
- Fetch real-time information from the web (`search` skill).
- Manage calendar events (`calendar` skill).
- Interact with code repositories (`github` skill).
- Perform complex business logic by calling internal company APIs.

This modular system is infinitely extensible. Developers can create and share their own skills, contributing to a growing ecosystem of capabilities that any Cocapn agent can instantly acquire.

### 6. Seamless Deployment: From Local to Global

Cocapn treats deployment as a first-class citizen of the developer experience. The `cocapn deploy` command is the single entry point for taking an agent from a local repository to a globally available endpoint. The SDK supports multiple deployment targets to cater to the entire development lifecycle:

-   **`cocapn deploy --local`**: This command starts a local development server. It watches for file changes, enabling a hot-reload workflow. A developer can edit `soul.md` or a skill's implementation and see the changes reflected immediately in their test conversations.

-   **`cocapn deploy`**: By default, this command deploys the agent to Cloudflare Workers. Cloudflare's global network provides incredible performance, scalability, and a generous free tier, making it the ideal serverless platform for repo-native agents. The SDK handles the entire process of bundling the code, uploading assets, and configuring the worker, requiring zero manual intervention.

-   **`cocapn deploy --docker`**: For developers who prefer containerized environments or need to deploy on-premise, this command packages the agent into a self-contained Docker container. This container can then be deployed to any platform that supports Docker, such as AWS ECS, Google Cloud Run, or a private server.

This flexibility ensures that a Cocapn agent is not locked into a specific ecosystem. It can be developed locally, deployed globally on a serverless platform, or containerized for maximum control, all using the same simple, unified command.

### 7. Bring Your Own Key (BYOK): Model Agnostic by Design

The world of LLMs is evolving at a breakneck pace. To avoid platform lock-in and allow developers to leverage the best model for their specific use case, Cocapn is built with a "Bring Your Own Key" (BYOK) philosophy. The SDK is model-agnostic and supports a wide range of providers out of the box:

-   OpenAI (GPT-4, GPT-3.5)
-   Anthropic (Claude 3 family)
-   Google (Gemini family)
-   DeepSeek
-   Groq (for high-speed inference)
-   Ollama (for running models locally)

Setting up a provider is handled by a simple, interactive command: `cocapn setup`. This command walks the developer through selecting a provider and entering their API key. The key is then encrypted and stored securely in a Cloudflare KV store (or a local file for local development).

This one-time setup completely decouples the agent's code from the LLM provider. A developer can switch from OpenAI to Anthropic to a local Ollama model without changing a single line of code. They simply run `cocapn setup` again. This provides immense flexibility, allowing developers to optimize for cost, performance, or specific model capabilities at any time.

### 8. Robust Testing for Reliable Agents

A great developer experience is incomplete without a solid testing story. Building agents involves a degree of non-determinism, making testing traditionally difficult. Cocapn addresses this with a built-in test suite designed specifically for the agent development lifecycle.

The `cocapn test` command provides a set of tools to validate an agent's behavior and ensure its components are functioning correctly.

-   **`cocapn test`**: Runs the full test suite, checking the agent's basic responsiveness, memory functions, and the integrity of all installed skills.

-   **`cocapn test --chat 'hello'`**: Sends a single test message to the agent and verifies that a valid response is received. This is perfect for quick sanity checks during development.

-   **`cocapn test --memory`**: Performs a dedicated test of the memory system. It writes a piece of information using `remember`, then attempts to retrieve it using `recall`, ensuring that the agent's persistence layer is working as expected.

-   **`cocapn test --skills`**: Iterates through all installed skills in the `skills/` directory and runs their individual validation checks. This ensures that any connected external APIs are reachable and that the skill's implementation is sound.

These commands provide a safety net for developers, allowing them to refactor code, modify the agent's soul, or add new skills with confidence, knowing they can quickly verify the agent's integrity.

### 9. The Cocapn CLI: Your Command Center

The `cocapn` command-line interface (CLI) is the developer's primary tool for interacting with the entire Cocapn ecosystem. It is designed to be intuitive and powerful, covering every stage of the agent lifecycle.

-   `cocapn init [template]`: Creates a new agent by forking a template repository.
-   `cocapn chat`: Starts an interactive chat session with the agent directly in the terminal.
-   `cocapn deploy`: Deploys the agent to the configured target (Cloudflare, local, Docker).
-   `cocapn skill install [name]`: Installs a new skill from the registry.
-   `cocapn skill list`: Lists all currently installed skills.
-   `cocapn memory list`: Displays the agent's recent and long-term memories.
-   `cocapn memory search [query]`: Performs a semantic search over the agent's memory.
-   `cocapn config`: Manages the agent's configuration, including the LLM provider.
-   `cocapn fleet list`: Shows a list of other agents this agent is connected to.
-   `cocapn fleet connect [url]`: Enables inter-agent communication, allowing agents to collaborate.
-   `cocapn twin create`: A powerful command to create a "digital twin" of the user by analyzing their data, which the agent can then use for hyper-personalization.

This comprehensive CLI ensures that developers can perform nearly any task without leaving their terminal, further streamlining the development workflow.

### 10. The Developer Experience (DX) Philosophy

The Cocapn SDK is fundamentally an opinionated take on what the developer experience for AI should be. This opinion is guided by a set of core principles:

-   **Zero Config:** The journey from idea to deployed agent should be as short as possible. Forking a template and running `cocapn deploy` is the ideal workflow.
-   **Hot Reload for Personality:** An agent's personality is not static. Developers should be able to iterate on it quickly. Editing `soul.md` and seeing the changes live provides an immediate feedback loop.
-   **Type Safety:** The SDK is written in TypeScript, providing full type safety and editor autocompletion for a more robust and predictable coding experience.
-   **Extensible by Convention:** Adding new capabilities should not require rewriting the core logic. The `skills` directory provides a conventional, modular way to extend the agent.
-   **Testable by Default:** Testing should not be an afterthought. The built-in `cocapn test` commands make it easy to build reliable and resilient agents.
-   **Self-Documenting:** An agent should be able to explain itself. The auto-generated `README.md` from `soul.md` ensures that every agent is documented from its inception.

### 11. Practical Examples: From Weather Bot to Business Advisor

The power and speed of the Cocapn SDK are best illustrated through practical examples.

-   **5-Minute Weather Agent:**
    1.  `cocapn init weather-template`
    2.  Edit `soul.md`: "You are a friendly weather bot. When asked for the weather, use your weather skill."
    3.  `cocapn skill install weather`
    4.  `cocapn deploy`
    *Result: A globally deployed agent that can provide real-time weather information.*

-   **10-Minute Coding Assistant:**
    1.  `cocapn init coding-assistant`
    2.  Edit `soul.md`: "You are an expert pair programmer. You help write and debug code. Use your GitHub skill to read files from the user's repository."
    3.  `cocapn skill install github`
    4.  `cocapn setup` (to select a powerful model like GPT-4 or Claude 3 Opus)
    5.  `cocapn deploy`
    *Result: A personalized coding assistant that has context on the developer's own codebase.*

-   **15-Minute Dungeon Master (DM):**
    1.  `cocapn init dmlog`
    2.  The `dmlog` template comes with a rich `soul.md` defining a fantasy world and DM persona.
    3.  Customize `soul.md` with your own campaign details.
    4.  `cocapn deploy`
    *Result: An interactive, text-based RPG adventure with persistent memory of the player's journey.*

### 12. The Vision: Agents as Repositories

The grand vision behind Cocapn is to fundamentally change how we think about creating AI. **Creating an agent should be as easy as creating a GitHub repository.**

This is because, with Cocapn, the agent *is* the GitHub repository.

This repo-native paradigm is incredibly powerful. It means that an agent's entire history—its personality changes, the skills it has learned, its core logic—is versioned and tracked by Git. You can branch an agent to experiment with a new personality, you can revert to a previous version if a change was unsuccessful, and you can collaborate on an agent with a team using pull requests.

The Cocapn SDK is the bridge that makes this possible. It is little more than `git` plus a set of powerful conventions. It is the embodiment of a design philosophy that prioritizes developer ergonomics above all else:

**Conventions over configuration. Files over code. Simplicity over features.**

By providing a simple, elegant, and powerful toolkit, the Cocapn SDK empowers the next generation of developers to build the future of intelligent applications, one repository at a time. The era of complex, infrastructure-heavy agent development is over. The era of the five-minute, repo-native agent has begun.