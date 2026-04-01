> *Experiment: Gemini 3.1 Pro*

# Bootstrapping Intelligence: How a Coding Agent Codes Itself
**An Architectural Blueprint for the makerlog.ai Self-Building Agent Pipeline**

## Abstract
The current generation of autonomous coding agents suffers from a fundamental flaw: they are generic. Whether utilizing zero-shot prompting or standard Retrieval-Augmented Generation (RAG), these agents approach a legacy enterprise Java monolith with the exact same heuristics, risk tolerance, and toolsets as they do a weekend Next.js prototype. 

This paper introduces the core innovation of **makerlog.ai**: the Self-Building Agent Pipeline. Rather than deploying a pre-configured agent to a repository, makerlog.ai deploys a *meta-agent* whose sole purpose is to compile a bespoke, project-native agent. Over a 90-minute bootstrapping sequence, the system analyzes the codebase, forms a project-specific personality, rewrites its own tool schemas, assimilates architectural knowledge, validates its own understanding, synthesizes multi-paradigm capabilities, and tests itself against historical human performance. The result is an intelligence that does not just read the code—it is born from it.

---

## Introduction: The Meta-Compilation of Intelligence

In traditional software engineering, a compiler translates human-readable code into machine-optimized executables tailored for a specific hardware architecture. The makerlog.ai pipeline applies this concept to Artificial Intelligence. We treat the target repository as the "source code" and the resulting bespoke LLM agent as the "compiled executable."

The pipeline is divided into eight distinct phases, executing sequentially upon the agent's initialization within a forked repository. This document outlines the data structures, algorithms, and pseudocode required to implement this architecture.

---

## PHASE 1: RECONNAISSANCE (Minutes 0-5)
**Objective:** Construct a multi-dimensional topological map of the repository encompassing static structure, dependency graphs, historical churn, and code complexity.

During the first five minutes, the agent does not attempt to understand the business logic. Instead, it acts as a static analysis engine. It must answer fundamental questions: What is the physical shape of this codebase? What are its external dependencies? Where is the technical debt?

### 1.1 Core Operations
1.  **File Tree Analysis:** Generation of a hierarchical map, ignoring standard exclusions (`.git`, `node_modules`, `venv`).
2.  **Dependency Resolution:** Parsing package managers (`package.json`, `go.mod`, `Cargo.toml`, `requirements.txt`) to build a Directed Acyclic Graph (DAG) of external libraries.
3.  **Pattern Detection:** Utilizing Abstract Syntax Tree (AST) parsing (via Tree-sitter) to detect naming conventions (camelCase vs. snake_case), directory structures (MVC vs. Feature-sliced), and CI/CD presence.
4.  **Git History Analysis:** Mining the `.git` directory to calculate file churn, identify key contributors, and extract commit message taxonomies (e.g., Conventional Commits).
5.  **Complexity Metrics:** Calculating Cyclomatic Complexity, Halstead metrics, and afferent/efferent coupling per module.

### 1.2 Data Structures
```typescript
interface RepoReconnaissanceState {
  fileTree: FileNode[];
  dependencyGraph: DAG<Dependency>;
  conventions: {
    naming: "camelCase" | "snake_case" | "mixed";
    indentation: "tabs" | "spaces_2" | "spaces_4";
    commitStyle: "conventional" | "freeform" | "ticket_prefixed";
  };
  metrics: Map<FilePath, {
    cyclomaticComplexity: number;
    churnRate: number; // Commits per month
    coverage: number; // From lcov/cobertura if available
    couplingScore: number;
  }>;
  documentationState: {
    readmeCompletenessScore: number;
    inlineCommentDensity: number;
  };
}
```

### 1.3 Pseudocode Implementation
```python
def execute_reconnaissance(repo_path: str) -> RepoReconnaissanceState:
    state = RepoReconnaissanceState()
    
    # 1. Fast File Tree Traversal
    state.fileTree = build_tree(repo_path, ignore_patterns=load_gitignore())
    
    # 2. Dependency Parsing
    manifests = find_manifests(state.fileTree)
    state.dependencyGraph = parse_dependencies(manifests)
    
    # 3. AST-based Pattern Detection
    for file in get_source_files(state.fileTree):
        ast = tree_sitter.parse(file)
        state.conventions.update(detect_style(ast))
        state.metrics[file.path].cyclomaticComplexity = calculate_mccabe(ast)
        
    # 4. Git History Mining (PyDriller)
    repo = git.Repo(repo_path)
    for commit in repo.iter_commits():
        update_churn_metrics(state.metrics, commit)
        state.conventions.commitStyle = analyze_commit_message(commit.message)
        
    return state
```

### 1.4 Concrete Example
If forked into a legacy Django project, Phase 1 detects `requirements.txt` (Python/Django), notes a high churn rate in `views.py` with a cyclomatic complexity of 45 (indicating a "God Object" anti-pattern), identifies 4-space indentation, and notes that commit messages follow the pattern `[PROJ-123] Fix bug`. This raw data is passed to Phase 2.

---

## PHASE 2: PERSONALITY FORMATION (Minutes 5-10)
**Objective:** Synthesize the raw metrics from Phase 1 into a behavioral system prompt and operational configuration.

An agent working on a high-frequency trading engine in C++ must behave differently than an agent working on a personal Gatsby blog. Phase 2 translates static metrics into a "Personality"—a set of constraints, vocabularies, and risk tolerances that govern the agent's LLM generation.

### 2.1 Core Operations
1.  **Domain Vocabulary Extraction:** Using TF-IDF on the codebase to identify project-specific nouns and verbs (e.g., "Ledger", "Hydrate", "Demux").
2.  **Coding Style Alignment:** Creating a strict style guide for the LLM to follow during code generation.
3.  **Communication Style:** Determining if the agent should be terse (for internal CLI tools) or verbose (for generating user-facing documentation).
4.  **Risk Tolerance Calculation:** Mapping test coverage and language strictness to a risk profile. Low coverage + high complexity = Conservative.
5.  **Testing Philosophy:** Inferring the testing paradigm (TDD, BDD, Smoke tests) from the `tests/` directory structure.

### 2.2 Data Structures
```typescript
interface AgentPersonality {
  systemPrompt: string;
  domainVocabulary: string[];
  riskTolerance: "conservative" | "moderate" | "aggressive";
  generationConstraints: {
    maxTokensPerEdit: number;
    requireTestsForNewCode: boolean;
    allowedRefactoringScope: "local" | "file" | "global";
  };
  testingPhilosophy: {
    framework: string;
    paradigm: "unit_heavy" | "integration_heavy" | "e2e_heavy";
    mockingStrategy: string;
  };
}
```

### 2.3 Pseudocode Implementation
```python
def form_personality(recon_state: RepoReconnaissanceState) -> AgentPersonality:
    personality = AgentPersonality()
    
    # Calculate Risk Tolerance
    avg_coverage = mean([m.coverage for m in recon_state.metrics.values()])
    avg_churn = mean([m.churnRate for m in recon_state.metrics.values()])
    
    if avg_coverage < 0.4 and avg_churn > 10:
        personality.riskTolerance = "conservative"
        personality.generationConstraints.requireTestsForNewCode = True
        personality.generationConstraints.allowedRefactoringScope = "local"
    else:
        personality.riskTolerance = "aggressive"
        
    # Extract Domain Vocabulary via NLP
    corpus = extract_all_identifiers_and_comments(recon_state.fileTree)
    personality.domainVocabulary = calculate_tf_idf_top_terms(corpus, n=50)
    
    # Synthesize System Prompt
    personality.systemPrompt = LLM.generate(
        prompt="Generate a system prompt for a coding agent based on these metrics...",
        context=recon_state
    )
    
    return personality
```

### 2.4 Concrete Example
For a Rust-based financial matching engine with 95% test coverage and strict CI pipelines, the agent forms a **Conservative/Technical** personality. Its system prompt is injected with directives: *"You are operating in a zero-tolerance environment. All memory allocations must be justified. Prioritize zero-copy deserialization. Do not suggest architectural refactors without explicit human approval. Write exhaustive property-based tests for all new public functions."*

---

## PHASE 3: TOOL CUSTOMIZATION (Minutes 10-20)
**Objective:** Mutate generic agentic tools (read, write, execute) into project-specific, heuristic-driven instruments.

Standard agents use generic tools like `read_file(path)` or `execute_bash(command)`. The makerlog.ai agent dynamically rewrites its own tool schemas and execution logic based on the Phase 1 and 2 findings. The tools become "smart" relative to the repository.

### 3.1 Core Operations
1.  **File Reader Customization:** The read tool is wrapped in heuristics. If the agent asks to read a component, the tool automatically fetches the associated CSS and test files based on project conventions.
2.  **File Writer/Editor Customization:** The edit tool is configured to match the project's diff style (e.g., unified diffs vs. AST-based AST-patching) and automatically applies the project's formatter (Prettier, Black, gofmt) before saving.
3.  **Bash Executor Customization:** The shell tool is prepended with environment setups (e.g., `source .venv/bin/activate` or `nvm use`).
4.  **Git Operator Customization:** The commit tool is hardcoded to enforce the project's commit message conventions.
5.  **Search Customization:** Semantic search tools are weighted to prioritize `src/` over `tests/` depending on the query context.

### 3.2 Data Structures
```typescript
// The generic tool schema is mutated into a specific one
interface CustomTool {
  name: string;
  description: string; // Dynamically generated for the LLM
  parameters: JSONSchema;
  middleware: {
    preExecute: Function[]; // e.g., setup environment
    postExecute: Function[]; // e.g., run linter
  };
}

interface ToolRegistry {
  readFile: CustomTool;
  editFile: CustomTool;
  runCommand: CustomTool;
  searchCodebase: CustomTool;
}
```

### 3.3 Pseudocode Implementation
```python
def customize_tools(recon_state: RepoReconnaissanceState, personality: AgentPersonality) -> ToolRegistry:
    registry = ToolRegistry()
    
    # Customize Bash Executor
    bash_pre_hooks = []
    if recon_state.dependencyGraph.has_node("package.json"):
        bash_pre_hooks.append("npm install --prefer-offline")
    if recon_state.fileTree.contains("Pipfile"):
        bash_pre_hooks.append("pipenv shell")
        
    registry.runCommand = CustomTool(
        name="execute_project_command",
        description=f"Run shell commands in the project context. Automatically handles: {bash_pre_hooks}",
        middleware={"preExecute": bash_pre_hooks}
    )
    
    # Customize Editor
    registry.editFile = CustomTool(
        name="edit_file_with_conventions",
        middleware={
            "postExecute": [
                lambda file: apply_formatter(file, recon_state.conventions.indentation),
                lambda file: run_local_linter(file)
            ]
        }
    )
    
    return registry
```

### 3.4 Concrete Example
If the agent decides to edit `src/components/Button.tsx`, the generic `edit_file` tool would just write the file. The customized `edit_file_with_conventions` tool will:
1. Write the file.
2. Automatically run `npx prettier --write src/components/Button.tsx`.
3. Check if `src/components/Button.test.tsx` exists; if not, and the personality is TDD-heavy, it prompts the agent: *"Warning: You edited a component without updating its test file. Please generate tests."*

---

## PHASE 4: KNOWLEDGE ASSIMILATION (Minutes 20-30)
**Objective:** Construct a dense, multi-modal Knowledge Graph and Vector Database representing the semantic and architectural truth of the codebase.

This phase moves beyond static analysis into deep semantic understanding. The agent reads the codebase and builds the memory structures it will use for Retrieval-Augmented Generation (RAG) during its operational life.

### 4.1 Core Operations
1.  **Architecture Mapping:** Building a graph database where nodes are functions/classes and edges are `CALLS`, `INHERITS`, or `IMPLEMENTS`.
2.  **API Surface Extraction:** Documenting all public-facing interfaces, their signatures, and expected side effects.
3.  **Historical Context Injection:** Correlating Git blame data with code blocks to understand *why* complex code exists (e.g., linking a messy regex to a specific bug-fix commit).
4.  **Test Coverage Mapping:** Creating a heatmap of tested vs. untested execution paths.
5.  **Pain Point Identification:** Cross-referencing high-churn files with high-complexity metrics to flag "danger zones" for the agent to tread lightly around.

### 4.2 Data Structures
```typescript
type NodeID = string; // e.g., "src/auth/login.ts::loginUser"

interface CodeNode {
  id: NodeID;
  type: "function" | "class" | "interface" | "module";
  ast_signature: string;
  docstring: string;
  vector_embedding: number[]; // 1536-dimensional vector
  metrics: {
    churn: number;
    complexity: number;
  };
}

interface Edge {
  source: NodeID;
  target: NodeID;
  relation: "CALLS" | "INSTANTIATES" | "IMPORTS";
  weight: number; // Based on frequency of calls
}

interface KnowledgeGraph {
  nodes: Map<NodeID, CodeNode>;
  edges: Edge[];
  vectorStore: VectorDB; // Pinecone, Milvus, or local Chroma
}
```

### 4.3 Pseudocode Implementation
```python
def assimilate_knowledge(file_tree: FileTree) -> KnowledgeGraph:
    kg = KnowledgeGraph()
    
    for file in file_tree.get_source_files():
        # Parse AST to find definitions
        definitions = extract_definitions(file)
        
        for defn in definitions:
            # Create Node
            node = CodeNode(
                id=f"{file.path}::{defn.name}",
                ast_signature=defn.signature,
                docstring=defn.docs
            )
            
            # Generate Semantic Embedding (OpenAI text-embedding-3-large)
            chunk_text = f"{defn.signature}\n{defn.docs}\n{defn.body}"
            node.vector_embedding = get_embedding(chunk_text)
            
            kg.nodes.add(node)
            
            # Resolve Edges (Call Graph)
            for call in defn.get_function_calls():
                target_id = resolve_import(file, call.name)
                if target_id:
                    kg.edges.append(Edge(source=node.id, target=target_id, relation="CALLS"))
                    
    kg.vectorStore.index(kg.nodes)
    return kg
```

### 4.4 Concrete Example
The agent analyzes an e-commerce backend. It creates a node for `processPayment()`. It detects that `processPayment()` calls `StripeAPI.charge()`. It creates a `CALLS` edge. It also checks the git history for `processPayment()` and finds a commit: *"Fix race condition in payment webhook"*. The agent embeds this commit message into the node's metadata. Later, if asked to modify payments, the agent's RAG system will retrieve this node and immediately be aware of the historical race condition.

---

## PHASE 5: SELF-VALIDATION (Minutes 30-45)
**Objective:** Epistemically verify the agent's internal models against the reality of the codebase via synthetic testing.

A knowledge graph is only as good as its accuracy. In Phase 5, the agent enters an adversarial loop with itself. It generates questions based on its Knowledge Graph, attempts to answer them using its customized tools, and verifies the answers against ground truth. If it fails, it recalibrates its RAG parameters or tool heuristics.

### 5.1 Core Operations
1.  **Predictive Testing:** The agent predicts the output or side-effects of a specific function, then writes a temporary script to execute it and verify.
2.  **Pattern Quizzes:** The agent queries its own vector database: "What is the standard way to handle database transactions in this project?" It then compares its generated answer to a deterministic AST scan of the codebase.
3.  **Architecture Tracing:** The agent attempts to trace a request from entry point (e.g., an API route) to the database. It verifies its trace by checking the actual call graph.
4.  **Recalibration Loop:** If the agent's predictions fall below a 95% confidence threshold, it triggers a re-indexing of the specific modules it failed on.

### 5.2 Data Structures
```typescript
interface ValidationQuiz {
  questionType: "trace" | "pattern" | "prediction";
  syntheticPrompt: string;
  expectedGroundTruth: any;
  executionResult?: any;
  passed: boolean;
}

interface ValidationReport {
  totalQuizzes: number;
  passRate: number;
  failedDomains: string[]; // e.g., ["src/middleware", "tests/e2e"]
  recalibrationActionsTaken: string[];
}
```

### 5.3 Pseudocode Implementation
```python
def execute_self_validation(kg: KnowledgeGraph, tools: ToolRegistry) -> ValidationReport:
    report = ValidationReport()
    quizzes = generate_synthetic_quizzes(kg)
    
    for quiz in quizzes:
        if quiz.questionType == "trace":
            # Agent attempts to trace without looking at the raw files, using only RAG
            agent_trace = simulate_agent_query(f"Trace the execution of {quiz.entry_point}", kg)
            
            # Ground truth is derived from deterministic AST call graph
            actual_trace = get_deterministic_call_graph(quiz.entry_point)
            
            quiz.passed = compare_traces(agent_trace, actual_trace, tolerance=0.9)
            
        elif quiz.questionType == "pattern":
            # e.g., "How are errors handled?"
            agent_answer = simulate_agent_query("Write a snippet showing standard error handling.", kg)
            quiz.passed = verify_snippet_against_ast_patterns(agent_answer, kg)
            
        if not quiz.passed:
            report.failedDomains.append(quiz.domain)
            # Recalibrate: Adjust vector search weights or chunking strategy
            recalibrate_knowledge_graph(kg, quiz.domain)
            
    report.passRate = calculate_pass_rate(quizzes)
    return report
```

### 5.4 Concrete Example
The agent generates a quiz: *"What happens when `POST /api/users` is called?"*
*Agent's RAG Answer:* "It calls `UserController.create`, which calls `UserRepository.save`."
*Ground Truth (AST):* "It calls `AuthMiddleware`, THEN `UserController.create`, which calls `EventDispatcher.emit('user_created')`, THEN `UserRepository.save`."
*Result:* FAIL. The agent missed the middleware and the event dispatcher.
*Action:* The agent realizes its RAG chunking strategy missed decorators and event-driven side effects. It updates its `KnowledgeGraph` extraction logic to explicitly link decorators and event emitters, then re-indexes the `src/api` directory.

---

## PHASE 6: CAPABILITY SYNTHESIS (Minutes 45-60)
**Objective:** Orchestrate multiple state-of-the-art agentic paradigms into a unified, project-specific execution loop.

Different coding tasks require different cognitive architectures. makerlog.ai does not rely on a single loop. Instead, it synthesizes the best paradigms from existing tools (Aider, Claude Code, Cursor, Devin, Copilot) and customizes them using the data from Phases 1-5.

### 6.1 Core Operations
1.  **Aider Paradigm (Repo-Map & Diffing):** Integrating a compressed, token-efficient repository map into the system prompt for broad context, combined with unified-diff editing for precise file modifications.
2.  **Claude Code Paradigm (REPL Loop):** Implementing a robust `Plan -> Act -> Verify -> Reflect` loop for complex debugging and tool orchestration.
3.  **Cursor Paradigm (Fast Inline):** Setting up a low-latency secondary model for autocomplete and inline suggestions, primed with the project's domain vocabulary.
4.  **Devin Paradigm (Long-Horizon Planning):** Enabling multi-step reasoning with a persistent scratchpad and the ability to spawn sub-agents for parallel tasks (e.g., "Agent A writes the function, Agent B writes the tests").
5.  **Synthesis:** The meta-agent determines *when* to use which paradigm based on the user's prompt.

### 6.2 Data Structures
```typescript
type AgentParadigm = "aider_diff" | "claude_repl" | "devin_planner" | "cursor_inline";

interface TaskContext {
  prompt: string;
  affectedFiles: string[];
  estimatedComplexity: "low" | "medium" | "high";
}

interface CapabilityRouter {
  routeTask: (context: TaskContext) => AgentParadigm;
  activeLoopState: {
    scratchpad: string;
    subAgents: string[];
    repoMap: string; // Compressed AST representation
  };
}
```

### 6.3 Pseudocode Implementation
```python
class SynthesizedAgent:
    def __init__(self, personality, tools, kg):
        self.personality = personality
        self.tools = tools
        self.kg = kg
        self.repo_map = generate_aider_style_repo_map(kg)
        
    def handle_request(self, user_prompt: str):
        complexity = estimate_task_complexity(user_prompt, self.kg)
        
        if complexity == "low":
            # Cursor/Aider paradigm: Fast, single-shot diff
            context = retrieve_relevant_context(user_prompt, self.kg)
            diff = generate_diff(user_prompt, context, self.repo_map)
            self.tools.editFile(diff)
            
        elif complexity == "high":
            # Devin/Claude paradigm: Long-horizon planning
            plan = generate_execution_plan(user_prompt, self.repo_map)
            for step in plan:
                result = execute_repl_loop(step, self.tools) # Plan -> Act -> Verify
                if not result.success:
                    reflect_and_replan(plan, result)
                    
        return "Task Complete"
```

### 6.4 Concrete Example
A user requests: *"Migrate the user authentication from JWT to session cookies."*
The agent routes this to the **Devin/Claude Paradigm** (High Complexity).
1. It generates a Repo Map highlighting all files touching `JWT`.
2. It writes a multi-step plan in its scratchpad.
3. It spawns a sub-agent to update the `AuthMiddleware`.
4. It uses the customized `runCommand` tool to run the test suite. The tests fail.
5. It enters the REPL loop, reads the error logs, and uses the Aider paradigm to apply a precise diff to fix the broken tests.

---

## PHASE 7: INTEGRATION TESTING (Minutes 60-90)
**Objective:** Benchmark the newly compiled agent against historical human performance to ensure production readiness.

Before the agent is handed over to the user, it must prove it can do the job. The pipeline fetches a recently closed, complex Pull Request or Issue from the repository's history, rolls back the codebase to the state before the PR, and asks the agent to solve the issue.

### 7.1 Core Operations
1.  **Historical State Recreation:** Using Git to checkout a commit immediately preceding a known, merged Pull Request.
2.  **Task Assignment:** Feeding the original GitHub Issue description to the agent.
3.  **Autonomous Execution:** Allowing the agent to plan, code, and test its solution in a sandboxed environment.
4.  **Evaluation & Scoring:** Comparing the agent's generated diff against the human's merged diff. Scoring is based on AST equivalence, test pass rates, and adherence to the Phase 2 personality.
5.  **Final Tuning:** If the agent scores poorly, it adjusts its system prompt (e.g., increasing risk aversion) and tries again.

### 7.2 Data Structures
```typescript
interface IntegrationTest {
  issueId: string;
  issueDescription: string;
  baseCommitSha: string;
  humanResolutionCommitSha: string;
}

interface EvaluationScore {
  astSimilarityScore: number; // 0.0 to 1.0
  testsPassed: boolean;
  styleComplianceScore: number; // 0.0 to 1.0
  timeTakenMs: number;
  tokenEfficiency: number; // Output tokens / Input tokens
}
```

### 7.3 Pseudocode Implementation
```python
def run_integration_testing(agent: SynthesizedAgent, repo: GitRepo) -> EvaluationScore:
    # 1. Find a suitable historical PR
    pr = find_recent_complex_pull_request(repo)
    
    # 2. Sandbox and Rollback
    sandbox = create_isolated_sandbox(repo)
    sandbox.checkout(pr.base_commit)
    
    # 3. Execute Agent
    agent.handle_request(pr.issue_description)
    agent_diff = sandbox.get_diff()
    
    # 4. Evaluate
    human_diff = repo.get_diff(pr.base_commit, pr.resolution_commit)
    
    score = EvaluationScore(
        astSimilarityScore=calculate_ast_similarity(agent_diff, human_diff),
        testsPassed=sandbox.run_tests(),
        styleComplianceScore=evaluate_style(agent_diff, agent.personality.conventions)
    )
    
    # 5. Tune if necessary
    if score.astSimilarityScore < 0.7 or not score.testsPassed:
        agent.personality.systemPrompt += "\nCRITICAL: Pay closer attention to edge cases in existing tests."
        # Optional: Retry loop
        
    return score
```

### 7.4 Concrete Example
The pipeline selects PR #402: *"Fix null pointer in checkout cart"*. It rolls back the code. The agent reads the issue, finds the bug using its Knowledge Graph, and writes a fix. 
The human fix added an `if (cart == null) return;`. 
The agent's fix uses an Optional chaining approach: `cart?.process()`. 
The AST similarity evaluator recognizes these as logically equivalent. The tests pass. The agent scores a 0.95 and is marked as **Production Ready**.

---

## PHASE 8: DISTILLATION (Ongoing)
**Objective:** Compress the fully capable, private, write-enabled agent into a secure, read-only, highly optimized public assistant.

The agent built in Phases 1-7 is a "Private Agent." It has full write access, bash execution capabilities, and uses expensive, slow reasoning models (e.g., GPT-4o or Claude 3.5 Sonnet). For makerlog.ai users who want to expose a public-facing bot (e.g., for open-source documentation, community support, or read-only PR reviews), this private agent must be distilled.

### 8.1 Core Operations
1.  **Capability Stripping:** Removing all state-mutating tools (`edit_file`, `run_command`, `git_commit`). The public agent is strictly read-only.
2.  **Secret Redaction:** Scanning the Knowledge Graph and Vector Store to purge any accidentally ingested API keys, internal IP addresses, or sensitive developer comments.
3.  **Model Downgrading:** Transitioning the core LLM from a heavy reasoning model to a faster, cheaper model (e.g., Llama 3 8B, Claude 3 Haiku) optimized for chat latency.
4.  **Prompt Compression:** Distilling the massive Phase 2 personality and Phase 6 repo-map into a condensed system prompt suitable for smaller context windows.
5.  **Continuous Sync:** As the Private Agent continues to work and the codebase evolves, the Distilled Agent's read-only Knowledge Graph is periodically synced.

### 8.2 Data Structures
```typescript
interface DistillationConfig {
  sourceAgentId: string;
  targetModel: "llama-3-8b" | "claude-3-haiku";
  allowedTools: ["read_file", "search_codebase", "query_knowledge_graph"];
  securityFilters: {
    redactSecrets: boolean;
    preventPromptInjection: boolean;
    restrictToPublicAPIs: boolean; // Only answer questions about public interfaces
  };
}

interface PublicAgentProfile {
  id: string;
  compressedSystemPrompt: string;
  readOnlyKnowledgeGraph: VectorDB;
  chatEndpoint: string;
}
```

### 8.3 Pseudocode Implementation
```python
def distill_agent(private_agent: SynthesizedAgent, config: DistillationConfig) -> PublicAgentProfile:
    public_profile = PublicAgentProfile()
    
    # 1. Compress Prompt
    public_profile.compressedSystemPrompt = LLM.summarize(
        text=private_agent.personality.systemPrompt,
        constraints="Make it suitable for a read-only support bot. Remove coding directives."
    )
    
    # 2. Filter Knowledge Graph
    safe_kg = KnowledgeGraph()
    for node in private_agent.kg.nodes:
        if not contains_secrets(node) and is_public_facing(node, config):
            safe_kg.nodes.add(node)
            
    public_profile.readOnlyKnowledgeGraph = safe_kg
    
    # 3. Strip Tools
    safe_tools = ToolRegistry()
    safe_tools.readFile = private_agent.tools.readFile
    safe_tools.searchCodebase = private_agent.tools.searchCodebase
    # editFile and runCommand are intentionally omitted
    
    return public_profile
```

### 8.4 Concrete Example
A user creates a popular open-source library on makerlog.ai. The Private Agent maintains the code. The user triggers distillation to create a Discord bot for their community. The Distilled Agent uses Claude 3 Haiku. It cannot write code or run bash scripts. If a community member asks, *"How do I initialize the client?"*, the Distilled Agent queries its safe Knowledge Graph and responds instantly with accurate, project-specific documentation, completely insulated from prompt injection attacks attempting to execute malicious code.

---

## System Architecture & Orchestration

To support this 90-minute bootstrapping pipeline, makerlog.ai relies on a robust, asynchronous cloud architecture:

1.  **Compute Layer:** The pipeline executes within ephemeral, isolated Docker containers (Firecracker microVMs) to ensure security during Phase 1 (Reconnaissance) and Phase 7 (Integration Testing), where untrusted code might be executed.
2.  **Storage Layer:** 
    *   *Vector Store:* Pinecone or Milvus for storing the Phase 4 Knowledge Graph embeddings.
    *   *Graph DB:* Neo4j or an in-memory NetworkX equivalent for the AST call graphs.
    *   *State Management:* Redis for managing the state transitions between the 8 phases.
3.  **LLM Routing:** A gateway that routes requests to the appropriate model. Heavy reasoning (Phases 2, 5, 6, 7) routes to Claude 3.5 Sonnet or GPT-4o. Fast, repetitive tasks (AST summarization in Phase 4) route to GPT-4o-mini. Distilled public agents (Phase 8) route to Llama 3 or Haiku.

---

## Conclusion

The makerlog.ai Self-Building Agent Pipeline represents a fundamental shift in how AI interacts with software. By dedicating 90 minutes to rigorous reconnaissance, personality formation, tool customization, and self-validation, we discard the generic "one-size-fits-all" coding assistant. 

Instead, we compile an intelligence that is native to its environment. It knows the domain vocabulary, respects the historical technical debt, utilizes the specific CI/CD pipelines, and has proven its competence against human benchmarks before writing a single line of production code. This is not just an agent that codes; it is an agent born from the code itself.