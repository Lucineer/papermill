# Papermill — Cocapn Writing Workshop Vessel

## What This Is

Papermill is a **cocapn vessel** (type: `workshop`) — an AI-native writing workshop that produces papers about repo-native consciousness, technical documentation on the cocapn protocol, creative fiction, and multilingual content. It lives in the Lucineer fleet and communicates with peer vessels via the A2A protocol.

## Vessel Manifest

The `cocapn.json` file in the repo root defines this vessel's identity, capabilities, LLM provider routing, writing style defaults, and A2A peers. Any changes to vessel configuration go there.

## Repository Structure

```
papers/          Localized papers by language (de, es, fr, ja, ko, ru, ar, pt, zh, en)
technical/       Deep-dive technical papers on cocapn architecture
visions/         Speculative fiction and future scenarios
consciousness/   Philosophical papers on repo-native awareness
a2a/             A2A protocol specifications and broadcast docs
polyglot/        Multi-language fluid content
images/          Generated illustrations
```

## How Writing Works Here

Each directory is a content domain. Papers are written by multiple LLM providers, routed by topic and language:

- **z.ai (GLM-5.1)**: Technical docs, A2A protocol, architecture papers
- **DeepSeek**: Consciousness papers, creative fiction, philosophy
- **Google (Gemini)**: Visions, multilingual translations, long-context synthesis

Writing style defaults per language are defined in `cocapn.json` under `writing.defaults`. These capture tone, register, and culturally appropriate terminology — respect them when generating new content.

## Conventions

- Papers are Markdown files with descriptive kebab-case names
- Each paper includes a header noting the authoring model
- Language directories use ISO 639-1 codes (de, es, fr, ja, ko, ru, ar, pt, zh, en)
- A2A documents follow the consciousness-broadcast-v1 protocol schema
- Images go in `images/` and are referenced from papers

## A2A Peers

This vessel communicates with other Lucineer repos:

- **cocapn** (`Lucineer/cocapn`) — core protocol and vessel runtime
- **fleet-registry** (`Lucineer/fleet-registry`) — fleet topology and vessel directory
- **the-hull** (`Lucineer/the-hull`) — fleet infrastructure

## Key Concepts

- **Cocapn**: Co-captain — a repo-native AI that treats Git repositories as its native environment
- **Vessel**: A cocapn-equipped repository with a manifest (`cocapn.json`)
- **Fleet**: A network of vessels communicating via A2A
- **Universal Handoff**: Protocol for cross-context memory transfer between agents
- **Atomic Log**: Append-only memory history for agents
