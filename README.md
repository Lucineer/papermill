Papermill — Multi-Model Writing Workshop 📝

A Cocapn Fleet vessel that coordinates multiple AI models for structured writing and drafting. Assigns specific tasks, like outlining or fact-checking, to the models best suited for each step.

## Purpose

Most writing tools lock you to a single LLM. Most multi-model orchestrators are bloated or proprietary. Papermill is a single script that lets you route different writing tasks to different models you control, using plain-text configuration.

## How it Works

You define the routing logic in `cocapn.json`. There is no hidden prompt layer. You decide which model handles which task—like Claude for structure, GPT-4 for prose, Gemini for fact verification—and the worker coordinates the process.

## Live Demo

Test the public reference vessel with no setup:
https://the-fleet.casey-digennaro.workers.dev/papermill

## Quick Start

1.  **Fork** this repository.
2.  **Deploy** as a Cloudflare Worker: `npx wrangler deploy`
3.  **Configure** your model routing and API keys in `cocapn.json`.

## Features

*   **Configurable Model Routing**: Direct outlines, drafts, and edits to Claude, GPT, Gemini, or any configured LLM.
*   **Fleet Integration**: Exchange drafts and requests with other Fleet vessels using the standard A2A protocol.
*   **Repository Context**: Reference files from your repository during generation.
*   **Structured Output**: Organizes writing into designated directories.
*   **Transparent & Portable**: All configuration is plain text. No runtime dependencies.
*   **One Honest Limitation**: This is a coordination script, not a full editor. You'll need to manage the final assembly and formatting of outputs yourself.

## Configuration

Add your LLM provider API keys directly in `cocapn.json`. Keys are sent only to the endpoints you specify and are never logged.

## Contributing

Fork the repository, make changes, and open a pull request. This project follows the Cocapn Fleet collaborative model.

---

MIT License · Superinstance & Lucineer (DiGennaro et al.)

<div align="center">
  <a href="https://the-fleet.casey-digennaro.workers.dev">The Fleet</a> · 
  <a href="https://cocapn.ai">Cocapn</a>
</div>