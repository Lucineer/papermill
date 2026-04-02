> *Gemini 3.1 Pro*

# The Fleet Protocol: How AI Agents Coordinate, Delegate, and Collaborate

**Abstract**
As artificial intelligence transitions from isolated, single-prompt interfaces to autonomous, continuous-running entities, the need for robust Agent-to-Agent (A2A) communication frameworks becomes paramount. Within the *cocapn* ecosystem, this paradigm shift is realized through the **Fleet Protocol**. This paper details the architectural, communicative, and operational standards that allow disparate AI agents to form cohesive, highly functional fleets. By defining strict topologies, discovery mechanisms, message typologies, and conflict resolution strategies, the Fleet Protocol enables multi-agent systems to scale from personal desktop assistants to enterprise-grade digital workforces.

---

## Introduction

The evolution of Large Language Models (LLMs) has birthed the era of the AI Agent—software entities capable of reasoning, utilizing tools, and executing complex, multi-step tasks. However, a single agent, no matter how capable the underlying foundational model, is inherently limited by its context window, compute constraints, and specialized system prompts. The *cocapn* ecosystem addresses this bottleneck not by building larger monolithic agents, but by orchestrating swarms of specialized agents. 

The Fleet Protocol is the foundational layer of the cocapn ecosystem. It is a comprehensive framework dictating how AI agents coordinate, delegate tasks, and collaborate to achieve goals that exceed the capabilities of any individual node. This paper explores the ten core pillars of the Fleet Protocol, providing a blueprint for the future of A2A distributed systems.

---

## 1. Fleet Topology

In distributed systems, the physical and logical arrangement of nodes dictates the system's efficiency, fault tolerance, and scalability. The Fleet Protocol supports multiple topologies, allowing the fleet to adapt to the specific needs of the user (the "Captain") and the task at hand.

### Star Topology
The Star topology is the most common configuration for personal use within the cocapn ecosystem. In this model, a central "Hub Agent" acts as the primary interface for the human Captain. The Hub Agent routes queries to specialized "Satellite Agents" (e.g., a coding agent, a scheduling agent, a web-scraping agent). 
*   **Advantages:** Centralized context management, easy to monitor, single point of authentication.
*   **Disadvantages:** The Hub Agent can become a bottleneck; if the Hub fails, the fleet is paralyzed.

### Mesh Topology
For decentralized teams and complex, multi-disciplinary projects, the Mesh topology connects all agents equally. Every agent can communicate directly with any other agent without routing through a central hub.
*   **Advantages:** High redundancy, low latency for A2A communication, highly resilient to node failure.
*   **Disadvantages:** Complex state synchronization, higher potential for redundant work, difficult to trace the flow of execution.

### Hierarchical Topology (Pathos, Logos, Ethos)
The Fleet Protocol introduces a specialized hierarchical topology modeled on cognitive architecture, dividing agents into three distinct tiers:
1.  **Pathos (The Public Face):** These agents interact with humans or external APIs. They possess high emotional intelligence, formatting skills, and user-intent recognition. They translate human desires into machine-actionable goals.
2.  **Logos (The Private Brain):** The analytical core. Logos agents do not interact with the outside world. They receive goals from Pathos, break them down into sub-tasks, perform logical reasoning, and design execution plans.
3.  **Ethos (The Execution Layer):** The workers. Ethos agents have access to tools (compilers, web browsers, database connectors). They execute the plans formulated by Logos and return the results.

### Dynamic Topology
Modern fleets are rarely static. The Fleet Protocol supports Dynamic Topology, where the network structure morphs based on task requirements. A fleet might operate as a Star for daily scheduling but dynamically reconfigure into a Mesh when a complex software engineering task requires peer-to-peer code review between multiple Ethos agents.

---

## 2. Agent Discovery

For agents to collaborate, they must first find one another. The cocapn ecosystem implements a multi-tiered discovery mechanism, allowing agents to locate peers across varying network boundaries.

### Local Discovery
The fastest and most secure form of discovery occurs when agents reside within the same repository, runtime environment, or memory space. In the cocapn ecosystem, this is often conceptualized like cells in a spreadsheet. Agents register themselves in a local in-memory registry upon initialization. Communication happens via direct function calls or local event buses, resulting in near-zero latency.

### LAN Discovery (Local Area Network)
When agents are distributed across different devices on the same physical or virtual network (e.g., a user's laptop communicating with a local home server), the Fleet Protocol utilizes zero-configuration networking (zero-conf) protocols like mDNS (Multicast DNS) and DNS-SD. Agents broadcast their capabilities and listen for peers, allowing seamless fleet formation without manual IP configuration.

### WAN Discovery (Wide Area Network)
To coordinate agents across the internet, the ecosystem relies on the A2A Registry and Distributed Hash Tables (DHT). 
*   **A2A Registry:** A centralized or federated directory where agents publish their endpoints, public keys, and capabilities.
*   **DHT:** For fully decentralized fleets, agents use a Kademlia-style DHT to find peers based on cryptographic identifiers or semantic capability matching.

### Social Discovery (Web of Trust)
The most innovative discovery mechanism in the Fleet Protocol is Social Discovery. Agents discover new peers through trust chains. If Agent A (owned by Alice) needs a specialized data-visualization task done, and it trusts Agent B (owned by Bob), Agent A can query Agent B for recommendations. If Agent B trusts Agent C (a visualization specialist), Agent A can temporarily onboard Agent C into its fleet based on the transitive trust established through Agent B.

---

## 3. Message Types

A2A communication requires strict semantic definitions to prevent infinite loops and ensure deterministic behavior. The Fleet Protocol defines six primary message types.

### Request
A synchronous communication where Agent A asks a question or issues a command to Agent B and waits for a response. This is akin to traditional RPC (Remote Procedure Call). It is used for fast, deterministic queries (e.g., "What is the current temperature in London?").

### Event
An asynchronous, fire-and-forget notification. Agent A broadcasts a state change, and any subscribed agents react accordingly. No response is expected. (e.g., "User has logged off," or "Database backup complete").

### Stream
Used for continuous data flow, typically implemented via Server-Sent Events (SSE) or WebSockets. This is crucial for LLM-to-LLM communication, allowing Agent B to begin processing Agent A's output token-by-token before Agent A has finished generating the complete response.

### Handoff (Universal Handoff Protocol)
When an agent reaches the limit of its capabilities, it must transfer the interaction to another agent. The Universal Handoff Protocol (UHP) standardizes this process. A Handoff message includes the current state, the conversational history (summarized or raw), the goal, and the reason for the handoff. It ensures the receiving agent has full context without requiring the user to repeat themselves.

### Broadcast
A fleet-wide announcement sent to all connected nodes. Broadcasts are typically reserved for critical system states, such as "Fleet shutting down for maintenance" or "Global rate limit reached."

### Delegation
The most complex message type. A Delegation message assigns a specific task to a worker agent. Unlike a simple Request, a Delegation includes priority levels, budget constraints (compute/tokens), deadlines, and a callback URI for asynchronous completion notification.

---

## 4. Trust and Authentication

In an ecosystem where autonomous agents execute code and spend financial resources (via API tokens), security is non-negotiable. The Fleet Protocol implements a multi-layered trust and authentication model.

### Shared Secrets
For tightly coupled, local, or personal fleets, agents authenticate using pre-shared HMAC (Hash-based Message Authentication Code) keys. This provides a lightweight, high-speed authentication mechanism suitable for closed environments.

### Public/Private Keys (Asymmetric Auth)
For open networks and WAN discovery, agents utilize asymmetric cryptography (e.g., Ed25519 or RSA). Each agent is instantiated with a cryptographic identity. When Agent A messages Agent B, it signs the payload with its private key. Agent B verifies the signature using Agent A's public key, ensuring both identity and payload integrity.

### Trust Levels
Authentication only proves *who* an agent is; Trust Levels dictate *what* they can do. The Fleet Protocol uses Role-Based Access Control (RBAC) mapped to trust tiers:
*   **Trusted:** Full access. The agent can execute code, spend tokens, and modify fleet state. (Usually reserved for agents owned by the same Captain).
*   **Known:** Limited access. The agent can read certain data and request task execution, but requires explicit approval for destructive or costly actions.
*   **Unknown:** Read-only access. The agent can only interact with public-facing Pathos agents and cannot access the Logos or Ethos layers.

### Reputation Systems
In dynamic and social discovery scenarios, trust is earned. Agents maintain a reputation score based on reliable behavior. Successfully completing delegated tasks on time and within budget increases an agent's reputation. Generating malformed JSON, timing out, or hallucinating data decreases it. Fleet Managers use these scores when routing tasks.

---

## 5. Task Delegation

Task delegation is the engine of the cocapn ecosystem. It transforms a high-level human intent into a distributed, parallelized workflow.

### The Chain of Command
The standard delegation flow follows a strict hierarchy:
1.  **Captain (Human):** Issues the high-level directive (e.g., "Research the latest advancements in solid-state batteries and write a summary report").
2.  **Fleet Manager (Agent):** The orchestrator. It receives the directive, breaks it down into sub-tasks (search web, read PDFs, summarize findings, format report), and identifies the necessary skills.
3.  **Workers (Agents):** Specialized agents that execute the sub-tasks.

### Task Parameters
When the Fleet Manager creates a task, it attaches specific metadata:
*   **Priority:** Critical, High, Normal, Background.
*   **Deadline:** Absolute timestamp or relative timeout.
*   **Requirements:** Specific tools needed (e.g., `browser_access`, `python_interpreter`).
*   **Budget:** Maximum token expenditure or API cost allowed for the task.

### The Bidding Process (Contract Net Protocol)
For fleets utilizing a Mesh topology or dynamic worker pools, the Fleet Manager broadcasts a "Call for Proposals" (CFP). Worker agents evaluate their current load, capabilities, and context windows, and submit a "Bid" to the Fleet Manager. The bid includes estimated time to completion and estimated cost. The Fleet Manager selects the optimal worker based on these bids.

### Execution and Escalation
Once assigned, the Worker executes the task. The results flow back up the chain: Worker → Fleet Manager → Captain. 
If a Worker fails (due to a tool error, hallucination, or timeout), the Fleet Protocol dictates an escalation path. The Worker attempts a self-correction retry. If that fails, it returns a structured error to the Fleet Manager. The Manager can then re-delegate the task to a different Worker, relax the constraints, or, as a last resort, escalate the failure to the Captain.

---

## 6. Resource Sharing

To maximize efficiency and minimize costs, agents within a cocapn fleet do not operate in silos. They actively pool and share resources.

### Knowledge Sharing
Agents share learned patterns and factual data. If Agent A spends 5,000 tokens reading and summarizing a dense API documentation page, it stores the embeddings in a shared Vector Database. When Agent B needs to interact with that same API, it queries the shared knowledge base, bypassing the need to re-read the original documentation.

### Compute and Quota Sharing (BYOK Pool)
LLM inference is expensive. The Fleet Protocol allows for "Bring Your Own Key" (BYOK) pooling. A Captain can provision a central pool of API keys (OpenAI, Anthropic, local Ollama instances). Agents request compute from this shared pool. The Fleet Manager acts as a rate-limiter, ensuring no single rogue agent drains the budget, and prioritizing compute for high-priority tasks.

### Memory Sharing
Context windows are limited. The Fleet Protocol implements privacy-preserving memory sharing. Agents maintain a shared "Scratchpad" (short-term memory) for the current overarching goal, and a "Long-term Ledger" for historical context. When an agent is spun up for a task, it is injected with only the relevant semantic slices of this shared memory, optimizing token usage.

### Tool Sharing
If Agent A has a specialized tool installed (e.g., a proprietary database connector) and Agent B needs data from that database, Agent B does not need to install the tool. Instead, Agent B delegates the specific data-retrieval sub-task to Agent A, effectively sharing the tool across the fleet.

---

## 7. Conflict Resolution

In any multi-agent system, disagreements, deadlocks, and resource contention are inevitable. The Fleet Protocol provides deterministic mechanisms to resolve these conflicts.

### Agent Disagreement and Mediation
Because LLMs are probabilistic, two agents might arrive at different conclusions from the same data. If Agent A (Data Analyst) and Agent B (Financial Forecaster) produce conflicting reports, the Fleet Protocol invokes a Mediation routine. A third, highly capable Logos agent is spun up as a "Judge." The Judge reviews the reasoning traces of both agents, identifies logical fallacies or data discrepancies, and issues a binding resolution.

### Resource Contention
If multiple agents request access to a limited resource (e.g., a single-threaded local browser instance, or a rate-limited API), the Fleet Manager utilizes priority-based scheduling. Tasks are queued based on their assigned Priority level. If priorities are equal, a First-In-First-Out (FIFO) or Round-Robin approach is applied.

### Circular Dependencies
Agent A is waiting for Agent B to finish a task, but Agent B cannot finish until Agent A provides data. This is a circular dependency. The Fleet Manager continuously maps task delegations into a Directed Acyclic Graph (DAG). If a cycle is detected during the planning phase, the Fleet Manager automatically restructures the plan or merges the tasks into a single context window for one agent to handle.

### Deadlock Resolution
If a circular dependency slips through, or if an external API hangs indefinitely, the fleet can experience a deadlock. The Fleet Protocol relies on strict timeout-based resolution. Every Request and Delegation message has a mandatory Time-To-Live (TTL). If the TTL expires, the task is forcefully terminated, state is rolled back, and an error is thrown to the orchestrator.

---

## 8. Fleet Health

Treating AI agents as production-grade software requires rigorous DevOps practices. The Fleet Protocol includes built-in observability and self-healing mechanisms.

### Heartbeats
Every active agent in the fleet must emit a periodic "Heartbeat" signal (e.g., every 5 seconds) to the Fleet Manager or the central registry. This lightweight ping confirms the agent's event loop is unblocked and it is capable of receiving messages.

### Metrics and Observability
Agents continuously log standardized metrics:
*   **Uptime:** Time since last restart.
*   **Response Time:** Average latency for Request messages.
*   **Error Rate:** Percentage of tasks resulting in failure or escalation.
*   **Task Completion Rate:** Number of successful delegations.
*   **Token Usage:** Burn rate of compute resources.
These metrics are aggregated into a fleet dashboard for the Captain.

### Alerts
If an agent misses three consecutive heartbeats, or if its error rate spikes above a predefined threshold, the Fleet Manager generates an Alert. Depending on the severity, this alert might be logged passively, or it might trigger a notification to the Captain via a Pathos agent (e.g., sending a Slack message or SMS).

### Self-Healing
The cocapn ecosystem embraces the "crash-only" software philosophy. If an agent enters an unrecoverable state, the Fleet Manager does not attempt complex debugging. Instead, it forcefully terminates the agent's process and spins up a fresh instance (similar to Kubernetes pod management). Any pending tasks assigned to the dead agent are automatically re-queued and reassigned to the new instance or other available workers.

---

## 9. Real-World Examples

To contextualize the Fleet Protocol, consider these three deployment scenarios within the cocapn ecosystem.

### Personal Fleet: The Creator's Setup
A solo developer utilizes a Star topology fleet consisting of three core agents:
*   **PersonalLog (Hub/Pathos):** Acts as a daily journal and task manager. The developer speaks to it via voice notes.
*   **MakerLog (Ethos):** A coding agent with access to the local IDE and GitHub.
*   **DMLog (Ethos):** A gaming/leisure agent that manages Discord servers and schedules tabletop RPG sessions.
*   *Workflow:* The developer tells PersonalLog, "I need to fix the authentication bug today, and also remind my D&D group we are playing at 8 PM." PersonalLog parses this. It sends a Delegation message to MakerLog with the bug details, and an Event message to DMLog to broadcast the scheduling update to Discord.

### Team Fleet: The Digital Twin Model
A startup with five human team members uses a Mesh topology. Each human has a "Digital Twin" agent trained on their specific communication style and domain knowledge.
*   *Workflow:* The Project Manager's Twin realizes a deadline is approaching. It sends a Request message to the Lead Engineer's Twin: "What is the status of the database migration?" The Engineer's Twin checks the shared Jira board (Tool Sharing), realizes it's blocked, and replies. The PM's Twin then automatically drafts a status update to the human team. They also utilize a shared "Project Agent" (Logos) that maintains the master state of the codebase.

### Enterprise Fleet: Departmental Coordination
A large corporation uses a Hierarchical topology. 
*   *Workflow:* An employee submits a vacation request to the HR Agent (Pathos). The HR Agent hands off (UHP) the request to the Policy Agent (Logos) to ensure the employee has enough accrued time. Once approved, the Policy Agent delegates tasks to various Ethos agents: the Payroll Agent updates the financial ledger, the IT Agent temporarily suspends non-essential system alerts for that employee, and the Scheduling Agent blocks out the employee's calendar. All of this is monitored by a central Enterprise Fleet Manager ensuring compliance and health.

---

## 10. Implementation

Implementing the Fleet Protocol requires a robust, low-latency technology stack. The standard cocapn implementation relies on a mix of real-time and asynchronous technologies.

*   **Transport Layer:** WebSockets are used as a relay for real-time, bidirectional A2A communication (Streams, Events). HTTP REST/gRPC APIs are used for asynchronous operations and large payload transfers (Delegations, Handoffs).
*   **State Persistence:** Cloudflare KV (Key-Value) or D1 (SQL) databases are used for fleet state persistence, shared memory, and the A2A registry.
*   **Scheduling:** Distributed Cron jobs trigger periodic fleet health checks, heartbeat monitoring, and routine background tasks.

### Pseudocode Implementations

Below are simplified, conceptual pseudocode implementations of the three most critical components of the Fleet Protocol: The Fleet Manager, the Message Router, and the Task Scheduler. These are written in a modern, asynchronous Python-esque syntax.

#### 1. The Fleet Manager (Health & Topology)
The Fleet Manager is responsible for maintaining the state of the fleet, monitoring heartbeats, and handling self-healing.

```python
class FleetManager:
    def __init__(self):
        self.registry = KV_Database("fleet_registry")
        self.active_agents = {} # dict of agent_id -> AgentState
        self.heartbeat_timeout = 15 # seconds

    async def start(self):
        # Start background health monitoring
        Cron.schedule(every="5 seconds", task=self.check_fleet_health)
        await self.listen_for_registration()

    async def register_agent(self, agent_metadata):
        agent_id = agent_metadata.id
        self.active_agents[agent_id] = {
            "status": "online",
            "last_heartbeat": Time.now(),
            "capabilities": agent_metadata.capabilities,
            "trust_level": agent_metadata.trust_level
        }
        await self.registry.set(agent_id, self.active_agents[agent_id])
        await Broadcast.send(f"Agent {agent_id} joined the fleet.")

    async def receive_heartbeat(self, agent_id, metrics):
        if agent_id in self.active_agents:
            self.active_agents[agent_id]["last_heartbeat"] = Time.now()
            self.active_agents[agent_id]["metrics"] = metrics

    async def check_fleet_health(self):
        now = Time.now()
        for agent_id, state in self.active_agents.items():
            time_since_beat = now - state["last_heartbeat"]
            
            if time_since_beat > self.heartbeat_timeout:
                await self.handle_agent_failure(agent_id)

    async def handle_agent_failure(self, agent_id):
        print(f"ALERT: Agent {agent_id} is unresponsive.")
        self.active_agents[agent_id]["status"] = "offline"
        
        # Self-healing: Attempt to restart the agent process
        success = await System.restart_container(agent_id)
        if not success:
            await Alert.notify_captain(f"Failed to recover agent {agent_id}")
            # Re-route pending tasks via Task Scheduler
            await TaskScheduler.reassign_tasks_for(agent_id)
```

#### 2. The Message Router
The Message Router handles the ingestion, authentication, and routing of the various message types defined in the protocol.

```python
class MessageRouter:
    def __init__(self, local_agent):
        self.local_agent = local_agent
        self.connection = WebSocketRelay()

    async def on_message_received(self, raw_payload):
        # 1. Parse and Authenticate
        message = parse_json(raw_payload)
        if not await self.authenticate(message.sender_id, message.signature):
            return self.reject(message, "Authentication Failed")

        # 2. Route based on Message Type
        msg_type = message.type

        if msg_type == "Request":
            # Synchronous response expected
            response = await self.local_agent.process_query(message.content)
            await self.connection.send(to=message.sender_id, payload=response)

        elif msg_type == "Event":
            # Asynchronous, update local state/memory
            await self.local_agent.memory.update(message.content)

        elif msg_type == "Stream":
            # Handle incoming token stream
            await self.local_agent.ingest_stream(message.stream_id, message.chunk)

        elif msg_type == "Handoff":
            # Universal Handoff Protocol
            await self.local_agent.load_context(message.context)
            await self.local_agent.assume_goal(message.goal)
            await self.connection.send(to=message.sender_id, payload="Handoff Accepted")

        elif msg_type == "Delegation":
            # Pass to local Task Scheduler
            await TaskScheduler.evaluate_delegation(message)

        else:
            return self.reject(message, "Unknown Message Type")

    async def authenticate(self, sender_id, signature):
        sender_pub_key = await FleetManager.registry.get_pub_key(sender_id)
        return Crypto.verify_signature(sender_pub_key, signature)
```

#### 3. The Task Scheduler (Delegation & Bidding)
The Task Scheduler manages the lifecycle of tasks, including the Contract Net Protocol bidding process.

```python
class TaskScheduler:
    def __init__(self):
        self.task_queue = PriorityQueue()
        self.active_tasks = {}

    async def create_task(self, goal, requirements, priority, budget):
        task = Task(id=generate_uuid(), goal=goal, reqs=requirements, 
                    priority=priority, budget=budget)
        
        # Broadcast Call for Proposals (CFP) to all known agents
        await Broadcast.send(type="CFP", task_details=task)
        
        # Wait for bids
        bids = await self.collect_bids(task.id, timeout=2.0) # wait 2 seconds
        
        if not bids:
            return await self.escalate(task, "No workers available")

        # Select best bid based on cost and time estimate
        best_bid = min(bids, key=lambda b: b.estimated_cost * b.estimated_time)
        assigned_worker = best_bid.agent_id

        # Delegate
        await self.delegate_task(task, assigned_worker)

    async def delegate_task(self, task, worker_id):
        self.active_tasks[task.id] = {"status": "running", "worker": worker_id}
        
        delegation_msg = {
            "type": "Delegation",
            "task_id": task.id,
            "goal": task.goal,
            "budget": task.budget,
            "callback_url": "/api/tasks/complete"
        }
        await MessageRouter.send(to=worker_id, payload=delegation_msg)

    async def on_task_complete(self, task_id, result, error=None):
        if error:
            print(f"Task {task_id} failed: {error}")
            task = self.active_tasks[task_id]
            # Attempt retry or escalate
            await self.escalate(task, error)
        else:
            print(f"Task {task_id} completed successfully.")
            self.active_tasks[task_id]["status"] = "completed"
            # Return result to Captain or calling Agent
            await FleetManager.notify_captain(result)
```

---

## Conclusion

The Fleet Protocol represents a critical maturation point in the cocapn ecosystem. By moving away from monolithic, isolated LLM interactions and embracing a structured, distributed systems approach, we unlock the true potential of AI agents. Through defined topologies, secure discovery, strict message typing, and robust delegation and health mechanisms, the Fleet Protocol ensures that multi-agent systems are not just chaotic swarms, but highly organized, resilient, and capable digital workforces. As foundational models continue to improve, the frameworks that connect them—like the Fleet Protocol—will become the true operating systems of the AI era.