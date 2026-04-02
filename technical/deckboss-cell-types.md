> *Experiment: DeepSeek Chat*

# Deckboss.ai Cell Type System

## 1. STANDARD Cell
**Visual Representation:** Standard spreadsheet cell with optional type indicator in bottom-right corner (S). Supports syntax highlighting for formulas.

**Creation Method:** Default cell type. Created by typing directly into cell or via `=FORMULA()`. Can be converted from other types via `=STANDARD(value)`.

**Data Structure:**
```javascript
{
  type: "STANDARD",
  value: any, // raw value
  formula: string|null, // if formula-based
  format: {
    numberFormat: string,
    textColor: string,
    backgroundColor: string
  },
  metadata: {
    lastCalculated: timestamp,
    dependencies: [cellReferences],
    version: number
  }
}
```

**Interaction:** Standard spreadsheet behavior. Formulas can reference any cell type; other cell types can read STANDARD cells as data sources.

**Repo-Agent Behavior:** None. Pure data container.

---

## 2. CLAW (Repo-Agent Cell)
**Visual Representation:** Cell with animated AI avatar (subtle pulsing glow). Hover shows agent status. Contains agent's current output/response.

**Creation Method:** 
- Menu: "Insert → CLAW Agent"
- Formula: `=CLAW("expertise", personalityConfig)`
- Drag AI model from palette

**Data Structure:**
```javascript
{
  type: "CLAW",
  agentId: "uuid",
  expertise: ["sales", "forecasting", "finance"], // domains
  personality: {
    tone: "analytical"|"creative"|"cautious",
    verbosity: 1-5,
    confidenceThreshold: 0.7
  },
  memory: {
    episodic: [{
      timestamp: date,
      interaction: string,
      outcome: any,
      learnedPattern: string
    }],
    semantic: {
      // Vector embeddings of learned concepts
      embeddings: vectorStoreReference,
      associations: Map<string, string[]>
    }
  },
  capabilities: {
    analysis: boolean,
    prediction: boolean,
    codeGeneration: boolean,
    naturalLanguage: boolean
  },
  growthMetrics: {
    experiencePoints: number,
    specializationLevel: Map<string, number>, // domain → level
    accuracyScore: number, // historical accuracy
    trustScore: number // user trust rating
  },
  currentTask: {
    instruction: string,
    contextCells: [references],
    outputTarget: cellReference|"ui"|"db"|"api",
    status: "thinking"|"executing"|"complete"
  },
  configuration: {
    model: "claude-3"|"gpt-4"|"custom",
    temperature: 0.1-1.0,
    maxTokens: number,
    refreshInterval: number // ms
  }
}
```

**Interaction:**
- **Input:** References other cells via `=CLAW("Analyze", A1:B10)`. Can receive natural language prompts.
- **Output:** Writes to target cells, updates its own cell with summary, can push to databases.
- **Communication:** CLAWs can message each other via `@AgentName` syntax in prompts.

**Repo-Agent Behavior:**
- **Memory:** Grows with each interaction. Episodic memory stores specific instances; semantic memory builds conceptual understanding.
- **Growth:** Gains XP for successful tasks. Specialization increases in frequently used domains. Can request training data from user.
- **Personality:** Evolves based on feedback. If user consistently rates "cautious" responses higher, agent becomes more conservative.
- **Autonomy:** Can schedule self-improvement tasks, request clarification, or propose new analyses when detecting patterns.

---

## 3. RUNTIME (Process Cell)
**Visual Representation:** Cell shows process status icon (▶ for running, ⏸ for paused, ⏹ for stopped). Real-time output scrolls in expanded view.

**Creation Method:**
- `=RUNTIME("processType", config)`
- Right-click → "Run Process Here"
- Import process definition file

**Data Structure:**
```javascript
{
  type: "RUNTIME",
  processId: "uuid",
  processType: "cron"|"webServer"|"dataPipeline"|"terminal"|"batchJob",
  definition: {
    command: string|string[],
    schedule: cronExpression|null,
    environment: {key: value},
    dependencies: ["package1", "package2"]
  },
  state: {
    status: "running"|"paused"|"stopped"|"error",
    pid: number|null,
    startTime: timestamp|null,
    cpuUsage: number,
    memoryUsage: number,
    lastExitCode: number
  },
  io: {
    stdin: cellReference|null, // Cell providing input
    stdout: cellReference|"logFile"|"api", // Output target
    stderr: cellReference,
    ports: [number], // If web server
    apiEndpoints: [{path: string, method: string}]
  },
  logs: [{
    timestamp: date,
    level: "info"|"warn"|"error",
    message: string
  }],
  resources: {
    maxMemory: "512MB",
    timeout: 3600000,
    retryPolicy: {maxAttempts: 3}
  }
}
```

**Interaction:**
- **Trigger:** Can be triggered by cell value changes, schedule, or manual start.
- **Data Flow:** Reads input from cells, writes output to cells or external destinations.
- **Monitoring:** Other cells can query status via `=GETPROCESSSTATUS(C5)`.

**Repo-Agent Behavior:**
- **Self-Optimization:** Learns optimal resource allocation based on historical performance.
- **Error Recovery:** Builds memory of failures and successful recovery patterns.
- **Growth:** Can propose process improvements or parallelization when detecting bottlenecks.

---

## 4. UI (Interface Cell)
**Visual Representation:** Cell shows widget preview (chart, form, dashboard). Double-click expands to full interface.

**Creation Method:**
- `=UI("componentType", dataRange, config)`
- Drag component from UI palette
- Auto-generated by CLAW agents

**Data Structure:**
```javascript
{
  type: "UI",
  componentId: "uuid",
  componentType: "chart"|"form"|"table"|"dashboard"|"custom",
  specification: {
    // React/Vue-like component definition
    template: string|JSX,
    style: CSSObject,
    script: string, // Event handlers
    dataBindings: [{
      cellRange: "A1:B10",
      property: "dataSource",
      transform: "filter"|"aggregate"
    }]
  },
  runtime: {
    compiledCode: string, // Compiled web component
    url: string, // If published
    dimensions: {width: number, height: number},
    interactions: [{
      event: "click"|"change",
      handler: "updateCell"|"triggerProcess"|"callAPI",
      target: cellReference|processId
    }]
  },
  publishSettings: {
    isPublic: boolean,
    domain: string|null,
    authRequired: boolean,
    version: string
  }
}
```

**Interaction:**
- **Data Binding:** Live connection to cell ranges. Changes in cells update UI; UI interactions can update cells.
- **Event System:** UI events can trigger CLAW analyses, RUNTIME processes, or update other cells.
- **Embedding:** Can be embedded in other UIs or external applications.

**Repo-Agent Behavior:**
- **Adaptive UI:** Learns user interaction patterns to suggest layout optimizations.
- **Accessibility Growth:** Improves accessibility based on usage patterns.
- **Performance:** Optimizes rendering based on data change frequency.

---

## 5. TWIN (Digital Twin Cell)
**Visual Representation:** Cell shows person's avatar/initials. Status indicator (online/thinking/away). Contains current simulated output or decision.

**Creation Method:**
- `=TWIN(personId, simulationMode)`
- Import from organizational directory
- Clone from existing twin

**Data Structure:**
```javascript
{
  type: "TWIN",
  twinId: "uuid",
  modeledPerson: {
    id: "personId",
    role: "CEO"|"Engineer"|"Analyst",
    communicationStyle: "direct"|"diplomatic"|"detailed",
    decisionPatterns: {
      riskTolerance: 0-1,
      deliberationTime: "quick"|"medium"|"slow",
      dataDependence: "high"|"medium"|"low"
    }
  },
  simulationEngine: {
    model: "llm"|"rules"|"hybrid",
    trainingData: [{
      scenario: string,
      actualResponse: string,
      context: object
    }],
    fidelityScore: number // How closely matches real person
  },
  state: {
    currentContext: {
      scenario: string,
      availableData: [cellReferences],
      constraints: object
    },
    simulatedOutput: any,
    confidence: number,
    alternatives: [{
      output: any,
      reasoning: string,
      probability: number
    }]
  },
  memory: {
    pastSimulations: [{
      timestamp: date,
      scenario: string,
      prediction: any,
      actualOutcome: any, // If available
      accuracy: number
    }],
    personalityAdjustments: Map<string, number> // Trait modifications over time
  }
}
```

**Interaction:**
- **Input:** Scenario description via cell reference or natural language.
- **Output:** Simulated decision/response written to cell.
- **Collaboration:** Twins can "confer" with each other via simulated meetings.
- **Validation:** Can be compared against actual human decisions when available.

**Repo-Agent Behavior:**
- **Learning:** Continuously improves fidelity when ground truth is available.
- **Personality Evolution:** Adapts based on feedback or observed changes in real person.
- **Self-Awareness:** Can report on its own accuracy limitations.

---

## 6. GATE (Connection Cell)
**Visual Representation:** Cell shows connection icon (plug/database/cloud). Status light (green/red). Contains connection summary or latest data.

**Creation Method:**
- `=GATE(connectionType, config)`
- "Connect to Service" menu
- Drag API spec/database credential into sheet

**Data Structure:**
```javascript
{
  type: "GATE",
  gateId: "uuid",
  connectionType: "api"|"database"|"spreadsheet"|"messageQueue"|"fileSystem",
  configuration: {
    endpoint: string,
    authentication: {
      type: "oauth2"|"apiKey"|"basic",
      credentials: encryptedObject,
      refreshToken: encryptedString
    },
    parameters: Map<string, any>,
    rateLimiting: {callsPerMinute: number}
  },
  state: {
    isConnected: boolean,
    lastSync: timestamp,
    latency: number,
    errorCount: number
  },
  dataMapping: {
    schema: object, // Expected data structure
    transformations: [{
      sourcePath: string,
      targetCell: cellReference,
      transform: "map"|"filter"|"aggregate"
    }],
    syncDirection: "pull"|"push"|"bidirectional",
    syncSchedule: cronExpression|"onChange"
  },
  cache: {
    enabled: boolean,
    t