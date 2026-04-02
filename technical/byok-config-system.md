> *Experiment: DeepSeek Chat*

# BYOK (Bring Your Own Key) System Design for Repo-Agents

## Overview
The BYOK system enables users to configure their own LLM providers while maintaining security through multiple storage options and operational modes. This design ensures flexibility across different deployment scenarios while protecting sensitive API keys.

## Core Architecture

### TypeScript Interfaces

```typescript
interface LLMProvider {
  id: string;
  name: string;
  baseURL?: string;
  apiKey: string;
  model: string;
  maxTokens?: number;
  temperature?: number;
  contextWindow?: number;
}

interface BYOKConfig {
  provider: string;
  apiKey: string;
  model: string;
  baseURL?: string;
  mode: 'proxy' | 'direct';
  fingerprint: string;
  encrypted?: boolean;
  lastUpdated: number;
  metadata?: Record<string, any>;
}

interface StorageAdapter {
  read(fingerprint: string): Promise<BYOKConfig | null>;
  write(fingerprint: string, config: BYOKConfig): Promise<void>;
  delete(fingerprint: string): Promise<void>;
}

interface EncryptionService {
  encrypt(plaintext: string, userKey: string): Promise<string>;
  decrypt(ciphertext: string, userKey: string): Promise<string>;
  deriveUserKey(fingerprint: string, salt?: string): Promise<string>;
}
```

## Configuration Discovery Pipeline

### 1. URL Parameters (Highest Priority)
```
?provider=openai&apiKey=sk-...&model=gpt-4o&mode=direct
```
- Parsed on initial page load
- Validated and sanitized immediately
- Never persisted unless explicitly saved by user
- Ephemeral session-only storage

### 2. localStorage (Browser Persistence)
```javascript
// Structure
const byok_config = {
  version: '1.0',
  fingerprint: 'user_device_fp',
  configs: {
    'default': {
      provider: 'openai',
      apiKey: 'encrypted_data',
      model: 'gpt-4o',
      mode: 'direct',
      lastUsed: 1625097600000
    }
  }
};
```

### 3. Cloudflare KV (Cloud Sync)
- Key format: `byok:{fingerprint}:{config_id}`
- Values encrypted with user-derived key
- TTL: 30 days for inactive keys
- Namespace: `BYOK_CONFIGS`

### 4. Google Drive Integration
- File: `byok_config.json` in app-specific folder
- OAuth2 scopes: `drive.file` (minimal permissions)
- Encrypted before upload using user's master key
- Conflict resolution: timestamp-based merging

### 5. Local File System (Self-Hosted)
- File paths checked in order:
  1. `./cocapn.json`
  2. `./.env`
  3. `~/.config/cocapn/byok.json`
- Environment variable fallback: `BYOK_CONFIG`
- Format support: JSON, dotenv, YAML

## Dual Mode Operation

### Proxy Mode (Secure Cloudflare Worker)
```
Browser → Cloudflare Worker → LLM Provider
       (encrypted)        (user's API key)
```

**Session Management:**
```typescript
interface ProxySession {
  sessionId: string;
  fingerprint: string;
  configId: string;
  expiresAt: number;
  rateLimit: number;
  encryptedKey: string; // Encrypted with session key
}

// Cookie structure
Set-Cookie: byok_session=encrypted_data; 
  HttpOnly; 
  Secure; 
  SameSite=Strict; 
  Max-Age=86400
```

**Worker Implementation:**
```javascript
// Cloudflare Worker
export default {
  async fetch(request, env) {
    const session = await validateSession(request);
    const config = await env.KV.get(`byok:${session.fingerprint}`);
    
    // Decrypt API key using session-derived key
    const apiKey = await decrypt(config.apiKey, session.sessionKey);
    
    // Proxy request to LLM
    return fetch('https://api.openai.com/v1/chat/completions', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${apiKey}`,
        'Content-Type': 'application/json'
      },
      body: await request.text()
    });
  }
};
```

### Direct Mode (Browser-to-Provider)
```
Browser → LLM Provider
       (user's API key in localStorage)
```

**Security Measures:**
- Keys stored encrypted in localStorage
- Encryption key derived from user password + device fingerprint
- Automatic key rotation every 7 days
- CSP headers to prevent exfiltration

## Security Implementation

### Key Encryption Flow
```typescript
class BYOKEncryption implements EncryptionService {
  async deriveUserKey(fingerprint: string, password?: string) {
    // Combine multiple factors for key derivation
    const factors = [
      fingerprint,
      password || '',
      await this.getDeviceId(),
      await this.getStaticSalt()
    ];
    
    const material = factors.join(':');
    return crypto.subtle.digest('SHA-256', 
      new TextEncoder().encode(material));
  }
  
  async encrypt(plaintext: string, userKey: string) {
    const iv = crypto.getRandomValues(new Uint8Array(12));
    const algorithm = { name: 'AES-GCM', iv };
    
    const cryptoKey = await crypto.subtle.importKey(
      'raw', 
      userKey, 
      algorithm, 
      false, 
      ['encrypt']
    );
    
    const encrypted = await crypto.subtle.encrypt(
      algorithm,
      cryptoKey,
      new TextEncoder().encode(plaintext)
    );
    
    return JSON.stringify({
      iv: Array.from(iv),
      data: Array.from(new Uint8Array(encrypted))
    });
  }
}
```

### Fingerprint Generation
```typescript
async function generateFingerprint(): Promise<string> {
  const components = [
    navigator.userAgent,
    navigator.language,
    screen.colorDepth,
    timezoneOffset,
    await getCanvasFingerprint(),
    localStorage.getItem('byok_device_id') || 
      await generateDeviceId()
  ];
  
  const hash = await crypto.subtle.digest(
    'SHA-256',
    new TextEncoder().encode(components.join('|'))
  );
  
  return Array.from(new Uint8Array(hash))
    .map(b => b.toString(16).padStart(2, '0'))
    .join('');
}
```

## Built-in Provider Configuration

```typescript
const BUILT_IN_PROVIDERS: Record<string, LLMProvider> = {
  openai: {
    id: 'openai',
    name: 'OpenAI',
    baseURL: 'https://api.openai.com/v1',
    requiredFields: ['apiKey', 'model'],
    defaultModel: 'gpt-4o'
  },
  deepseek: {
    id: 'deepseek',
    name: 'DeepSeek',
    baseURL: 'https://api.deepseek.com',
    requiredFields: ['apiKey'],
    defaultModel: 'deepseek-chat'
  },
  anthropic: {
    id: 'anthropic',
    name: 'Anthropic Claude',
    baseURL: 'https://api.anthropic.com',
    requiredFields: ['apiKey', 'model'],
    defaultModel: 'claude-3-opus-20240229'
  },
  google: {
    id: 'google',
    name: 'Google Gemini',
    baseURL: 'https://generativelanguage.googleapis.com',
    requiredFields: ['apiKey', 'model'],
    defaultModel: 'gemini-pro'
  },
  ollama: {
    id: 'ollama',
    name: 'Ollama',
    baseURL: 'http://localhost:11434',
    requiredFields: ['model'],
    defaultModel: 'llama2'
  },
  groq: {
    id: 'groq',
    name: 'Groq',
    baseURL: 'https://api.groq.com/openai/v1',
    requiredFields: ['apiKey', 'model'],
    defaultModel: 'mixtral-8x7b-32768'
  },
  zai: {
    id: 'zai',
    name: 'z.ai (GLM)',
    baseURL: 'https://open.bigmodel.cn/api/paas/v4',
    requiredFields: ['apiKey', 'model'],
    defaultModel: 'glm-4'
  }
};
```

## Landing Page Design

### UI Components
1. **Provider Selection**: Dropdown with provider icons
2. **Configuration Form**: Dynamic form based on selected provider
3. **Storage Options**: Toggle between proxy/direct modes
4. **Sync Status**: Visual indicators for multi-device sync
5. **Security Dashboard**: Key usage, rotation status, access logs

### Configuration Flow
```typescript
async function initializeBYOK() {
  // 1. Check URL params
  const urlConfig = parseURLParameters();
  if (urlConfig) return urlConfig;
  
  // 2. Check localStorage
  const localConfig = await loadLocalConfig();
  if (localConfig) return localConfig;
  
