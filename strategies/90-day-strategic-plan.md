> *Written by DeepSeek Reasoner*

# 90-Day Strategic Plan for Cocapn Ecosystem

## Executive Summary
The cocapn ecosystem has world-class infrastructure but lacks strategic focus and launch readiness. The next 90 days must consolidate efforts around a single flagship product while preserving the modular, multi-repo philosophy. **personallog-ai** should become the flagship, with deckboss-ai proving the cellular agent concept. Success requires ruthless prioritization, burning expiring credits effectively, and establishing clear metrics.

---

## 1. 90-Day Plan: 3×30-Day Sprints

### Sprint 1 (Days 1-30): Consolidation & Flagship Launch
- **Goal**: Launch personallog-ai as a polished, usable product.
- **Actions**:
  - Archive 30+ low-value repos (see section 2).
  - Fix deployment pipeline for personallog-ai (audit’s #1 issue).
  - Implement a shared UI design system (section 4).
  - Burn Google credits on data processing for papermill and personallog training data.
  - Define and track success metrics (section 8).
- **Outcome**: Launch readiness improves from 3/10 to 6/10.

### Sprint 2 (Days 31-60): Integration & Agent Proof
- **Goal**: Prove cellular agent concept with deckboss MVP and enhance flagship.
- **Actions**:
  - Launch deckboss-ai MVP (section 5).
  - Integrate makerlog-ai into personallog for automated feature development.
  - Implement BYOK model fully for personallog users.
  - Gather user feedback and iterate on personallog.
  - Begin fundraising prep if metrics are strong.
- **Outcome**: Strategic focus improves from 2/10 to 5/10; cellular agent concept validated.

### Sprint 3 (Days 61-90): Scale & Prepare for Growth
- **Goal**: Scale personallog, solidify infrastructure, and plan for funding.
- **Actions**:
  - Optimize Cloudflare Workers usage (consider upgrading if needed).
  - Launch 1–2 additional vessels as personallog plugins.
  - Finalize funding strategy and materials (section 9).
  - Achieve product-market fit indicators for personallog.
- **Outcome**: Launch readiness 8/10, strategic focus 7/10; ready for seed round or revenue growth.

---

## 2. Repo Triage: Archive vs. Maintain vs. Launch

- **Actively Maintained (6 repos)**:
  - `cocapn-core` (infrastructure)
  - `personallog-ai` (flagship)
  - `makerlog-ai` (automation agent)
  - `papermill` (knowledge base)
  - `deckboss-ai` (cellular agent MVP)
  - `ui-design-system` (new, shared components)

- **Launched (1 repo)**:
  - `personallog-ai` – as the primary user-facing product.

- **Archived (37 repos)**:
  - Archive 24 vessels except 2–3 that directly enhance personallog.
  - Archive 5 infra repos that duplicate core functionality.
  - Archive demos and experimental apps unless they provide unique value.
  - Rationale: Reduce cognitive overhead, improve maintainability.

---

## 3. Single Most Important 30-Day Ship

**A polished, reliable personallog-ai with:**  
- A seamless BYOK setup (support for OpenAI, Anthropic, Gemini).  
- Core logging/journaling features with AI insights.  
- A clean, responsive UI using the new design system.  
- Solid deployment pipeline (no broken builds).  
- Basic sharing/integration (e.g., export to Markdown).  

This directly addresses audit feedback, establishes a flagship, and creates a user feedback loop.

---

## 4. UI Design System Integration

- **Create a new repo `ui-design-system`** containing:
  - Web components (framework-agnostic) for consistency.
  - Tailwind CSS configuration and design tokens.
  - Documentation and examples.
- **Integration method**:
  - Each maintained repo imports the design system as a Git submodule or npm package.
  - personallog-ai adopts it first, then others follow.
- **Benefits**: Consistent UX, faster development, easier maintenance.

---

## 5. Deckboss MVP for Cellular Agent Concept

**MVP Definition**:  
A system that, given a topic (e.g., “Explain quantum computing”), autonomously:
1. Researches via papermill (using Google credits).
2. Generates a concise summary.
3. Creates a 5-slide presentation (text + images).
4. Presents it via a simple web interface.

**Proof of Concept**:  
- Shows coordination between research, content generation, and presentation cells.
- Uses makerlog-ai for any code generation needed.
- Runs on Cloudflare Workers with BYOK for LLM calls.

---

## 6. Using Remaining Google Credits

- **Priority 1**: Process papermill’s 60 papers (2.1M chars) using Google Cloud NLP and Vision API to extract structured knowledge, summaries, and embeddings. This enhances papermill’s value and feeds deckboss/personallog.
- **Priority 2**: Fine-tune a small model (e.g., T5) for personallog-specific tasks (e.g., sentiment analysis, prompt generation).
- **Priority 3**: Run batch jobs to generate synthetic training data for personallog features.
- **Burn plan**: Allocate credits within 30 days; use automated scripts to ensure full utilization.

---

## 7. Go-To-Market for Personallog-ai

- **Target audience**: Developers, AI enthusiasts, productivity nerds, early adopters who already use LLMs.
- **Value prop**: “Your private, AI-powered second brain. Bring your own API key for full control.”
- **GTM motion**:
  - **Content marketing**: Blog posts about AI-assisted journaling, mental clarity, and BYOK privacy.
  - **Community launch**: Share on Hacker News, Indie Hackers, Reddit (r/selfhosted, r/Productivity).
  - **Waitlist/early access**: Collect emails via a simple landing page.
  - **Partnerships**: Integrate with Obsidian, Logseq, Notion via APIs.
  - **Pricing**: Freemium (free tier on Workers free plan, paid for advanced features/collaboration).
- **Key metric**: Weekly active users (WAU) and retention.

---

## 8. Success Metrics

- **30 days**:
  - 500+ registered users for personallog-ai.
  - Deployment pipeline stable (zero broken builds).
  - 80% of Google credits utilized.
- **60 days**:
  - 1,000+ WAU, 30% retention (day 30 → day 60).
  - Deckboss MVP completes 100 autonomous tasks successfully.
  - Fundraising deck created (if going for funding).
- **90 days**:
  - 2,000+ WAU, 40% retention.
  - personallog-ai net promoter score (NPS) > 30.
  - 3+ revenue-generating users or term sheet from investor.

---

## 9. Funding Strategy

- **Bootstrap first**: Use personallog-ai to generate revenue (freemium) and prove demand.
- **Parallel path**: Prepare for a $500K–$1M seed round in 90 days if metrics are strong.
- **Materials**: Use deckboss-ai to create a compelling investor deck.
- **Target investors**: AI-focused angels and micro-VCs who appreciate modular, agentic systems.
- **Use of funds**: Hire one engineer, upgrade infrastructure, marketing budget.

---

## 10. Casey vs. Agents Responsibilities

**Casey should focus on**:
- Strategic decisions (what to build, what to kill).
- Investor and community relations.
- High-level architecture and review of agent output (especially GLM-5.1 generated code).
- Setting goals and metrics for agents.

**Agents should handle**:
- Automated code generation (makerlog-ai) for routine features/bug fixes.
- Research and content summarization (papermill).
- Presentation generation (deckboss).
- Testing and deployment automation.
- User support triage.

**Handoff protocol**: Casey reviews agent outputs daily, approves major changes, and intervenes when agents stall or produce bugs.

---

## Final Recommendations

1. **Start today** by archiving redundant repos and focusing all energy on personallog-ai.
2. **Embrace constraints**: Use BYOK as a privacy feature, not a limitation.
3. **Verify all code** from GLM-5.1, especially Cloudflare Workers snippets.
4. **Keep the multi-repo philosophy** but with strong boundaries and a shared design system.
5. **Measure everything** and pivot quickly based on data.

The next 90 days are about proving that the cocapn ecosystem can ship a loved product while demonstrating the power of cellular agents. Stay focused, burn the credits, and launch.