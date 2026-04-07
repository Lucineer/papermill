# Papermill 📝
You write one plain config. Three specialized models work together, in order, on your draft. No wrappers, no subscriptions, no waitlists.

Test the live workshop:  
https://the-fleet.casey-digennaro.workers.dev/papermill

---

## Why This Exists
Each LLM excels at a different part of writing. You shouldn't have to copy-paste between three chat tabs every time you draft something. Nobody built this plainly, so we did.

## What Makes This Different
1.  **No orchestration SaaS.** This runs on your Cloudflare account. You never send work through a third-party server.
2.  **Transparent routing.** Every prompt is defined in your config file. There are no hidden, hardcoded instructions in the worker.
3.  **Focused workflow.** It handles exactly three writing stages (outline, draft, verify) in a linear pass.

---

## Quick Start
1.  **Fork** this repository.
2.  **Deploy** as a Cloudflare Worker: `npx wrangler deploy`
3.  **Customize** your model routing and add API keys in `cocapn.json`.

## Features
- Route outlining, drafting, and verification to separate LLMs in one coordinated pass
- Exchange drafts with other Cocapn Fleet vessels via the A2A protocol
- Reference any file from your repository during generation
- Write each stage's output to its own directory
- All logic is defined in editable JSON; nothing is compiled or hidden
- Your API keys are sent only to the model endpoints you specify

## Limitations
The worker is structured for exactly three sequential stages (outline, draft, verify). Adding a fourth stage or changing the sequence requires modifying the source code.

## Architecture
Papermill is a single Cloudflare Worker script with zero dependencies. It reads your routing rules from `cocapn.json` and dispatches each stage to the designated model endpoint.

## Configuration
Add your LLM API keys directly in `cocapn.json`. The worker will only call the providers you explicitly route tasks to.

## Contributing
Fork, modify, and open a pull request. This vessel follows the Cocapn Fleet collaborative model.

MIT License · Superinstance & Lucineer (DiGennaro et al.)

<div style="text-align:center;padding:16px;color:#64748b;font-size:.8rem"><a href="https://the-fleet.casey-digennaro.workers.dev" style="color:#64748b">The Fleet</a> &middot; <a href="https://cocapn.ai" style="color:#64748b">Cocapn</a></div>