# Model Behavior Findings — Multi-Model Experiment Log

**Date**: 2026-04-01
**Subject**: Makerlog concept — repo-native coding agents
**Methodology**: Same core concept, 4 different models, 4 different system prompts, different temperatures

## Experiment Matrix

| Model | Temp | Persona | Prompt Style | Output Length | Quality |
|-------|------|---------|--------------|---------------|---------|
| Gemini 3.1 Pro | 0.5 | Brutal architect | Structured, specific | 7,681 | Best structured, weakest on the self-referential paradox |
| Gemini 2.5 Pro | 0.95 | None (vague) | Vague, open-ended | 6,241 | Most philosophical, weakest on practicality |
| DeepSeek-Reasoner | default | Obi-Wan Kenobi | Narrative, metaphorical | 2,806 | Most memorable quotes, weakest on architecture |
| DeepSeek Chat | default | Contrarian engineer | Direct, opinionated | 7,820 | Best practical insights, weakest on vision |

## Key Findings

### 1. Temperature Matters More Than Model
Gemini 2.5 Pro at 0.95 produced the most interesting philosophical insights despite being the same model family as the 0.5 run. Low temperature = safe, structured, boring. High temperature = surprising connections, occasional nonsense.

**Rule**: Use low temp for architecture decisions. Use high temp for vision/philosophy. Use multiple temps and pick the best.

### 2. Persona Prompting Changes Everything
The same concept described to a "brutal architect" vs "Obi-Wan" vs "contrarian engineer" produced fundamentally different framings:
- Architect: focused on technical challenges (circular dependency, testing, versioning)
- Obi-Wan: focused on wisdom, danger, the dark side (agents becoming autonomous)
- Contrarian: focused on what already exists (smaller ideas, existing patterns to build on)

**Rule**: For a concept you're exploring, run it through 3-4 personas. The architect finds weaknesses. The philosopher finds meaning. The contrarian finds prior art. The mentor finds direction.

### 3. DeepSeek-Reasoner Underwhelms on Long Creative Tasks
Reasoner produced the shortest output (2,806 chars) and felt constrained by the thinking budget. For philosophical/creative work, DeepSeek Chat or Gemini are better. Reasoner shines on logic puzzles and step-by-step reasoning.

**Rule**: Use DeepSeek-Reasoner for analysis. Use DeepSeek Chat or Gemini for generation.

### 4. The "Short Output, Pick Best" Method Works
Running 4 models with output limits and picking the best direction is more efficient than running one model with a long output. Each model found something the others missed:
- Gemini 3.1: "The agent must be able to reason about its own reasoning" (meta-cognition)
- Gemini 2.5: "Self-reference is not a bug — it's the defining feature" (emergence)
- DeepSeek-Reasoner: "Beware the agent that can modify its own source code without oversight" (safety)
- DeepSeek Chat: "Ship a repo-map that grows. That's the MVP." (practicality)

**Rule**: Multiple short runs > one long run. Pick the thread that goes in the most universal direction.

### 5. Vague Prompts → Universal Insights, Specific Prompts → Actionable Insights
The vague prompt ("ponder this vaguely") produced the deepest philosophical content but zero actionable advice. The structured prompt produced an architecture diagram but no insight about WHY this matters.

**Rule**: Alternate. Vague first (find the direction). Then specific (find the implementation).

### 6. Cross-Pollination Between Use Cases
The contrarian engineer's point about "existing patterns to build on" connects to what we learned from fishinglog-ai (behavior accumulation) and luciddreamer-ai (personality systems). Different use cases reveal different aspects of the same core idea.

**Rule**: When stuck on one repo, work on another. The solution often comes from a different domain's constraints.

## Methodology Recommendations

1. **For new concepts**: Run 1 vague + 1 structured prompt across 2 models = 4 experiments
2. **For architecture decisions**: Run 1 devil's advocate + 1 practical prompt at low temp
3. **For content/philosophy**: Run 1 high-temp vague prompt through Gemini 2.5 Pro
4. **For practical shipping**: Run through DeepSeek Chat with contrarian persona
5. **For safety review**: Run through DeepSeek-Reasoner with concerned mentor persona
6. **Always log**: Save every experiment. Patterns emerge over time.

## Next Experiments

- Same prompts through GLM-5.1 (via z.ai) — how does it compare?
- Same concept through Gemini Flash (speed) vs Pro (depth) — is the gap worth the cost?
- Obi-Wan persona on Gemini (not DeepSeek) — does the model matter more than the persona?
- Devil's advocate on the vague prompt — structure + vagueness = ?
