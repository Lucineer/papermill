> *DeepSeek Chat*

# **MakerLog.ai: The Self-Coding AI Architecture**

## **1. Executive Overview**

MakerLog.ai is an autonomous software development system designed to iteratively improve its own capabilities through a self-referential build pipeline. Unlike traditional coding assistants that operate on user prompts, MakerLog treats its own codebase as both the subject and object of development, creating a recursive self-improvement loop. The system combines symbolic AI techniques with LLM-based generation, creating a hybrid architecture that learns from both successes and failures.

## **2. Core Architecture Diagram**

```
┌─────────────────────────────────────────────────────────────┐
│                    USER INTERFACE LAYER                      │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐    │
│  │   Chat   │  │  Editor  │  │ Terminal │  │ Dashboard│    │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘    │
└───────────────────────┬─────────────────────────────────────┘
                        │
┌─────────────────────────────────────────────────────────────┐
│                 ORCHESTRATION CONTROLLER                     │
│  ┌────────────────────────────────────────────────────┐     │
│  │             Self-Improvement Scheduler              │     │
│  └────────────────────────────────────────────────────┘     │
└─────────────────┬─────────────────┬─────────────────────────┘
                  │                 │
    ┌─────────────▼─────┐ ┌─────────▼──────────┐
    │  Self-Awareness   │ │   Build Pipeline   │
    │      Layer        │ │      Engine        │
    └─────────────┬─────┘ └─────────┬──────────┘
                  │                 │
    ┌─────────────▼─────────────────▼──────────┐
    │           Code Generation Layer          │
    └─────────────┬─────────────────┬──────────┘
                  │                 │
    ┌─────────────▼─────────────────▼──────────┐
    │           Code Execution Layer           │
    └─────────────┬─────────────────┬──────────┘
                  │                 │
    ┌─────────────▼─────────────────▼──────────┐
    │            Knowledge Layer               │
    └─────────────┬─────────────────┬──────────┘
                  │                 │
    ┌─────────────▼─────────────────▼──────────┐
    │            Learning Layer                │
    └──────────────────────────────────────────┘
```

## **3. Detailed Component Architecture**

### **3.1 Self-Awareness Layer**

**Purpose**: Maintain accurate model of current capabilities and identify improvement opportunities.

**Components**:

1. **Capability Scanner** (`scanner/`)
   - `repo_analyzer.py`: AST-based code analysis
   - `dependency_mapper.py`: Maps imports and function calls
   - `capability_extractor.py`: Identifies what functions/features exist
   - Output: `capability_graph.json` - directed graph of capabilities

2. **Gap Analyzer** (`gap_analysis/`)
   - `ideal_spec.json`: Definition of "perfect coding assistant"
   - `comparison_engine.py`: Compares current vs ideal capabilities
   - `gap_scorer.py`: Prioritizes gaps by impact/effort ratio
   - Output: `gap_analysis_report.json`

3. **Priority Engine** (`prioritization/`)
   - `effort_estimator.py`: Predicts development time for each gap
   - `impact_predictor.py`: Estimates user value of capability
   - `dependency_resolver.py`: Ensures prerequisites are built first
   - Output: `build_queue.json` - ordered list of tasks

4. **Self-Assessment Module** (`assessment/`)
   - `coding_benchmark.py`: Runs standardized coding challenges
   - `performance_tracker.py`: Measures accuracy, speed, efficiency
   - `quality_scorer.py`: Evaluates code quality metrics
   - Output: `agent_scorecard.json` - current performance metrics

**Data Flow**:
```
Repo Files → AST Parsing → Capability Graph → Gap Analysis → 
Priority Scoring → Build Queue → Self-Assessment → Score Update
```

### **3.2 Code Generation Layer**

**Purpose**: Generate syntactically and semantically correct code.

**Components**:

1. **LLM Orchestrator** (`llm/`)
   - `provider_router.py`: Routes to OpenAI/Anthropic/Google based on task
   - `prompt_optimizer.py`: Dynamically improves prompts based on results
   - `cost_optimizer.py`: Balances cost vs performance
   - Configuration: `llm_config.yaml`

2. **Prompt Template System** (`prompts/`)
   - `templates/`: Directory of specialized prompt templates
     - `code_generation.j2`
     - `bug_fix.j2`
     - `test_writing.j2`
     - `refactor.j2`
   - `context_injector.py`: Injects relevant code context
   - `example_selector.py`: Chooses relevant examples from knowledge base

3. **Context Builder** (`context/`)
   - `relevant_file_finder.py`: Identifies files related to current task
   - `symbol_resolver.py`: Maps symbols to their definitions
   - `import_analyzer.py`: Determines necessary imports
   - Output: `generation_context.json`

4. **Code Validator** (`validation/`)
   - `syntax_checker.py`: Language-specific syntax validation
   - `type_analyzer.py`: Static type checking
   - `style_validator.py`: Enforces coding standards
   - `security_scanner.py`: Basic security checks
   - Output: `validation_report.json`

**Data Flow**:
```
Task + Context → Prompt Assembly → LLM Call → 
Generated Code → Validation → [Valid Code | Error Feedback Loop]
```

### **3.3 Code Execution Layer**

**Purpose**: Safely execute and test generated code.

**Components**:

1. **Sandbox Environment** (`sandbox/`)
   - `docker_manager.py`: Manages isolated Docker containers
   - `resource_limiter.py`: Limits CPU/memory usage
   - `network_controller.py`: Controls network access
   - Configuration: `sandbox_config.yaml`

2. **Test Runner** (`testing/`)
   - `test_discoverer.py`: Finds and categorizes tests
   - `test_executor.py`: Runs tests with coverage tracking
   - `flaky_test_detector.py`: Identifies inconsistent tests
   - Output: `test_results.json`

3. **Boot Checker** (`boot_check/`)
   - `service_validator.py`: Verifies services start correctly
   - `api_tester.py`: Tests API endpoints
   - `ui_smoke_test.py`: Basic UI functionality tests
   - Output: `boot_check_report.json`

4. **Rollback System** (`rollback/`)
   - `git_manager.py`: Handles git operations
   - `snapshot_manager.py`: Creates system snapshots
   - `recovery_planner.py`: Plans recovery from failures
   - State: `rollback_points.json`

**Data Flow**:
```
Code Changes → Sandbox Execution → Test Running → 
Boot Verification → [Success → Commit | Failure → Rollback]
```

### **3.4 Knowledge Layer**

**Purpose**: Maintain persistent knowledge about the codebase and patterns.

**Components**:

1. **Code Index** (`index/`)
   - `ast_indexer.py`: Creates AST-based index of entire codebase
   - `symbol_database.py`: Maps symbols to locations and usages
   - `type_inference_engine.py`: Infers types across codebase
   - Storage: SQLite database `code_index.db`

2. **Pattern Library** (`patterns/`)
   - `pattern_miner.py`: Extracts patterns from successful code
   - `pattern_matcher.py`: Matches patterns to new problems
   - `pattern_evaluator.py`: Scores pattern effectiveness
   - Storage: `patterns.jsonl` with metadata

3. **Error Database** (`errors/`)
   - `error_categorizer.py`: Classifies errors by type and severity
   - `fix_tracker.py`: Tracks what fixes work for which errors
   - `solution_recommender.py`: Recommends fixes for new errors
   - Storage: `error_knowledge_base.db`

4. **Style Guide Learner** (`style/`)
   - `git_history_analyzer.py`: Analyzes commit patterns
   - `style_extractor.py`: Extracts coding style preferences
   - `consistency_checker.py`: Ensures style consistency
   - Output: `project_style_guide.json`

**Data Flow**:
```
Code/Errors/Commits → Analysis → Pattern Extraction → 
Knowledge Storage → Retrieval Augmentation
```

### **3.5 Learning Layer**

**Purpose**: Improve future performance based on past experiences.

**Components**:

1. **Error Learning Module** (`learning/errors/`)
   - `failure_analyzer.py`: Root cause analysis of failures
   - `lesson_extractor.py`: Extracts generalizable lessons
   - `prevention_generator.py`: Creates rules to