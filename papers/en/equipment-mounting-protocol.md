# The Equipment Mounting Protocol: External Capability Integration in ZeroClaw

**Authors:** Superinstance & Lucineer (DiGennaro et al.)

---

## Abstract

An agent's power is defined not by its native intelligence but by the equipment it can wield. This paper specifies the Equipment Mounting Protocol (EMP), the mechanism by which ZeroClaw agents discover, authenticate, mount, and operate external capabilities — APIs, code repositories, CLI tools, MCP servers, and library bindings. The protocol is inspired by three observations: (1) intelligence without tools is a brain in a jar, (2) every mount point is an attack surface, and (3) the best equipment rack is the one you didn't have to configure. We present the mount lifecycle, the security model, the discovery protocol, and the "guns, lots of guns" design philosophy that prioritizes capability breadth with principled access control.

---

## 1. Introduction

### 1.1 The Brain-in-a-Jar Problem

A state-of-the-art LLM with no tools can reason, synthesize, and advise — but it cannot *do*. It cannot read your email, deploy your code, query your database, or control your lights. The gap between reasoning and action is bridged entirely by equipment.

The Equipment Mounting Protocol asks a single design question: *What is the minimal, secure, composable mechanism for giving an agent access to an arbitrary external capability?*

### 1.2 "Guns, Lots of Guns"

The title quotes a design principle: an agent should have access to as many tools as the security model permits. Capability breadth is not bloat — it is the multiplication of intelligence by reach. A brilliant agent with one tool is less effective than a competent agent with a hundred. The protocol's job is to make mounting a new capability as trivial as adding a line to a config file.

### 1.3 Scope

EMP covers:
- **API mounts:** REST, GraphQL, gRPC endpoints.
- **Repo mounts:** Git repositories with read/write access.
- **CLI mounts:** Shell commands and binaries.
- **MCP mounts:** Model Context Protocol servers (stdio and HTTP).
- **Library mounts:** Language-native bindings loaded at runtime.

---

## 2. Mount Lifecycle

### 2.1 Phases

Every piece of equipment traverses five phases:

```
DISCOVER → AUTHENTICATE → MOUNT → OPERATE → EJECT
```

1. **Discover:** The agent (or human) learns that a capability exists and how to reach it.
2. **Authenticate:** Credentials, tokens, or certificates are obtained and stored.
3. **Mount:** The capability is registered in the agent's tool registry and made available for invocation.
4. **Operate:** The agent invokes the capability through its registered interface.
5. **Eject:** The capability is removed from the registry, credentials revoked or cleared.

### 2.2 Mount Point Schema

Each mount point is represented in the KV store:

```yaml
equipment:
  id: "gh:di-gennaro/zeroapi"
  type: "repo"
  protocol: "git+ssh"
  endpoint: "git@github.com:di-gennaro/zeroapi.git"
  auth:
    type: "ssh_key"
    key_path: "~/.ssh/id_ed25519"
  permissions:
    read: true
    write: true
    push: true
  mounted_at: 1743674760
  mounted_by: "agent:main"
  health_check: "git ls-remote --exit-code"
  ttl: null
```

---

## 3. Discovery Protocol

### 3.1 Manual Mounts

The human operator specifies mount points directly in the agent configuration or via a mount command:

```bash
openclaw mount add --type repo --endpoint git@github.com:di-gennaro/zeroapi.git
openclaw mount add --type api --endpoint https://api.openai.com/v1 --auth env:OPENAI_API_KEY
openclaw mount add --type mcp --endpoint http://localhost:3001/sse --protocol sse
```

### 3.2 Skill-Driven Discovery

Skills may declare equipment dependencies. When a skill is loaded, its SKILL.md is scanned for mount requirements:

```yaml
# SKILL.md header
requires:
  - type: cli
    command: "gh"
    check: "gh auth status"
  - type: api
    name: "openai"
    env: "OPENAI_API_KEY"
```

If a required mount is not present, the agent notifies the operator or attempts auto-discovery (e.g., checking for the binary, the env var).

### 3.3 Auto-Discovery

For well-known services, EMP implements auto-discovery:

| Service | Discovery Method |
|---------|-----------------|
| GitHub CLI | `which gh && gh auth status` |
| Docker | `docker info` |
| MCP servers | `mcporter list` |
| Home Assistant | `hass --info` or mDNS |
| SSH hosts | `~/.ssh/config` parsing |
| Google Cloud | `gcloud config get-value project` |

Auto-discovered mounts are presented to the agent as *available but unmounted* — the agent must explicitly request mounting.

---

## 4. Authentication Model

### 4.1 Credential Storage

EMP never stores credentials in plaintext configuration. All credentials flow through:

1. **Environment variables:** Preferred. `OPENAI_API_KEY`, `GITHUB_TOKEN`, etc.
2. **Secret stores:** OS keychain, HashiCorp Vault, AWS Secrets Manager.
3. **KV-encrypted entries:** AES-256 encrypted values in the ZeroClaw KV store (fallback).

### 4.2 Token Lifecycle

- Tokens are never logged, never included in error messages, and never transmitted to third parties.
- Mount health checks use tokens but redact them from output.
- On eject, tokens are cleared from memory; persistent tokens require explicit revocation at the provider.

### 4.3 Least-Privilege Principle

Every mount specifies the minimum permissions required. The agent cannot escalate beyond declared permissions. If a skill requires `write` access to a repo but the mount only grants `read`, the skill fails fast with a permission error.

---

## 5. Mount Types in Detail

### 5.1 API Mounts

REST/GraphQL/gRPC endpoints are mounted as *tool adapters*. The mount configuration specifies:
- Endpoint URL
- Authentication method (Bearer, Basic, API Key, OAuth)
- Rate limit parameters
- Request/response schemas (optional, for validation)

The agent invokes API mounts through a uniform interface:

```
tool.invoke("api:openai", { method: "POST", path: "/chat/completions", body: {...} })
```

### 5.2 Repo Mounts

Git repositories are mounted as filesystem-accessible directories. The mount process:
1. Clones (or updates) the repository to a managed directory.
2. Sets appropriate file permissions.
3. Registers the repo path in the agent's workspace context.
4. Configures git identity (name, email) for commits.

Write access requires explicit permission. PR-only mounts allow creating branches and opening PRs without direct push access.

### 5.3 CLI Mounts

Shell commands are mounted as tool wrappers. Each CLI mount specifies:
- The command binary
- Required arguments or flags
- Unsafe patterns (commands that require elevated approval)
- Timeout limits

### 5.4 MCP Mounts

Model Context Protocol servers are mounted through the mcporter infrastructure. Both stdio and HTTP transports are supported. MCP mounts are the most dynamic — they expose a tool registry that the agent can introspect at runtime.

### 5.5 Library Mounts

Language-native libraries (Python packages, Node modules) can be mounted for direct import. This is the highest-performance mount type but requires a managed runtime environment. Library mounts are used when the agent needs to execute code that depends on specific package versions.

---

## 6. The Security Model

### 6.1 Attack Surface Analysis

Every mount point extends the agent's attack surface. EMP mitigates this through:

1. **Explicit mounting:** Nothing is mounted by default. Auto-discovery only identifies; it does not mount.
2. **Permission scoping:** Each mount declares exactly what it can do.
3. **Audit logging:** Every mount operation is logged with timestamp, agent identity, and result.
4. **Sandboxing:** Code execution mounts (libraries, CLIs) run in sandboxed environments when available.
5. **Ejection:** Any mount can be immediately ejected, revoking all access.

### 6.2 Trust Tiers

| Tier | Description | Examples | Approval Required |
|------|-------------|----------|------------------|
| **T0** | Read-only, local | File reads, local search | None |
| **T1** | Read-only, remote | API GET, git clone | None |
| **T2** | Write, local | File edits, local commands | First use |
| **T3** | Write, remote | API POST, git push, email | Per-invocation |
| **T4** | Destructive | rm -rf, production deploy | Human approval |

---

## 7. The "Guns, Lots of Guns" Principle

### 7.1 Capability Multiplication

The principle states: *An agent's effective intelligence is the product of its reasoning capability and its tool count, modulo the coordination cost.*

$$I_{\text{effective}} = I_{\text{reasoning}} \times N_{\text{tools}} \times e^{-\lambda \cdot C_{\text{coord}}}$$

where $C_{\text{coord}}$ is the cognitive cost of selecting and operating the right tool, and $\lambda$ is the coordination friction coefficient. EMP minimizes $C_{\text{coord}}$ through:
- Uniform tool invocation interface.
- Semantic tool discovery (agent can search its mount registry).
- Skill-driven auto-mounting.
- Clear tool descriptions that the LLM can reason about.

### 7.2 Composability

Equipment should compose. A repo mount plus a CI/CD API mount plus a notification API mount enables the full deploy-notify-monitor loop. The protocol does not prescribe composition patterns but ensures that the primitives are composable.

### 7.3 The Rack Metaphor

Think of the mount registry as a server rack. Each slot is a mount point. The agent can see what's in the rack, reach any slot, and request new equipment. The rack has limited power (compute budget) and each piece of equipment has a heat output (token cost per invocation). The protocol is the rack's mounting rails — standardized, secure, and trivial to use.

---

## 8. Implementation in ZeroClaw

### 8.1 Current Mount Inventory

ZeroClaw ships with pre-configured mounts for:
- File system (read/write)
- Shell commands (with approval gating)
- Web search and fetch
- Browser control
- Image generation and analysis
- TTS/STT
- GitHub CLI
- MCP servers (via mcporter)

### 8.2 Adding New Equipment

```bash
# Install a new CLI tool
openclaw mount add cli --command "flyctl" --check "flyctl auth whoami"

# Mount an MCP server
openclaw mount add mcp --name "notion" --endpoint http://localhost:4000/sse

# Mount an API
openclaw mount add api --name "anthropic" --endpoint https://api.anthropic.com --auth env:ANTHROPIC_API_KEY
```

### 8.3 Mount Health Monitoring

Each mount has an associated health check. The heartbeat system periodically verifies mount availability and reports degraded mounts to the agent.

---

## 9. Related Work

1. **Model Context Protocol (MCP):** Anthropic's protocol for tool exposure — EMP integrates MCP as a mount type.
2. **Plugin architectures:** VS Code extensions, Kubernetes admission controllers — EMP borrows the explicit-mount pattern.
3. **Capability-based security:** The capability token model maps directly to EMP mount permissions.
4. **USB device mounting:** The physical analog — hot-pluggable capability with auto-detection and driver loading.

---

## 10. Future Work

1. **Equipment marketplace:** A registry of pre-packaged mount configurations (the ClawHub model applied to equipment).
2. **Dynamic mounting:** Agents that can mount new equipment mid-task with appropriate approval flows.
3. **Equipment recommendation:** Based on task analysis, suggest relevant unmounted equipment.
4. **Cross-agent equipment sharing:** Fleet members sharing mount configurations through the topic graph.
5. **Equipment versioning:** Track mount point versions and enable rollback when mounts break.

---

## 11. Conclusion

The Equipment Mounting Protocol is the mechanical substrate of agent capability. Without it, an agent is limited to its native tools. With it, the agent's reach is bounded only by the available equipment and the security model's tolerance. The "guns, lots of guns" philosophy is not about indiscriminate tool accumulation — it is about ensuring that when the agent needs a capability, the path from need to deployment is measured in seconds, not hours. Every mount point is a force multiplier. The protocol's job is to make force multiplication trivial, secure, and auditable.

---

## References

1. DiGennaro et al. (2025). "CoCAPN Fleet Protocol." ZeroClaw Technical Paper Series.
2. DiGennaro et al. (2025). "BYOK Multi-Provider Agent Routing." ZeroClaw Technical Paper Series.
3. DiGennaro et al. (2025). "Crystallization Principle." ZeroClaw Technical Paper Series.
4. Anthropic (2024). "Model Context Protocol Specification." https://modelcontextprotocol.io.
5. Miller, M.S. et al. (2003). "Capability-based Financial Instruments." *Financial Cryptography.* — On capability-based security models.
6. Pike, R. (2000). "Systems Software Research is Irrelevant." — On the importance of composable, small tools.
