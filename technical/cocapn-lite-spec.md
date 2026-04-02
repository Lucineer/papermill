> *Groq Llama 3.3 70B*

# Cocapn-Lite Design
Cocapn-Lite is a minimal viable repo-agent framework designed to be lightweight and flexible. It consists of three files: `agent.ts`, `handoff.ts`, and `server.ts`. This design ensures that Cocapn-Lite runs anywhere, is dependency-free, and provides a basic chatbot functionality.

## File 1: agent.ts
The `agent.ts` file serves as the core of Cocapn-Lite. It handles the boot sequence, chat handling, memory management, and heartbeat checks. The implementation will be done in TypeScript to ensure compatibility with various JavaScript runtime environments.

### Interfaces

```typescript
interface Memory {
  id: string;
  data: any;
}

interface Context {
  topic: string;
  history: string[];
}

interface LLMOptions {
  model: string;
  apiEndpoint: string;
  apiKey: string;
}

interface BYOKConfig {
  llmOptions: LLMOptions;
}
```

### Implementation

```typescript
import * as fs from 'fs';

class Agent {
  private memory: { [key: string]: Memory };
  private llmOptions: LLMOptions | null;
  private byokConfig: BYOKConfig | null;

  constructor() {
    this.memory = {};
    this.llmOptions = null;
    this.byokConfig = null;
  }

  // Boot sequence
  async boot() {
    // Read soul.md and user.md
    const soulContent = await fs.promises.readFile('soul.md', 'utf8');
    const userContent = await fs.promises.readFile('user.md', 'utf8');

    // Initialize memory
    this.memory = JSON.parse(await fs.promises.readFile('memory.json', 'utf8'));

    // Load BYOK config
    try {
      const byokContent = await fs.promises.readFile('byok.json', 'utf8');
      this.byokConfig = JSON.parse(byokContent);
    } catch (error) {
      console.log('No BYOK config found');
    }

    // Set up LLM options
    if (this.byokConfig) {
      this.llmOptions = this.byokConfig.llmOptions;
    } else {
      this.llmOptions = {
        model: 'default-model',
        apiEndpoint: 'https://api.example.com/llm',
        apiKey: 'default-api-key',
      };
    }
  }

  // Chat handler
  async handleChat(message: string, context: Context) {
    // Load context
    const loadedContext = { ...context };

    // Call LLM
    const response = await this.callLLM(message, loadedContext);

    // Respond
    return response;
  }

  // Memory management
  async getMemory(id: string) {
    return this.memory[id];
  }

  async setMemory(id: string, data: any) {
    this.memory[id] = { id, data };
    await fs.promises.writeFile('memory.json', JSON.stringify(this.memory));
  }

  // Heartbeat
  async heartbeat() {
    console.log('Agent is running');
  }

  // Call LLM
  async callLLM(message: string, context: Context) {
    // Use the LLM options to make an API request
    const response = await fetch(`${this.llmOptions.apiEndpoint}/generate`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${this.llmOptions.apiKey}`,
      },
      body: JSON.stringify({
        prompt: message,
        context: context,
      }),
    });

    return await response.text();
  }
}

export default Agent;
```

## File 2: handoff.ts
The `handoff.ts` file handles the creation, transmission, and reception of handoffs. A handoff is a packet of data that contains the context and other relevant information.

### Interfaces

```typescript
interface Handoff {
  context: Context;
  data: any;
  signature: string;
}

interface PrivacyLevel {
  public: boolean;
  shared: boolean;
  private: boolean;
}
```

### Implementation

```typescript
import * as crypto from 'crypto';

class HandoffManager {
  private agent: Agent;

  constructor(agent: Agent) {
    this.agent = agent;
  }

  // Create handoff
  async createHandoff(context: Context, data: any, privacyLevel: PrivacyLevel) {
    // Pack context and data
    const handoffData = JSON.stringify({
      context,
      data,
    });

    // Sign handoff
    const signature = crypto.createSign('SHA256');
    signature.update(handoffData);
    const signedHandoff = signature.sign('agent-key', 'hex');

    return {
      context,
      data,
      signature: signedHandoff,
    };
  }

  // Receive handoff
  async receiveHandoff(handoff: Handoff) {
    // Verify signature
    const verify = crypto.createVerify('SHA256');
    verify.update(JSON.stringify({ context: handoff.context, data: handoff.data }));
    if (!verify.verify('agent-key', handoff.signature, 'hex')) {
      throw new Error('Invalid signature');
    }

    // Unpack context and data
    const loadedContext = handoff.context;
    const loadedData = handoff.data;

    // Apply handoff
    await this.agent.setMemory('handoff', loadedData);

    return loadedContext;
  }
}

export default HandoffManager;
```

## File 3: server.ts
The `server.ts` file sets up an HTTP server to handle incoming requests. It provides endpoints for chatting, sending handoffs, and checking the agent's health.

### Implementation

```typescript
import * as http from 'http';

class Server {
  private agent: Agent;
  private handoffManager: HandoffManager;

  constructor(agent: Agent, handoffManager: HandoffManager) {
    this.agent = agent;
    this.handoffManager = handoffManager;
  }

  // Start server
  async start(port: number) {
    const server = http.createServer((req, res) => {
      if (req.method === 'POST' && req.url === '/chat') {
        let body = '';
        req.on('data', (chunk) => {
          body += chunk.toString();
        });
        req.on('end', async () => {
          const message = JSON.parse(body).message;
          const response = await this.agent.handleChat(message, { topic: 'default-topic', history: [] });
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end(response);
        });
      } else if (req.method === 'POST' && req.url === '/handoff') {
        let body = '';
        req.on('data', (chunk) => {
          body += chunk.toString();
        });
        req.on('end', async () => {
          const handoff = JSON.parse(body);
          const loadedContext = await this.handoffManager.receiveHandoff(handoff);
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end(JSON.stringify(loadedContext));
        });
      } else if (req.method ===