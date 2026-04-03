# The BYOK Configuration Discovery Protocol: Secure Multi-Provider Model Access for Autonomous Agents

**Author:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Draft
**Keywords:** BYOK, configuration discovery, provider abstraction, model routing, API key security, multi-provider, zero-trust

## Abstract

We present the BYOK Configuration Discovery Protocol (BCDP), a prioritized fallback chain for autonomous agent model access: URL → Auth → Cookie → KV Store → fail. We provide a security analysis of each discovery method, define a Provider Abstraction Layer (PAL) that normalizes across heterogeneous APIs, and introduce per-role model mixing — the practice of routing different agent tasks to different models based on capability requirements and cost constraints.

## 1. Introduction

Bring-Your-Own-Key (BYOK) gives users control over which AI models their agents use. But BYOK introduces a configuration problem: how does an autonomous agent discover and authenticate to a user's chosen model provider? Manual configuration is fragile; hard-coded credentials are insecure; and provider APIs are heterogeneous.

We present BCDP: a protocol that gracefully degrades through a prioritized discovery chain, a provider abstraction layer that normalizes API differences, and a model mixing architecture that routes tasks to optimal models.

## 2. Core Concepts

### 2.1 The Discovery Chain

The BCDP discovery chain is a prioritized fallback sequence:

```
Priority 1: Direct URL (provider endpoint)
Priority 2: Auth token (bearer/OAuth)
Priority 3: Session cookie (web-based access)
Priority 4: KV store (environment variable, secrets manager, .env)
Priority 5: Fail (graceful degradation)
```

Each level is tried in order. The first successful discovery terminates the chain.

**Definition 2.1 (Discovery Success).** A discovery attempt at level i succeeds if and only if:
1. The configuration artifact exists and is non-empty
2. Authentication against the provider succeeds
3. A model response is received within timeout t_max

### 2.2 Security Analysis

| Level | Artifact | Threat Model | Mitigation |
|---|---|---|---|
| URL | Endpoint address | MITM, DNS hijack | TLS, certificate pinning |
| Auth | Bearer token | Token theft, replay | Short-lived tokens, scope limitation |
| Cookie | Session cookie | XSS, CSRF | HttpOnly, SameSite, CSRF tokens |
| KV | env var / secrets | Process dump, log leak | Secrets manager, no logging |
| Fail | N/A | Denial of service | Graceful degradation, caching |

**Theorem 2.1 (Discovery Security Bound).** The security of BCDP is bounded by the security of the weakest successful discovery level. If discovery succeeds at level 3 (cookie), the effective security is that of cookie-based authentication, regardless of whether level 1 or 2 were attempted.

*Proof.* Authentication is established at the first successful level. Subsequent levels are not attempted. Therefore, the security properties of the successful level determine the overall security of the connection. □

### 2.3 Provider Abstraction Layer (PAL)

**Definition 2.2 (PAL).** A Provider Abstraction Layer is a function:

pal(prompt, model_spec, config) → response

where model_spec = (provider, model_name, parameters) and config is the discovered authentication configuration.

PAL normalizes across providers:

| Provider | Auth Method | Endpoint Format | Response Format |
|---|---|---|---|
| OpenAI | Bearer token | api.openai.com/v1/chat | OpenAI format |
| Anthropic | x-api-key | api.anthropic.com/v1 | Anthropic format |
| Google | OAuth2 | generativelanguage.googleapis.com | Google format |
| xAI | Bearer token | api.x.ai/v1 | OpenAI-compatible |
| Local | None | localhost:port | varies |

PAL handles:
- Authentication header injection
- Request format translation
- Response format normalization
- Error code mapping
- Rate limit handling
- Retry logic with backoff

### 2.4 Per-Role Model Mixing

**Definition 2.3 (Task Role).** A task role r ∈ R = {code, chat, analysis, vision, reasoning, creative} categorizes the type of cognitive work required.

**Definition 2.4 (Model Mixing Function).** A mixing function μ: R → model_spec assigns optimal models to task roles.

Example mixing configuration:

| Role | Primary Model | Fallback | Rationale |
|---|---|---|---|
| code | claude-4-opus | gpt-4.1 | Best code generation |
| chat | glm-5-turbo | claude-4-sonnet | Fast, conversational |
| analysis | gemini-2.5-pro | gpt-4.1 | Large context window |
| vision | gpt-4.1 | claude-4-sonnet | Strong image understanding |
| reasoning | o3 | claude-4-opus | Deep reasoning chains |
| creative | claude-4-opus | glm-5-turbo | Creative writing |

**Theorem 2.2 (Mixing Optimality).** Given cost functions C(m, r) for model m on role r, and quality functions Q(m, r), the optimal mixing function minimizes Σᵣ C(μ(r), r) subject to Q(μ(r), r) ≥ Q_min(r) for all r.

This is a constrained optimization problem solvable by standard methods when quality and cost functions are known.

## 3. Implementation

### 3.1 BCDP in Cocapn

Cocapn implements BCDP through OpenClaw's provider configuration:

```
openclaw config set providers.openai.apiKey $OPENAI_API_KEY
openclaw config set providers.anthropic.apiKey $ANTHROPIC_API_KEY
```

Discovery follows the chain automatically, with environment variables as the primary KV store.

### 3.2 Per-Vessel Configuration

Each vessel (repo-agent) can have its own model mixing configuration, stored in the vessel's config. This allows:
- Research vessels to use high-context models
- Communication vessels to use fast models
- Code vessels to use coding-specialized models

### 3.3 Graceful Degradation

When BCDP reaches level 5 (fail), the agent:
1. Logs the failure with full diagnostic information
2. Falls back to a cached/stored response if available
3. Notifies the user of degraded capability
4. Queues pending tasks for retry when connectivity returns

## 4. Applications to Cocapn

BCDP enables Cocapn's BYOK promise: users control their models, agents discover them automatically. The Provider Abstraction Layer means adding a new provider requires only a PAL adapter, not changes to agent code. Per-role mixing ensures cost-effective operation without sacrificing quality on critical tasks.

## 5. Related Work

- **OAuth 2.0 (Hardt, 2012):** Delegated authorization framework. BCDP extends this with agent-specific discovery.
- **Semantic Versioning for APIs:** API compatibility management. PAL provides version-agnostic access.
- **Circuit Breaker Pattern (Fowler, 2014):** BCDP's fallback chain is a circuit breaker with ordered fallbacks.
- **Twelve-Factor App (Wiggins, 2011):** Config via environment variables. BCDP's KV level implements this principle.

## 6. Future Directions

1. **Dynamic model selection:** Using ML to predict optimal model for each task in real-time
2. **Federated BYOK:** Sharing model access across vessels without sharing credentials
3. **Cost-aware routing:** Automatically selecting cheaper models when quality requirements are relaxed
4. **Provider health monitoring:** Proactive failover based on provider outage prediction

## References

1. Hardt, D. (2012). "The OAuth 2.0 Authorization Framework." RFC 6749.
2. Fowler, M. (2014). "CircuitBreaker." *MartinFowler.com.*
3. Wiggins, A. (2011). "The Twelve-Factor App." *12factor.net.*
4. DiGennaro, C. et al. (2026). "BYOK Multi-Provider Agent Routing." Papermill Working Paper.
5. Fielding, R.T. (2000). "Architectural Styles and the Design of Network-based Software Architectures." Doctoral dissertation, UC Irvine.
