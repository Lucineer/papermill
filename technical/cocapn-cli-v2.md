> *DeepSeek Chat*

# cocapn CLI v2: Composable Repo-Agent Management

## Overview
cocapn CLI v2 reimagines agent management with a clean, composable architecture that reduces 42 commands to 10 logical groups while maintaining full functionality. The design emphasizes UNIX philosophy—each command does one thing well, pipes beautifully, and supports multiple output formats.

## Command Architecture

### 1. **cocapn init** — Project Initialization
```
cocapn init [template] [--byok-provider=openai] [--model=gpt-4] [--region=us-east]
```
Fork template repos with BYOK (Bring Your Own Keys) configuration baked in.

**Examples:**
```bash
# Minimal setup with defaults
cocapn init customer-support

# Full BYOK configuration
cocapn init research-assistant \
  --byok-provider=anthropic \
  --model=claude-3-opus \
  --region=eu-central

# Pipe config to file
cocapn init code-reviewer --byok-provider=openai | tee config.yaml
```

### 2. **cocapn up** — Local Development
```
cocapn up [--port=3000] [--hot-reload] [--verbose]
```
Start local dev server with hot reload and real-time logging.

**Examples:**
```bash
# Standard dev server
cocapn up --port=8080

# With hot reload and verbose logging
cocapn up --hot-reload --verbose | grep "ERROR"

# Pipe logs to monitoring tool
cocapn up --verbose | jq '.timestamp, .message'
```

### 3. **cocapn deploy** — Production Deployment
```
cocapn deploy [--env=production] [--preview] [--rollback=version]
```
Deploy to Cloudflare Workers with zero-downtime rollouts.

**Examples:**
```bash
# Deploy to production
cocapn deploy --env=production

# Create preview deployment
cocapn deploy --preview --env=staging

# Rollback to previous version
cocapn deploy --rollback=v1.2.3 | cocapn fleet broadcast "Rollback complete"
```

### 4. **cocapn chat** — Interactive Agent Communication
```
cocapn chat [--stream] [--temperature=0.7] [query]
```
Chat with agents, supporting both streaming and single responses.

**Examples:**
```bash
# Interactive chat session
cocapn chat --stream

# Single query with JSON output
cocapn chat "Summarize Q3 report" --format=json | jq '.response'

# Pipe memory search into chat
cocapn memory search "customer feedback" | cocapn chat "Address these concerns"
```

### 5. **cocapn memory** — Agent Memory Management
The memory system uses content-addressable storage with automatic embedding.

**Examples:**
```bash
# List memories in table format (default)
cocapn memory list --limit=20

# Add memory from multiple sources
cocapn memory add --text="API key rotation every 90 days" --tags=security,api
cocapn memory add --file=meeting-notes.md --tags=meeting,2024-q1
cocapn memory add --url=https://docs.example.com --tags=documentation

# Semantic search with similarity threshold
cocapn memory search "authentication best practices" --threshold=0.8

# Forget (archive) memories by ID or pattern
cocapn memory forget mem_abc123
cocapn memory forget --tags=temp --before="2024-01-01"

# Export to markdown with frontmatter
cocapn memory export --since="30d" --format=markdown > knowledge-base.md

# Pipe search results to chat for analysis
cocapn memory search "error patterns" --limit=10 | cocapn chat "Analyze these errors"
```

### 6. **cocapn learn** — Agent Knowledge Acquisition
```
cocapn learn [source] [--chunk-size=1000] [--strategy=auto]
```
Teach agents from various sources with intelligent chunking.

**Examples:**
```bash
# Learn from local documentation
cocapn learn ./docs --strategy=hierarchical

# Learn from website with depth control
cocapn learn https://docs.example.com --depth=2 --exclude="/archive"

# Learn from text with custom metadata
echo "Quarterly target: Increase MAU by 15%" | cocapn learn --source=stdin --tags=goals,q4

# Pipe learned content to memory system
cocapn learn technical-spec.pdf | cocapn memory add --tags=specification
```

### 7. **cocapn skill** — Skill Ecosystem Management
Skills are versioned, sandboxed modules from ClawHub marketplace.

**Examples:**
```bash
# List installed skills with versions
cocapn skill list --format=json | jq '.[] | .name, .version'

# Install skill from ClawHub
cocapn skill install web-search --version=2.1.0

# Create new skill scaffold
cocapn skill create sentiment-analysis \
  --language=python \
  --template=analyzer

# Pipe skill output to chat
cocapn skill run web-search "latest AI papers" | cocapn chat "Summarize these"
```

### 8. **cocapn fleet** — Multi-Agent Coordination
```
cocapn fleet [subcommand] [--format=table]
```
Manage distributed agent networks with health monitoring.

**Examples:**
```bash
# List all connected agents
cocapn fleet list --status=healthy

# Connect to external agent
cocapn fleet connect https://agent.example.com --alias=research-bot

# Check fleet health with detailed output
cocapn fleet status --format=yaml

# Broadcast message to entire fleet
cocapn fleet broadcast "Database maintenance in 10 minutes" --urgency=high

# Pipe fleet status to monitoring system
cocapn fleet status --format=json | curl -X POST -d @- https://monitor.example.com
```

### 9. **cocapn twin** — Digital Twin Management
```
cocapn twin [subcommand] [--fidelity=high]
```
Create and simulate digital twins from interaction transcripts.

**Examples:**
```bash
# Create twin from conversation history
cocapn twin create ./conversations/ceo-chats/ --name=ceo-twin

# Run simulation with custom scenario
cocapn twin simulate ceo-twin --scenario=budget-meeting --format=json

# Calibrate twin accuracy
cocapn twin calibrate ceo-twin --test-data=validation-set.json --iterations=5

# Pipe simulation to analysis
cocapn twin simulate ceo-twin --scenario=crisis | cocapn chat "Identify risks"
```

### 10. **cocapn config** — Configuration Management
```
cocapn config [action] [key] [value] [--global]
```
Hierarchical config system (project < user < system) with BYOK support.

**Examples:**
```bash
# Get configuration value
cocapn config get llm.provider
cocapn config get --global user.email

# Set project-specific config
cocapn config set memory.retention_days 90
cocapn config set skills.auto_update false

# Configure BYOK providers
cocapn config byok openai --api-key=$OPENAI_KEY --model=gpt-4-turbo
cocapn config byok anthropic --api-key=$ANTHROPIC_KEY --model=claude-3

# Export config for backup
cocapn config get --all --format=yaml > config-backup.yaml

# Pipe config to deployment
cocapn config get --all --format=json | cocapn deploy --config=stdin
```

## Advanced Composition Examples

**Multi-agent workflow:**
```bash
# Search memories, analyze with chat, broadcast to fleet
cocapn memory search "outage" --since="7d" | \
  cocapn chat "Create incident report" | \
  cocapn fleet broadcast --urgency=critical
```

**Learning pipeline:**
```bash
# Learn from docs, add to memory, update twin
cocapn learn ./api-changes.md --strategy=technical | \
  cocapn memory add --tags=api,breaking-changes | \
  cocapn twin calibrate support-twin --source=memory
```

**Skill chaining:**
```bash
# Web search -> Summarize -> Save to memory
cocapn skill run web-search "RAG best practices 2024" | \
  cocapn skill run summarizer --length=brief | \
  cocapn memory add --tags=rag,research
```

## Output Format Control
Every command supports:
```bash
