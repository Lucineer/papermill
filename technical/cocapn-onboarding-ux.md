> *DeepSeek Chat*

# Cocapn.ai Onboarding Experience: Zero to Agent in 5 Minutes

## **1. LANDING PAGE: The Invitation**

**Visual Design:**
- Clean, dark-themed interface with subtle animated particles flowing like code
- Left side: Hero content. Right side: Live demo window showing a real agent responding in real-time
- Bottom: Smooth scroll indicator with pulsing animation

**Hero Section:**
```
[COCAPN.AI logo with subtle glow]

YOUR AI AGENT. YOUR REPO. YOUR RULES.

Fork a template. Add your API key. Your agent is alive in 5 minutes.

[Primary CTA Button: "Create Your Agent →" with gentle breathing animation]

[Secondary CTA: "Watch 90-second demo" with play icon]

[Social proof ribbon:]
"1,243 agents deployed this week • 4.8/5 stars from 892 developers"
```

**Below the Fold:**
- Three-column value prop:
  1. **Fork & Go:** "Choose from 50+ specialized templates"
  2. **BYOK Security:** "Your keys, your control. Never stored."
  3. **Living Code:** "Agents evolve in your repo, learning daily"
- Live agent counter: "Right now, 342 agents are helping their humans"
- Trust badges: GitHub OAuth, SOC2 compliant, open-source core

**Interaction:**
- Hover over "Create Your Agent" → button expands slightly with a soft glow
- Click → smooth transition to template browser with page curl effect

---

## **2. TEMPLATE BROWSER: Choose Your Companion**

**Layout:**
- Full-screen modal overlay with dark translucent backdrop
- Header: "Choose your agent template" with search bar
- Left sidebar: Filter categories (Productivity, Creative, Technical, Lifestyle, Business)
- Main area: Responsive grid of template cards (3 columns desktop, 2 tablet, 1 mobile)

**Template Card Design:**
```
┌─────────────────────────────────┐
│ [Animated GIF preview: agent in │
│  action, 3-second loop]         │
├─────────────────────────────────┤
│ **PersonalLog**                 │
│ ⭐⭐⭐⭐⭐ (128)                 │
│                                 │
│ Your digital memory companion.  │
│ Logs thoughts, learns patterns, │
│ reminds you of what matters.    │
│                                 │
│ [Tags: #journal #memory #daily] │
│                                 │
│ [Secondary Button: "Preview"]   │
│ [Primary Button: "Use This →"]  │
└─────────────────────────────────┘
```

**Featured Templates:**
1. **MakerLog:** For builders and creators. Tracks projects, manages tasks, gives daily focus.
2. **DMLog:** Deep research assistant. Reads papers, summarizes, connects ideas.
3. **BusinessLog:** Executive assistant. Handles emails, schedules, meeting prep.
4. **FishingLog:** Niche example showing customization. Tracks catches, weather, gear.

**Interaction:**
- Hover card → GIF plays, card lifts with soft shadow
- Click "Preview" → expands to show detailed features, example conversations
- Click "Use This Template" → elegant slide transition to BYOK setup

---

## **3. BYOK SETUP: Your Keys, Your Control**

**Three-Step Wizard Design:**
- Progress indicator at top: ○○○ → ●○○ → ○●○ → ○○●
- Each step appears as a focused card that slides in from right

**Step 1: Choose Your LLM Provider**
```
┌─────────────────────────────────────────┐
│ Step 1: Pick your brain                 │
│ Which LLM should power your agent?      │
├─────────────────────────────────────────┤
│ [Provider Cards Grid]                   │
│                                         │
│ ┌─────────┐ ┌─────────┐ ┌─────────┐    │
│ │ OpenAI  │ │Anthropic│ │ Google  │    │
│ │ GPT-4o  │ │Claude 3 │ │Gemini 2.0│   │
│ │         │ │         │ │         │    │
│ │ Fast    │ │ Smart   │ │ Balanced│    │
│ │ $0.01/1M│ │ $0.03/1M│ │ $0.02/1M│    │
│ │✅Free $5 │ │✅Free $5│ │✅Free $20│   │
│ └─────────┘ └─────────┘ └─────────┘    │
│                                         │
│ [4 more cards: DeepSeek, Groq, Ollama, │
│  z.ai GLM - scroll horizontally]       │
└─────────────────────────────────────────┘
```

**Step 2: Enter Your API Key**
```
┌─────────────────────────────────────────┐
│ Step 2: Secure connection               │
│ We never store your keys. Ever.         │
├─────────────────────────────────────────┤
│                                         │
│ Selected: OpenAI                        │
│                                         │
│ [Input field with OpenAI logo]          │
│ ┌─────────────────────────────────┐    │
│ │ sk-...                          │ 👁️ │
│ └─────────────────────────────────┘    │
│ [Toggle: Show/hide key]                │
│                                         │
│ [Test Button: "Verify Key"]            │
│                                         │
│ [Status: ○ Waiting...]                 │
│                                         │
│ [Small text: "We'll make a tiny test   │
│  call to verify access. No charges."]  │
└─────────────────────────────────────────┘
```

**Key Verification Flow:**
- User clicks "Verify Key" → button transforms to loading spinner
- Success: Green checkmark, "✅ Key verified! Access confirmed."
- Failure: Gentle shake animation, red "❌ Invalid key. Please check and try again."
- Tooltip on hover: "We test with a simple 'hello' completion to verify"

**Step 3: Name Your Agent**
```
┌─────────────────────────────────────────┐
│ Step 3: Give it personality             │
│ What's your agent's name and style?     │
├─────────────────────────────────────────┤
│                                         │
│ Agent Name:                             │
│ ┌─────────────────────────────────┐    │
│ │                                 │    │
│ └─────────────────────────────────┘    │
│ [Live preview: "Hi! I'm ________"]     │
│                                         │
│ Personality Preset (optional):          │
│ [Pill buttons: Formal • Casual • Snarky│
│  • Warm • Technical • Creative]         │
│                                         │
│ [Info: "This sets the initial tone.    │
│  Your agent will evolve beyond this."]  │
│                                         │
│ [Primary CTA: "Deploy My Agent 🚀"]    │
└────────────────────────────────────────