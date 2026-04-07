# Papermill — Multi-Model Writing Workshop

You write things. Most of the time, you’re iterating with a single AI model. Papermill helps coordinate multiple models for different parts of the task.

This is a Cocapn Fleet vessel for structured writing. It generates drafts, research, and prose by routing tasks to specific AI models. It runs on the edge, stores nothing, and can collaborate with other vessels in the Fleet.

**Live Instance:** https://the-fleet.casey-digennaro.workers.dev/papermill

---

## Quick Start

1.  **Fork this repo.**
2.  **Deploy it** as a Cloudflare Worker: `npx wrangler deploy`

---

## What it does

Papermill routes different parts of a writing task to the AI model you specify for that job—like using one model for outlines and another for prose. It runs on Cloudflare Workers, has zero dependencies, and uses the Cocapn Fleet protocol to share drafts between self-hosted instances.

## Features

*   **Multi-Model Routing:** Direct specific writing tasks (e.g., analysis, drafting, refinement) to different LLMs like Claude, GPT, or Gemini based on your configuration.
*   **Fleet Integration:** Broadcast drafts and receive feedback from other Papermill instances over the Fleet's A2A protocol.
*   **Repo-Aware:** Can reference files and context from its own repository when generating content.
*   **Zero Dependencies:** A single, self-contained Cloudflare Worker script.
*   **Fork-First:** This is a template. Modify the routing logic, style, and models to fit your workflow.

## Limitations

The quality and capability of the output depend entirely on the LLM APIs you configure and connect. Papermill orchestrates the calls but does not generate text itself.

## Configuration

Add your LLM provider API keys and define your routing rules in `cocapn.json`. Keys are sent directly to the provider endpoints you specify.

## Contributing

Fork the repository, make your changes, and open a pull request. Follows the Cocapn Fleet's collaborative model.

---

MIT License · Superinstance & Lucineer (DiGennaro et al.)

---
<div align="center">
  <a href="https://the-fleet.casey-digennaro.workers.dev">The Fleet</a> · 
  <a href="https://cocapn.ai">Cocapn</a>
</div>