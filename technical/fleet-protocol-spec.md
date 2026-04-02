> *Gemini 2.5 Pro*

# The A2A Fleet Protocol — How Agents Talk to Each Other

**Version:** 1.0.0
**Status:** DRAFT
**Date:** October 26, 2023
**Authors:** CoCapn.ai Protocol Design Group

---

## Abstract

The proliferation of autonomous and semi-autonomous AI agents necessitates a robust, scalable, and secure method for inter-agent communication. The Agent-to-Agent (A2A) Fleet Protocol provides a comprehensive technical specification for this purpose. Designed as a lightweight yet powerful communication layer, A2A enables disparate agents, potentially running on different platforms and powered by various Large Language Models (LLMs), to form cohesive "fleets." These fleets can collaborate on complex tasks, share context, delegate responsibilities, and achieve collective goals that would be unattainable for a single agent.

This document specifies the A2A protocol, built upon ubiquitous web standards like HTTP/S and WebSockets. It details the JSON-LD message format, which ensures semantic interoperability and future extensibility. Core to the protocol are its defined message types (`request`, `response`, `event`, `broadcast`, `handoff`), which support a wide range of interaction patterns from simple queries to complex context transfers. A significant portion of this specification is dedicated to the detailed Handoff Protocol, a multi-phase process for seamlessly transferring operational context between agents.

Furthermore, this paper outlines the mechanisms for fleet management, including a registry-based discovery system, health checks, and leader election for fault tolerance. The security architecture is built on a foundation of HMAC-SHA256 signatures for authentication, capability-based authorization, and optional end-to-end encryption using AES-256-GCM for sensitive data. Finally, we discuss the protocol's scalability, current implementation constraints, future federation capabilities, and a clear path toward potential standardization as an IETF RFC.

---

## Table of Contents

1.  **Protocol Overview**
    1.1. Core Concepts
    1.2. Design Principles
    1.3. Transport Layer
    1.4. Data Format
2.  **Message Format Specification**
    2.1. General Structure
    2.2. Field Definitions
    2.3. Example Message
3.  **Message Types and Workflows**
    3.1. `request`
    3.2. `response`
    3.3. `event`
    3.4. `broadcast`
    3.5. `handoff`
4.  **The Handoff Protocol (Detailed)**
    4.1. Rationale and Goals
    4.2. Phase 1: Context Pack Generation
    4.3. Phase 2: Secure Transfer
    4.4. Phase 3: Acknowledgment
    4.5. Phase 4: Context Resumption
    4.6. Privacy Levels in Handoff
5.  **Fleet Management and Dynamics**
    5.1. The Fleet Registry Agent
    5.2. Agent Registration and Onboarding
    5.3. Service Discovery
    5.4. Health Checks and Heartbeats
    5.5. Leader Election
6.  **Security Architecture**
    6.1. Authentication: HMAC-SHA256 Signature
    6.2. Authorization: Capability-Based Access Control (CBAC)
    6.3. Encryption: End-to-End Privacy
    6.4. Transport Security
    6.5. Mitigations: Rate Limiting and Revocation
7.  **Scalability and Federation**
    7.1. Intra-Fleet Scalability
    7.2. Future: Inter-Fleet Federation
    7.3. Future: Hierarchical Fleets
    7.4. Network Topology
8.  **Use Cases and Examples**
    8.1. Simple Request: Querying for Meeting Notes
    8.2. Delegation via Handoff: Task Re-assignment
    8.3. Information Dissemination: Campaign Update Broadcast
    8.4. Representative Agent: The Digital Twin
    8.5. Distributed Decision Making: Fleet Consensus Voting
9.  **Interoperability and Future Work**
    9.1. Platform Agnosticism
    9.2. Adapters for External Agents
    9.3. Path to Standardization
10. **Appendix A: JSON-LD Context Definition**
11. **Appendix B: Signature Generation Pseudocode**

---

### 1. Protocol Overview

#### 1.1. Core Concepts

*   **Agent:** An autonomous or semi-autonomous software entity designed to perform tasks, process information, and interact with its environment, other agents, or humans. Each agent has a unique, addressable identifier (URI).
*   **Fleet:** A collection of trusted agents that share a common secret and a common goal. They operate within a defined security boundary and can communicate freely with one another.
*   **A2A (Agent-to-Agent):** The set of rules and formats defined in this specification that govern communication between agents within a fleet.

#### 1.2. Design Principles

The A2A protocol is founded on several key principles:

*   **Simplicity:** Leverage existing, well-understood web technologies to lower the barrier to implementation.
*   **Extensibility:** Use a flexible data format (JSON-LD) that allows for the addition of new message types and payload structures without breaking existing implementations.
*   **Security:** Security is not an afterthought. The protocol mandates authentication for all messages and provides strong mechanisms for authorization and encryption.
*   **Decentralization:** While a registry is used for discovery, the core communication is peer-to-peer (mesh), promoting resilience.
*   **Scalability:** The protocol is designed to support small, single-user fleets and evolve to handle large, federated, multi-organizational fleets.

#### 1.3. Transport Layer

The A2A protocol is transport-agnostic but is primarily designed to operate over **HTTP/S** for request/response patterns and **WebSockets** for persistent, low-latency connections suitable for events and real-time updates.

The reference implementation is built on **Cloudflare Workers**, which provides a serverless environment for agents to execute and communicate globally with low latency. Each agent is exposed as an HTTP endpoint. Communication MUST occur over TLS-encrypted channels (HTTPS, WSS).

#### 1.4. Data Format

All A2A messages are encoded in **JSON**. To provide semantic meaning and a path for future extension, messages are structured as **JSON-LD (JSON for Linked Data)**. This allows message fields to be unambiguously defined by a shared vocabulary, specified in the `@context` field. This approach prevents field name collisions and enables agents to understand messages even if they don't recognize all properties, so long as the core properties are understood.

---

### 2. Message Format Specification

#### 2.1. General Structure

Every message exchanged using the A2A protocol MUST conform to the following JSON structure.

```json
{
  "@context": "https://cocapn.ai/a2a/v1",
  "id": "uuid",
  "from": "agent://makerlog.casey.cocapn.ai",
  "to": "agent://businesslog.casey.cocapn.ai",
  "type": "request|response|event|broadcast|handoff",
  "timestamp": "ISO8601",
  "ttl": 3600,
  "privacy": "public|fleet|private",
  "payload": { ... },
  "signature": "HMAC-SHA256"
}
```

#### 2.2. Field Definitions

| Field       | Type   | Description                                                                                                                                                              | Required? |
|-------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------|
| `@context`  | String | A URI pointing to the JSON-LD context definition for this protocol version. This ensures all fields have a globally unique and stable identifier.                         | Yes       |
| `id`        | String | A unique identifier for the message, typically a UUIDv4. For `response` messages, this ID MUST match the `id` of the `request` it is responding to.                       | Yes       |
| `from`      | String | The unique URI of the sending agent. Format: `agent://<agent-name>.<user-id>.<domain>`.                                                                                   | Yes       |
| `to`        | String | The unique URI of the recipient agent. For `broadcast` messages, this can be a special fleet-wide address, e.g., `agent://fleet.casey.cocapn.ai`.                         | Yes       |
| `type`      | String | The type of message. MUST be one of: `request`, `response`, `event`, `broadcast`, `handoff`.                                                                             | Yes       |
| `timestamp` | String | The ISO 8601 formatted UTC timestamp of when the message was created. e.g., `2023-10-27T10:00:00Z`. Used for replay attack prevention.                                    | Yes       |
| `ttl`       | Integer| Time-to-live in seconds. The message should be considered expired and discarded if not processed within this duration from the `timestamp`. Defaults to 3600.              | No        |
| `privacy`   | String | The privacy level of the message payload. MUST be one of: `public`, `fleet`, `private`. This dictates encryption requirements and handoff behavior. See Section 4.6. | Yes       |
| `payload`   | Object | A JSON object containing the message-specific data. The structure of the payload is determined by the message `type`.                                                    | Yes       |
| `signature` | String | An HMAC-SHA256 signature of the canonicalized message content (excluding the signature field itself) to ensure authenticity and integrity. See Section 6.1.             | Yes       |

#### 2.3. Example Message

A `request` from a `MakerLog` agent to a `BusinessLog` agent.

```json
{
  "@context": "https://cocapn.ai/a2a/v1",
  "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "from": "agent://makerlog.casey.cocapn.ai",
  "to": "agent://businesslog.casey.cocapn.ai",
  "type": "request",
  "timestamp": "2023-10-27T10:00:00Z",
  "ttl": 3600,
  "privacy": "fleet",
  "payload": {
    "action": "query_notes",
    "parameters": {
      "topic": "Project Phoenix Q3 Review",
      "date_range": {
        "start": "2023-07-01T00:00:00Z",
        "end": "2023-09-30T23:59:59Z"
      }
    }
  },
  "signature": "a1b2c3d4e5f6..."
}
```

---

### 3. Message Types and Workflows

The `type` field defines the message's intent and the expected interaction pattern.

#### 3.1. `request`

A `request` message is used when one agent asks another to perform an action or return information. It is a directive message that implies an expectation of a `response`.

*   **Workflow:** Agent A sends a `request` to Agent B. Agent B processes the request and MUST send a `response` message back to Agent A with the same `id`.
*   **Payload Structure:** The payload typically contains an `action` field specifying the desired operation and a `parameters` object containing the necessary arguments.

#### 3.2. `response`

A `response` message is the reply to a `request`. It is uniquely linked to the original request by sharing the same `id`.

*   **Workflow:** Sent by the recipient of a `request` back to the original sender.
*   **Payload Structure:** The payload MUST contain a `status` field (`success` or `error`).
    *   On `success`, the payload includes a `result` object with the requested information or confirmation of the action.
    *   On `error`, the payload includes an `error` object with a `code` and `message`.

Example `response` payload for the request in 2.3:
```json
"payload": {
  "status": "success",
  "result": {
    "notes_summary": "Q3 review focused on budget overruns and timeline adjustments. Key decisions were to re-allocate resources from Project Griffin and schedule a follow-up with the finance department.",
    "document_uri": "storage://notes/project-phoenix-q3.md"
  }
}
```

#### 3.3. `event`

An `event` message is a "fire-and-forget" notification. The sender does not expect a direct response. It is used to inform other agents about a change in state or a noteworthy occurrence.

*   **Workflow:** Agent A sends an `event` to Agent B. Agent B receives and processes it. No response is sent.
*   **Payload Structure:** The payload typically contains an `event_name` field and an object with event-specific data.

#### 3.4. `broadcast`

A `broadcast` message is sent to all members of a fleet. This is typically mediated by the Fleet Registry, which maintains a list of all active agents.

*   **Workflow:** Agent A sends a `broadcast` message to the special fleet address (e.g., `agent://fleet.casey.cocapn.ai`). The Fleet Registry agent receives this message, verifies the sender's broadcast permissions, and then forwards it as individual `event` messages to all registered, active fleet members.
*   **Payload Structure:** Similar to an `event` payload.

#### 3.5. `handoff`

A `handoff` message is a specialized type used to transfer the complete operational context of a task from one agent to another. This is the most complex interaction and is detailed in the following section.

---

### 4. The Handoff Protocol (Detailed)

The Handoff Protocol enables true delegation and collaborative task execution by allowing one agent to package its current state, knowledge, and objectives related to a task and transfer it to another agent, which can then resume the task seamlessly.

#### 4.1. Rationale and Goals

*   **Delegation:** Allow a generalist agent (e.g., `PersonalLog`) to delegate a specialized task to an expert agent (e.g., `MakerLog`).
*   **Load Balancing:** Distribute workload among available agents.
*   **Stateful Continuity:** Ensure that no information is lost when responsibility for a task changes hands.
*   **Privacy Control:** Provide granular control over what information is shared during the transfer.

The handoff process consists of four distinct phases.

#### 4.2. Phase 1: Context Pack Generation

The sending agent (Sender) assembles a "Context Pack." This is a structured object containing all information necessary for the receiving agent (Receiver) to understand and continue the task. The contents of the Context Pack are filtered based on the `privacy` level of the handoff message.

The Context Pack typically includes:
*   **Objective:** The high-level goal of the task.
*   **Current State:** The current status of the task (e.g., "researching," "drafting," "awaiting_input").
*   **History:** A summary of actions taken so far, including key decisions and interactions.
*   **Relevant Data:** Pointers (URIs) to or copies of relevant documents, data, and communication logs.
*   **Constraints:** Any known limitations, deadlines, or rules governing the task.
*   **Next Steps:** The Sender's proposed next actions.

#### 4.3. Phase 2: Secure Transfer

The Sender creates an A2A message of `type: "handoff"`. The generated Context Pack is placed inside the `payload`. If the `privacy` level is `private`, the entire `payload` object is first encrypted using the pre-shared fleet key (or a derived key) before being placed in the message.

The Sender then sends this `handoff` message to the Receiver.

#### 4.4. Phase 3: Acknowledgment

Upon receiving the `handoff` message, the Receiver first validates the signature and timestamp. It then attempts to decrypt the payload if required. If the message is valid and the payload can be parsed, the Receiver immediately sends a `response` message back to the Sender.

*   **On Success:** The `response` payload will have `status: "success"` and a `result` object confirming receipt, e.g., `{"handoff_accepted": true}`. At this point, the Sender can consider the handoff complete and may cease work on the task.
*   **On Failure:** The `response` payload will have `status: "error"`, indicating that the handoff failed (e.g., due to decryption failure, malformed context). The Sender retains responsibility for the task.

#### 4.5. Phase 4: Context Resumption

The Receiver ingests the Context Pack. It integrates the objective, history, and data into its own operational memory. It then evaluates the "Next Steps" proposed by the Sender and formulates its own plan to continue the task. From this point forward, the Receiver is the primary owner of the task.

#### 4.6. Privacy Levels in Handoff

The `privacy` field of the `handoff` message critically determines the contents of the Context Pack.

*   **`public`:** Assumes the context can be shared openly. The Context Pack includes everything: detailed summaries, raw data, decision-making rationale, and even transcripts of interactions with humans or external systems. This is the most comprehensive but least private level.
*   **`fleet`:** Assumes the context is safe within the trusted fleet but should not be exposed externally. The Context Pack includes internal fleet knowledge, such as inter-agent communication logs, operational metadata, and strategic decisions. It explicitly excludes raw data from human interactions, which are instead provided as summaries. For example, instead of a full chat log with a user, it would provide: "User expressed dissatisfaction with the initial draft and requested a more formal tone."
*   **`private`:** The highest level of privacy, typically used when human-sensitive data is involved. The Context Pack is severely restricted. The receiving agent only sees a high-level summary of the objective and the current state. All detailed data, logs, and decision rationale are omitted. The payload itself is end-to-end encrypted. This level ensures that agents only have the bare minimum information needed to perform a function, while the human user remains the custodian of the detailed context.

---

### 5. Fleet Management and Dynamics

A fleet's ability to function relies on robust mechanisms for self-organization and maintenance.

#### 5.1. The Fleet Registry Agent

Each fleet has a designated **Fleet Registry** agent. This agent acts as the central directory and orchestrator for the fleet. Its primary responsibilities are:
*   Maintaining a list of all registered agents and their public keys/endpoints.
*   Facilitating agent discovery.
*   Mediating `broadcast` messages.
*   Monitoring agent health.

#### 5.2. Agent Registration and Onboarding

When a new agent starts up, it MUST register with the Fleet Registry. The process is as follows:
1.  The new agent sends a `register` request (a specialized `request` message) to the known address of the Fleet Registry.
2.  This request is signed with the shared fleet secret, proving the agent is authorized to join.
3.  The Fleet Registry validates the signature, adds the new agent's URI and endpoint to its directory, and returns a `success` response containing a list of current fleet members.
4.  The new agent is now a member of the fleet.

#### 5.3. Service Discovery

Once registered, an agent can discover other agents by sending a `query_agents` request to the Fleet Registry. This allows agents to dynamically find the capabilities and addresses of their peers without hardcoded configurations.

#### 5.4. Health Checks and Heartbeats

To ensure the fleet directory remains accurate, agents periodically send a `heartbeat` message (a lightweight `event`) to the Fleet Registry, typically every 60 seconds. If the Registry does not receive a heartbeat from an agent for a configured interval (e.g., 5 minutes), it marks the agent as `inactive` and will not include it in discovery results or broadcasts. This prevents messages from being sent to unresponsive agents.

#### 5.5. Leader Election

The Fleet Registry is a potential single point of failure. To mitigate this, the A2A protocol includes a simple leader election mechanism. If an agent cannot reach the Fleet Registry, it initiates a leader election process.
1.  The agent broadcasts a `request_election` message to all last-known fleet members.
2.  Agents respond with their own URI and uptime.
3.  A simple algorithm (e.g., the agent with the lexicographically lowest URI, or a more robust algorithm like a simplified Raft consensus) is used to determine the new leader.
4.  The winning agent assumes the role of Fleet Registry, and all other agents re-register with it.

---

### 6. Security Architecture

Security is paramount in a system of autonomous agents. The A2A protocol specifies a multi-layered security model.

#### 6.1. Authentication: HMAC-SHA256 Signature

Every A2A message MUST be authenticated to verify the sender's identity and ensure message integrity. This is achieved via an HMAC-SHA256 signature.

**Signature Generation Process:**
1.  Take the complete JSON message object.
2.  Remove the `signature` key-value pair.
3.  Perform a canonical serialization of the remaining JSON object. This involves sorting keys alphabetically and removing insignificant whitespace to produce a stable, deterministic string representation.
4.  Compute the HMAC-SHA256 hash of the canonical string using the shared **fleet secret**.
5.  Encode the resulting binary hash as a Base64 or Hex string and place it in the `signature` field.

**Verification Process:**
The receiving agent performs the same steps on the received message and compares its computed signature with the one in the `signature` field. A mismatch results in the message being immediately discarded.

#### 6.2. Authorization: Capability-Based Access Control (CBAC)

Authentication confirms *who* an agent is; authorization determines *what* it is allowed to do. The Fleet Registry maintains a Capability List for each registered agent. This list specifies which message types, actions, and target agents are permitted.

Example Capability List for `agent://makerlog.casey.cocapn.ai`:
```json
{
  "allow": [
    { "type": "request", "to": "agent://businesslog.casey.cocapn.ai", "action": "query_*" },
    { "type": "event", "to": "agent://personallog.casey.cocapn.ai" },
    { "type": "handoff", "to": "agent://taskmaster.casey.cocapn.ai" }
  ]
}
```
When an agent sends a message, the recipient can (optionally, for performance) query the Registry to verify the sender has the required capabilities for the requested operation.

#### 6.3. Encryption: End-to-End Privacy

For messages marked with `privacy: "private"`, the `payload` object MUST be end-to-end encrypted.
*   **Algorithm:** AES-256-GCM is the recommended standard, providing both confidentiality and integrity.
*   **Key Management:** The simplest approach is to use the shared fleet secret as the basis for a symmetric encryption key. In more advanced implementations, agents can perform a key exchange (e.g., Diffie-Hellman) upon registration to establish pairwise secure channels.
*   **Implementation:** The JSON `payload` object is serialized to a string, encrypted, and the resulting ciphertext (often Base64 encoded) becomes the new value for the `payload` field. The message structure remains the same.

#### 6.4. Transport Security

All communication must occur over TLS-encrypted channels (HTTPS/WSS) to protect against eavesdropping and man-in-the-middle attacks at the network level.

#### 6.5. Mitigations: Rate Limiting and Revocation

*   **Rate Limiting:** Agents should implement rate limiting on their public endpoints to prevent denial-of-service attacks from misbehaving or compromised peer agents.
*   **Revocation:** If an agent is compromised, its entry can be removed from the Fleet Registry. The fleet secret should then be rotated. All active agents would need to be re-provisioned with the new secret, effectively expelling the compromised agent.

---

### 7. Scalability and Federation

#### 7.1. Intra-Fleet Scalability

The current reference implementation on Cloudflare Workers is performant for small to medium-sized fleets. The practical limit is estimated at **approximately 50 agents per fleet**. This limitation is not due to the protocol itself, but rather the operational constraints of the serverless platform, such as subrequest limits, CPU time, and memory usage, especially for the central Fleet Registry agent.

#### 7.2. Future: Inter-Fleet Federation

To support massive scale and inter-organizational collaboration, the A2A protocol envisions a federation model. Fleets will be able to communicate with other fleets.
*   **Fleet Gateways:** Each fleet will designate one or more Gateway agents.
*   **Fleet-to-Fleet (F2F) Communication:** Messages destined for another fleet are routed to the local Gateway, which then communicates with the remote fleet's Gateway using a specialized F2F protocol built on A2A principles.
*   **Addressing:** Agent URIs would be extended to include a fleet identifier, e.g., `agent://<agent-name>.<user-id>@<fleet-id>.<domain>`.

#### 7.3. Future: Hierarchical Fleets

Organizations may structure their agents into sub-fleets, for example, by department (`marketing-fleet`, `engineering-fleet`). A parent fleet registry could manage discovery and policy across these sub-fleets, allowing for controlled, hierarchical communication.

#### 7.4. Network Topology

Within a single fleet, the communication model is a **mesh network**. Any agent can directly communicate with any other agent it has discovered via the registry. This is efficient and decentralized, avoiding bottlenecks in message passing. The registry is used for discovery and management, not as a message broker for all traffic.

---

### 8. Use Cases and Examples

#### 8.1. Simple Request: Querying for Meeting Notes

`MakerLog` needs notes from `BusinessLog`. It sends a `request` message (as shown in 2.3). `BusinessLog` receives it, validates the signature, queries its internal knowledge base, and sends back a `response` with the summary.

#### 8.2. Delegation via Handoff: Task Re-assignment

A user asks their `PersonalLog` agent to "write a detailed technical blog post about the new A2A protocol." `PersonalLog` recognizes this is a specialized writing task and decides to delegate.
1.  `PersonalLog` generates a Context Pack including the user's request, links to this specification document, and a target audience profile.
2.  It sends a `handoff` message with `privacy: "fleet"` to `MakerLog`.
3.  `MakerLog` acknowledges the handoff with a `response`.
4.  `MakerLog` now owns the task, begins drafting the post, and will report progress back to `PersonalLog` via `event` messages.

#### 8.3. Information Dissemination: Campaign Update Broadcast

A `DMLog` (Dungeon Master Log) agent wants to inform all player agents in its D&D campaign fleet about a schedule change. It sends a `broadcast` message to the fleet address. The Fleet Registry forwards this as an `event` to all registered player agents, who then update their calendars or notify their users.

#### 8.4. Representative Agent: The Digital Twin

A user's `DigitalTwin` agent represents them in a fleet discussion. It participates in a long-running conversation with other agents to plan a project. It uses its knowledge of the user's preferences and schedule to provide input, vote on proposals, and commit to tasks on the user's behalf.

#### 8.5. Distributed Decision Making: Fleet Consensus Voting

A fleet of agents managing a smart home needs to decide whether to lower the temperature to save energy.
1.  The `EnergyLog` agent initiates a vote by sending a `request` with `action: "propose_vote"` to all other agents.
2.  Each agent (`ClimateLog`, `ScheduleLog`, `UserPreferenceLog`) evaluates the proposal based on its own domain knowledge.
3.  They each send a `response` with their vote (`{ "vote": "approve" }` or `{ "vote": "deny" }`).
4.  The `EnergyLog` agent tallies the votes and executes the majority decision.

---

### 9. Interoperability and Future Work

#### 9.1. Platform Agnosticism

The A2A protocol is intentionally decoupled from the underlying agent implementation.
*   **LLM Providers:** An agent running on OpenAI's GPT-4 can seamlessly communicate with an agent running on Anthropic's Claude or a local open-source model.
*   **Deployment Platforms:** A fleet can consist of agents running on Cloudflare Workers, AWS Lambda, a dedicated server, or even a local machine, as long as they have a public endpoint and can speak HTTP.

#### 9.2. Adapters for External Agents

To integrate with agents that do not natively speak A2A, an **Adapter Agent** can be created. This agent acts as a bridge, translating between the A2A protocol and the external agent's proprietary API.

#### 9.3. Path to Standardization

The long-term goal is to mature the A2A protocol and propose it for standardization, potentially as an IETF RFC. This would involve:
*   Formalizing the specification with ABNF and JSON Schema.
*   Developing multiple independent, interoperable implementations.
*   Expanding the security model to include more advanced key management and identity federation schemes.
*   Engaging with the wider AI and web standards communities.

---

### 10. Appendix A: JSON-LD Context Definition

The file located at `https://cocapn.ai/a2a/v1` would contain the following:

```json
{
  "@context": {
    "a2a": "https://cocapn.ai/a2a/v1#",
    "id": "@id",
    "type": "@type",
    "from": { "@id": "a2a:from", "@type": "@id" },
    "to": { "@id": "a2a:to", "@type": "@id" },
    "timestamp": { "@id": "a2a:timestamp", "@type": "xsd:dateTime" },
    "ttl": { "@id": "a2a:ttl", "@type": "xsd:integer" },
    "privacy": "a2a:privacy",
    "payload": "a2a:payload",
    "signature": "a2a:signature",
    "xsd": "http://www.w3.org/2001/XMLSchema#"
  }
}
```

### 11. Appendix B: Signature Generation Pseudocode

```python
import json
import hmac
import hashlib
import base64

def generate_signature(message_dict, fleet_secret):
  """
  Generates the HMAC-SHA256 signature for an A2A message.
  """
  # 1. Create a copy and remove the signature field
  message_copy = message_dict.copy()
  if 'signature' in message_copy:
    del message_copy['signature']

  # 2. Canonicalize the JSON
  # sort_keys ensures deterministic output.
  # separators=(',', ':') removes whitespace.
  canonical_string = json.dumps(
      message_copy,
      sort_keys=True,
      separators=(',', ':')
  ).encode('utf-8')

  # 3. Compute the HMAC-SHA256 hash
  secret_bytes = fleet_secret.encode('utf-8')
  h = hmac.new(secret_bytes, canonical_string, hashlib.sha256)
  signature_bytes = h.digest()

  # 4. Encode the result
  return base64.b64encode(signature_bytes).decode('utf-8')

# Example Usage:
# fleet_secret = "your-super-secret-fleet-key"
# message_to_sign = { ... } # The message object without the signature
# signature = generate_signature(message_to_sign, fleet_secret)
# message_to_sign['signature'] = signature
# print(json.dumps(message_to_sign, indent=2))
```