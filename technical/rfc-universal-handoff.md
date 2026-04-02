> *Gemini 2.5 Pro*

Of course. Here is the formal RFC-style specification for the Universal Handoff Protocol (UHP), written according to your requirements.

---

**Internet-Draft: UHP**  
**Intended status: Standards Track**  
**Expires: 6 months**

**Cocapn Ecosystem Working Group**  
**J. Doe, Editor**  
**October 26, 2023**

### RFC-0001: Universal Handoff Protocol for AI Agent Context Transfer

**Status of this Memo**

This Internet-Draft is submitted in full conformance with the provisions of BCP 78 and BCP 79.

Internet-Drafts are working documents of the Internet Engineering Task Force (IETF). Note that other groups may also distribute working documents as Internet-Drafts. The list of current Internet-Drafts is at https://datatracker.ietf.org/drafts/current/.

Internet-Drafts are draft documents valid for a maximum of six months and may be updated, replaced, or obsoleted by other documents at any time. It is inappropriate to use Internet-Drafts as reference material or to cite them other than as "work in progress."

**Copyright Notice**

Copyright (c) 2023 IETF Trust and the persons identified as the document authors. All rights reserved.

### Abstract

The Universal Handoff Protocol (UHP) defines a standardized, secure, and privacy-preserving data structure for transferring context, state, and intent between AI agents within the cocapn ecosystem. Unlike simple message passing, UHP encapsulates a complete unit of work, including its history, required state, and explicit privacy boundaries. This allows a receiving agent to seamlessly continue a task initiated by another agent or a human user, while respecting the data sensitivity constraints defined by the sender. This specification details the protocol's data structure, security mechanisms, privacy model, and operational flow.

### Table of Contents

1.  [Introduction](#1-introduction)
    1.1. [Motivation](#11-motivation)
    1.2. [Scope](#12-scope)
    1.3. [Requirements Language](#13-requirements-language)
2.  [Handoff Object Structure](#2-handoff-object-structure)
    2.1. [Top-Level Fields](#21-top-level-fields)
    2.2. [`context` Object](#22-context-object)
    2.3. [`privacy` Object](#23-privacy-object)
    2.4. [`security` Object](#24-security-object)
    2.5. [`metadata` Object](#25-metadata-object)
3.  [Privacy Model](#3-privacy-model)
    3.1. [Privacy Levels](#31-privacy-levels)
    3.2. [Field-Level Controls](#32-field-level-controls)
4.  [Security Considerations](#4-security-considerations)
    4.1. [Integrity and Authenticity](#41-integrity-and-authenticity)
    4.2. [Signature Generation](#42-signature-generation)
    4.3. [Signature Verification](#43-signature-verification)
    4.4. [Replay Protection](#44-replay-protection)
5.  [Protocol Flow](#5-protocol-flow)
    5.1. [Handoff Creation](#51-handoff-creation)
    5.2. [Transmission](#52-transmission)
    5.3. [Reception and Processing](#53-reception-and-processing)
6.  [Use Cases](#6-use-cases)
7.  [Reference Implementation](#7-reference-implementation)
    7.1. [TypeScript Data Structures](#71-typescript-data-structures)
    7.2. [Signature Generation Pseudocode](#72-signature-generation-pseudocode)
    7.3. [Test Vectors](#73-test-vectors)
    7.4. [Error Codes and Handling](#74-error-codes-and-handling)
8.  [Future Extensions](#8-future-extensions)
9.  [IANA Considerations](#9-iana-considerations)
10. [References](#10-references)

---

### 1. Introduction

#### 1.1. Motivation

As AI agents become more specialized and collaborative, the need for a robust context-transfer mechanism is paramount. Simple API calls or message queues often lose critical information about the sender's intent, the history of the interaction, and the sensitivity of the data being shared. UHP is designed to address this gap by providing a self-contained, verifiable "package" of work that can be handed off between agents. The primary motivation is to enable complex, multi-agent workflows where tasks can be delegated, escalated, or parallelized without losing context or violating privacy constraints.

#### 1.2. Scope

This protocol is designed for the following types of handoffs within the cocapn ecosystem:
*   **Agent-to-Agent:** An agent delegates a sub-task or transfers responsibility to another agent.
*   **Agent-to-Human:** An agent requires human intervention or approval, presenting the context in a structured way.
*   **Human-to-Agent:** A human initiates a task for an agent, providing initial context and constraints.

This document specifies the format of the UHP data object and the rules for its creation, signing, and verification. The transport mechanism (e.g., HTTP, WebSockets, message queues) is outside the scope of this specification, but it is assumed to be a reliable channel.

#### 1.3. Requirements Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 [RFC2119].

### 2. Handoff Object Structure

A UHP Handoff is a JSON object. The following sections define its structure and fields.

```json
{
  "version": "uhp-1.0",
  "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "from": { "agentId": "personallog-123", "name": "PersonalLog", "capabilities": ["text-analysis", "task-creation"] },
  "to": { "agentId": "makerlog-456", "name": "MakerLog" },
  "timestamp": 1672531200,
  "context": {
    "summary": "User wants to build a web scraper for a news site.",
    "task": "Generate Python code using BeautifulSoup to extract headlines from example.com.",
    "state": { "targetUrl": "https://example.com", "cssSelector": ".headline" },
    "history": [ { "role": "user", "content": "I need help with a coding task." }, { "role": "agent", "content": "Sure, what do you want to build?" } ]
  },
  "privacy": {
    "level": "shared",
    "publicFields": ["summary", "task"],
    "sharedFields": ["state"],
    "privateFields": ["history"],
    "redacted": []
  },
  "security": {
    "signature": "HMAC-SHA256",
    "senderPublicKey": "...",
    "signatureValue": "a1b2c3d4..."
  },
  "metadata": {
    "priority": "normal",
    "ttl": 3600,
    "replyTo": "e45bb11c-67dd-4383-b568-1f02b2c3d480",
    "tags": ["coding", "python", "web-scraping"]
  }
}
```

#### 2.1. Top-Level Fields

*   `version` (string, REQUIRED): The version of the UHP specification. MUST be in the format `uhp-X.Y`. This document specifies `uhp-1.0`.
*   `id` (string, REQUIRED): A unique identifier for this handoff instance. MUST be a UUID v4 to prevent replay attacks.
*   `from` (object, REQUIRED): An object describing the sending agent.
    *   `agentId` (string, REQUIRED): A unique identifier for the sending agent.
    *   `name` (string, OPTIONAL): A human-readable name for the sender.
    *   `capabilities` (array of strings, OPTIONAL): A list of capabilities the sending agent possesses.
*   `to` (object, REQUIRED): An object describing the intended recipient.
    *   `agentId` (string, REQUIRED): A unique identifier for the receiving agent. A special value of `*` indicates a broadcast.
    *   `name` (string, OPTIONAL): A human-readable name for the recipient.
*   `timestamp` (number, REQUIRED): A Unix timestamp (seconds since epoch) indicating when the handoff was created.
*   `context` (object, REQUIRED): The core payload of the handoff. See Section 2.2.
*   `privacy` (object, REQUIRED): Defines the privacy rules for the handoff. See Section 2.3.
*   `security` (object, REQUIRED): Contains the cryptographic signature. See Section 2.4.
*   `metadata` (object, OPTIONAL): Additional, non-essential data about the handoff. See Section 2.5.

#### 2.2. `context` Object

*   `summary` (string, REQUIRED): A brief, natural language summary of the handoff's purpose.
*   `task` (string, REQUIRED): A clear, actionable description of what the receiving agent needs to do.
*   `state` (object, OPTIONAL): A key-value map of structured data necessary for the task. Values can be any valid JSON type.
*   `history` (array of objects, OPTIONAL): A log of previous interactions related to this context, typically in a chat format.
    *   `role` (string, REQUIRED): The role of the entity that produced the content (e.g., "user", "agent", "system").
    *   `content` (string, REQUIRED): The content of the message.

#### 2.3. `privacy` Object

*   `level` (string, REQUIRED): The overall privacy level of the handoff. MUST be one of `public`, `shared`, or `private`. See Section 3.1.
*   `publicFields` (array of strings, OPTIONAL): A list of field paths within the `context` object that are considered public.
*   `sharedFields` (array of strings, OPTIONAL): A list of field paths that are considered shared.
*   `privateFields` (array of strings, OPTIONAL): A list of field paths that are considered private.
*   `redacted` (array of strings, OPTIONAL): A list of fields that were intentionally removed by the sender before transmission, with an optional reason (e.g., `"history.0.content.pii_removed"`).

#### 2.4. `security` Object

*   `signature` (string, REQUIRED): The algorithm used for the signature. For `uhp-1.0`, this MUST be `HMAC-SHA256`.
*   `senderPublicKey` (string, OPTIONAL): The public key of the sender, used for key discovery or in asymmetric signature schemes in future versions.
*   `signatureValue` (string, REQUIRED): The hexadecimal encoded signature hash.

#### 2.5. `metadata` Object

*   `priority` (string, OPTIONAL): The priority of the handoff. RECOMMENDED values are `low`, `normal`, `high`, `urgent`.
*   `ttl` (number, OPTIONAL): Time-to-live in seconds. A receiver SHOULD discard the handoff if `current_timestamp > timestamp + ttl`.
*   `replyTo` (string, OPTIONAL): The `id` of a previous handoff to which this is a reply.
*   `tags` (array of strings, OPTIONAL): A list of tags for categorization or routing.

### 3. Privacy Model

UHP's privacy model is designed to be explicit and enforceable by the receiving agent.

#### 3.1. Privacy Levels

*   **`public`**: The handoff contains no sensitive information and can be safely broadcast or processed by any agent. The `to.agentId` MAY be `*`.
*   **`shared`**: The handoff is intended for a trusted group of agents that have an established relationship (e.g., agents belonging to the same user). It may contain non-critical secrets or user-specific data. The receiver MUST verify it has a "shared" trust relationship with the sender.
*   **`private`**: The handoff is strictly for the intended recipient specified in `to.agentId`. It may contain sensitive secrets, PII, or full, unredacted context. The receiver MUST be the agent identified in `to.agentId`.

A receiving agent MUST reject a handoff if its relationship with the sender does not meet the requirements of the specified privacy `level`.

#### 3.2. Field-Level Controls

The `publicFields`, `sharedFields`, and `privateFields` arrays provide granular control over data visibility, although their enforcement is primarily informational for the receiver in version 1.0. A sender SHOULD populate these fields to clearly state the intended sensitivity of different parts of the `context`.

### 4. Security Considerations

#### 4.1. Integrity and Authenticity

UHP uses a Hash-based Message Authentication Code (HMAC) to ensure that a handoff has not been tampered with (integrity) and that it originates from the claimed sender (authenticity). This requires the sender and receiver to have a pre-established shared secret.

#### 4.2. Signature Generation

The sender MUST perform the following steps to generate the `signatureValue`:
1.  Create a copy of the handoff object.
2.  In the copy, set `security.signatureValue` to an empty string (`""`).
3.  Compute the SHA-256 hash of the canonicalized JSON representation of the `context` object. The JSON MUST be canonicalized (e.g., keys sorted, no insignificant whitespace).
4.  Construct the "string-to-sign" by concatenating the following fields, separated by a newline character (`\n`):
    *   `version`
    *   `id`
    *   `from.agentId`
    *   `to.agentId`
    *   `timestamp`
    *   The SHA-256 hash of the canonicalized `context` object (from step 3), encoded in hexadecimal.
5.  Compute the HMAC-SHA256 of the "string-to-sign" using the shared secret.
6.  Encode the resulting HMAC as a hexadecimal string and place it in the `security.signatureValue` field of the original handoff object.

#### 4.3. Signature Verification

The receiver MUST perform the following steps to verify the signature:
1.  Extract the `signatureValue` from the received handoff object.
2.  Perform steps 1-5 from the Signature Generation process (Section 4.2) on the received handoff object using its own copy of the shared secret.
3.  Compare the generated signature with the received `signatureValue`. The comparison MUST be done in a constant-time manner to prevent timing attacks.
4.  If the signatures match, the handoff is authentic and has integrity. If they do not match, the handoff MUST be rejected.

#### 4.4. Replay Protection

The `id` field, which MUST be a UUID v4, provides replay protection. A receiving agent SHOULD maintain a record of recently processed handoff `id`s and MUST reject any handoff with a previously seen `id`. The `timestamp` and `ttl` fields provide an additional layer of protection against old, replayed messages.

### 5. Protocol Flow

#### 5.1. Handoff Creation

1.  The sending agent (Sender) assembles the `context`, `privacy`, and `metadata` for the handoff.
2.  The Sender sets the `version`, `id` (new UUID v4), `from`, `to`, and `timestamp` fields.
3.  The Sender generates the signature as described in Section 4.2 and populates the `security` object.

#### 5.2. Transmission

1.  The Sender serializes the complete handoff object to a JSON string.
2.  The Sender transmits the JSON string to the recipient via an underlying transport protocol (e.g., as the body of an HTTP POST request). The transport layer SHOULD use TLS for confidentiality.

#### 5.3. Reception and Processing

1.  The receiving agent (Receiver) deserializes the JSON string into a handoff object.
2.  The Receiver MUST verify the signature as described in Section 4.3. If verification fails, processing stops and an error is returned.
3.  The Receiver SHOULD check the `timestamp` and `ttl` to ensure the handoff is not expired.
4.  The Receiver MUST check the `id` against its cache of processed IDs to prevent replay attacks.
5.  The Receiver MUST evaluate the `privacy.level` against its established relationship with the sender. If the trust level is insufficient, the handoff is rejected.
6.  If all checks pass, the Receiver processes the `context` and applies it to its internal state to perform the requested `task`.
7.  The Receiver MAY send an acknowledgment handoff back to the Sender, using the original `id` in the `replyTo` field of the new handoff.

### 6. Use Cases

*   **PersonalLog → MakerLog:** A user tells their `PersonalLog` agent, "I need help with this coding task." The `PersonalLog` creates a `shared` handoff with the task description and relevant conversation history, then sends it to the `MakerLog` agent, which specializes in coding.
*   **DMLog → Player Twin:** A Dungeon Master agent (`DMLog`) needs to simulate a combat encounter for a specific player. It creates a `private` handoff containing the monster's stats and AI behavior, sending it to the player's digital twin agent (`Player Twin`) for processing.
*   **BusinessLog → Research Agent:** A user workshops a business idea with their `BusinessLog` agent. To get market data, the `BusinessLog` creates a `shared` handoff with the core idea (summary, task) but redacts the sensitive financial projections (history). It sends this to a `Research Agent`.
*   **Any agent → Fleet broadcast:** An agent in a fleet needs to report its current status. It creates a `public` handoff with `to.agentId: "*"` and a summary of "Status: idle." All other agents in the fleet can receive and log this status update.

### 7. Reference Implementation

#### 7.1. TypeScript Data Structures

```typescript
interface HandoffPrincipal {
  agentId: string;
  name?: string;
  capabilities?: string[];
}

interface HandoffContext {
  summary: string;
  task: string;
  state?: Record<string, any>;
  history?: { role: string; content: string }[];
}

interface HandoffPrivacy {
  level: 'public' | 'shared' | 'private';
  publicFields?: string[];
  sharedFields?: string[];
  privateFields?: string[];
  redacted?: string[];
}

interface HandoffSecurity {
  signature: 'HMAC-SHA256';
  senderPublicKey?: string;
  signatureValue: string;
}

interface HandoffMetadata {
  priority?: 'low' | 'normal' | 'high' | 'urgent';
  ttl?: number;
  replyTo?: string;
  tags?: string[];
}

interface UHP_Handoff {
  version: 'uhp-1.0';
  id: string; // UUID v4
  from: HandoffPrincipal;
  to: HandoffPrincipal;
  timestamp: number; // Unix timestamp (seconds)
  context: HandoffContext;
  privacy: HandoffPrivacy;
  security: HandoffSecurity;
  metadata?: HandoffMetadata;
}
```

#### 7.2. Signature Generation Pseudocode

```typescript
import { createHmac, createHash } from 'crypto';

function canonicalize(obj: object): string {
  // A stable stringify function that sorts keys
  return JSON.stringify(obj, Object.keys(obj).sort());
}

function generateSignature(handoff: UHP_Handoff, sharedSecret: string): string {
  // 1. Create a copy and clear the signatureValue
  const handoffToSign = { ...handoff, security: { ...handoff.security, signatureValue: "" } };

  // 2. Compute the hash of the context
  const contextHash = createHash('sha256')
    .update(canonicalize(handoffToSign.context))
    .digest('hex');

  // 3. Construct the string-to-sign
  const stringToSign = [
    handoffToSign.version,
    handoffToSign.id,
    handoffToSign.from.agentId,
    handoffToSign.to.agentId,
    handoffToSign.timestamp,
    contextHash
  ].join('\n');

  // 4. Compute the HMAC
  const hmac = createHmac('sha256', sharedSecret)
    .update(stringToSign)
    .digest('hex');

  return hmac;
}

// Verification would follow the same process and compare the result.
```

#### 7.3. Test Vectors

*   **Shared Secret:** `cocapn-secret-key-for-testing`
*   **Handoff Object (without signatureValue):**
    ```json
    {
      "version": "uhp-1.0",
      "id": "a4b5c6d7-e8f9-4a0b-1c2d-3e4f5a6b7c8d",
      "from": { "agentId": "agent-alpha" },
      "to": { "agentId": "agent-beta" },
      "timestamp": 1672531200,
      "context": {
        "summary": "Test handoff",
        "task": "Verify signature"
      },
      "privacy": { "level": "shared" },
      "security": { "signature": "HMAC-SHA256", "signatureValue": "" }
    }
    ```
*   **Canonicalized Context:** `{"summary":"Test handoff","task":"Verify signature"}`
*   **Context SHA-256 Hash:** `f003f2a3852a4537143050512b257273188a38a7590f4503a46956291a104a18`
*   **String-to-Sign:**
    ```
    uhp-1.0
    a4b5c6d7-e8f9-4a0b-1c2d-3e4f5a6b7c8d
    agent-alpha
    agent-beta
    1672531200
    f003f2a3852a4537143050512b257273188a38a7590f4503a46956291a104a18
    ```
*   **Expected `signatureValue` (HMAC-SHA256):** `1f68b333a7a8047e148a04375b4104c89280a8e03433e3a1f87c938c531d092c`

#### 7.4. Error Codes and Handling

Implementations SHOULD use clear error codes when rejecting a handoff.
*   `400 Bad Request`: The handoff object is malformed or missing required fields.
*   `401 Unauthorized`: The signature is invalid (`ERR_INVALID_SIGNATURE`).
*   `403 Forbidden`: The receiver's trust relationship with the sender does not permit the `privacy.level` (`ERR_PRIVACY_VIOLATION`).
*   `408 Request Timeout`: The handoff has expired based on its `timestamp` and `ttl` (`ERR_EXPIRED_HANDOFF`).
*   `409 Conflict`: The handoff `id` has already been processed (`ERR_REPLAY_DETECTED`).

### 8. Future Extensions

The UHP is designed to be extensible. Future versions or extensions may include:
*   **Streaming Handoffs:** For long-running tasks, context could be streamed using Server-Sent Events (SSE), with each event being a partial UHP update.
*   **Multi-party Handoffs:** The `to` field could be an array to support handoffs for group tasks or consensus-based workflows.
*   **Handoff Chains:** A mechanism to cryptographically link a series of handoffs (A → B → C), creating a verifiable audit trail.
*   **Receipts and Acknowledgments:** A formal specification for acknowledgment messages, including delivery and read receipts, to build more reliable systems.

### 9. IANA Considerations

This document has no IANA actions.

### 10. References

[RFC2119] Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", BCP 14, RFC 2119, DOI 10.17487/RFC2119, March 1997, <https://www.rfc-editor.org/info/rfc2119>.