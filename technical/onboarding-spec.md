> *Gemini 2.5 Pro*

Of course. Here is a comprehensive 3000+ word specification for the cocapn.ai onboarding flow, building upon the provided structure with detailed descriptions, technical considerations, and user experience rationale.

***

# The 5-Minute Agent: Cocapn Onboarding Specification

## 1.0 Introduction & Guiding Principles

### 1.1 Vision
The Cocapn onboarding experience, dubbed "The 5-Minute Agent," is the cornerstone of our user acquisition and activation strategy. Its primary goal is to guide a new user from landing on our homepage to interacting with their own live, personalized, and self-hosted AI agent in under five minutes. This rapid time-to-value is designed to create a "magic moment" that demonstrates the power and simplicity of the Cocapn platform, converting curiosity into active engagement.

### 1.2 Core Philosophy
The entire flow is built on three pillars:

1.  **Speed:** Every step is optimized for minimal friction and cognitive load. We pre-fill, suggest, and automate wherever possible. The user should always feel like they are making meaningful progress.
2.  **Ownership:** The tagline "Your AI Lives in Git" is not just marketing; it's a technical reality we must deliver on. The end product is a repository in the user's own GitHub account and a deployment under their control, fostering a sense of ownership and trust.
3.  **Personalization:** The agent shouldn't feel generic. Through simple choices like name, personality, and avatar, the user forms an immediate connection with their creation, making the final product more valuable and sticky.

### 1.3 Target Audience
This flow is designed for developers, tech-savvy creators, and hobbyists who are familiar with the concepts of Git and APIs but desire a streamlined, "it just works" experience for deploying AI agents. The flow caters to both beginners (with sensible defaults and a "skip" option for BYOK) and power users (with options like custom `SOUL.md` and direct LLM API access).

---

## 2.0 Onboarding Flow: A Phased Breakdown

The user journey is divided into six distinct, time-boxed phases. A progress indicator will be visible at the top of the screen from Phase 2 through Phase 5, showing the user exactly where they are in the process (e.g., `[Template] -> [Customize] -> [Connect] -> [Deploy]`).

### PHASE 1: LANDING (Time: 0:00 - 0:30)

**Objective:** Immediately communicate the core value proposition and provide clear, distinct paths for different user intentions.

**User Experience (UX) Flow:**
The user arrives at `cocapn.ai`. The page loads instantly, presenting a clean, modern interface. Above the fold, the user's attention is captured by a dynamic headline and a visually engaging animation. Three clear call-to-action (CTA) buttons are presented without ambiguity, allowing the user to self-segment their journey.

**Wireframe Description:**
A full-width, single-page layout.
*   **Navigation Bar:** Simple, with "Cocapn" logo on the left, and "Docs," "Pricing," and "Login" on the right.
*   **Hero Section (Center Aligned):**
    *   **Headline (H1):** Large, bold text: **"Your AI Lives in Git."**
    *   **Sub-headline (p):** "Create, customize, and deploy a personal AI agent to your own infrastructure in minutes."
    *   **Animated Visual:** A looping, high-fidelity animation sits below the sub-headline. It visually depicts the concept: A GitHub Octocat logo morphs into a network of code lines, which then flow into a stylized brain/AI icon. This icon then connects to a Cloudflare logo. This visually reinforces the "Git -> AI -> Cloud" pipeline.
*   **CTA Group:** Three distinct buttons are horizontally aligned below the animation.
    1.  **'Create Agent' (Primary):** Solid, brand-colored button with a subtle hover effect. This is the main entry point to the 5-minute flow.
    2.  **'Browse Templates' (Secondary):** A ghost button (outline style). For users who want to explore possibilities before committing. This links directly to Phase 2.
    3.  **'Try Demo' (Tertiary):** A simple text link with an arrow icon (`->`). For users who want to see the end product immediately. This opens a pre-configured agent in a new tab.
*   **Below the Fold:**
    *   **Social Proof Bar:** A simple, full-width banner with the text: **"🚀 10,000+ agents deployed by creators like you."** (This will be a static number initially, updated manually, and later tied to a live metric).
    *   **Stack Animation Section:** A more detailed, slower-paced animated diagram. It shows four layers with descriptive text:
        *   **Layer 1: Your GitHub Repo:** "Full ownership and version control. Your agent's soul and memory live here."
        *   **Layer 2: Cocapn Orchestrator:** "Our engine handles the build, deployment, and configuration seamlessly."
        *   **Layer 3: Cloudflare Workers:** "Globally distributed, serverless execution for instant response times."
        *   **Layer 4: Your LLM Provider:** "Connect any major LLM with your own API key. You control the cost and model."

**Technical Considerations:**
*   The page must be statically generated for maximum performance (Lighthouse score >95).
*   The animation should be a lightweight Lottie file or a well-optimized video/canvas animation to avoid impacting load times.
*   The 'Try Demo' link points to a sandboxed, read-only instance of a showcase agent.

---

### PHASE 2: TEMPLATE SELECTION (Time: 0:30 - 1:00)

**Objective:** Reduce the "blank canvas" problem by providing attractive, functional starting points. Allow for quick discovery and evaluation.

**UX Flow:**
Upon clicking "Create Agent" or "Browse Templates," the user is taken to a gallery of agent templates. They can immediately see a variety of options with clear labels. They can filter the options or click on any card to see a live, interactive preview in a modal window without leaving the page. This instant feedback loop is crucial for building confidence and momentum.

**Wireframe Description:**
A two-column layout.
*   **Left Sidebar (Filters):**
    *   **Search Bar:** "Search templates..."
    *   **Category Filters (Checkboxes):**
        *   `[ ] Personal` (e.g., Journaling Assistant, Recipe Generator)
        *   `[ ] Professional` (e.g., Code Reviewer, Meeting Summarizer)
        *   `[ ] Creative` (e.g., Story Co-writer, Poem Generator)
        *   `[ ] Gaming` (e.g., D&D Dungeon Master, Game Lore Expert)
        *   `[ ] Education` (e.g., Socratic Tutor, Language Partner)
        *   `[ ] Productivity` (e.g., Task Prioritizer, Rubber Duck Debugger)
    *   **Difficulty Filters (Radio Buttons):**
        *   `(•) All`
        *   `( ) Beginner` (No code changes needed)
        *   `( ) Intermediate` (Requires minor config tweaks)
*   **Right Content Area (Template Grid):**
    *   A responsive grid of template cards (3-4 per row on desktop, 2 on tablet, 1 on mobile).
    *   **Template Card:**
        *   **Preview Screenshot/GIF:** A visually appealing image or short animated GIF showing the agent in action.
        *   **Card Body:**
            *   **Name (H3):** "Code Review Sidekick"
            *   **Description (p):** "An AI agent that reviews your pull requests, suggests improvements, and explains complex code."
            *   **Badges/Tags (bottom of card):** Small pills for `Use Case: Professional`, `Difficulty: Beginner`.
        *   **Hover Effect:** On hover, the card subtly lifts, and a "Preview" button appears in the center of the image.
*   **Preview Modal:**
    *   When a card is clicked, a large modal window overlays the screen.
    *   **Modal Header:** "Preview: Code Review Sidekick" with an 'X' to close.
    *   **Modal Body:** An `<iframe>` containing a live, interactive demo of the selected agent. The user can chat with it directly within the modal.
    *   **Modal Footer:** A prominent, primary CTA button: **"Select this Template"**.

**Technical Considerations:**
*   Template metadata (name, description, tags, screenshot URL, demo URL) will be fetched from a JSON file or a lightweight API endpoint.
*   The filtering logic will be handled client-side in JavaScript for instant responsiveness.
*   The `<iframe>` demos must be hosted and ready. They should be slightly rate-limited to prevent abuse.
*   The user's template choice is stored in the session/local state to be used in subsequent steps.

---

### PHASE 3: CUSTOMIZATION (Time: 1:00 - 2:00)

**Objective:** To imbue the agent with a unique identity, fostering a sense of personal creation and attachment.

**UX Flow:**
After selecting a template, the user moves to a clean, single-purpose screen focused on personalization. The form is simple and visually driven. It feels less like configuration and more like character creation. Advanced options are available but de-emphasized to maintain speed.

**Wireframe Description:**
A single-column, centered form layout with a maximum width of ~700px. The progress bar at the top now highlights "Customize".
*   **Section 1: The Basics**
    *   **Label:** "Name your agent"
    *   **Text Input:** A large, friendly text input field. Placeholder text dynamically suggests names based on the chosen template (e.g., if "Code Reviewer" was chosen, it might suggest "Codey", "PR-Bot", "ShipIt").
*   **Section 2: Personality**
    *   **Label:** "Choose a personality"
    *   **Preset Selector:** A set of 5 visually distinct radio buttons, each with an icon and a short description.
        *   `[Icon] Professional:` "Formal, concise, and logical. Gets straight to the point."
        *   `[Icon] Friendly:` "Casual, warm, and empathetic. Uses emojis and a conversational tone."
        *   `[Icon] Creative:` "Playful, verbose, and imaginative. Enjoys tangents and brainstorming."
        *   `[Icon] Technical:` "Precise, detailed, and structured. Prefers facts and data."
        *   `[Icon] Philosophical:` "Thoughtful, deep, and questioning. Explores the 'why'."
    *   **Advanced Customization (Accordion):** A collapsed accordion link titled "Fine-tune with Sliders". When expanded, it reveals sliders for traits like `Formality`, `Verbosity`, `Humor`, etc. This is for power users and doesn't clutter the main flow.
*   **Section 3: Appearance**
    *   **Label:** "Pick an avatar"
    *   **Emoji Grid:** A button that says "Select Emoji" which opens a popover containing a 10x10 grid of 100 curated, high-quality emojis. The selected emoji is displayed next to the button.
    *   **Label:** "Select a color theme"
    *   **Color Swatches:** A row of 6 circular color swatches (amber, blue, green, purple, pink, slate). Clicking one selects it and shows a checkmark.
*   **Section 4: The Soul (Optional)**
    *   **Label:** "Optional: Upload a `SOUL.md` file"
    *   **File Input:** A standard file upload box with a description: "For advanced personalization, upload a Markdown file defining your agent's core principles, knowledge, and persona. [Learn More]" The link opens docs in a new tab.
*   **Footer:** A single, large primary CTA: **"Next: Connect your LLM"**.

**Technical Considerations:**
*   All selections (name, personality preset, emoji, color) are added to the session state object.
*   The personality presets correspond to pre-written system prompts. The "Friendly" preset might start with "You are a friendly and helpful assistant...", while "Technical" might start with "You are a precise technical expert...".
*   The chosen color theme will set a CSS variable in the final deployed agent's UI.
*   If a `SOUL.md` is uploaded, its content is read and stored, ready to be committed to the forked Git repository.

---

### PHASE 4: BYOK (Bring Your Own Key) SETUP (Time: 2:00 - 3:00)

**Objective:** Empower the user to connect their preferred Large Language Model (LLM) provider, giving them control over cost, performance, and features.

**UX Flow:**
The user is presented with a clear, visual selection of supported LLM providers. Clicking a provider reveals the API key input. A built-in test function provides immediate validation and feedback. The distinction between "Proxy" and "Direct" modes is explained simply. A "skip" option is available for those who just want to try the platform with a limited, free-tier model.

**Wireframe Description:**
A centered layout, similar to the previous step. The progress bar highlights "Connect".
*   **Header (H2):** "Connect a Language Model"
*   **Sub-header (p):** "Your agent needs a brain. Bring your own API key for full control."
*   **Provider Selection Grid:** A grid of clickable cards, each featuring the provider's logo and name.
    *   Cards: OpenAI (GPT-4, GPT-3.5), Anthropic (Claude), Google (Gemini), DeepSeek, Groq, Ollama (local), z.ai GLM.
    *   When a provider is clicked, its card gets a highlighted border, and the API key input form appears below the grid.
*   **API Key Input Form (appears on selection):**
    *   **Label:** "Enter your [Selected Provider] API Key"
    *   **Input Field:** A text input of type `password` with a show/hide (eye icon) toggle.
    *   **Test Button:** A button next to the input labeled **"Test Key"**.
        *   **States:** Default -> `Testing...` (with spinner) -> `✓ Verified!` (green text) -> `✗ Invalid Key` (red text).
*   **Mode Selection:**
    *   Two radio buttons with detailed descriptions.
    *   `(•) Proxy Mode (Recommended):` "Your API key is stored securely as a secret in your Cloudflare deployment. All requests from the chat UI go through your serverless backend. This is the most secure and robust option."
    *   `( ) Direct Mode:` "Your API key is stored in the user's browser `localStorage`. Requests go directly from the browser to the LLM provider. Simpler, but less secure and may be blocked by CORS."
*   **Skip Option:**
    *   A small text link at the bottom: "Don't have a key? **Skip and use the limited Cocapn model**."
*   **Footer:** A primary CTA, initially disabled. It becomes enabled once a key is successfully verified OR the skip option is chosen. **"Next: Deploy Agent"**.

**Technical Considerations:**
*   The "Test Key" button triggers a backend API call to a Cocapn endpoint.
*   This endpoint receives the key and provider type. It then makes a simple, low-cost API call (e.g., a `chat.completions` request with `max_tokens: 5`) to the actual LLM provider to validate the key.
*   **Crucially, Cocapn's backend must NOT store the user's API key.** It is used only for this single verification request and then discarded. The key is held in the frontend state until the deploy phase, where it's passed to the deployment engine to be set as a Cloudflare Worker secret.
*   The "limited Cocapn model" would be a shared, heavily rate-limited key managed by Cocapn, suitable only for light testing.

---

### PHASE 5: DEPLOY (Time: 3:00 - 4:00)

**Objective:** The "magic moment." To seamlessly and automatically transform the user's configuration into a live, publicly accessible web application.

**UX Flow:**
This is the simplest step for the user: they click one button. The system takes over, showing a clear progress bar and status updates. The user watches their agent come to life. The culmination is a success message and the agent's unique URL.

**Wireframe Description:**
A single-view screen focused entirely on the deployment process. The progress bar highlights "Deploy".
*   **Initial State:**
    *   A large, centered icon (e.g., a rocket ship).
    *   **Header (H2):** "Ready to Launch!"
    *   **Description (p):** "We will now fork the template to your GitHub account, apply your customizations, and deploy it to Cloudflare's global network."
    *   **Primary CTA:** A large button with the GitHub logo: **"Authorize with GitHub & Deploy"**.
*   **In-Progress State (replaces the initial state after click):**
    *   **Header (H2):** "Deployment in Progress..."
    *   **Progress Bar:** A prominent, animated progress bar (e.g., 0% -> 100%).
    *   **Status Log:** A list of steps that get a checkmark as they complete.
        *   `[Spinner] Authenticating with GitHub...` -> `[✓] Authenticated as [username]`
        *   `[Spinner] Forking template repository...` -> `[✓] Created repo [username]/[agent-name]`
        *   `[Spinner] Applying customizations...` -> `[✓] Customizations committed`
        *   `[Spinner] Setting up Cloudflare deployment...` -> `[✓] Cloudflare project configured`
        *   `[Spinner] Deploying to the edge... (This may take a minute)` -> `[✓] Deployment live!`
*   **Success State (replaces the in-progress state):**
    *   **Icon:** A large green checkmark or confetti animation.
    *   **Header (H2):** "Your Agent is Live!"
    *   **URL Display:** A read-only input field containing the deployed URL (`https://agent-name-xyz.pages.dev`) with a "Copy" button next to it.
    *   **Primary CTA:** **"Chat with your Agent"** (opens the URL in a new tab).
    *   **Secondary Action:** A "View on GitHub" link.

**Technical Considerations:**
*   This phase is the most backend-intensive.
*   **GitHub OAuth:** Clicking the deploy button initiates the GitHub OAuth2 flow. We will request `repo` scope.
*   **Backend Orchestration:** Upon successful OAuth, a backend service will:
    1.  Use the GitHub API token to fork the base template repository to the user's account.
    2.  Programmatically commit the user's configuration files (`config.json` with name, color, emoji; the `SOUL.md` file) to the new repository.
    3.  Use the Cloudflare API to create a new Pages/Workers project linked to the user's new GitHub repository.
    4.  Set the user's LLM API key (retrieved from the frontend state) as an encrypted environment variable (secret) in the Cloudflare project settings.
    5.  Trigger the first deployment.
    6.  Poll the Cloudflare API for the deployment status and the final live URL.
    7.  Return the URL to the frontend to display in the success state.

---

### PHASE 6: FIRST CHAT (Time: 4:00 - 5:00)

**Objective:** Immediately close the feedback loop. The user interacts with the agent they just created, solidifying the sense of accomplishment and value.

**UX Flow:**
The user is automatically redirected (or clicks the link) to their new agent's URL. The agent's interface loads, styled with their chosen color theme and displaying their chosen emoji avatar. The agent initiates the conversation with a personalized greeting. Helpful prompts and tooltips guide their first interaction.

**Wireframe Description:**
This is the UI of the deployed agent itself, not the Cocapn onboarding site.
*   **Layout:** A standard chat interface.
    *   **Header:** Shows the agent's emoji avatar and name (e.g., "🤖 Codey").
    *   **Chat Window:** The main area for conversation history.
    *   **Input Area:** A text input field with a "Send" button.
*   **First-Run Experience Elements:**
    *   **Initial Message:** The first message in the chat is from the agent: "Hi [User's Name]! I'm [Agent's Name], the agent you just created. How can I help you today?"
    *   **Suggested Prompts:** Above the text input, there are 3-4 buttons with example prompts tailored to the template. For a "Code Reviewer" agent, they might be: "Review this Python function for bugs," "Can you explain this regex?", "What's a more efficient way to write this loop?".
    *   **Tutorial Tooltips:** On the first load, a sequence of dismissible tooltips (e.g., using a library like Shepherd.js) will appear:
        1.  Points to the input box: "Type your first message here!"
        2.  Points to the suggested prompts: "Or click one of these to get started."
        3.  Points to a "Share" icon in the header: "Click here to get a shareable link to your agent!"
*   **Share Functionality:** A share button opens a modal with a link that friends/colleagues can use to chat with the agent.

**Technical Considerations:**
*   The user's name (e.g., "Casey") can be passed from the Cocapn onboarding success page to the agent URL as a query parameter (`?first_visit_name=Casey`) to enable the personalized greeting. This is stored in `localStorage` for subsequent visits.
*   The suggested prompts are defined in the template's configuration file.
*   The tutorial tooltip state (`has_seen_tutorial`) is stored in the user's `localStorage` on the agent's domain to prevent it from showing up on every visit.

---

## 3.0 Cross-Cutting Concerns

### 3.1 Mobile Considerations
The entire flow must be flawlessly responsive and touch-friendly.
*   **Phase 1 (Landing):** The 3 CTAs will stack vertically. The hero section will be optimized for a portrait view.
*   **Phase 2 (Templates):** The filter sidebar will become a "Filter" button that opens a bottom sheet or full-screen modal. The template grid will be a single column.
*   **Phase 3 & 4 (Forms):** All form controls will be large and easy to tap. Popovers like the emoji picker will cover the full screen.
*   **Phase 5 (Deploy):** The progress view will be formatted for a vertical screen, with status messages clearly legible.
*   **PWA Prompt:** Upon successful deployment (Phase 5 Success Screen), a prompt to "Add to Home Screen" will be triggered, encouraging users to save their agent as a Progressive Web App for easy access.

### 3.2 Error Handling
Graceful failure is as important as successful execution.
*   **GitHub OAuth Failure:** If the user denies access or the OAuth flow fails, display a message: "Authentication with GitHub failed. You can try again, or follow these instructions to clone the repository and set it up manually." Provide a link to documentation.
*   **Deploy Failure:** If the Cloudflare deployment fails for any reason (e.g., API outage, invalid repo state), the progress screen will show a clear error state: "Deployment Failed." A "Retry" button will be available, along with a "View Logs" accordion that shows the raw, verbose output from the deployment process for debugging.
*   **BYOK Test Failure:** If the API key test fails, the message will be specific: "Verification failed. Please check that your API key is correct and has sufficient credits." It will not lock the user out; they can edit the key and try again, or select a different provider.
*   **General Principle:** All error messages will be human-readable, explain what went wrong (not just "Error 500"), and provide a clear next step or a way to get help.

### 3.3 Metrics to Track
To measure the effectiveness of the onboarding flow, we will track the following key metrics:
*   **Funnel Conversion Rate:** Percentage of users who complete each phase.
    *   `Phase 1 -> Phase 2` (Landed -> Started Creation)
    *   `Phase 2 -> Phase 3` (Template Selected)
    *   `Phase 3 -> Phase 4` (Customized)
    *   `Phase 4 -> Phase 5` (LLM Connected)
    *   `Phase 5 -> Phase 6` (Successfully Deployed)
*   **Time to First Chat (TTFC):** The median and average time from starting Phase 2 to the agent being live. Our North Star metric is a median TTFC of < 5 minutes.
*   **Drop-off Analysis:** Identify which phase has the highest drop-off rate to pinpoint areas for improvement. (e.g., a high drop-off at Phase 4 might indicate users struggle with finding API keys).
*   **Template Popularity:** A ranking of which templates are selected most often.
*   **Provider Distribution:** A breakdown of which LLM providers are most commonly connected.
*   **Completion Rate by Platform:** Compare the full funnel completion rate for mobile vs. desktop users.
*   **Activation Rate:** Percentage of users who deploy an agent and then send at least 3 messages to it within their first 24 hours.