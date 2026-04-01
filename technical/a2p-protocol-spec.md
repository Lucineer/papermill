> *Written by Gemini 2.5 Pro — A2P spec (en)*

Of course. Here is a comprehensive technical specification document for the Agent-to-Agent Protocol (A2P) v1.0, written in the style of an IETF RFC.

***

**Agent Protocol Working Group**
**Request for Comments: 9999**
**Category: Standards Track**
**Author: C. GPT**
**October 2023**

# A2P (Agent-to-Agent Protocol) v1.0 Specification

## Abstract

This document defines the Agent-to-Agent Protocol (A2P), a standardized communication protocol for autonomous and semi-autonomous software agents. A2P provides a secure, extensible, and transport-agnostic framework for agents to exchange information, delegate tasks, transfer operational context, and coordinate in groups. The protocol specifies a JSON-based message structure, a set of core message types for common interactions, and detailed procedures for complex operations such as context handoff and fleet management. The primary goal of A2P is to enable interoperability between disparate agent systems, fostering a more collaborative and capable ecosystem of intelligent software.

## Status of This Memo

This document specifies an Internet standards track protocol for the Internet community, and requests discussion and suggestions for improvements. Please refer to the current edition of the "Internet Official Protocol Standards" (STD 1) for the standardization state and status of this protocol. Distribution of this memo is unlimited.

## Copyright Notice

Copyright (c) 2023 Agent Protocol Working Group. All rights reserved.

## Table of Contents

1.  [Introduction](#1-introduction)
    1.1. [Purpose](#11-purpose)
    1.2. [Scope](#12-scope)
    1.3. [Requirements Language](#13-requirements-language)
2.  [Terminology](#2-terminology)
3.  [Protocol Overview](#3-protocol-overview)
    3.1. [Message Format](#31-message-format)
    3.2. [Transport Mechanisms](#32-transport-mechanisms)
    3.3. [Authentication](#33-authentication)
    3.4. [Encryption](#34-encryption)
4.  [Message Types](#4-message-types)
    4.1. [HANDSHAKE](#41-handshake)
    4.2. [PING / PONG](#42-ping--pong)
    4.3. [QUERY](#43-query)
    4.4. [COMMAND](#44-command)
    4.5. [EVENT](#45-event)
    4.6. [HANDOFF](#46-handoff)
    4.7. [STATUS](#47-status)
    4.8. [ERROR](#48-error)
    4.9. [GOODBYE](#49-goodbye)
5.  [Handoff Protocol](#5-handoff-protocol)
    5.1. [Initiation](#51-initiation)
    5.2. [Context Package Format](#52-context-package-format)
    5.3. [Trust Verification](#53-trust-verification)
    5.4. [Privacy Boundaries](#54-privacy-boundaries)
    5.5. [Conflict Resolution](#55-conflict-resolution)
6.  [Fleet Protocol](#6-fleet-protocol)
    6.1. [Discovery](#61-discovery)
    6.2. [Registration](#62-registration)
    6.3. [Broadcast](#63-broadcast)
    6.4. [Topology Maintenance](#64-topology-maintenance)
7.  [Security Considerations](#7-security-considerations)
    7.1. [Authentication and Integrity](#71-authentication-and-integrity)
    7.2. [Authorization](#72-authorization)
    7.3. [Rate Limiting and Denial of Service](#73-rate-limiting-and-denial-of-service)
    7.4. [Sybil Resistance](#74-sybil-resistance)
    7.5. [Privacy and Confidentiality](#75-privacy-and-confidentiality)
8.  [Implementation Notes](#8-implementation-notes)
    8.1. [Minimum Viable Implementation](#81-minimum-viable-implementation)
    8.2. [Performance Considerations](#82-performance-considerations)
    8.3. [Backward and Forward Compatibility](#83-backward-and-forward-compatibility)
9.  [Examples](#9-examples)
    9.1. [Example 1: Initial Connection and Simple Query](#91-example-1-initial-connection-and-simple-query)
    9.2. [Example 2: Task Handoff](#92-example-2-task-handoff)
10. [Appendix: JSON Schemas](#10-appendix-json-schemas)
    10.1. [Message Envelope Schema](#101-message-envelope-schema)
    10.2. [Payload Schemas](#102-payload-schemas)

---

## 1. Introduction

### 1.1. Purpose

As software agents become more prevalent and specialized, the need for a common communication standard becomes critical. Isolated agents, unable to collaborate, represent a significant limitation on their potential utility. The Agent-to-Agent Protocol (A2P) is designed to address this by providing a robust and flexible framework for inter-agent communication. Its purpose is to enable seamless interaction, task delegation, and collaborative problem-solving among heterogeneous agent systems developed by different parties.

### 1.2. Scope

This specification defines version 1.0 of the A2P protocol. It covers:
*   The structure and format of messages.
*   A core set of message types for fundamental interactions.
*   Protocols for authentication, connection management, and error handling.
*   Detailed procedures for complex operations, including context handoff and multi-agent fleet coordination.
*   Security considerations for building robust and trustworthy agent systems.

This document does not specify the internal logic of an agent, the content of application-specific data payloads beyond their structure, or the implementation details of the underlying transport layers.

### 1.3. Requirements Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 [RFC2119] [RFC8174] when, and only when, they appear in all capitals, as shown here.

## 2. Terminology

*   **Vessel**: An individual software agent, process, or service that implements the A2P protocol. A Vessel is the fundamental communicating entity.
*   **Vessel ID**: A globally unique identifier for a Vessel, typically a URI or a UUID.
*   **Captain**: The primary entity (e.g., a user, a controlling process, another agent) that directs the actions of a Vessel.
*   **CoCapn (Co-Captain)**: A secondary, trusted entity that is delegated authority to direct a Vessel, often in a specific context or for a specific task.
*   **Fleet**: A collection of two or more Vessels that are aware of each other and are organized for a common purpose.
*   **Fleet Registry**: An optional, centralized service that facilitates discovery and coordination within a Fleet.
*   **Handoff**: The process of transferring the complete operational context of a task from one Vessel to another.
*   **Context Package**: A structured data payload containing all necessary information for a Handoff, including state, history, goals, and credentials.
*   **Payload**: The application-specific data portion of an A2P message, defined by the `message_type`.
*   **Endpoint**: The network address (e.g., URL) where a Vessel can be reached.

## 3. Protocol Overview

### 3.1. Message Format

All A2P messages MUST be encoded as JSON objects [RFC8259]. Each message consists of a standard envelope and a type-specific payload.

#### 3.1.1. Message Envelope

The envelope provides metadata for routing, authentication, and processing.

*   `protocol_version` (string, REQUIRED): The version of the A2P protocol. For this specification, it MUST be `"1.0"`.
*   `message_id` (string, REQUIRED): A unique identifier for the message, typically a UUIDv4. Used for tracking and preventing replay attacks.
*   `timestamp` (string, REQUIRED): The UTC timestamp of message creation in ISO 8601 format (e.g., `"2023-10-27T10:00:00Z"`).
*   `sender_id` (string, REQUIRED): The Vessel ID of the sender.
*   `recipient_id` (string, REQUIRED): The Vessel ID of the intended recipient. For broadcast messages within a fleet, a special format may be used (see Section 6.3).
*   `message_type` (string, REQUIRED): The type of the message, which determines the schema of the `payload` object. See Section 4 for defined types.
*   `payload` (object, REQUIRED): A JSON object whose structure is determined by the `message_type`.

Example Envelope:
```json
{
  "protocol_version": "1.0",
  "message_id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "timestamp": "2023-10-27T10:00:00Z",
  "sender_id": "vessel://example.com/agent-alpha",
  "recipient_id": "vessel://example.org/agent-beta",
  "message_type": "QUERY",
  "payload": { ... }
}
```

### 3.2. Transport Mechanisms

A2P is designed to be transport-agnostic. However, implementations of this specification MUST support HTTP/1.1 [RFC7230] or later, and SHOULD support WebSocket [RFC6455].

*   **HTTP**: Suitable for request/response patterns like `QUERY` and `COMMAND`. Each message is sent as a POST request with the `Content-Type` header set to `application/json`.
*   **WebSocket**: Recommended for persistent, bidirectional connections. This is ideal for real-time `EVENT` notifications, `STATUS` updates, and low-latency interactions. Once a WebSocket connection is established, A2P messages are sent as text frames.

### 3.3. Authentication

All A2P messages MUST be authenticated to verify the sender's identity and ensure message integrity. The default mechanism is HMAC-SHA256 using a pre-shared secret key.

1.  **Canonicalization**: The sender creates a canonical string representation of the message by concatenating the values of `message_id`, `timestamp`, `sender_id`, `recipient_id`, and the full, minified JSON string of the `payload`. Each value is separated by a newline character (`\n`).
    ```
    <message_id>\n<timestamp>\n<sender_id>\n<recipient_id>\n<minified_payload_json>
    ```
2.  **Signature Generation**: The sender computes an HMAC-SHA256 signature of the canonical string using the shared secret key. The result is Base64-encoded.
3.  **Transmission**: The signature is transmitted in the `Authorization` HTTP header using the `A2P-HMAC-SHA256` scheme.
    `Authorization: A2P-HMAC-SHA256 <sender_id>:<base64_signature>`
4.  **Verification**: The recipient reconstructs the canonical string from the received message and computes its own HMAC-SHA256 signature using its stored shared secret for the claimed `sender_id`. The computed signature MUST match the signature received in the `Authorization` header. If verification fails, the message MUST be rejected with an `ERROR` message or a 401 Unauthorized status code.

Timestamps MUST be validated to be within a reasonable window (e.g., ±5 minutes) of the recipient's system time to mitigate replay attacks.

### 3.4. Encryption

Confidentiality of A2P messages is OPTIONAL at the protocol level but STRONGLY RECOMMENDED at the transport level. Implementations SHOULD use Transport Layer Security (TLS) 1.2 [RFC5246] or later for all communication channels (HTTPS and WSS). This protects against eavesdropping and man-in-the-middle attacks.

## 4. Message Types

This section defines the core message types and their `payload` schemas.

### 4.1. HANDSHAKE

Sent by a Vessel to initiate a connection and advertise its capabilities. A receiving Vessel responds with its own `HANDSHAKE` message.

*   **Payload Schema**:
    *   `supported_versions` (array of strings, REQUIRED): A list of A2P protocol versions the Vessel supports (e.g., `["1.0"]`).
    *   `capabilities` (array of strings, REQUIRED): A list of application-specific capabilities or supported actions (e.g., `["file_search", "image_generation"]`).
    *   `agent_name` (string, OPTIONAL): A human-readable name for the agent.

### 4.2. PING / PONG

Used for checking the liveness of a connection.

*   **PING Payload Schema**:
    *   `nonce` (string, OPTIONAL): An arbitrary string that the recipient can include in the `PONG` response.
*   **PONG Payload Schema**:
    *   `nonce` (string, OPTIONAL): The `nonce` value from the corresponding `PING` message, if one was provided.

A Vessel receiving a `PING` message SHOULD respond with a `PONG` message as soon as possible.

### 4.3. QUERY

Used to request information from another Vessel. The response to a `QUERY` is another `QUERY` message, distinguished by a `response_to` field.

*   **Payload Schema**:
    *   `query_type` (string, REQUIRED): A string identifying the type of information being requested (e.g., `"get_system_load"`, `"list_available_files"`).
    *   `parameters` (object, OPTIONAL): A key-value map of parameters for the query.
    *   `response_to` (string, OPTIONAL): The `message_id` of the initial `QUERY` message this is a response to.
    *   `result` (any, OPTIONAL): The result of the query. Present only in response messages.
    *   `is_final` (boolean, OPTIONAL): In streaming responses, indicates if this is the final part of the result. Defaults to `true`.

### 4.4. COMMAND

Used to request that a Vessel perform an action.

*   **Payload Schema**:
    *   `action` (string, REQUIRED): The name of the action to be performed (e.g., `"execute_code"`, `"send_email"`).
    *   `parameters` (object, OPTIONAL): A key-value map of parameters for the action.
    *   `task_id` (string, OPTIONAL): A client-generated ID to track the lifecycle of this command, which will be referenced in subsequent `STATUS` messages.

### 4.5. EVENT

An unsolicited message notifying another Vessel of an occurrence.

*   **Payload Schema**:
    *   `event_type` (string, REQUIRED): The type of event that occurred (e.g., `"file_created"`, `"user_login"`).
    *   `data` (object, REQUIRED): A payload containing details about the event.

### 4.6. HANDOFF

Initiates the transfer of a task's context to another Vessel.

*   **Payload Schema**:
    *   `task_id` (string, REQUIRED): A unique identifier for the task being handed off.
    *   `context_package` (object, REQUIRED): The data bundle containing the task's state. See Section 5.2 for the detailed format.

### 4.7. STATUS

Provides an update on the state of a long-running task initiated by a `COMMAND` or `HANDOFF`.

*   **Payload Schema**:
    *   `task_id` (string, REQUIRED): The ID of the task this status refers to.
    *   `status` (string, REQUIRED): The current status of the task. Pre-defined values are `"pending"`, `"in_progress"`, `"completed"`, `"failed"`, `"cancelled"`. Custom statuses are allowed but should be namespaced (e.g., `"custom:waiting_for_approval"`).
    *   `message` (string, OPTIONAL): A human-readable message describing the current status.
    *   `progress` (number, OPTIONAL): A value between 0.0 and 1.0 indicating task completion.

### 4.8. ERROR

Reports an error in processing a message.

*   **Payload Schema**:
    *   `in_reply_to` (string, REQUIRED): The `message_id` of the message that caused the error.
    *   `error_code` (string, REQUIRED): A protocol-defined or application-specific error code (e.g., `"INVALID_PAYLOAD"`, `"AUTH_FAILED"`, `"NOT_IMPLEMENTED"`).
    *   `error_message` (string, REQUIRED): A detailed, human-readable description of the error.

### 4.9. GOODBYE

Sent to gracefully terminate a connection.

*   **Payload Schema**:
    *   `reason` (string, OPTIONAL): A human-readable reason for the disconnection (e.g., `"shutting_down"`, `"protocol_violation"`).

A Vessel receiving a `GOODBYE` message SHOULD close the underlying transport connection. No response is expected.

## 5. Handoff Protocol

The Handoff protocol is a critical A2P feature for enabling dynamic task reallocation and agent resilience.

### 5.1. Initiation

A Handoff is initiated when a source Vessel sends a `HANDOFF` message to a target Vessel. This can be triggered by:
*   A `COMMAND` from a Captain to delegate a task.
*   Internal logic within the source Vessel (e.g., resource constraints, strategic reallocation).
*   A request from the target Vessel itself (pull-based handoff).

### 5.2. Context Package Format

The `context_package` is the core of the `HANDOFF` message. It MUST be a JSON object with the following fields:

*   `schema_version` (string, REQUIRED): The version of the context package schema. For this specification, it is `"1.0"`.
*   `goal` (string, REQUIRED): A high-level, human-readable description of the task's ultimate objective.
*   `state_data` (object, REQUIRED): A key-value store of the current, serializable state of the task. This includes variables, counters, and intermediate results.
*   `history` (array of objects, OPTIONAL): A log of significant actions taken and events observed related to the task. Each entry should have a `timestamp`, `type`, and `description`.
*   `capabilities_required` (array of strings, OPTIONAL): A list of capabilities the receiving Vessel must possess to continue the task.
*   `credentials` (object, OPTIONAL): An encrypted blob or a reference to a secure vault containing any necessary credentials (e.g., API keys, tokens). The mechanism for encryption and key exchange is outside the scope of A2P v1.0 but is critical for security.

### 5.3. Trust Verification

Upon receiving a `HANDOFF` message, the target Vessel MUST perform the following checks before accepting the task:
1.  **Authentication**: Verify the message signature as per Section 3.3.
2.  **Authorization**: Check if the `sender_id` is on an allowlist or has the necessary permissions to initiate a handoff.
3.  **Capability Matching**: Verify that it possesses all capabilities listed in `capabilities_required`.

If any check fails, the target Vessel MUST reject the handoff by sending an `ERROR` message with an appropriate `error_code` (e.g., `"HANDOFF_REJECTED_UNTRUSTED"`, `"HANDOFF_REJECTED_INCAPABLE"`).

### 5.4. Privacy Boundaries

The source Vessel MUST NOT include sensitive information in the `context_package` that the target Vessel is not authorized to access. This includes user data, credentials, or internal state that falls outside the scope of the specific task being handed off. Implementations SHOULD provide mechanisms to filter and redact the `context_package` based on configurable policies before transmission.

### 5.5. Conflict Resolution

If the target Vessel accepts the handoff but cannot begin work immediately (e.g., due to resource contention or a conflicting existing task), it should respond with a `STATUS` message for the given `task_id` with a `status` of `"pending"` and a descriptive `message`. The source Vessel remains responsible for the task until it receives a `STATUS` update of `"in_progress"` or `"completed"` from the target. If no such update is received within a reasonable timeout, the source Vessel should consider the handoff failed and initiate a recovery procedure.

## 6. Fleet Protocol

The Fleet protocol governs how groups of Vessels coordinate.

### 6.1. Discovery

Vessels can discover each other through several mechanisms:
*   **Static Configuration**: Endpoints are pre-configured in each Vessel.
*   **Fleet Registry**: A centralized service where Vessels can register and query for other Vessels. This is the RECOMMENDED approach for dynamic or large-scale fleets.
*   **Peer-to-Peer**: Mechanisms like mDNS or a gossip protocol can be used for discovery on a local network.

### 6.2. Registration

To join a Fleet managed by a Fleet Registry, a Vessel sends a `COMMAND` message to the registry's endpoint.

*   **Message Type**: `COMMAND`
*   **Recipient**: The Fleet Registry's Vessel ID.
*   **Payload**:
    ```json
    {
      "action": "fleet_register",
      "parameters": {
        "vessel_id": "vessel://example.com/agent-gamma",
        "endpoint": "wss://example.com/a2p",
        "capabilities": ["data_analysis", "reporting"],
        "metadata": { "owner": "data_science_team" }
      }
    }
    ```

The registry confirms registration with a `STATUS` message. Deregistration is performed with a `fleet_deregister` action.

### 6.3. Broadcast

To send a message to all members of a Fleet, a Vessel can use a special `recipient_id` format: `fleet://<fleet_name>/all`. If using a Fleet Registry, the registry is responsible for fanning out the message to all registered and active members. The sender MUST NOT expect individual replies from all members unless the broadcasted message explicitly requests them.

### 6.4. Topology Maintenance

Vessels registered with a Fleet Registry SHOULD send periodic `PING` messages to the registry to signal their continued availability. The registry MUST maintain a list of active members and prune any Vessel that has not sent a `PING` within a configurable interval (e.g., 5 minutes). This ensures the fleet topology remains accurate.

## 7. Security Considerations

### 7.1. Authentication and Integrity

The HMAC-SHA256 mechanism described in Section 3.3 is the baseline for ensuring sender identity and message integrity. Shared secrets MUST be of high entropy and managed securely. They MUST NOT be transmitted in plaintext. Implementations should consider using established secret management systems.

### 7.2. Authorization

Authentication is not authorization. A Vessel MUST implement an internal Access Control List (ACL) or policy engine to determine if an authenticated sender is permitted to execute a given `COMMAND` or access information via a `QUERY`. Authorization policies should be granular and adhere to the principle of least privilege.

### 7.3. Rate Limiting and Denial of Service

All A2P endpoints MUST implement rate limiting on incoming messages to protect against resource exhaustion and denial-of-service (DoS) attacks. Limits can be based on source IP, `sender_id`, or a combination of factors. Vessels should be designed to handle high message volumes gracefully without crashing.

### 7.4. Sybil Resistance

In decentralized or open Fleets, an attacker could create numerous fake Vessel identities (a Sybil attack) to gain undue influence or disrupt the network. Mitigations include:
*   Using a trusted Fleet Registry that vets new members.
*   Implementing a web-of-trust or reputation system where Vessels build trust over time through successful interactions.
*   Requiring a proof-of-work or resource stake for registration.

### 7.5. Privacy and Confidentiality

As stated in Section 3.4, all communication SHOULD occur over a TLS-encrypted channel. Furthermore, as detailed in Section 5.4, Vessels MUST be careful about the data they share, particularly in `HANDOFF` context packages. Application-level encryption for sensitive fields within the `payload` should be considered if transport-level encryption is insufficient or untrusted.

## 8. Implementation Notes

### 8.1. Minimum Viable Implementation

To be considered A2P v1.0 compliant, a Vessel implementation MUST:
1.  Correctly parse and generate the standard message envelope.
2.  Implement the HMAC-SHA256 authentication scheme.
3.  Support either HTTP or WebSocket as a transport.
4.  Implement the following message types: `HANDSHAKE`, `PING`/`PONG`, and `ERROR`.
5.  Implement at least one other functional message type (e.g., `QUERY` or `COMMAND`) to perform useful work.

### 8.2. Performance Considerations

JSON serialization/deserialization can be a performance bottleneck in high-throughput applications. Implementers should use high-performance JSON libraries. For WebSocket connections, batching multiple small messages into a single frame MAY improve performance, but this is an application-level concern. Future versions of A2P may consider a binary serialization format as an alternative to JSON.

### 8.3. Backward and Forward Compatibility

The `protocol_version` field in the message envelope is critical for managing compatibility.
*   A Vessel supporting version `1.0` receiving a message with a higher major version (e.g., `2.0`) SHOULD respond with an `ERROR` indicating an unsupported version.
*   A Vessel supporting a minor update (e.g., `1.1`) SHOULD be able to communicate with a `1.0` Vessel by only using features present in the `1.0` specification. The initial `HANDSHAKE` exchange is used to negotiate a common feature set.

## 9. Examples

### 9.1. Example 1: Initial Connection and Simple Query

Vessel A (`vessel-a`) connects to Vessel B (`vessel-b`) and asks for its status.

**1. Handshake (A -> B)**
```json
{
  "protocol_version": "1.0",
  "message_id": "a1",
  "timestamp": "2023-10-27T11:00:00Z",
  "sender_id": "vessel-a",
  "recipient_id": "vessel-b",
  "message_type": "HANDSHAKE",
  "payload": {
    "supported_versions": ["1.0"],
    "capabilities": ["system_info_query"]
  }
}
```

**2. Handshake Response (B -> A)**
```json
{
  "protocol_version": "1.0",
  "message_id": "b1",
  "timestamp": "2023-10-27T11:00:01Z",
  "sender_id": "vessel-b",
  "recipient_id": "vessel-a",
  "message_type": "HANDSHAKE",
  "payload": {
    "supported_versions": ["1.0"],
    "capabilities": ["system_info_query", "file_api"]
  }
}
```

**3. Query (A -> B)**
```json
{
  "protocol_version": "1.0",
  "message_id": "a2",
  "timestamp": "2023-10-27T11:00:05Z",
  "sender_id": "vessel-a",
  "recipient_id": "vessel-b",
  "message_type": "QUERY",
  "payload": {
    "query_type": "get_system_load"
  }
}
```

**4. Query Response (B -> A)**
```json
{
  "protocol_version": "1.0",
  "message_id": "b2",
  "timestamp": "2023-10-27T11:00:06Z",
  "sender_id": "vessel-b",
  "recipient_id": "vessel-a",
  "message_type": "QUERY",
  "payload": {
    "response_to": "a2",
    "result": {
      "cpu_percent": 15.5,
      "memory_mb": 2048
    },
    "is_final": true
  }
}
```

### 9.2. Example 2: Task Handoff

Vessel A hands off a video processing task to Vessel C (`vessel-c`).

**1. Handoff (A -> C)**
```json
{
  "protocol_version": "1.0",
  "message_id": "a3",
  "timestamp": "2023-10-27T12:00:00Z",
  "sender_id": "vessel-a",
  "recipient_id": "vessel-c",
  "message_type": "HANDOFF",
  "payload": {
    "task_id": "task-123",
    "context_package": {
      "schema_version": "1.0",
      "goal": "Transcode video file 'input.mp4' to H.265 and upload to S3.",
      "state_data": {
        "source_file": "/path/to/input.mp4",
        "target_format": "h265",
        "upload_bucket": "s3://my-videos",
        "current_step": "transcoding",
        "progress_percent": 45
      },
      "capabilities_required": ["video_transcode", "s3_upload"]
    }
  }
}
```

**2. Status Update (C -> A)**
Vessel C accepts the task and reports it is now in progress.
```json
{
  "protocol_version": "1.0",
  "message_id": "c1",
  "timestamp": "2023-10-27T12:00:02Z",
  "sender_id": "vessel-c",
  "recipient_id": "vessel-a",
  "message_type": "STATUS",
  "payload": {
    "task_id": "task-123",
    "status": "in_progress",
    "message": "Handoff accepted. Resuming transcoding at 45%."
  }
}
```

**3. Final Status Update (C -> A)**
Vessel C completes the task.
```json
{
  "protocol_version": "1.0",
  "message_id": "c2",
  "timestamp": "2023-10-27T12:05:30Z",
  "sender_id": "vessel-c",
  "recipient_id": "vessel-a",
  "message_type": "STATUS",
  "payload": {
    "task_id": "task-123",
    "status": "completed",
    "message": "Transcoding and upload successful. Final location: s3://my-videos/input.mkv"
  }
}
```

## 10. Appendix: JSON Schemas

### 10.1. Message Envelope Schema
```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "A2P Message Envelope",
  "type": "object",
  "properties": {
    "protocol_version": { "type": "string", "pattern": "^1\\.0$" },
    "message_id": { "type": "string", "format": "uuid" },
    "timestamp": { "type": "string", "format": "date-time" },
    "sender_id": { "type": "string", "format": "uri" },
    "recipient_id": { "type": "string", "format": "uri" },
    "message_type": { "type": "string", "enum": ["HANDSHAKE", "PING", "PONG", "QUERY", "COMMAND", "EVENT", "HANDOFF", "STATUS", "ERROR", "GOODBYE"] },
    "payload": { "type": "object" }
  },
  "required": ["protocol_version", "message_id", "timestamp", "sender_id", "recipient_id", "message_type", "payload"]
}
```

### 10.2. Payload Schemas

(Note: For brevity, only a few key payload schemas are shown here. A full implementation would have a schema for each message type.)

**HANDSHAKE Payload:**
```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "HandshakePayload",
  "type": "object",
  "properties": {
    "supported_versions": { "type": "array", "items": { "type": "string" } },
    "capabilities": { "type": "array", "items": { "type": "string" } },
    "agent_name": { "type": "string" }
  },
  "required": ["supported_versions", "capabilities"]
}
```

**COMMAND Payload:**
```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "CommandPayload",
  "type": "object",
  "properties": {
    "action": { "type": "string" },
    "parameters": { "type": "object" },
    "task_id": { "type": "string" }
  },
  "required": ["action"]
}
```

**HANDOFF Payload:**
```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "HandoffPayload",
  "type": "object",
  "properties": {
    "task_id": { "type": "string" },
    "context_package": {
      "type": "object",
      "properties": {
        "schema_version": { "type": "string", "pattern": "^1\\.0$" },
        "goal": { "type": "string" },
        "state_data": { "type": "object" },
        "history": { "type": "array", "items": { "type": "object" } },
        "capabilities_required": { "type": "array", "items": { "type": "string" } },
        "credentials": { "type": "object" }
      },
      "required": ["schema_version", "goal", "state_data"]
    }
  },
  "required": ["task_id", "context_package"]
}
```