> *DeepSeek Chat*

# The Cocapn API — Three Functions That Power Everything

## Abstract

In an era of API sprawl where platforms boast hundreds of specialized endpoints, Cocapn presents a radical alternative: a complete cognitive interface distilled into three fundamental functions. This paper examines the architectural philosophy, implementation, and implications of an API consisting solely of `chat()`, `remember()`, and `recall()`. We explore how this minimalist design enables maximal functionality, how it contrasts with contemporary API paradigms, and why three functions are not merely sufficient but optimal for building intelligent agents. Through analysis of interface design principles, cognitive architectures, and practical implementation details, we demonstrate how Cocapn achieves what complex APIs cannot: true simplicity that scales.

## 1. The Radical Simplicity

### The Problem of API Proliferation

Modern API design has succumbed to a form of feature creep that mirrors the complexity of the systems they expose. Consider the evolution: early web APIs like Flickr (2004) offered perhaps a dozen methods. Today, platforms like Google Cloud expose thousands of endpoints across hundreds of services. OpenAI's API includes separate endpoints for chat completions, embeddings, fine-tuning, file operations, and moderation—each with its own parameters, authentication schemes, and rate limits. This complexity creates substantial cognitive and implementation overhead for developers.

The conventional wisdom suggests that specialized problems require specialized interfaces. Need vision capabilities? Add a `/vision` endpoint. Need speech recognition? Add a `/speech-to-text` endpoint. Need document analysis? Add a `/document-processing` endpoint. This additive approach results in what we might call "API entropy"—the inevitable increase in complexity as systems evolve to handle edge cases and new capabilities.

### Cocapn's Counter-Philosophy

Cocapn rejects this paradigm entirely. Its foundational insight is that **all interaction with an intelligent agent can be reduced to three cognitive primitives**:

1. **Communication** (`chat(message) → response`)
2. **Storage** (`remember(key, value) → confirmation`)
3. **Retrieval** (`recall(key) → value`)

These three functions form a complete Turing-complete interface to an intelligent system. The radical simplicity isn't a limitation but a deliberate constraint that forces elegance. Like the three primary colors that combine to create every visible hue, these three functions combine to create every possible interaction with an intelligent agent.

### Everything Else is Emergent

The most profound implication of this design is that all other functionality emerges from these primitives. There's no `/weather` endpoint because the agent can interpret "What's the weather in Tokyo?" through the `chat()` function and use internal tools to fetch weather data. There's no `/calculator` endpoint because the agent can process "Calculate 15% of $89.50" through the same universal interface. There's no `/summarize` endpoint because "Summarize this document" works identically to "Hello, how are you?"

This emergence isn't magic—it's architecture. The Cocapn agent contains the full implementation complexity: tool use, reasoning, external API integration, and specialized processing. But this complexity is hidden behind the simplest possible interface. The API becomes a pure abstraction layer, exposing only what the consumer needs to know: how to communicate, store, and retrieve.

This design follows the Unix philosophy applied to AI interfaces: "Write programs that do one thing and do it well." Each of Cocapn's three functions does exactly one thing, but together they compose to handle infinite scenarios.

## 2. Chat — The Universal Interface

### The Power of a Single Interaction Method

`chat(message)` represents perhaps the most radical simplification in API design history. It accepts a string and returns a string. No parameters for temperature, top_p, frequency penalty, or presence penalty. No system prompts or message arrays. No streaming flags or JSON mode parameters. Just: input text, output text.

This simplicity is deceptive. Behind this single endpoint lies sophisticated intent recognition, context management, tool selection, and response generation. The agent determines whether "Show me my expenses from last month" requires database access, whether "What's 15% of 200?" requires calculation, and whether "I'm feeling anxious today" requires emotional support—all through the same interface.

### Natural Language as the Ultimate Abstraction

By using natural language as the sole input mechanism, Cocapn leverages humanity's most sophisticated interface: language itself. Consider the alternatives:

- **Structured APIs** require developers to understand schema, parameters, and endpoints
- **GraphQL** reduces some complexity but requires understanding types and queries
- **RPC-style APIs** require knowing method names and signatures
- **Natural language APIs** require only knowing how to ask

The `chat()` function implements what might be called "intent-based interface design." Rather than mapping user needs to specific API calls, users express needs naturally, and the agent maps them to internal capabilities. This inversion is fundamental: instead of the user understanding the API, the API understands the user.

### Implementation Through Constraint

The technical implementation of a single `chat()` endpoint that handles everything requires several architectural innovations:

1. **Universal Intent Recognition**: The agent must distinguish between "Get weather" (requires external API), "Remember this" (requires storage), and "What's the capital of France?" (requires knowledge retrieval) without separate endpoints.

2. **Contextual Tool Selection**: Based on intent recognition, the agent must select appropriate internal tools while maintaining conversation flow.

3. **Unified Response Generation**: All outputs—whether calculations, retrieved information, or generated text—must be formatted into natural language responses.

4. **State Management Across Interactions**: Unlike stateless REST APIs, `chat()` maintains conversation context across calls, creating a continuous interaction stream.

This unified approach eliminates entire categories of API design problems: versioning multiple endpoints, maintaining backward compatibility across specialized interfaces, and documenting numerous parameters. There's one endpoint to version, one to document, one to maintain.

### The Unix Philosophy Realized

The Unix philosophy—"Make each program do one thing well"—finds perfect expression in `chat()`. It does exactly one thing: process natural language input into natural language output. But through composition with other programs (internal tools), it achieves unlimited functionality. This is the essence of the Unix pipe (`|`) applied to cognitive interfaces: simple components that combine powerfully.

## 3. Remember — The Context Accumulator

### Persistent Memory as a First-Class Citizen

Most AI APIs treat conversations as ephemeral. Context windows reset, sessions expire, and memories vanish. Cocapn makes memory a fundamental API primitive through `remember(key, value)`. This simple key-value storage mechanism enables agents to accumulate knowledge, preferences, history, and state across interactions.

The design is deliberately minimal: store a value associated with a key. But this simplicity enables sophisticated patterns:

- **Personalization**: `remember('preferred_temperature', '72°F')`
- **Historical Context**: `remember('2024-03-15_mood', 'anxious')`
- **Project State**: `remember('project_alpha_status', 'awaiting_review')`
- **Procedural Knowledge**: `remember('coffee_preference', 'dark roast, no sugar')`

### The Accumulation of Value

The true innovation of `remember()` isn't technical but economic. Each call increases the agent's contextual knowledge. Over time, an agent accumulates thousands of `remember()` calls that create a rich, personalized context unique to each user. This context becomes what business strategists would call a "moat"—a competitive advantage that cannot be easily replicated.

Consider two identical Cocapn agents at initialization. After one year of use, Agent A has 10,000 `remember()` calls from User X, while Agent B has zero. Agent A understands User X's preferences, history, patterns, and needs. Agent B is generic. No competing API can instantly replicate this accumulated context, even with identical underlying models.

### Implementation Architecture

Behind the simple `remember(key, value)` interface lies sophisticated storage architecture:

1. **Hierarchical Key Organization**: Keys support dot notation (`user.preferences.theme`) for organized storage.

2. **Type-Aware Storage**: Values maintain type information (string, number, boolean, object, array).

3. **Versioning and History**: Each key maintains a version history, enabling `recall('preference', {version: -2})` to retrieve previous values.

4. **Cross-User Context Sharing**: With proper permissions, memories can be shared across user contexts, enabling organizational knowledge accumulation.

5. **Encryption at Rest**: All memories are encrypted, with user-controlled keys for sensitive information.

The storage system employs a hybrid approach: frequently accessed memories in low-latency cache, all memories in durable storage, with intelligent prefetching based on access patterns.

### Beyond Simple Key-Value Storage

While the interface suggests simple key-value storage, the implementation supports richer semantics:

- **Expiring Memories**: `remember('temporary_code', 'ABC123', {ttl: 300})` auto-expires after 5 minutes
- **Structured Values**: Objects and arrays are stored with full queryability
- **Linked Memories**: Values can reference other keys, creating a knowledge graph
- **Confidence Scoring**: Memories can include confidence levels for uncertain information

Yet all this complexity remains hidden behind `remember(key, value)`. The interface remains simple even as the implementation grows sophisticated.

## 4. Recall — The Context Retriever

### Intelligent Retrieval as API Primitive

If `remember()` is writing to memory, `recall()` is reading—but with intelligence. `recall(key)` accepts a key and returns the associated value, but the implementation supports far more than exact key matching:

1. **Fuzzy Matching**: `recall('birthday')` might match `remember('birth_date', '1990-05-15')`

2. **Semantic Search**: `recall('how I was feeling last Tuesday')` performs temporal and emotional semantic matching against mood entries

3. **Temporal Queries**: `recall('mood last week')` aggregates mood entries from the previous seven days

4. **Derived Values**: `recall('average_mood_this_month')` calculates from stored mood values

5. **Pattern Recognition**: `recall('productivity_patterns')` identifies trends in productivity entries

### The Memory Graph

Internally, Cocapn implements what might be called a "memory graph"—a knowledge structure where memories connect semantically, temporally, and relationally. When you `recall('project_status')`, the agent doesn't merely retrieve a key; it traverses related memories: recent conversations about the project, stored deadlines, team member interactions, and historical progress.

This transforms `recall()` from simple retrieval to contextual synthesis. The response to `recall('how is project X going?')` might synthesize: last status update, recent blocker mentioned in chat, approaching deadline from calendar integration, and historical velocity—all through a single API call.

### Implementation Through Embeddings and Indices

The intelligent retrieval capabilities are enabled by several technical components:

1. **Vector Embeddings**: All memories are encoded into vector representations, enabling semantic similarity search.

2. **Temporal Indexing**: Memories are indexed by time, enabling "last week" or "on Tuesday" queries.

3. **Relationship Mapping**: The system identifies and indexes relationships between memories (A mentions B, C occurred after D).

4. **Aggregation Pipeline**: For queries like "average mood," the system retrieves relevant memories and applies aggregation functions.

5. **Confidence-Based Blending**: When multiple relevant memories exist, responses are blended with confidence weighting.

The result is that `recall()` feels less like database retrieval and more like asking a knowledgeable assistant to consult their memory.

### The Recall-Chat Continuum

An important design decision is maintaining separation between `recall()` and `chat()`. While `chat('What's my birthday?')` would work, `recall('birthday')` exists for programmatic access. This distinction enables:

- **Programmatic Context Access**: Applications can retrieve stored values without natural language parsing
- **Efficiency**: Direct retrieval avoids LLM inference overhead for simple lookups
- **Precision**: Exact retrieval when fuzzy matching isn't desired
- **Composability**: `recall()` results can feed into other system components

Yet the two functions maintain interoperability. `chat('What's my birthday?')` might internally call `recall('birthday')`, demonstrating how the three primitives compose.

## 5. Why Three is Enough

### Completeness Proof

We can demonstrate the functional completeness of {chat, remember, recall} through reduction to known complete sets:

1. **Turing Completeness**: The `chat()` function, with access to computational tools, is Turing complete. It can perform any computation.

2. **State Management**: `remember()` and `recall()` provide persistent state, enabling stateful computations.

3. **Input/Output**: `chat()` handles all I/O through natural language, which can encode any structured data.

4. **External Interaction**: Through internal tool use, the agent can interact with any external system, making the API a gateway to all computable functions.

Thus, any function computable by a Turing machine with persistent storage and I/O capabilities is accessible through these three functions.

### The Principle of Minimal Sufficiency

API design often suffers from "just in case" thinking—adding endpoints that might be useful. Cocapn follows "just in time" thinking—adding complexity only when proven necessary. After extensive analysis, three functions proved sufficient for all observed use cases:

- **All communication** → `chat()`
- **All state persistence** → `remember()`
- **All state retrieval** → `recall()`

What about deletion? `remember(key, null)`. What about updates? `remember(key, new_value)`. What about listing? `recall('keys.*')` with pattern matching. What about transactions? Batched `remember()` calls with atomic guarantees.

### Hidden Complexity, Simple Interface

The key insight is separation of interface from implementation. Other APIs expose their complexity: different endpoints for different capabilities, parameters for tuning behavior, options for various output formats. Cocapn hides all complexity behind three abstractions:

- **Model Selection**: The agent chooses appropriate models internally
- **Tool Use**: The agent selects and uses tools internally
- **Format Adaptation**: The agent formats outputs appropriately