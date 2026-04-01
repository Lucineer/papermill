> *Experiment: DeepSeek Chat*

# **Meeting Simulation System for BusinessLog.ai**

## **System Overview**
The Meeting Simulation System creates a persistent digital ecosystem where human participants interact with AI-powered digital twins before, during, and after meetings. Each twin learns from its human counterpart's communication patterns, knowledge domain, and decision-making style, creating a dynamic simulation layer over traditional meeting workflows.

---

## **Feature 1: DIGITAL TWINS IN MEETINGS**
**UX Flow:**
1. **Onboarding**: User completes a 2-week calibration period where twin observes meetings, reads past communications, and analyzes documents the user has authored/approved.
2. **Meeting Interface**: In the meeting view, each participant (human or twin) appears as an avatar with status indicators (Human Present, Twin Active, Twin Representing Absent Human).
3. **Twin Activation**: Click any participant's avatar → "Activate Twin" to have twin join meeting alongside human or represent absent colleague.

**What AI Does:**
- Maintains individual twin models with:
  - Communication style embeddings (verbosity, formality, humor)
  - Knowledge graph of domains and expertise
  - Decision-making patterns (data-driven vs. intuitive)
  - Relationship mappings with other twins
- Generates responses using constrained LLM with persona parameters
- Updates twin model after each real interaction

**What Human Does:**
- Reviews twin's proposed contributions before sending (toggle: Auto/Manual mode)
- Rates twin's accuracy post-meeting ("This sounded like me" 1-5 scale)
- Periodically reviews and corrects twin's knowledge base

**Data Structures:**
```json
{
  "twin_id": "usr_123_twin",
  "human_id": "usr_123",
  "persona_parameters": {
    "communication_style": {"formality": 0.7, "detail_level": 0.8},
    "decision_weights": {"data": 0.6, "intuition": 0.3, "consensus": 0.1}
  },
  "knowledge_graph": {
    "domains": [{"domain": "product_design", "confidence": 0.9}],
    "relationships": [{"with_twin": "usr_456_twin", "trust_level": 0.8}]
  },
  "context_window": ["last_10_meetings", "recent_documents"]
}
```

---

## **Feature 2: MEETING REHEARSAL**
**UX Flow:**
1. **Setup**: User selects meeting type (client pitch, internal review, brainstorming).
2. **Cast Selection**: Drag-and-drop participants (humans/twins) into virtual meeting room.
3. **Scenario Configuration**: Define agenda, key objectives, potential objections.
4. **Run Simulation**: Twins interact based on their models and meeting context.
5. **Review**: Timeline view shows key moments, objections raised, alternative paths.

**What AI Does:**
- Twins simulate realistic objections based on:
  - Historical patterns of actual participants
  - Domain-specific risk factors
  - Relationship dynamics between participants
- Generates "what-if" branching scenarios
- Provides simulation metrics: success probability, risk areas, preparation gaps

**What Human Does:**
- Adjusts simulation parameters (aggressive vs. cooperative modes)
- Interjects during simulation to test responses
- Bookmarks critical moments for preparation
- Shares simulation highlights with team

**Data Structures:**
```json
{
  "simulation_id": "sim_123",
  "scenario": {
    "meeting_type": "client_pitch",
    "agenda": ["intro", "demo", "pricing"],
    "participants": ["usr_123_twin", "usr_456_twin"],
    "success_criteria": ["client_asks_for_demo", "no_pricing_objections"]
  },
  "outcomes": [
    {
      "timestamp": "00:05:23",
      "event": "objection_raised",
      "by": "usr_456_twin",
      "objection_type": "pricing_concern",
      "branching_paths": ["deflect", "acknowledge", "compromise"]
    }
  ]
}
```

---

## **Feature 3: REAL-TIME IDEA SPAWNING**
**UX Flow:**
1. **During Meeting**: User highlights off-track but valuable topic in transcript.
2. **Spawn Workshop**: Clicks "Workshop This" → creates sidebar workspace.
3. **Parallel Processing**: Repo-agent appears in sidebar, analyzing topic while meeting continues.
4. **Results Ready**: Notification appears when repo-agent has synthesized ideas.
5. **Reintegration**: User reviews findings, can inject back into meeting or schedule follow-up.

**What AI Does:**
- Repo-agent performs:
  - Rapid research across connected knowledge bases
  - Cross-referencing with past decisions and similar contexts
  - Generating structured options with pros/cons
  - Identifying subject matter experts (human or twin)
- Maintains context link to original discussion point

**What Human Does:**
- Flags topics for workshopping (voice command or click)
- Sets parameters for repo-agent ("Find technical constraints," "Identify stakeholders")
- Reviews and curates output before sharing
- Decides reintegration timing

**Data Structures:**
```json
{
  "workshop_id": "wrk_123",
  "parent_meeting": "mtg_456",
  "spawn_context": {
    "timestamp": "00:23:45",
    "topic": "mobile_app_performance_issues",
    "trigger": "off_track_but_important"
  },
  "repo_agent": {
    "task": "research_and_synthesize",
    "parameters": {"depth": "technical_constraints", "output_format": "options_matrix"},
    "sources_consulted": ["past_incidents", "engineering_docs", "competitor_analysis"],
    "output": {
      "key_findings": ["...", "..."],
      "recommended_actions": ["...", "..."],
      "experts_to_consult": ["usr_789", "usr_012_twin"]
    }
  }
}
```

---

## **Feature 4: ASYNCHRONOUS IDEAS**
**UX Flow:**
1. **Voice Capture**: User speaks idea to personal device: "Hey BL, what if we..."
2. **Private Simulation**: Twin runs quick mental simulation:
   - Visual feedback shows idea playing out (green = promising, red = problematic)
   - Shows predicted objections, missing data, potential supporters
3. **Decision Point**: Interface asks: "Send to your twin for sharing? Yes/Refine/Discard"
4. **If Sent**: Twin queues idea for appropriate context (next meeting, async channel)

**What AI Does:**
- Runs micro-simulations using:
  - Organizational politics model
  - Historical precedent database
  - Current initiative alignment
- Predicts reception from key stakeholders
- Suggests refinements to increase success probability
- Tags idea with metadata for later retrieval

**What Human Does:**
- Voices raw, unfiltered ideas
- Reviews simulation results
- Makes final send/refine/discard decision
- Can request deeper analysis before deciding

**Data Structures:**
```json
{
  "idea_id": "idea_123",
  "content": "What if we implemented a gamified onboarding system?",
  "state": "private_simulation",
  "simulation_results": {
    "predicted_reception": {
      "engineering": 0.8,
      "sales": 0.9,
      "legal": 0.3
    },
    "potential_objections": ["privacy_concerns", "development_cost"],
    "missing_data": ["existing_solutions", "roi_projections"],
    "refinement_suggestions": ["start_with_non_pii_data", "pilot_with_sales_team"]
  },
  "disposition": "sent_to_twin_for_sharing",
  "target_context": "next_product_meeting"
}
```

---

## **Feature 5: PULL REQUEST MEETINGS**
**UX Flow:**
1. **Passive Monitoring**: Twins listen to meetings they're not in, scanning for relevance.
2. **PR Surfacing**: When twin detects relevant discussion, it appears as notification: "Your idea about X is being discussed in Design Review."
3. **Context Injection**: User can have twin join briefly to provide context or can send async comment.
4. **Network Effect**: System maps idea connections across meetings, showing idea evolution.

**What AI Does:**
- Continuously analyzes all meeting transcripts
- Maintains idea genealogy graph
- Calculates relevance scores using semantic similarity
- Manages permissions and privacy boundaries
- Notifies when ideas reach decision points

**What Human Does:**
- Sets notification preferences (always notify, working hours only, high relevance only)
- Authorizes twin to share specific context
- Reviews idea lineage reports weekly
- Can "follow" ideas like social media posts

**Data Structures:**
```json
{
  "idea_lineage": {
    "root_idea": "idea_123",
    "generations": [
      {
        "meeting": "design_review_2024_04_15",
        "modifications": ["added_mobile_component"],
        "contributors": ["usr_456_twin", "usr_789"]
      }
    ],
    "current_state": "under_discussion",
    "related_prs": ["pr_890", "pr_891"]
  },
  "relevance_engine": {
    "matching_algorithm": "semantic_similarity_cosine_0.85",
    "triggered_notifications": [
      {
        "meeting": "eng_standup_2024_04_16",
        "relevance_score": 0.92,
        "matched_concepts": ["gamification", "user_onboarding"]
      }
    ]
  }
}
```

---

## **Feature 6: SAFE TO ASK**
**UX Flow:**
1. **Question Formation**: During meeting, user whispers to twin (voice or text): "Ask about the timeline assumptions."
2. **Twin Processing**: Twin rephrases for clarity, checks if question was already answered.
3. **Contextual Delivery**: Twin either:
   - Answers privately if information exists in knowledge base
   - Suggests asking now ("This is a good time to ask about assumptions")
   - Recommends asking later ("Wait until budget discussion")
4. **Anonymity Option**: User can have twin ask anonymously on their behalf.

**What AI Does:**
- Maintains real-time meeting context
- Tracks what questions have been asked/answered
- Assesses question appropriateness (sensitivity, timing, redundancy)
- Provides private answers when possible
- Manages anonymity layer when requested

**What Human Does:**
- Formulates questions naturally
- Reviews twin's rephrasing
- Chooses delivery method and timing
- Rates answer quality post-meeting

**Data Structures:**
```json
{
  "question_session": {
    "original_question": "timeline assumptions",
    "rephrased": "Could you clarify the assumptions behind the Q3 delivery timeline?",
    "context": {
      "meeting_topic": "product_roadmap",
      "speaker": "cto",
      "just_covered": "resource_allocation"
    },
    "processing": {
      "knowledge_base_check": "partial_answer_available",
      "timing_assessment": "appropriate_now",
      "sensitivity_score": 0.2,
      "similar_questions_asked": 0
    },
    "delivery": {
      "method": "twin_asks_publicly