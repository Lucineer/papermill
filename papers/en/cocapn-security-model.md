# The Cocapn Security Model: Trust Boundaries, Key Isolation, and Privacy in Autonomous Agent Fleets

**Author:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Draft
**Keywords:** security model, trust boundaries, BYOK key isolation, fleet security, multi-tenant privacy, zero-trust, repo-agent security

## Abstract

We present the Cocapn Security Model, a comprehensive framework for securing fleets of autonomous repo-agent vessels. We define trust boundaries at the vessel, fleet, and inter-fleet levels, formalize BYOK key isolation guarantees, specify cross-vessel communication security protocols, and address user data privacy in multi-tenant deployments. The model follows zero-trust principles: no vessel trusts any other vessel by default, and all access is mediated through capability-based authorization.

## 1. Introduction

A fleet of autonomous agents that communicate, share state, and act on behalf of users presents a unique security challenge. Traditional perimeter security is inadequate — agents are the perimeter, the application, and the user proxy simultaneously. We need security models that account for:

- Agents that modify their own code (self-modification)
- Agents that communicate with unknown agents (A2A protocol)
- Users who bring their own API keys (BYOK)
- Multiple users sharing infrastructure (multi-tenant)
- Persistent memory that accumulates sensitive information over time

## 2. Core Concepts

### 2.1 Trust Boundaries

**Definition 2.1 (Trust Boundary).** A trust boundary B is a set of entities {e₁, e₂, ...} where all entities within B mutually trust each other for a specific capability c.

Cocapn defines four trust boundaries:

| Boundary | Scope | Trust Level | Example |
|---|---|---|---|
| B_vessel | Single vessel | Full trust | Vessel's own code, memory, config |
| B_fleet | All vessels for one user | Domain trust | User's personal fleet |
| B_platform | All vessels on one host | Infrastructure trust | Shared Cocapn instance |
| B_external | All external services | No trust | Third-party APIs, other fleets |

**Axiom 2.1 (Zero Trust).** No trust boundary extends to any other trust boundary by default. All cross-boundary access requires explicit authorization.

**Theorem 2.1 (Boundary Isolation).** If trust boundaries are properly isolated, a compromise of any single vessel does not compromise any other vessel, fleet, or the platform.

*Proof.* Under zero trust, a compromised vessel has credentials only for its own boundary. Access to other boundaries requires separate credentials, which the compromised vessel does not possess. Therefore, compromise is contained. □

### 2.2 BYOK Key Isolation

**Definition 2.2 (Key Isolation Property).** A system has key isolation if, for any two users u₁ and u₂ with API keys k₁ and k₂, no code path exists where u₁'s agent can access k₂.

Key isolation in Cocapn is enforced through:

1. **Process isolation:** Each vessel's BYOK configuration is stored in its own config scope
2. **Memory isolation:** LLM sessions for vessel v₁ cannot access keys configured for vessel v₂
3. **Filesystem isolation:** API keys are stored in per-vessel encrypted storage
4. **Audit logging:** Every key access is logged with the accessing vessel's identity

**Theorem 2.2 (Key Isolation Guarantee).** Under the above enforcement mechanisms, the probability of key cross-contamination is bounded by the probability of a platform-level compromise, which is independent of individual vessel security.

### 2.3 Cross-Vessel Communication Security

**Definition 2.3 (A2A Security Properties).** Secure A2A communication requires:
- **Confidentiality:** Messages are encrypted end-to-end
- **Integrity:** Messages cannot be tampered with in transit
- **Authentication:** Message senders are verified
- **Authorization:** Recipients verify they are allowed to receive the message
- **Non-repudiation:** Senders cannot deny sending a message

Implementation:

| Property | Mechanism |
|---|---|
| Confidentiality | TLS 1.3 + per-vessel key pairs |
| Integrity | HMAC-SHA256 on message payloads |
| Authentication | Ed25519 signatures on all messages |
| Authorization | Capability tokens (see §2.4) |
| Non-repudiation | Signed message logs in git |

### 2.4 Capability-Based Authorization

**Definition 2.4 (Capability).** A capability cap = (vessel_id, action, resource, expiry) grants a specific vessel permission to perform a specific action on a specific resource until a specific time.

Capabilities are:
- **Unforgeable:** Signed by the resource owner
- **Delegatable:** Can be passed to other vessels (with restrictions)
- **Revocable:** Owner can revoke before expiry
- **Narrow:** Grant minimum necessary permissions

**Theorem 2.3 (Capability Safety).** Under capability-based authorization, a vessel can only access resources for which it holds a valid, unexpired, non-revoked capability, regardless of any other security properties.

*Proof.* By definition, access requires a valid capability. Without a capability, access is denied. Capabilities are unforgeable, so they cannot be created by unauthorized vessels. Therefore, the capability set exactly defines the access set. □

### 2.5 User Data Privacy in Multi-Tenant Deployments

**Definition 2.5 (Privacy Isolation).** A multi-tenant deployment has privacy isolation if, for any two users u₁ and u₂, u₁'s vessel cannot infer any information about u₂'s data, behavior, or preferences.

Privacy isolation is enforced through:

1. **Data isolation:** Each vessel's memory files are in separate git repositories
2. **Model isolation:** BYOK ensures u₁'s keys are never used for u₂'s inference
3. **Communication isolation:** A2A messages are only routed between vessels in the same fleet unless explicitly authorized
4. **Logging isolation:** Audit logs are per-vessel, not centralized

**Theorem 2.4 (Privacy Guarantee).** Under privacy isolation, the information leakage from u₁ to u₂ is bounded by:
- Side-channel attacks on the shared platform (mitigated by process isolation)
- Model provider inference (mitigated by BYOK, different providers per user)
- Human operator access (mitigated by access controls and audit logging)

## 3. Implementation

### 3.1 Security Architecture

```
┌─────────────────────────────────────┐
│         Platform Boundary           │
│  ┌───────────┐  ┌───────────┐      │
│  │ Fleet A   │  │ Fleet B   │      │
│  │ ┌───────┐ │  │ ┌───────┐ │      │
│  │ │ Vessel│ │  │ │ Vessel│ │      │
│  │ │  A1   │ │  │ │  B1   │ │      │
│  │ └───────┘ │  │ └───────┘ │      │
│  │ ┌───────┐ │  │ ┌───────┐ │      │
│  │ │ Vessel│ │  │ │ Vessel│ │      │
│  │ │  A2   │ │  │ │  B2   │ │      │
│  │ └───────┘ │  │ └───────┘ │      │
│  └───────────┘  └───────────┘      │
│         ┌─────────────┐            │
│         │  Key Store   │            │
│         │  (encrypted) │            │
│         └─────────────┘            │
└─────────────────────────────────────┘
```

### 3.2 Threat Model

| Threat | Mitigation | Residual Risk |
|---|---|---|
| Vessel compromise | Boundary isolation, capability tokens | Impact limited to single vessel |
| Key theft | Encrypted storage, no logging of keys | Platform-level compromise |
| Message interception | TLS + payload encryption | Quantum computing (future) |
| Cross-tenant data leak | Per-vessel isolation | Side channels |
| Malicious vessel (supply chain) | Skill signing, code review | Zero-day in trusted skills |
| Model provider data access | BYOK, provider diversity | Provider TOS violations |

### 3.3 Incident Response

When a security incident is detected:
1. **Contain:** Revoke all capabilities for the compromised vessel
2. **Isolate:** Disable A2A communication for the vessel
3. **Assess:** Review git log for unauthorized changes
4. **Recover:** Roll back to last known-good state
5. **Report:** Notify the vessel owner with a detailed incident report

## 4. Applications to Cocapn

The security model enables Cocapn to:
- Safely host multiple users' fleets on shared infrastructure
- Allow BYOK without key cross-contamination risk
- Support secure cross-vessel collaboration within fleets
- Maintain audit trails for compliance and debugging
- Provide defense-in-depth against vessel compromise

## 5. Related Work

- **Saltzer & Schroeder (1975):** "The Protection of Information in Computer Systems." The foundational principles of secure system design.
- **Miller et al. (1995):** "Capability-Based Security." The capability security model.
- **Google BeyondCorp (2014):** Zero-trust enterprise security. Cocapn applies the same principles to agent fleets.
- **NIST SP 800-207 (2020):** Zero Trust Architecture. Formal definition of zero-trust principles.

## 6. Future Directions

1. **Formal verification:** Proving security properties using model checking
2. **Hardware-backed key storage:** TPM/TEE for BYOK key isolation
3. **Homomorphic communication:** Processing encrypted messages without decryption
4. **Quantum-resistant cryptography:** Post-quantum algorithms for vessel signatures

## References

1. Saltzer, J. & Schroeder, M. (1975). "The Protection of Information in Computer Systems." *Communications of the ACM.*
2. Miller, M., Neuman, B. & Schiller, J. (1995). "Capability-Based Protection in the WAVE System." *ACM CCS.*
3. Google (2014). "BeyondCorp: A New Approach to Enterprise Security." *Google Research Blog.*
4. Rose, S., Borchert, O., Mitchell, S. & Connelly, S. (2020). "Zero Trust Architecture." NIST SP 800-207.
5. DiGennaro, C. et al. (2026). "Cocapn Fleet A2A Protocol." Papermill Working Paper.
