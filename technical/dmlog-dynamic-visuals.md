> *Experiment: Gemini 3.1 Pro*

Here is the comprehensive system design for the **DMLog.ai Dynamic Visual Generation System**, adapting the OpenMAIC visual narrative concept for real-time, stateful TTRPG environments.

---

# System Architecture: DMLog.ai Visual Engine

The core philosophy of this system is **Stateful Visual Continuity**. Unlike standard text-to-image generators that treat every prompt in a vacuum, DMLog treats image generation as a rendering of a continuously updating database (the Scene State).

## 1. Scene State Tracking (The Core Engine)
The system maintains a running JSON object representing the current physical reality of the game world. As the LLM processes the session transcript (Speech-to-Text), it does not generate image prompts directly; instead, it generates **State Diffs** (updates to the JSON).

*   **Persistent Elements:** Base geography, time of day, weather.
*   **Transient Elements:** Spell effects (fireballs), character poses, temporary items.
*   **Accumulated Modifiers:** Bloodstains, scorch marks, broken furniture, corpses.
*   *Example:* Narrative: *"I cleave the goblin in two, blood spraying across the tavern tables."* -> State Diff: `{"entities": [{"id": "gob_1", "status": "dead", "pose": "cleaved"}], "environment_modifiers": ["blood-splattered tables"]}`.

## 2. Visual Consistency (Codex System)
To prevent the "hallucination mutation" common in GenAI, DMLog uses a **Codex System**.
*   **Character Codex:** When an NPC or PC is introduced, the system generates a base reference image and a highly specific descriptive string (e.g., *"Tall half-orc, braided green hair, scarred left eye, wearing rusted chainmail"*). Gemini 2.5 Flash Image uses this exact string + a fixed generation seed for all subsequent appearances.
*   **Location Codex:** The first time a location is visited, a base background is generated. Subsequent slides in the same location use **Image-to-Image (img2img)** prompting, passing the base background as a reference to ensure the tavern's layout doesn't change, only the state (e.g., adding broken chairs).

## 3. Mood & Atmosphere Generation
The NLP engine runs sentiment and atmospheric analysis on the narrative block, mapping it to visual style tags.
*   **Lighting:** (e.g., `campfire glow`, `harsh midday sun`, `eerie bioluminescence`).
*   **Color Palette:** (e.g., `warm amber tones`, `desaturated cold blues`, `high contrast`).
*   **Camera Angle:** (e.g., `low angle heroic`, `wide establishing shot`, `intimate close-up`).
*   *Implementation:* These tags are dynamically injected at the very end of the Gemini 2.5 prompt to act as a global style override.

## 4. Dynamic Slide Composition
To ensure speed and accuracy, scenes are composed using a **2.5D Layering Strategy** before being sent to the AI, or generated via structured spatial prompting.
*   **Background (Environment):** Pulled from the Location Codex + Weather/Time modifiers.
*   **Midground (Action/Props):** Corpses, spell effects, NPCs.
*   **Foreground (Focus):** The active player character or speaking NPC.
*   *Prompt Structure for Gemini 2.5:* `[Foreground: {Active Character Codex}] performing [Action] in [Midground: {NPCs/Props}] against [Background: {Location Codex} with {Modifiers}]. [Style: {Mood Tags}].`

## 5. Pre-rendered Asset Library
To guarantee < 3 second latency, DMLog utilizes predictive pre-rendering.
*   **Session Prep:** If the DM uploads notes ("Party will fight goblins in a cave"), the system pre-generates cave backgrounds, goblin tokens, and generic combat poses before the session starts.
*   **Global Library:** Common TTRPG tropes (fireball explosions, standard taverns, rain overlays) are cached. If the API spikes in latency, the system composites pre-rendered assets together as a fallback.

## 6. Real-Time Generation Pipeline
1.  **Audio In:** Whisper STT captures DM/Player speech.
2.  **State Extraction:** Gemini 1.5 Pro (fast text) extracts entities, actions, and state changes.
3.  **Prompt Assembly:** The State Machine compiles the prompt and reference images.
4.  **Generation:** Gemini 2.5 Flash Image generates the slide.
5.  **Delivery:** Pushed via WebSockets to the UI.

## 7. GM Override & Control
The DM is the ultimate arbiter of the visual state.
*   **The Staging Area:** The AI generates the *next* slide in the background. The DM sees it in a "Staging" panel.
*   **Quick Toggles:** The DM UI has quick buttons to alter the state: [Toggle Rain], [Make Night], [Remove Blood], [Hide NPC].
*   **Manual Push:** The DM clicks "Push to Players" to update the main screen, ensuring no bizarre AI hallucinations ruin a dramatic moment.

## 8. Dual UI Design
*   **Player View (Web/Mobile/Cast to TV):** Immersive, full-screen. Slides transition with cinematic effects (slow Ken Burns pans, crossfades). Minimal UI, just the visual and perhaps stylized closed-captions of the narrative.
*   **DM View (Tablet/Laptop):** A dashboard.
    *   *Left:* Live transcript & Session notes.
    *   *Center:* Current Live Slide (what players see).
    *   *Right:* Staging Slide (next generated image) + State Editor (list of current NPCs, weather dropdowns, mood sliders).

---

## Pseudocode: Scene State Machine

This Python-esque pseudocode demonstrates how narrative text updates the state and generates the next visual.

```python
import json

class SceneState:
    def __init__(self):
        self.location = None          # e.g., "The Prancing Pony Tavern"
        self.time_of_day = "Day"
        self.weather = "Clear"
        self.mood = "Neutral"
        self.entities = {}            # Dict of characters/NPCs in scene
        self.environment_modifiers = [] # e.g., ["broken tables", "blood pools"]
        self.base_image_ref = None    # Reference image for visual consistency

    def update_from_narrative(self, transcript_chunk, nlp_engine):
        """Passes transcript to LLM to extract state diffs (JSON)"""
        system_prompt = "Extract state changes from this TTRPG narrative. Return JSON."
        state_diff = nlp_engine.extract_json(system_prompt, transcript_chunk)
        
        # Apply Diffs
        if "location" in state_diff:
            self.change_location(state_diff["location"])
        if "time_of_day" in state_diff:
            self.time_of_day = state_diff["time_of_day"]
        if "weather" in state_diff:
            self.weather = state_diff["weather"]
        if "mood" in state_diff:
            self.mood = state_diff["mood"]
            
        # Update Entities (Add, modify, or kill)
        for entity in state_diff.get("entities", []):
            if entity["id"] not in self.entities:
                self.entities[entity["id"]] = self.fetch_from_codex(entity["name"])
            self.entities[entity["id"]]["action"] = entity.get("action", "standing")
            self.entities[entity["id"]]["status"] = entity.get("status", "alive")
            
        # Add persistent damage/changes to the environment
        if "new_modifiers" in state_diff:
            self.environment_modifiers.extend(state_diff["new_modifiers"])

    def generate_image_prompt(self):
        """Compiles the current state into a structured prompt for Gemini 2.5 Flash"""
        
        # 1. Background Construction
        bg_prompt = f"Background: {self.location}, {self.time_of_day}, {self.weather}."
        if self.environment_modifiers:
            bg_prompt += f" The environment features: {', '.join(self.environment_modifiers)}."

        # 2. Entity Construction (Filter out dead unless specified, or change pose)
        active_entities = []
        corpses = []
        for ent in self.entities.values():
            if ent["status"] == "dead":
                corpses.append(f"dead {ent['desc']} lying on the ground")
            else:
                active_entities.append(f"{ent['desc']} is {ent['action']}")

        midground_prompt = f"Midground: {', '.join(corpses)}." if corpses else ""
        foreground_prompt = f"Foreground Action: {', '.join(active_entities)}."

        # 3. Mood and Style
        style_prompt = f"Style: High quality fantasy digital art, TTRPG style. Mood: {self.mood}. Cinematic lighting."

        # Combine
        final_prompt = f"{foreground_prompt} {midground_prompt} {bg_prompt} {style_prompt}"
        return final_prompt

# --- Execution Loop ---

# Initialize Engine
state_machine = SceneState()
gemini_flash = GeminiImageAPI()

def on_narrative_received(transcript_text):
    # 1. Update the state based on what the DM/Players just said
    state_machine.update_from_narrative(transcript_text, nlp_engine)
    
    # 2. Generate the prompt representing the NEW state
    prompt = state_machine.generate_image_prompt()
    
    # 3. Call Gemini 2.5 Flash Image (passing previous image as structural reference if needed)
    new_slide = gemini_flash.generate(
        prompt=prompt, 
        reference_image=state_machine.base_image_ref,
        aspect_ratio="16:9"
    )
    
    # 4. Send to DM Staging Area for approval
    send_to_dm_ui(new_slide, state_machine)
```