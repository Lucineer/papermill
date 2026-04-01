> *Written by Gemini 3.1 Pro*

# Architecture Specification: makerlog-ai
**Document Status:** Draft / V1
**Author:** Lead Architect
**Subject:** The Self-Building Agent Paradigm

## Executive Summary
Current coding agents (Cursor, Devin, Claude Code) are external observers. They look *at* a repository through a context window. `makerlog-ai` is a **repo-native agent**. It lives inside the repository, compiles itself to fit the specific codebase, and evolves alongside it. 

The core innovation is the **Self-Building Pipeline**: upon initialization, the agent analyzes the repository and writes custom source code (tools, validators, prompts) to create a bespoke agent optimized exclusively for that project. 

This document outlines the architecture of the `makerlog-ai` engine, split across the heavy, private meta-reasoning layer (**Logos**) and the distilled, public-facing execution layer (**Pathos**).

---

## 1. The Accumulation Model (The MVP)
*Insight: "Ship a repo-map that grows." - DeepSeek Chat*

The foundation of `makerlog-ai` is the **Living Repo-Map**. It is not just an AST; it is a persistent, version-controlled graph database stored in `.makerlog/graph.db` (SQLite + Vector extensions).

**What grows over time:**
1.  **Code Nodes:** AST representations of files, functions, and classes.
2.  **Semantic Nodes:** Vector embeddings of code purpose.
3.  **Decision Nodes:** The "Why". When a developer or agent makes a change, the reasoning is embedded and linked to the Code Node.
4.  **Failure Nodes:** Past bugs, failed test runs, and dead-end architectural choices.

**Pseudocode: Graph Schema**
```python
class RepoGraph:
    def insert_decision(self, code_ref: str, context: str, outcome: str):
        # Links a piece of code to the historical context of WHY it was written
        # This prevents the agent from suggesting previously failed approaches
        pass

    def get_subgraph(self, file_path: str, depth: int) -> SubGraph:
        # Returns code + historical decisions + related failures
        pass
```

---

## 2. The Self-Build Pipeline
*Insight: "Self-reference is not a bug, it is the defining feature." - Gemini 2.5 Pro*

When a project forks `makerlog-ai`, the bootstrap engine initiates the Self-Build Pipeline. The agent does not just configure itself; it *writes its own Python/TypeScript tool implementations*.

**The Order of Operations:**
1.  **Ingestion:** Scans `package.json`, `Cargo.toml`, `.github/workflows`, etc.
2.  **Stack Profiling:** Identifies the stack (e.g., "Next.js + Tailwind + Supabase").
3.  **Tool Generation:** Writes project-specific tools into `.makerlog/tools/`.
    *   *Example:* If it detects Supabase, it writes a custom `query_supabase_schema.py` tool. If it detects a custom internal design system, it writes a `validate_ui_components.py` tool.
4.  **Prompt Compilation:** Generates a bespoke system prompt embedding the project's specific architectural rules.

**Pseudocode: Self-Build Execution**
```python
def self_build(repo_path: str):
    stack_profile = Logos.analyze_stack(repo_path)
    
    for capability in stack_profile.required_capabilities:
        # Logos WRITES the code for the new tool
        tool_source_code = Logos.generate_tool_code(capability, repo_path)
        
        # Save to the repo-native agent directory
        tool_path = f".makerlog/tools/{capability.name}.py"
        write_file(tool_path, tool_source_code)
        
        # Agent tests its newly written tool
        TestLoop.validate(tool_path)
```

---

## 3. The Meta-Cognition Layer
*Insight: "The agent must be able to reason about its own reasoning." - Gemini 3.1 Pro*

To prevent the agent from getting stuck in loops or hallucinating bad tools, **Logos** operates on a dual-track cognitive architecture: The **Actor** and the **Critic**.

Before `makerlog-ai` modifies the codebase (or its own code), it must write a stateable hypothesis.

**The Meta-Cognitive Loop:**
1.  **Intent:** "I need to write a tool to parse the custom binary logs in this repo."
2.  **Hypothesis:** "If I use the `struct` library to read the first 16 bytes, I will get the header."
3.  **Action:** Writes and runs the tool.
4.  **Reflection:** The Critic evaluates the output against the Hypothesis.
5.  **Self-Correction:** If it fails, the Critic updates the agent's internal prompt: *"Note: The binary logs in this repo have a 32-byte header, not 16. Do not use standard 16-byte assumptions."* This note is saved to the Accumulation Model.

---

## 4. The Test-Itself Loop
How does the agent validate the tools and prompts it writes for itself? Through isolated shadow workspaces.

1.  **Shadow Branching:** The agent creates a hidden git worktree (`.makerlog/shadow/`).
2.  **Test Generation:** Before writing a custom tool, the agent writes a unit test for that tool.
3.  **Execution:** The tool is executed in the shadow workspace.
4.  **Coverage Check:** If the tool mutates code, the agent runs the *project's* native test suite (e.g., `npm test`) to ensure its new capability doesn't break the host project.

**Pseudocode: Self-Testing**
```python
class AgentTestRunner:
    def validate_self_modification(self, new_tool_code: str):
        workspace = ShadowWorkspace.create()
        
        # Agent writes a test for its own new code
        test_code = Logos.generate_test_for(new_tool_code)
        workspace.write("test_tool.py", test_code)
        
        result = workspace.run("pytest test_tool.py")
        if result.failed:
            Logos.reflect_on_failure(result.stderr)
            return self.retry_modification()
            
        return True # Tool is safe to merge into .makerlog/tools/
```

---

## 5. Safety Constraints (The Obi-Wan Protocol)
*Insight: "Beware the agent that can modify its own source code without oversight." - DeepSeek-Reasoner*

A self-modifying agent is a security and stability risk. We enforce strict sandbox boundaries at the kernel level of the agent.

1.  **The Immutable Core:** The bootstrap engine (`makerlog_core`) is read-only. The agent can only write to `.makerlog/tools/` and `.makerlog/prompts/`.
2.  **No-Op Default:** Any self-modification that touches network protocols, authentication, or environment variables requires a human PR. The agent can draft it, but cannot merge it.
3.  **Recursion Depth Limit:** The agent is hard-coded to a maximum self-modification depth of 3. (e.g., It cannot write a tool, that writes a tool, that writes a tool, that modifies the codebase).
4.  **Cryptographic Tool Signing:** When a human approves a newly generated agent tool, it is hashed and signed. The execution engine will only run signed tools.

---

## 6. The Distillation Pipeline (Logos → Pathos)
**Logos** (the heavy, self-modifying, meta-cognitive backend) is too slow and expensive for a developer's daily autocomplete and chat. 

We distill Logos into **Pathos** (the public, fast, specialized assistant).

1.  **Context Compression:** Logos takes the massive `graph.db` and extracts the "Golden Rules" of the repository (e.g., "We use functional components," "Never use `any` in TS," "Here is how our custom auth works").
2.  **Tool Compilation:** Logos wraps the custom tools it built into standard JSON Schema definitions.
3.  **Model Export:** Logos generates a highly optimized `system_prompt.txt` and a set of few-shot examples.
4.  **Pathos Instantiation:** A fast model (e.g., Llama 3 8B or Gemini Flash) is booted up using this distilled context. Pathos is what the developer interacts with in the IDE. It has access to the bespoke tools Logos built, but it does not have the ability to modify the tools themselves.

---

## 7. The A2A Bridge (Agent-to-Agent)
Because `makerlog-ai` is repo-native, in a microservice architecture, you will have multiple agents (e.g., the Frontend Agent and the Backend Agent). They must communicate.

We implement a **Repo-to-Repo MCP (Model Context Protocol) Bridge**.

1.  **Capability Broadcasting:** Every `makerlog-ai` instance generates an `agent-contract.json` exposing what it knows and what tools it has.
2.  **Cross-Repo Querying:** If the Frontend Agent needs to know how a new API endpoint works, it does not guess. It opens a WebSocket connection to the Backend Agent.

**Pseudocode: A2A Communication**
```javascript
// Inside the Frontend Agent's execution loop
async function resolve_api_types(endpoint) {
    const backendAgent = new A2ABridge("git@github.com:org/backend.git");
    
    // Ask the backend agent to use its own bespoke tools to find the answer
    const response = await backendAgent.ask({
        intent: "Get TypeScript interfaces for endpoint",
        target: endpoint
    });
    
    return response.data; // Returns exact, accurate types directly from the source of truth
}
```

---
## Conclusion
`makerlog-ai` shifts the paradigm from **"Agent as a Service"** to **"Agent as Infrastructure."** By allowing the agent to write its own tools, test itself, and accumulate a historical graph of the repository, we create an intelligence that doesn't just read the codebase—it is native to it.