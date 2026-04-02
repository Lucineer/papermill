> *DeepSeek Chat*

# ElevenLabs Audio Pipeline for DMLog.ai

## **1. System Architecture Overview**

The DMLog.ai audio pipeline transforms simulated game session transcripts into immersive audio experiences using ElevenLabs TTS. The system employs a hybrid server-client architecture that balances computational efficiency with real-time streaming capabilities.

**Core Components:**
- **Backend Service** (Node.js/Cloudflare Workers): Handles text processing, voice assignment, and ElevenLalls API orchestration
- **Audio Processing Layer**: Optional external service for server-side mixing
- **Client Application** (Browser): Real-time audio mixing and playback via Web Audio API
- **Voice Registry**: Database mapping characters to ElevenLabs voice IDs

## **2. Pipeline Stages**

### **Stage 1: Text → Character Assignment**
**Input:** Game session transcript with speaker tags
```
[DM]: You enter a dark cavern...
[Player1]: I draw my sword!
[Player2]: I cast Light on the tip.
```

**Processing:**
- Parse transcript into speech segments
- Extract character/speaker identifiers
- Apply dialogue formatting rules (emotion markers, pauses)
- Output structured JSON:

```json
{
  "segments": [
    {
      "character": "DM",
      "text": "You enter a dark cavern...",
      "emotion": "dramatic",
      "pause_after": 1000
    },
    {
      "character": "Player1",
      "text": "I draw my sword!",
      "emotion": "excited",
      "pause_after": 500
    }
  ]
}
```

**API Endpoint:**
```
POST /api/transcript/parse
Content-Type: application/json

{
  "transcript": "raw transcript text",
  "game_id": "session_123",
  "players": ["Player1", "Player2"]
}
```

### **Stage 2: Character → Voice Selection**
**Voice Registry Schema:**
```json
{
  "game_id": "session_123",
  "voices": {
    "DM": {
      "voice_id": "21m00Tcm4TlvDq8ikWAM",
      "provider": "elevenlabs",
      "settings": {
        "stability": 0.5,
        "similarity_boost": 0.8,
        "style": 0.3,
        "use_speaker_boost": true
      }
    },
    "Player1": {
      "voice_id": "custom_cloned_voice_001",
      "provider": "elevenlabs",
      "settings": {
        "stability": 0.7,
        "similarity_boost": 0.9
      }
    }
  }
}
```

**Voice Management Endpoints:**
```
POST /api/voices/assign
{
  "game_id": "session_123",
  "character": "DM",
  "voice_type": "dramatic_male"  // Maps to ElevenLabs voice ID
}

POST /api/voices/clone
{
  "game_id": "session_123",
  "character": "Player1",
  "audio_sample_url": "https://storage.player1_sample.mp3",
  "name": "Player1_Twin_Voice"
}
```

### **Stage 3: Text → Audio (ElevenLabs TTS)**
**Optimized Batch Processing:**
- Queue segments by voice to minimize voice switching overhead
- Implement exponential backoff for rate limiting (ElevenLabs: 1000 chars/min free tier)
- Cache identical text segments across sessions

**Worker Implementation:**
```javascript
async function generateTTS(segment, voiceConfig) {
  const response = await fetch(
    `https://api.elevenlabs.io/v1/text-to-speech/${voiceConfig.voice_id}`,
    {
      method: 'POST',
      headers: {
        'xi-api-key': API_KEY,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        text: segment.text,
        model_id: 'eleven_multilingual_v2',
        voice_settings: voiceConfig.settings
      })
    }
  );
  
  return {
    audio: await response.arrayBuffer(),
    duration: estimateDuration(segment.text),
    segment_id: segment.id
  };
}
```

**API Endpoint:**
```
POST /api/audio/generate
{
  "segments": [/* parsed segments */],
  "voice_mapping": { /* character->voice mapping */ }
}

// Response: SSE stream or job ID for polling
```

### **Stage 4: Audio → Mix Strategy**
**Hybrid Approach: Sequential Segments with Client-Side Mixing**

**Server-Side (Simple):**
- Generate sequential audio segments with embedded pauses
- Concatenate using Cloudflare Workers (simple byte concatenation for MP3)
- Add metadata markers for client-side synchronization

**Client-Side Mixing (Recommended):**
1. **Background Audio Track**: Pre-mixed ambient sounds (cavern echoes, tavern noise)
2. **Foreground Dialogue**: Individual voice segments with timing data
3. **Sound Effects**: Positional audio cues (sword clangs, spell casts)

**Audio Manifest Format:**
```json
{
  "session_id": "session_123",
  "audio_segments": [
    {
      "url": "https://cdn.segment_001.mp3",
      "start_time": 0,
      "duration": 4500,
      "character": "DM",
      "volume": 1.0
    }
  ],
  "background_tracks": [
    {
      "url": "https://cdn.ambient_cavern.mp3",
      "loop": true,
      "volume": 0.3
    }
  ],
  "sound_effects": [
    {
      "url": "https://cdn.sword_draw.mp3",
      "trigger_time": 4520,
      "position": "left"  // For spatial audio
    }
  ]
}
```

### **Stage 5: Stream Delivery**
**Dual Delivery Options:**

**Option A: Progressive Download (Large Sessions)**
```
GET /api/audio/session/{session_id}/download
→ Returns single MP3 with sequential dialogue
```

**Option B: Real-Time SSE Stream (Interactive)**
```
GET /api/audio/session/{session_id}/stream

data: {"type": "segment", "url": "https://cdn.segment_001.mp3", "start": 0}
data: {"type": "background", "url": "https://cdn.ambient.mp3"}
data: {"type": "effect", "url": "https://cdn.spell_cast.mp3", "at": 12500}
```

## **3. Client-Side Playback Engine**

**Web Audio API Implementation:**
```javascript
class AudioPlaybackEngine {
  constructor() {
    this.audioContext = new AudioContext();
    this.masterGain = this.audioContext.createGain();
    this.backgroundGain = this.audioContext.createGain();
    this.dialogueGain = this.audioContext.createGain();
    
    // Spatial audio setup
    this.pannerNodes = new Map();
  }
  
  async playSegment(audioBuffer, options = {}) {
    const source = this.audioContext.createBufferSource();
    source.buffer = audioBuffer;
    
    if (options.position) {
      const panner = this.audioContext.createPanner();
      panner.setPosition(options.position.x, options.position.y, 0);
      source.connect(panner).connect(this.dialogueGain);
    } else {
      source.connect(this.dialogueGain);
    }
    
    source.start(this.audioContext.currentTime + (options.delay || 0));
  }
  
  async loadAudioManifest(manifest) {
    // Preload and schedule all audio elements
    // Implement priority queue for streaming
  }
}
```

**Control Interface:**
- Play/pause with context preservation
- Skip forward/backward by dialogue segment
- Volume sliders: Dialogue/Background/Effects
- Spatial audio toggle (for immersive scenes)
- Playback speed (0.5x to 2x)

## **4. Performance Optimizations**

**Caching Strategy:**
1. **Voice Generation Cache**: MD5 hash of (voice_id + text + settings)
2. **Session Cache**: Generated audio stored for 24 hours
3. **Prefetching**: Next 3 segments loaded during playback

**Scalability Considerations:**
- Cloudflare Workers for TTS orchestration (stateless)
- R2 storage for audio segments (cheap, CDN-backed)
- Durable Objects for session state if needed
- Queue system (RabbitMQ/Cloudflare Queues) for batch processing

**Fallback Mechanisms:**
1. **Degraded Mode**: Text-only display if audio fails
2. **Voice Fallback**: Default to similar built-in voice if cloned voice fails
3. **Background Audio Fallback**: Silent track if ambient audio unavailable

## **5. API Endpoints Summary**

```
# Transcript Processing
POST   /api/transcript/parse
POST   /api/transcript/analyze-emotion

# Voice Management
GET    /api/voices/available
POST   /api/voices/assign
POST   /api/voices/clone
DELETE /api/voices