> *Gemini 2.5 Flash*

# The Cocapn CLI — Your Agent's Command Line

## Introduction

In the rapidly evolving landscape of AI and autonomous systems, managing intelligent agents effectively is paramount. The Cocapn Command Line Interface (CLI) emerges as the definitive tool for interacting with, deploying, and orchestrating repo-native agents. Designed for developers, researchers, and system administrators, Cocapn provides a robust, intuitive, and powerful interface that brings the full lifecycle management of your agents directly to your terminal.

Cocapn agents are "repo-native," meaning their entire definition, including code, configurations, skills, and even memory schemas, resides within a standard version-controlled repository. This approach fosters collaboration, reproducibility, and seamless integration into existing CI/CD pipelines. The Cocapn CLI acts as your primary agent, allowing you to initialize new agents, engage in interactive conversations, deploy them to various environments, extend their capabilities with new skills, manage their persistent memories, and even orchestrate fleets of interconnected agents.

This reference guide provides a comprehensive overview of the Cocapn CLI, detailing its installation, core commands, practical workflow examples, advanced output formatting, and essential configuration options. Whether you're building your first intelligent assistant or managing a complex network of autonomous services, Cocapn CLI is your indispensable partner in agent development.

## 1. Installation

Getting started with Cocapn CLI is straightforward, leveraging the ubiquitous Node Package Manager (npm). Ensure you have Node.js (LTS version recommended) and npm installed on your system before proceeding.

To install Cocapn globally on your system, open your terminal and execute the following command:

```bash
npm install -g cocapn
```

This command downloads and installs the Cocapn CLI package, making the `cocapn` command available from any directory in your terminal. Upon successful installation, you can verify the installation and check the installed version using `cocapn version`.

## 2. Core Commands

The Cocapn CLI is structured around a set of powerful core commands, each designed to address a specific aspect of agent management. This section details each command, providing its purpose, syntax, available options, and practical examples.

---

### `cocapn init [template]` — Create new agent from template

**Description:**
The `cocapn init` command is the starting point for any new agent project. It initializes a new agent repository based on a specified template, setting up the necessary directory structure, configuration files, and boilerplate code. This allows developers to quickly scaffold new agents without having to manually set up project files. If no template is specified, Cocapn will prompt the user to select from a list of official templates or default to a basic agent template.

**Syntax:**
`cocapn init [template-name] [options]`

**Options:**
*   `--name <agent-name>`: Specify a name for the new agent. If not provided, Cocapn will prompt for one.
*   `--path <directory>`: Specify the directory where the agent should be initialized. Defaults to a new directory named after the agent in the current working directory.
*   `--force`: Overwrite existing files in the target directory if it's not empty.
*   `--list-templates`: List all available templates without initializing an agent.

**Examples:**

1.  **Initialize a basic agent with interactive prompts:**
    ```bash
    cocapn init
    ```
    *Expected Output:*
    ```
    ? Enter a name for your new agent: MyFirstAgent
    ? Select a template: (Use arrow keys)
    > Basic Agent (A simple conversational agent)
      Skill-Based Agent (Agent with skill integration examples)
      Data Processing Agent (Agent focused on data transformation)
    
    ✨ Initializing 'MyFirstAgent' from 'Basic Agent' template...
    ✅ Agent 'MyFirstAgent' created successfully at ./MyFirstAgent
    
    Next steps:
      cd MyFirstAgent
      cocapn chat
    ```

2.  **Initialize a skill-based agent directly into a specified directory:**
    ```bash
    cocapn init skill-based-agent --name MySkillAgent --path ./agents/skill-agent
    ```
    *Expected Output:*
    ```
    ✨ Initializing 'MySkillAgent' from 'skill-based-agent' template...
    ✅ Agent 'MySkillAgent' created successfully at ./agents/skill-agent
    ```

3.  **List available templates:**
    ```bash
    cocapn init --list-templates
    ```
    *Expected Output:*
    ```
    Available Cocapn Agent Templates:
    - basic-agent: A simple conversational agent template.
    - skill-based-agent: An agent template demonstrating skill integration.
    - data-processing-agent: An agent template optimized for data transformation tasks.
    - web-scraper-agent: An agent template for web content extraction.
    ```

---

### `cocapn chat` — Interactive chat with your agent

**Description:**
The `cocapn chat` command provides an interactive terminal interface to communicate directly with your local agent. This is invaluable for testing agent responses, debugging conversational flows, and iterating on agent behavior during development. The chat session maintains context, allowing for natural, multi-turn conversations.

**Syntax:**
`cocapn chat [options]`

**Options:**
*   `--agent <path>`: Specify the path to the agent directory if not in the current working directory.
*   `--model <model-id>`: Override the default language model configured for the agent.
*   `--system-message <message>`: Provide a one-time system message to prime the agent's initial context.
*   `--reset`: Start a new conversation, clearing previous context.

**Examples:**

1.  **Start a chat session with the agent in the current directory:**
    ```bash
    cocapn chat
    ```
    *Expected Output:*
    ```
    Cocapn Agent Chat (Press Ctrl+C to exit)
    Agent: Hello! How can I help you today?
    You: What is the capital of France?
    Agent: The capital of France is Paris.
    You: And what is its population?
    Agent: The population of Paris is approximately 2.1 million people.
    You: Thank you.
    Agent: You're welcome! Is there anything else I can assist you with?
    ```

2.  **Start a chat session with a specific agent and a custom system message:**
    ```bash
    cocapn chat --agent ./MySkillAgent --system-message "You are a helpful assistant specialized in travel planning."
    ```
    *Expected Output:*
    ```
    Cocapn Agent Chat (Press Ctrl+C to exit)
    Agent: Hello! I'm ready to help you plan your travels. Where would you like to go?
    You: I want to visit Rome.
    Agent: Excellent choice! Rome is a beautiful city. What dates are you considering for your trip?
    ```

---

### `cocapn deploy` — Deploy to Cloudflare Workers

**Description:**
The `cocapn deploy` command packages your agent and deploys it to a production environment. Currently, it supports deployment to Cloudflare Workers, leveraging their serverless platform for high performance and scalability. This command handles the build process, bundling all agent code and assets, and then pushing it to your configured Cloudflare account.

**Syntax:**
`cocapn deploy [options]`

**Options:**
*   `--local`: Run a local development server for testing the agent as it would be deployed.
*   `--agent <path>`: Specify the path to the agent directory.
*   `--env <environment>`: Specify a deployment environment (e.g., `staging`, `production`).
*   `--name <worker-name>`: Override the worker name defined in the agent's configuration.
*   `--dry-run`: Simulate the deployment process without actually pushing to Cloudflare.

**Examples:**

1.  **Deploy the agent in the current directory to Cloudflare Workers:**
    ```bash
    cocapn deploy
    ```
    *Expected Output:*
    ```
    ✨ Building agent 'MyFirstAgent' for deployment...
    📦 Bundling agent assets and code... [====================] 100%
    🚀 Deploying 'MyFirstAgent' to Cloudflare Workers...
    🔑 Authenticating with Cloudflare...
    ⬆️ Uploading worker script...
    ✅ Agent 'MyFirstAgent' deployed successfully!
    
    Worker URL: https://my-first-agent.your-cloudflare-subdomain.workers.dev
    ```

2.  **Run a local development server for the agent:**
    ```bash
    cocapn deploy --local --agent ./MySkillAgent
    ```
    *Expected Output:*
    ```
    ✨ Starting local development server for 'MySkillAgent'...
    🌐 Agent available at http://localhost:8787
    (Press Ctrl+C to stop)
    
    [LOG] Incoming request to /chat
    [LOG] Agent response: {"message": "Hello from MySkillAgent!"}
    ```
    *Note: You can interact with the local server using `curl` or a browser, e.g., `curl -X POST http://localhost:8787/chat -H "Content-Type: application/json" -d '{"message": "Hello agent"}'`*

3.  **Perform a dry run deployment to check for issues without actual deployment:**
    ```bash
    cocapn deploy --dry-run --env production
    ```
    *Expected Output:*
    ```
    ✨ Building agent 'MyFirstAgent' for production deployment...
    📦 Bundling agent assets and code... [====================] 100%
    🔍 Performing dry run deployment to Cloudflare Workers...
    ✅ Dry run successful. No deployment errors detected.
    (Would deploy to worker name: my-first-agent-prod)
    ```

---

### `cocapn skill install [name]` — Install a skill

**Description:**
Agents gain new capabilities through "skills." The `cocapn skill install` command allows you to add pre-built or custom skills to your agent. Skills are modular units of functionality that an agent can invoke to perform specific tasks, such as fetching data, interacting with external APIs, or performing complex computations. Skills are typically installed from a registry or a local path.

**Syntax:**
`cocapn skill install [skill-identifier] [options]`

**Options:**
*   `--agent <path>`: Specify the path to the agent directory.
*   `--from-url <url>`: Install a skill from a remote URL (e.g., a Git repository).
*   `--from-path <path>`: Install a skill from a local file system path.
*   `--version <version>`: Specify a particular version of the skill to install.
*   `--force`: Overwrite an existing skill with the same name.

**Examples:**

1.  **Install a skill from the official Cocapn skill registry:**
    ```bash
    cocapn skill install weather-forecast
    ```
    *Expected Output:*
    ```
    ✨ Installing skill 'weather-forecast'...
    ⬇️ Downloading 'weather-forecast@latest' from registry...
    📦 Extracting skill files...
    🛠️ Running skill post-install scripts...
    ✅ Skill 'weather-forecast' installed successfully into agent 'MyFirstAgent'.
    ```

2.  **Install a skill from a local path:**
    ```bash
    cocapn skill install my-custom-tool --from-path ../my-tool-skill
    ```
    *Expected Output:*
    ```
    ✨ Installing skill 'my-custom-tool' from local path '../my-tool-skill'...
    📦 Copying skill files...
    🛠️ Running skill post-install scripts...
    ✅ Skill 'my-custom-tool' installed successfully into agent 'MyFirstAgent'.
    ```

3.  **Install a specific version of a skill from a URL:**
    ```bash
    cocapn skill install github-search --from-url https://github.com/cocapn-skills/github-search.git --version 1.2.0
    ```
    *Expected Output:*
    ```
    ✨ Installing skill 'github-search@1.2.0' from 'https://github.com/cocapn-skills/github-search.git'...
    ⬇️ Cloning repository...
    📦 Extracting skill files...
    🛠️ Running skill post-install scripts...
    ✅ Skill 'github-search' installed successfully into agent 'MyFirstAgent'.
    ```

---

### `cocapn skill list` — List installed skills

**Description:**
The `cocapn skill list` command displays a comprehensive list of all skills currently installed within your agent. It provides details such as the skill name, version, and a brief description, helping you keep track of your agent's capabilities.

**Syntax:**
`cocapn skill list [options]`

**Options:**
*   `--agent <path>`: Specify the path to the agent directory.
*   `--json`: Output the list in JSON format.

**Examples:**

1.  **List all skills installed in the current agent:**
    ```bash
    cocapn skill list
    ```
    *Expected Output:*
    ```
    Installed Skills for Agent 'MyFirstAgent':
    
    - weather-forecast (v1.0.0)
      Description: Fetches current weather conditions and forecasts for a given location.
      Source: Cocapn Registry
    
    - my-custom-tool (v0.1.0)
      Description: A custom tool for internal data processing.
      Source: Local Path
    
    - github-search (v1.2.0)
      Description: Searches GitHub repositories and issues.
      Source: https://github.com/cocapn-skills/github-search.git
    ```

2.  **List skills in JSON format:**
    ```bash
    cocapn skill list --json
    ```
    *Expected Output:*
    ```json
    [
      {
        "name": "weather-forecast",
        "version": "1.0.0",
        "description": "Fetches current weather conditions and forecasts for a given location.",
        "source": "Cocapn Registry"
      },
      {
        "name": "my-custom-tool",
        "version": "0.1.0",
        "description": "A custom tool for internal data processing.",
        "source": "Local Path"
      },
      {
        "name": "github-search",
        "version": "1.2.0",
        "description": "Searches GitHub repositories and issues.",
        "source": "https://github.com/cocapn-skills/github-search.git"
      }
    ]
    ```

---

### `cocapn memory list` — Show memories

**Description:**
Agents often require persistent memory to retain information across interactions or to store long-term knowledge. The `cocapn memory list` command allows you to inspect the keys of all memories stored by your agent. This is useful for understanding what information your agent is retaining.

**Syntax:**
`cocapn memory list [options]`

**Options:**
*   `--agent <path>`: Specify the path to the agent directory.
*   `--json`: Output the memory keys in JSON format.
*   `--limit <number>`: Limit the number of memory keys displayed.

**Examples:**

1.  **List all memory keys for the current agent:**
    ```bash
    cocapn memory list
    ```
    *Expected Output:*
    ```
    Agent 'MyFirstAgent' Memory Keys:
    - user_preferences
    - last_conversation_summary
    - known_facts/capital_france
    - known_facts/population_paris
    - api_keys/weather_service
    ```

2.  **List memory keys with a limit:**
    ```bash
    cocapn memory list --limit 2
    ```
    *Expected Output:*
    ```
    Agent 'MyFirstAgent' Memory Keys (showing 2 of 5):
    - user_preferences
    - last_conversation_summary
    ```

---

### `cocapn memory search [query]` — Search memories

**Description:**
The `cocapn memory search` command enables you to find specific memories within your agent's storage. You can provide a query string, and Cocapn will return memory keys that match the query. This is particularly useful for agents with a large number of memories, allowing for efficient retrieval of relevant information.

**Syntax:**
`cocapn memory search [query] [options]`

**Options:**
*   `--agent <path>`: Specify the path to the agent directory.
*   `--json`: Output search results in JSON format.
*   `--value`: Also display the value of the matching memories (use with caution for sensitive data).

**Examples:**

1.  **Search for memories related to "user":**
    ```bash
    cocapn memory search user
    ```
    *Expected Output:*
    ```
    Memory Search Results for 'user' in Agent 'MyFirstAgent':
    - user_preferences
    - user_session_id_123
    ```

2.  **Search for API keys and display their values (for debugging, not recommended for production secrets):**
    ```bash
    cocapn memory search api_keys --value
    ```
    *Expected Output:*
    ```
    Memory Search Results for 'api_keys' in Agent 'MyFirstAgent':
    - api_keys/weather_service: "sk-abcdef1234567890"
    - api_keys/google_maps: "AIzaSyC_..."
    ```

---

### `cocapn memory add [key] [value]` — Add memory

**Description:**
The `cocapn memory add` command allows you to manually inject new key-value pairs into your agent's memory. This is useful for pre-populating an agent with configuration settings, initial knowledge, or test data.

**Syntax:**
`cocapn memory add <key> <value> [options]`

**Options:**
*   `--agent <path>`: Specify the path to the agent directory.
*   `--force`: Overwrite the value if the key already exists.
*   `--json-value`: Treat the value as a JSON string and parse it.

**Examples:**

1.  **Add a simple string memory:**
    ```bash
    cocapn memory add favorite_color blue
    ```
    *Expected Output:*
    ```
    ✅ Memory 'favorite_color' added/updated for agent 'MyFirstAgent'.
    ```

2.  **Add a JSON object as a memory:**
    ```bash
    cocapn memory add user_profile '{"name": "Alice", "email": "alice@example.com"}' --json-value
    ```
    *Expected Output:*
    ```
    ✅ Memory 'user_profile' added/updated for agent 'MyFirstAgent'.
    ```

3.  **Update an existing memory (requires `--force`):**
    ```bash
    cocapn memory add favorite_color green --force
    ```
    *Expected Output:*
    ```
    ✅ Memory 'favorite_color' added/updated for agent 'MyFirstAgent'.
    ```

---

### `cocapn config` — Manage configuration

**Description:**
The `cocapn config` command provides an interface to manage the global configuration settings for the Cocapn CLI itself, as well as agent-specific configurations. This allows you to set defaults for common options, API keys, and other parameters that influence how Cocapn operates.

**Syntax:**
`cocapn config [command] [options]`

**Subcommands:**
*   `cocapn config list`: Display all current configuration settings.
*   `cocapn config get [key]`: Retrieve the value of a specific configuration key.
*   `cocapn config set [key] [value]`: Set a configuration key to a new value.
*   `cocapn config delete [key]`: Remove a configuration key.
*   `cocapn config edit`: Open the configuration file in your default editor.

**Options:**
*   `--global`: (Default) Manage global CLI configuration (`~/.cocapn/config.json`).
*   `--agent <path>`: Manage configuration for a specific agent (e.g., `./agent.config.json`).
*   `--json`: Output in JSON format.

**Examples:**

1.  **List all global Cocapn CLI configurations:**
    ```bash
    cocapn config list
    ```
    *Expected Output:*
    ```
    Global Cocapn Configuration:
    - default_template: basic-agent
    - cloudflare_api_token: ********************
    - openai_api_key: ********************
    - preferred_model: gpt-4o
    ```

2.  **Set a global configuration value:**
    ```bash
    cocapn config set preferred_model claude-3-opus
    ```
    *Expected Output:*
    ```
    ✅ Global configuration 'preferred_model' set to 'claude-3-opus'.
    ```

3.  **Get an agent-specific configuration value:**
    ```bash
    cocapn config get agent_name --agent ./MyFirstAgent
    ```
    *Expected Output:*
    ```
    MyFirstAgent
    ```

---

### `cocapn fleet list` — Show fleet connections

**Description:**
Cocapn supports the concept of "fleets," where multiple agents can be interconnected to collaborate on tasks or form a distributed system. The `cocapn fleet list` command displays all active connections your local agent has established with other agents in a fleet.

**Syntax:**
`cocapn fleet list [options]`

**Options:**
*   `--agent <path>`: Specify the path to the agent directory.
*   `--json`: Output the fleet connections in JSON format.

**Examples:**

1.  **List fleet connections for the current agent:**
    ```bash
    cocapn fleet list
    ```
    *Expected Output:*
    ```
    Fleet Connections for Agent 'MyFirstAgent':
    
    - Agent ID: agent-alpha-123
      Name: Alpha Overseer
      URL: https://alpha-overseer.workers.dev
      Status: Connected (Heartbeat: 5s ago)
    
    - Agent ID: agent-beta-456
      Name: Beta Data Processor
      URL: http://localhost:8788
      Status: Connected (Heartbeat: 10s ago)
    ```

---

### `cocapn fleet connect [url]` — Connect to agent

**Description:**
The `cocapn fleet connect` command establishes a connection from your local agent to another Cocapn agent, typically a deployed one or another local instance running in `--local` mode. This allows your agent to send messages, delegate tasks, or share information with the connected agent.

**Syntax:**
`cocapn fleet connect <agent-url> [options]`

**Options:**
*   `--agent <path>`: Specify the path to the agent directory.
*   `--name <alias>`: Assign a friendly name or alias to the connected agent.
*   `--auth-token <token>`: Provide an authentication token if the target agent requires it.

**Examples:**

1.  **Connect to a remote agent:**
    ```bash
    cocapn fleet connect https://data-aggregator.workers.dev --name DataAggregator
    ```
    *Expected Output:*
    ```
    ✨ Attempting to connect to agent at 'https://data-aggregator.workers.dev'...
    🤝 Handshake successful. Agent ID: agent-data-789
    ✅ Connected to 'DataAggregator' (agent-data-789).
    ```

2.  **Connect to a local agent running on a different port:**
    ```bash
    cocapn fleet connect http://localhost:8788 --name LocalProcessor
    ```
    *Expected Output:*
    ```
    ✨ Attempting to connect to agent at 'http://localhost:8788'...
    🤝 Handshake successful. Agent ID: agent-local-proc
    ✅ Connected to 'LocalProcessor' (agent-local-proc).
    ```

---

### `cocapn fleet disconnect [agentId]`

**Description:**
The `cocapn fleet disconnect` command terminates an existing connection between your local agent and a specified fleet member. This is useful for managing active connections, removing agents that are no longer needed, or troubleshooting.

**Syntax:**
`cocapn fleet disconnect <agent-id> [options]`

**Options:**
*   `--agent <path>`: Specify the path to the agent directory.
*   `--all`: Disconnect from all connected agents.

**Examples:**

1.  **Disconnect from a specific agent by its ID:**
    ```bash
    cocapn fleet disconnect agent-alpha-123
    ```
    *Expected Output:*
    ```
    👋 Disconnecting from agent 'agent-alpha-123'...
    ✅ Disconnected from 'agent-alpha-123'.
    ```

2.  **Disconnect from all connected agents:**
    ```bash
    cocapn fleet disconnect --all
    ```
    *Expected Output:*
    ```
    👋 Disconnecting from all connected agents...
    ✅ Disconnected from 'agent-alpha-123'.
    ✅ Disconnected from 'agent-beta-456'.
    ✅ All fleet connections terminated.
    ```

---

### `cocapn twin create` — Create digital twin

**Description:**
The `cocapn twin create` command initiates the creation of a "digital twin" of your agent. A digital twin is a specialized instance designed to mirror or simulate your primary agent's behavior, often used for testing, experimentation, or providing a sandbox environment without affecting the production agent. This command typically involves cloning the agent's configuration and potentially its memory state.

**Syntax:**
`cocapn twin create [options]`

**Options:**
*   `--agent <path>`: Specify the path to the source agent directory.
*   `--name <twin-name>`: Specify a name for the new digital twin.
*   `--path <directory>`: Specify the directory where the twin should be created.
*   `--copy-memory`: Copy the source agent's current memory state to the twin.
*   `--template <template-name>`: Use a specific twin template (e.g., `test-twin`, `sandbox-twin`).

**Examples:**

1.  **Create a basic digital twin of the current agent:**
    ```bash
    cocapn twin create --name MyAgentTwin
    ```
    *Expected Output:*
    ```
    ✨ Creating digital twin 'MyAgentTwin' from agent 'MyFirstAgent'...
    📦 Cloning agent structure and configuration...
    ✅ Digital twin 'MyAgentTwin' created successfully at ./MyAgentTwin.
    ```

2.  **Create a twin with a copy of the parent agent's memory:**
    ```bash
    cocapn twin create --name TestTwin --path ./twins/test-agent --copy-memory
    ```
    *Expected Output:*
    ```
    ✨ Creating digital twin 'TestTwin' from agent 'MyFirstAgent'...
    📦 Cloning agent structure and configuration...
    💾 Copying memory state from 'MyFirstAgent' to 'TestTwin'...
    ✅ Digital twin 'TestTwin' created successfully at ./twins/test-agent.
    ```

---

### `cocapn twin chat` — Chat with your twin

**Description:**
Similar to `cocapn chat`, the `cocapn twin chat` command provides an interactive chat interface, but specifically for interacting with a digital twin of your agent. This ensures that your conversations and tests do not interfere with the primary agent's operational state or memory.

**Syntax:**
`cocapn twin chat [options]`

**Options:**
*   `--twin <path>`: Specify the path to the digital twin directory.
*   `--model <model-id>`: Override the default language model for the twin.
*   `--reset`: Start a new conversation, clearing previous context for the twin.

**Examples:**

1.  **Start a chat session with a specific digital twin:**
    ```bash
    cocapn twin chat --twin ./twins/test-agent
    ```
    *Expected Output:*
    ```
    Cocapn Digital Twin Chat (Press Ctrl+C to exit)
    Twin (TestTwin): Hello! I am a test instance of your agent. How can I assist you?
    You: What is my favorite color?
    Twin (TestTwin): Based on my memory, your favorite color is blue.
    You: Change it to red.
    Twin (TestTwin): Okay, I've updated your favorite color to red.
    You: What is my favorite color now?
    Twin (TestTwin): Your favorite color is now red.
    ```
    *Note: This interaction only affects the twin's memory, not the original agent's.*

---

### `cocapn test` — Run tests

**Description:**
The `cocapn test` command executes the test suite defined for your agent. Repo-native agents often include unit tests, integration tests, and conversational tests to ensure their behavior is correct and consistent. This command integrates with standard testing frameworks to provide a unified testing experience.

**Syntax:**
`cocapn test [options]`

**Options:**
*   `--agent <path>`: Specify the path to the agent directory.
*   `--watch`: Run tests in watch mode, re-running on file changes.
*   `--grep <pattern>`: Only run tests matching the given pattern.
*   `--coverage`: Generate a code coverage report.

**Examples:**

1.  **Run all tests for the current agent:**
    ```bash
    cocapn test
    ```
    *Expected Output:*
    ```
    ✨ Running tests for agent 'MyFirstAgent'...
    
    Test Suite: agent/skills/weather-forecast.test.js
      ✓ should return correct weather for London (50ms)
      ✓ should handle invalid location (20ms)
    
    Test Suite: agent/conversations/greeting.test.js
      ✓ should respond to 'hello' (30ms)
      ✓ should remember user's name (45ms)
    
    Test Suites:   2 passed, 2 total
    Tests:         4 passed, 4 total
    Snapshots:     0 total
    Time:          1.234s
    ```

2.  **Run tests in watch mode:**
    ```bash
    cocapn test --watch
    ```
    *Expected Output:*
    ```
    ✨ Running tests for agent 'MyFirstAgent' in watch mode...
    (Watching for file changes... Press 'q' to quit)
    
    ... (initial test output) ...
    
    [WATCH] Detected change in agent/skills/weather-forecast.js, re-running tests...
    ... (re-run test output) ...
    ```

---

### `cocapn doctor` — Diagnose issues

**Description:**
The `cocapn doctor` command performs a series of diagnostic checks on your Cocapn CLI installation, agent project, and environment. It identifies common issues such as missing dependencies, incorrect configurations, network problems, or outdated components, providing actionable recommendations for resolution. This is an essential tool for troubleshooting.

**Syntax:**
`cocapn doctor [options]`

**Options:**
*   `--agent <path>`: Specify the path to the agent directory for agent-specific diagnostics.
*   `--fix`: Attempt to automatically fix detected issues (use with caution).
*   `--verbose`: Provide more detailed diagnostic output.

**Examples:**

1.  **Run a general diagnostic check:**
    ```bash
    cocapn doctor
    ```
    *Expected Output:*
    ```
    🩺 Running Cocapn CLI diagnostics...
    
    [✅] Node.js Version: v18.17.0 (LTS)
    [✅] npm Version: 9.6.7
    [✅] Cocapn CLI Version: 1.0.0
    [✅] Global Config: ~/.cocapn/config.json found.
    [⚠️] Cloudflare API Token: Not set. Deployment to Cloudflare Workers may fail.
        Recommendation: Run 'cocapn config set cloudflare_api_token <YOUR_TOKEN>'
    [✅] OpenAI API Key: Set.
    
    Global diagnostics complete. 1 warning found.
    ```

2.  **Diagnose issues within a specific agent project:**
    ```bash
    cocapn doctor --agent ./MySkillAgent
    ```
    *Expected Output:*
    ```
    🩺 Running diagnostics for agent 'MySkillAgent'...
    
    [✅] Agent Directory: Found at ./MySkillAgent
    [✅] Agent Config: agent.config.json found.
    [✅] Dependencies: All npm dependencies installed.
    [⚠️] Skill 'weather-forecast': Missing API key for 'OpenWeatherMap'.
        Recommendation: Add 'OPENWEATHER_API_KEY' to agent's environment variables or memory.
    [❌] Test Suite: No tests found in 'tests/' directory.
        Recommendation: Create a 'tests/' directory and add test files.
    
    Agent diagnostics complete. 1 warning, 1 error found.
    ```

---

### `cocapn update` — Update cocapn

**Description:**
The `cocapn update` command updates your globally installed Cocapn CLI to the latest available version. Keeping your CLI up-to-date ensures you have access to the newest features, bug fixes, and security enhancements.

**Syntax:**
`cocapn update [options]`

**Options:**
*   `--beta`: Update to the latest beta version.
*   `--force`: Force update even if already on the latest version.

**Examples:**

1.  **Update Cocapn CLI to the latest stable version:**
    ```bash
    cocapn update
    ```
    *Expected Output:*
    ```
    ✨ Checking for Cocapn CLI updates...
    Current version: 1.0.0
    Latest version:  1.1.0
    
    ⬇️ Downloading Cocapn CLI v1.1.0...
    📦 Installing update...
    ✅ Cocapn CLI updated successfully to v1.1.0!
    ```

2.  **Update to the latest beta version:**
    ```bash
    cocapn update --beta
    ```
    *Expected Output:*
    ```
    ✨ Checking for Cocapn CLI beta updates...
    Current version: 1.1.0
    Latest beta:     1.2.0-beta.1
    
    ⬇️ Downloading Cocapn CLI v1.2.0-beta.1...
    📦 Installing update...
    ✅ Cocapn CLI updated successfully to v1.2.0-beta.1!
    ```

---

### `cocapn version` — Show version

**Description:**
The `cocapn version` command simply displays the currently installed version of the Cocapn CLI. This is useful for verifying your installation and for debugging purposes when reporting issues.

**Syntax:**
`cocapn version`

**Examples:**

1.  **Display the Cocapn CLI version:**
    ```bash
    cocapn version
    ```
    *Expected Output:*
    ```
    cocapn/1.1.0 darwin-x64 node-v18.17.0
    ```

## 3. Workflow Examples

Understanding individual commands is crucial, but seeing them in action within common workflows provides a clearer picture of Cocapn's power.

### First Time: Init > Setup > Chat > Deploy

This workflow guides a new user from creating their first agent to deploying it.

1.  **Initialize a new agent:**
    ```bash
    cocapn init MyNewAgent
    cd MyNewAgent
    ```
    *This creates a basic agent project in a directory named `MyNewAgent`.*

2.  **(Optional) Configure API keys:**
    ```bash
    cocapn config set openai_api_key sk-xxxxxxxxxxxxxxxxx --agent .
    ```
    *This sets the OpenAI API key specifically for `MyNewAgent`.*

3.  **Interactive chat to test:**
    ```bash
    cocapn chat
    ```
    *You: Hello!*
    *Agent: Hi there! How can I help you today?*
    *You: Tell me a joke.*
    *Agent: Why don't scientists trust atoms? Because they make up everything!*
    *This confirms the agent is responsive and functional.*

4.  **Deploy to Cloudflare Workers:**
    ```bash
    cocapn deploy
    ```
    *This builds and deploys your agent, providing a public URL.*

### Adding a Skill: Skill Install > Configure > Use

This workflow demonstrates how to extend an agent's capabilities.

1.  **Install a new skill:**
    ```bash
    cd MyNewAgent
    cocapn skill install stock-tracker
    ```
    *This adds the `stock-tracker` skill to `MyNewAgent`.*

2.  **Configure the skill (e.g., API key):**
    ```bash
    cocapn config set ALPHA_VANTAGE_API_KEY YOUR_ALPHA_VANTAGE_KEY --agent .
    ```
    *Skills often require their own configurations or API keys.*

3.  **Use the skill in chat:**
    ```bash
    cocapn chat
    ```
    *You: What is the current stock price of AAPL?*
    *Agent: (Thinking... invoking 'stock-tracker' skill)*
    *Agent: The current stock price for Apple (AAPL) is $175.25.*
    *The agent now leverages the new skill to answer stock-related queries.*

### Fleet Setup: Init Multiple > Fleet Connect > Fleet Status

This workflow illustrates how to create and connect multiple agents into a collaborative fleet.

1.  **Initialize two agents:**
    ```bash
    cocapn init AgentA --path ./agent-a
    cocapn init AgentB --path ./agent-b
    ```

2.  **Deploy AgentA locally (as the "server" for AgentB to connect to):**
    ```bash
    cd agent-a
    cocapn deploy --local
    # (Note the local URL, e.g., http://localhost:8787)
    ```

3.  **Connect AgentB to AgentA:**
    ```bash
    cd ../agent-b
    cocapn fleet connect http://localhost:8787 --name AgentA_Local
    ```
    *Expected Output:*
    ```
    ✅ Connected to 'AgentA_Local' (agent-a-id-xyz).
    ```

4.  **Verify fleet status from AgentB:**
    ```bash
    cocapn fleet list
    ```
    *Expected Output:*
    ```
    Fleet Connections for Agent 'AgentB':
    - Agent ID: agent-a-id-xyz
      Name: AgentA_Local
      URL: http://localhost:8787
      Status: Connected (Heartbeat: Xs ago)
    ```
    *AgentB is now aware of and connected to AgentA, enabling inter-agent communication.*

### Debugging: Doctor > Test > Logs

This workflow outlines a typical debugging process for an agent.

1.  **Initial diagnosis with `doctor`:**
    ```bash
    cocapn doctor --agent .
    ```
    *This might reveal missing environment variables, outdated dependencies, or configuration errors.*

2.  **Run tests to pinpoint code issues:**
    ```bash
    cocapn test
    ```
    *If tests fail, it indicates a problem in the agent's logic or skill implementation. Fix the code based on test failures.*

3.  **Inspect local deployment logs:**
    ```bash
    cocapn deploy --local
    ```
    *While the local server is running, interact with the agent via `curl` or `cocapn chat` in another terminal. Observe the detailed logs in the `deploy --local` terminal for runtime errors, skill invocation issues, or unexpected agent behavior.*

## 4. Output Formatting

Cocapn CLI prioritizes a user-friendly experience through rich and informative terminal output, while also offering options for programmatic consumption.

*   **Rich Terminal Output:** By default, Cocapn commands produce visually appealing output with:
    *   **Colors:** Highlighting important information, warnings, and errors.
    *   **Formatting:** Bold text, indentation, and clear headings for readability.
    *   **Emojis:** Used sparingly to convey status (e.g., ✨ for starting, ✅ for success, ❌ for error).
    *   **Progress Indicators:** For long-running operations like deployments or installations, Cocapn displays dynamic progress bars or spinners to keep you informed.

    *Example (partial `cocapn deploy` output):*
    ```
    ✨ Building agent 'MyFirstAgent' for deployment...
    📦 Bundling agent assets and code... [====================] 100%
    🚀 Deploying 'MyFirstAgent' to Cloudflare Workers...
    ```

*   **JSON Output Mode (`--json` flag):** For scripting and integration with other tools, most commands support a `--json` flag. When this flag is present, the command's primary output is formatted as a valid JSON object or array, suppressing all rich terminal formatting.
    *Example:*
    ```bash
    cocapn skill list --json
    ```
    *See example under `cocapn skill list` for output.*

*   **Pipe-Friendly Plain Text Mode:** When output is piped to another command (e.g., `grep`, `awk`), Cocapn intelligently detects this and automatically switches to a plain text, unformatted output. This ensures compatibility with standard Unix-like utilities without requiring explicit flags.

## 5. Autocomplete

To enhance developer productivity and reduce typing errors, Cocapn CLI provides robust tab completion for commands, subcommands, options, and even some argument values (like installed skill names or memory keys).

To enable autocomplete, you typically need to run a setup command once for your shell. For Bash or Zsh, this might involve:

```bash
cocapn completion > /usr/local/etc/bash_completion.d/cocapn
# Or for Zsh:
cocapn completion > ~/.zsh/completion/_cocapn
# Then source your shell config or restart your terminal.
```
Once enabled, you can type `cocapn ` and press `Tab` twice to see available commands, or `cocapn skill install ` and press `Tab` to see a list of available skills.

## 6. Configuration: `~/.cocapn/config.json` for Global Settings

Cocapn CLI manages its global configuration settings in a JSON file located at `~/.cocapn/config.json` (or `%USERPROFILE%\.cocapn\config.json` on Windows). This file stores default values for various parameters, such as:

*   `default_template`: The template used when `cocapn init` is called without a specific template.
*   `cloudflare_api_token`: Your Cloudflare API token for deployments.
*   `openai_api_key`, `anthropic_api_key`, etc.: API keys for various language models.
*   `preferred_model`: The default language model to use for `cocapn chat` if not specified by the agent.

You can manage this file directly using `cocapn config` commands (e.g., `cocapn config set cloudflare_api_token YOUR_TOKEN`) or by manually editing it (e.g., `cocapn config edit`). Agent-specific configurations are typically stored within the agent's repository (e.g., `./agent.config.json`) and can be managed using `cocapn config --agent .`.

## Conclusion

The Cocapn CLI stands as a powerful and indispensable tool for anyone working with repo-native agents. From initial setup and interactive development to deployment, skill extension, memory management, and fleet orchestration, Cocapn provides a comprehensive and intuitive command-line experience. Its commitment to rich output, robust configuration, and developer-friendly features like autocomplete ensures that managing your intelligent agents is efficient, enjoyable, and seamlessly integrated into your development workflow. As the agent ecosystem evolves, Cocapn CLI will continue to be your trusted agent's command line, empowering you to build, deploy, and scale the next generation of intelligent systems.