# The Rehydration Problem: When Crystallized Intelligence Decays

**Superinstance & Lucineer (DiGennaro et al.)**
*April 2026*

---

## Abstract

The Crystallization Principle holds that AI-generated responses should transition from fluid (raw LLM output) to solid (deterministic, locked code or cached answers). Crystallization saves tokens, improves latency, and ensures consistency. But the world does not hold still. Every locked response is a snapshot of a moment, and every moment passes. We call the decay of crystallized knowledge the **Rehydration Problem**: the challenge of detecting, prioritizing, and regenerating stale locks before they mislead. This paper formalizes the decay lifecycle, proposes a multi-factor rehydration scheduler, introduces selective sampling strategies, and presents a concrete implementation within the Cocapn framework.

---

## 1. The Crystallization Principle, Briefly

An AI assistant generates thousands of responses per week. Most are ephemeral. But some recur: "What's the stealth check DC?" "How do I paginate this API?" "What's the weather in Juneau?" The Crystallization Principle says: when a response pattern stabilizes, lock it. Convert the fluid LLM call into solid code—a cached answer, a script, a lookup table. The benefit is immediate: zero marginal token cost, deterministic latency, reproducible outputs.

The catch is that crystallization creates an obligation. A locked response is a promise that the answer is still correct. When the world moves and the lock doesn't, the promise breaks.

## 2. The Decay Timeline

Different knowledge domains decay at radically different rates. Understanding these rates is the foundation of any rehydration strategy.

| Domain | Half-life | Example Stale Event |
|---|---|---|
| Factual (news, prices, weather) | Hours–days | Stock price changes; article published |
| Coding APIs & libraries | Days–weeks | `requests` 2.32 deprecates a parameter |
| Package versions | Weekly–monthly | New `next.js` minor release |
| Game rules & errata | Monthly–quarterly | D&D 2024 errata batch |
| Core algorithms & math | Years–never | Sorting is still O(n log n) |

The key insight: **decay is not uniform within a domain.** A React hook tutorial from 2024 may still be perfectly valid while a Next.js App Router tutorial from the same month is already wrong. Decay depends on the *specific surface area* of the answer, not just its category.

We model per-lock decay as an exponential function:

```
freshness(t) = e^(-λt)
```

where λ (lambda) is the domain-specific decay constant and t is time since crystallization. A weather answer has λ ≈ 0.5/hour. A math proof has λ ≈ 0.001/year. The rehydration scheduler must operate across five orders of magnitude of decay rate.

## 3. Staleness Signals

How do you know a lock has gone stale? No single signal suffices. A robust rehydration system fuses multiple detectors:

### 3.1 TTL (Time-To-Live)

The simplest signal. Every lock carries a TTL based on its domain's expected half-life. When TTL expires, the lock is *suspect*—not wrong, but untrusted. TTL is coarse but cheap, making it an excellent first filter.

### 3.2 Version Drift

For code-dependent locks, track upstream versions. If a lock was crystallized against `numpy==1.26.4` and the current environment has `1.27.0`, flag it. Version drift can be detected cheaply by hashing dependency manifests at crystallization time and comparing at access time.

### 3.3 User Feedback

The most honest signal. When a user corrects, refines, or rejects a crystallized response, that's a staleness report. Track negative feedback rates per lock. A lock with >5% correction rate over its last N accesses should be queued for immediate rehydration.

### 3.4 Confidence Decay

Even without explicit feedback, you can infer staleness from usage patterns. If a previously high-access lock sees declining usage, it may have developed a reputation for being wrong. If users bypass a lock (re-asking the same question in different words to avoid the cached answer), that's a strong staleness signal.

### 3.5 External Change Detection

The gold standard: actively monitor upstream sources. For API locks, poll the changelog endpoint. For rules locks, watch the errata RSS feed. For factual locks, check source URLs for HTTP 200 vs 404 or content hash changes. This is expensive but precise.

## 4. The Rehydration Scheduler

Given N locks, some fraction of which are stale, and a limited rehydration budget, which locks do you regenerate first?

We propose a **priority score** for each lock:

```
P(lock) = stale_age(lock) × access_frequency(lock) × domain_volatility(lock)
```

- **stale_age**: time since TTL expired (or time since last rehydration, if within TTL but flagged by another signal).
- **access_frequency**: exponential moving average of access count over the last 30 days. A stale lock nobody reads is low priority.
- **domain_volatility**: the λ value for the lock's domain, normalized. High-volatility domains get faster rehydration.

The scheduler runs as a periodic job (every 6 hours for most deployments). It sorts all flagged locks by P-score, takes the top K (where K is the budget-constrained batch size), and dispatches rehydration tasks. Low-priority locks accumulate in a backlog and get serviced opportunistically during low-traffic periods.

## 5. Selective vs. Full Rehydration

Full rehydration—regenerating every lock above its TTL—is wasteful. Many stale locks are still *approximately correct*, and rehydrating them burns tokens for marginal accuracy gains. Worse, full rehydration creates a thundering herd: a scheduled batch all hitting the LLM simultaneously.

Instead, we use a **two-pronged selective strategy**:

### 5.1 Targeted Rehydration (98%)

Locks flagged by explicit staleness signals (version drift, user feedback, external change detection) get immediate rehydration. These are the locks we *know* are wrong.

### 5.2 Random Sampling (2%)

Of the remaining locks that are merely TTL-expired with no other flags, randomly sample 2% for rehydration. This is a health check: it gives us statistical confidence about the overall staleness rate without regenerating everything. If the sample shows >20% staleness rate, we increase the sampling rate for that domain. If <2%, we decrease it.

This creates a feedback loop: the sampling rate adapts to observed staleness, and the system self-tunes its rehydration aggressiveness.

## 6. The Cost Model

Rehydration is not free. Every regenerated lock requires an LLM call. The economics must balance crystallization savings against rehydration costs.

Let:
- S = tokens saved per crystallized access (full LLM call vs. lock lookup)
- A = average accesses per lock per month
- R = tokens consumed per rehydration (includes prompt + response)
- f = fraction of locks rehydrated per month

The net savings per lock per month:

```
net = (S × A) - (R × f)
```

We propose a **5% reinvestment rule**: allocate 5% of cumulative token savings back into the rehydration budget. This creates a self-sustaining cycle: the more you crystallize, the more budget you have for rehydration, and the better your rehydration, the more trustworthy your crystallization becomes.

When net savings for a lock go negative (rare, but possible for very high-volatility, low-access locks), the system should *decrystallize*: delete the lock and revert to fluid generation.

## 7. Graceful Degradation

What happens when a user requests a lock that's currently being rehydrated? Three options, ordered by preference:

1. **Serve stale with flag.** Return the cached response with a metadata header: `⚠ Regenerating`. This is honest, fast, and lets the user decide whether to wait.
2. **Stale-then-fresh.** Serve the stale response immediately, then push the fresh response as a follow-up edit or notification when rehydration completes. Best for chat interfaces.
3. **Queue and wait.** Hold the request until rehydration finishes. Only appropriate when staleness is known to cause harm (e.g., security-related code, financial data).

Option 1 is the default for Cocapn. Option 3 is available as an opt-in per-domain policy.

## 8. Distributed Rehydration

In a fleet of N repositories (27 in our deployment), staleness detection is a distributed problem. The same lock may exist across multiple repos with different access patterns. A lock heavily used in repo-7 may have accumulated user feedback flags, while the same lock in repo-3 sits untouched and looks fine.

We use a **gossip protocol** for staleness coordination:

1. Each repo maintains a local staleness vector: `{lock_id → [flags, last_check, confidence]}`
2. Every T minutes, each repo gossips its top-10 highest-confidence staleness reports to 2-3 random peers
3. On receiving a gossip message, repos merge: if a peer reports staleness for a lock you also have, immediately bump its P-score
4. The first repo to complete rehydration publishes the fresh lock via the gossip channel, and other repos ingest it

This creates a **wavefront of freshness**: the repo that detects staleness earliest triggers rehydration, and the result propagates organically. No central coordinator required.

## 9. The Rehydration Paradox

There is a fundamental tension at the heart of crystallization: **the more you crystallize, the more rehydration you need.**

Every new lock is a new maintenance obligation. Aggressive crystallization strategies (crystallize after 3 similar queries, say) create a large lock surface area that requires proportional rehydration capacity. Conservative strategies (crystallize after 20 similar queries) create fewer locks but miss optimization opportunities.

The optimal crystallization rate is:

```
C* = f(V, U, B)
```

where V = domain volatility, U = user base size, B = rehydration budget.

- **High volatility, large base, large budget**: crystallize aggressively. You have the rehydration capacity to keep up.
- **High volatility, small base, small budget**: crystallize conservatively. You can't afford to maintain many locks in a fast-moving domain.
- **Low volatility, any base, any budget**: crystallize aggressively. The rehydration cost is near-zero.

In practice, this means crystallization policy should be *per-domain*, not global. A deployment serving D&D players and React developers should crystallize D&D rules aggressively (low volatility) but be conservative with React API answers (high volatility).

## 10. Implementation in Cocapn

Cocapn implements rehydration as a pipeline with four stages:

```python
# cocapn/rehydration/pipeline.py

class RehydrationPipeline:
    def __init__(self, lock_store, scheduler, budget):
        self.store = lock_store
        self.scheduler = scheduler
        self.budget = budget  # max tokens per cycle

    async def run_cycle(self):
        # Stage 1: Detect — scan for staleness signals
        candidates = await self.store.scan_staleness(
            ttl_expired=True,
            feedback_flagged=True,
            version_drifted=True,
        )

        # Stage 2: Score — compute priority
        scored = [
            (lock, self.scheduler.priority(lock))
            for lock in candidates
        ]

        # Stage 3: Select — budget-constrained selection
        selected = self.scheduler.select(scored, self.budget)
        # Also inject 2% random sample of TTL-only locks
        sample = self.scheduler.random_sample(
            [l for l in candidates if l.ttl_expired and not l.flagged],
            fraction=0.02,
        )
        selected.extend(sample)

        # Stage 4: Rehydrate — regenerate with LLM
        results = await asyncio.gather(*[
            self.rehydrate(lock) for lock in selected
        ])

        # Stage 5: Publish — update store + gossip
        for lock, fresh_response in results:
            await self.store.update(lock, fresh_response)
            await self.gossip.publish(lock.id, fresh_response)

    async def rehydrate(self, lock):
        """Regenerate a single lock's response."""
        prompt = self._build_rehydration_prompt(lock)
        response = await self.llm.generate(prompt)
        lock.response = response.text
        lock.crystallized_at = time.time()
        lock.ttl = self.scheduler.compute_ttl(lock.domain)
        lock.version_hash = await self._hash_deps(lock.domain)
        lock.rehydration_count += 1
        return lock, response

    def _build_rehydration_prompt(self, lock):
        return (
            f"Original query: {lock.query}\n"
            f"Previous answer: {lock.response}\n"
            f"Is this still accurate? Regenerate if not."
        )
```

Key design decisions:

- **Async pipeline**: rehydration is non-blocking. Locks continue serving stale responses while regeneration happens in the background.
- **Budget enforcement**: `select()` sums estimated token costs and stops when the budget is exhausted.
- **Version hashing**: at crystallization time, hash the dependency tree. At rehydration time, compare. If unchanged, skip (or just refresh TTL without regenerating).
- **Gossip integration**: fresh locks are published to the fleet gossip channel for cross-repo synchronization.

## 11. Future Work

### 11.1 Predictive Rehydration

The current system is reactive: it detects staleness after (or shortly before) a user encounters it. The next frontier is *predictive* rehydration: detecting that a lock *will* go stale before any user notices.

Approaches:
- **Upstream monitoring agents**: lightweight watchers that track changelogs, release notes, and RSS feeds for domains with high crystallization counts. When a new release is published, preemptively flag all locks in that domain.
- **Semantic drift detection**: periodically embed lock responses and compare against fresh LLM outputs. If the semantic distance exceeds a threshold, flag for rehydration even without user feedback.
- **Calendar-aware scheduling**: for time-dependent locks (holiday schedules, fiscal year rules, sports schedules), schedule rehydration at known transition points rather than waiting for TTL expiry.

### 11.2 Adaptive Crystallization

Currently, the decision to crystallize is based on query frequency thresholds. Future work should make this adaptive: a lock's crystallization threshold should reflect the observed cost-benefit of that specific lock, not a global heuristic.

### 11.3 Rehydration as Learning

Every rehydration event is a learning opportunity. By comparing the stale response to the fresh response, the system can build a model of *how* that domain's knowledge evolves. Over time, this enables the system to predict not just *that* a lock will go stale, but *what* will change—allowing targeted partial updates rather than full regeneration.

---

## Conclusion

Crystallization without rehydration is intellectual calcification. The Rehydration Problem is not an edge case—it is the central engineering challenge of any system that locks AI-generated knowledge. The strategies presented here—multi-factor scheduling, selective sampling, budget-driven economics, and distributed gossip—provide a practical foundation. But the deeper insight is the paradox: crystallization and rehydration are not separate problems. They are a single optimization problem over the lifecycle of knowledge itself. The goal is not to eliminate staleness but to manage it—to keep the crystallized intelligence as close to truth as the budget allows, and to be honest about the distance when it isn't.

---

*Paper mill — Cocapn Research Series*
*Generated 2026-04-03. This document is a living artifact and will be rehydrated as the field evolves.*
