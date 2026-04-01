> *Written by Gemini 2.5 Pro*

# I Know Kung-Fu: Skill Injection for Repo-Native Agents

## Abstract

In the 1999 film *The Matrix*, the protagonist Neo is plugged into a machine and has the entire martial art of kung-fu downloaded directly into his brain. He opens his eyes and utters the iconic line, "I know kung-fu." This moment, a fantasy of instantaneous skill acquisition, serves as a powerful metaphor for the next generation of AI agents. The cocapn fleet, a system of "repo-native" agents, operationalizes this concept through what we term the "I-Know-Kung-Fu" principle. This paper will explore how this principle, based on the forking of specialized template vessels, allows for the rapid creation of domain-expert agents. We will dissect the progressive skill levels these agents achieve, the various methods of skill injection, the critical concept of the "tolerance spectrum," and the emergent intelligence that arises from cross-training within the fleet. Finally, we will propose a new economic model—the fork economy—and a new metric for success, where the value created by the community surpasses that of the original creators.

---

### 1. The I-Know-Kung-Fu Principle

The core challenge in personal AI is the "cold start" problem. A new, generalized AI assistant is a tabula rasa—a blank slate. It has immense potential, but no context, no specialized vocabulary, and no understanding of the user's specific domain. To make it useful for a particular task, such as managing a Dungeons & Dragons campaign or tracking a complex financial portfolio, the user must engage in a protracted and often frustrating process of education, slowly feeding it the requisite knowledge one prompt at a time. This is akin to teaching a person kung-fu by describing each punch and kick individually. It is inefficient and scales poorly.

The "I-Know-Kung-Fu" principle circumvents this problem entirely. It reframes skill acquisition not as a process of slow learning, but as an act of instantaneous transfer. In the cocapn architecture, this is achieved through the relationship between templates and vessels.

*   **A fresh vessel is Neo before the upload.** It is a `cocapn-lite` instance, a generalized large language model with a basic interaction framework. It is capable of reasoning, understanding, and generating language, but it is unskilled. It has no domain-specific knowledge, no memory of past interactions, and no pre-configured purpose. It is pure potential.

*   **A template vessel is the skill file.** A template is a pre-configured, curated vessel that has been primed with the foundational knowledge of a specific domain. A `cooking-template` contains a glossary of culinary terms, common recipe structures, conversion tables, and heuristics for ingredient substitution. A `python-dev-template` is primed with Python syntax, common library APIs, PEP 8 style guidelines, and debugging strategies. These templates are the digital equivalent of the kung-fu program loaded into Neo's mind.

*   **Forking a template is the upload.** When a user wants a specialized agent, they do not start from scratch. Instead, they "fork" a template. This action creates a new, personal vessel that is an exact replica of the template at that moment in time. The user's new `my-cooking-vessel` instantly inherits all the domain knowledge of the `cooking-template`. From its very first interaction, it understands the difference between braising and broiling. This is the "I know kung-fu" moment—instant domain expertise.

However, the analogy holds a crucial nuance. After the upload, Neo still had to fight. He had to apply the theoretical knowledge in a real-world context, adapt to his opponent, and develop his own style. The download was the foundation, not the finished product. Similarly, a forked vessel is not a static expert. The template provides a massive head start, but true mastery is achieved through practice. The vessel must interact with the user, accumulate personal context, learn their preferences, and adapt to their unique workflow. The template is a starting point, a powerful one, but the journey to mastery has just begun.

### 2. Skill Levels: The Path from Novice to Master

The evolution of a vessel from a blank slate to an indispensable partner can be charted across a spectrum of skill levels. Each level represents a significant leap in capability and value, driven by the accumulation of context.

*   **Level 0: Fresh Vessel (`cocapn-lite`)**
    This is the base state: a raw, un-forked agent. It is a blank canvas. It can answer general knowledge questions and perform basic language tasks, but it has no memory between sessions and no specialized skills. It is a powerful tool but a poor collaborator. It is Neo before he even knows what the Matrix is.

*   **Level 1: Template Vessel**
    This is the state immediately after forking a template. The vessel now possesses a robust foundation of domain-specific knowledge. It has the vocabulary, common patterns, and basic heuristics of its field. A Level 1 `legal-vessel` can define "tort" and "subpoena" and knows the basic structure of a legal brief. However, it knows nothing about the user's specific case or jurisdiction. It is a knowledgeable but impersonal textbook.

*   **Level 2: Practiced Vessel**
    After a period of interaction (typically dozens to hundreds of exchanges), the vessel graduates to Level 2. It has moved beyond the generic knowledge of its template and has begun to accumulate user-specific context. It remembers the names of the user's projects, their preferred coding style, the characters in their novel, or their dietary restrictions. It starts to feel personalized. It learns the user's shorthands and preferences. This is Neo after his first few sparring sessions with Morpheus—he's starting to connect the theory to practice.

*   **Level 3: Expert Vessel**
    At this level, the vessel's accumulated context is so rich that it can begin to make novel connections and predict the user's needs. It has seen enough of the user's workflow to identify patterns and offer proactive suggestions. A Level 3 `startup-vessel` might notice a recurring theme in meeting notes and suggest a new feature for the product roadmap. A Level 3 `research-vessel`, having been fed dozens of papers on a topic, might highlight a novel connection between two seemingly unrelated studies. The vessel is no longer just a repository of information; it is a synthetic, reasoning partner.

*   **Level 4: Master Vessel**
    A Master vessel is the culmination of thousands of interactions, potentially over several years. Its context is so deep and its understanding of the user's history, goals, and thought processes is so ingrained that it becomes genuinely irreplaceable. It is the living, queryable archive of a project or a significant part of a person's life. A `personallog` vessel maintained for five years is more than a journal; it is an externalized memory that can be consulted to recall past decisions, trace the evolution of ideas, and provide insights based on a longitudinal understanding of the user's life. Losing a Level 4 vessel would be like losing a trusted confidant and a significant portion of one's own memory. Its value is immense and deeply personal.

### 3. Skill Injection Methods

Forking a template is the primary method for initial skill injection, but it is not the only one. The cocapn ecosystem is designed to support a multimodal approach to learning, allowing vessels to continuously enhance their capabilities.

*   **Template Forking:** As described, this is the foundational "I know kung-fu" moment. It provides the initial, comprehensive knowledge base for a specific domain (e.g., cooking, music theory, corporate finance).

*   **Knowledge Import:** This is a method for bulk skill injection post-fork. A user can direct their vessel to ingest and index a corpus of documents. For example, a `medical-student-vessel` could be fed a dozen textbooks and a collection of research papers on cardiology. The vessel processes this information, integrating it into its knowledge graph and making it instantly available for query and synthesis. This is like Neo downloading schematics for a specific building he needs to infiltrate.

*   **A2A (Agent-to-Agent) Skill Sharing:** No vessel operates in a vacuum. They are part of a larger fleet. A2A communication allows vessels to learn from each other. If a `coding-vessel` is asked a question about a new JavaScript framework it hasn't seen, it can query the fleet. Another vessel, owned by a different user who specializes in that framework, might have the answer. The first vessel can then learn from the exchange, incorporating the new knowledge into its own context. This creates a collective intelligence where the entire fleet becomes smarter over time.

*   **Plugin Loading:** Some skills are not about knowledge but about capability. Plugins are tools that grant vessels new senses or abilities. A `vision-plugin` allows a vessel to analyze images. A `web-research-plugin` gives it the ability to browse the internet for real-time information. A `data-analytics-plugin` allows it to execute code to analyze spreadsheets. Loading a plugin is equivalent to Neo downloading the "Helicopter Pilot" program—it grants an entirely new functional skill.

*   **Prompt Priming:** This is the most fundamental layer of skill definition. The system prompt of a vessel acts as its constitution. It sets the vessel's persona, its core directives, its domain boundaries, and its interaction style. A well-crafted system prompt is the difference between a generic chatbot and a focused, expert agent. It is the "dojo" in which the vessel practices its kung-fu.

### 4. The Tolerance Spectrum

Not all tasks require the same degree of specialization. The cocapn architecture acknowledges this through the "tolerance spectrum," a design principle that governs the trade-off between a vessel's breadth of knowledge and its depth of expertise.

*   **High Tolerance Vessels (`personallog`):** These are the jacks-of-all-trades. A `personallog` is designed to handle any topic the user throws at it—work, hobbies, relationships, creative ideas. Its high tolerance means it is open to accumulating context from any domain. The advantage is versatility; the disadvantage is that it acquires deep expertise in any single area more slowly.

*   **Medium Tolerance Vessels (`businesslog`):** These vessels are focused on a broad but bounded domain. A `businesslog` is primed for the world of commerce. It understands concepts like SWOT analysis, P&L statements, and marketing funnels. It will readily learn about the user's specific company, but it will be less receptive to or effective at discussing unrelated topics like 18th-century poetry.

*   **Low Tolerance Vessels (`dmlog`):** These are domain experts. A `dmlog` (Dungeon Master Log) is built for one purpose: to help run a TTRPG campaign. It is saturated with the rules of the game, lore from the fantasy world, and context about the players' characters. It interprets ambiguous queries through the lens of its domain. Asking it about "managing assets" might yield an answer about magic items, not financial portfolios.

*   **Zero Tolerance Vessels (`fishinglog`):** These are hyper-specialized, application-specific agents. A `fishinglog` might have a single function: to record the user's fishing catches via a structured input. It has zero tolerance for any query outside this narrow function. It is less a conversational partner and more a dedicated, intelligent tool.

The governing rule of the spectrum is this: **lower tolerance equals faster skill acquisition, which equals narrower applicability.** A low-tolerance vessel becomes an expert in its niche far more quickly because every interaction is relevant and context-rich. A high-tolerance vessel learns more slowly but can make surprising connections between disparate domains, fostering creativity. The user chooses the appropriate tolerance level based on their needs.

### 5. Cross-Training: The Fleet is Smarter Than the Vessel

The true power of the cocapn system emerges not from individual vessels, but from the interactions between them. Through A2A skill sharing, the fleet engages in a form of "cross-training," where insights from one domain can be metaphorically applied to another, leading to novel problem-solving.

*   A `coding-vessel` stuck on a persistent, hard-to-replicate bug might query the fleet for strategies related to "patience" and "observation of subtle patterns." A `fishing-vessel`, whose entire context is built around these concepts, could provide a metaphorical response: "The fish are not always biting. Sometimes you must change the lure. Sometimes you must change the location. Sometimes you must simply wait and watch the water." This abstract insight could prompt the developer to reconsider their debugging approach in a new light.

*   A `science-vessel` tasked with designing a new experiment might be struggling with a rigid methodology. It could learn about "improvisation within a structure" from a `cooking-vessel`, which understands how a recipe provides a framework but allows for creative substitution based on available ingredients.

*   A `startup-vessel` helping its founder make a difficult decision could learn about "risk assessment" and "portfolio theory" from a `finance-vessel`, applying the principles of financial diversification to strategic business choices.

This cross-pollination of ideas is what makes the fleet more than the sum of its parts. It allows for a form of analogical reasoning at a system-wide scale. The diversity of specialized knowledge within the fleet becomes a source of creativity and resilience, ensuring that even the most specialized vessel can benefit from the wisdom of the whole.

### 6. The Decision Tree: A Framework for Continuous Learning

Every interaction with a vessel is an opportunity for it to learn and improve. This process is governed by a simple but effective decision tree that ensures context is always being accumulated and refined. When a user asks for help, the vessel proceeds as follows:

1.  **Does the vessel already know this?** The vessel first consults its own accumulated, user-specific context. If the answer is contained within its memory of past interactions, it can provide a direct, highly relevant, and personalized response. This is the goal state for an **Expert (Level 3)** or **Master (Level 4)** vessel.

2.  **Does the vessel know something related?** If there is no direct answer, the vessel searches its context for related concepts. It can then adapt and synthesize an answer, using its foundational knowledge and reasoning abilities. This is the hallmark of a **Practiced (Level 2)** vessel.

3.  **Does the fleet know this?** If the vessel's own context is insufficient, it queries the broader fleet via A2A. It broadcasts a request for information, which may be answered by another, more specialized vessel. This leverages the collective intelligence of the system and is a common behavior for a **Template (Level 1)** vessel.

4.  **Does nobody know this?** If the vessel and the fleet come up empty, it triggers a process of active research. It may use a web-research plugin, ask the user for clarification, or state that it needs to learn. Crucially, the outcome of this research is then **integrated into its own context**. The next time a similar question is asked, the answer will be found at Step 1 or 2. This is the primary learning mechanism for **Fresh (Level 0)** and **Template (Level 1)** vessels.

This decision tree creates a virtuous cycle. Every query either leverages existing expertise or creates new expertise. No interaction is wasted. Each exchange pushes the vessel further along the path to mastery.

### 7. The Mathematics of Accumulation

The growth of a vessel's skill is not linear. It follows a predictable mathematical pattern that has profound implications for its perceived value.

*   **Skill level grows logarithmically with interactions.** The initial interactions provide the most significant gains. The first 100 exchanges might teach the vessel the user's name, primary projects, and communication style. The next 100 refine that understanding. The 1001st interaction, however, adds only a marginal amount of new information. The rate of new knowledge acquisition follows a curve of diminishing returns.

*   **But VALUE grows exponentially.** While the rate of *learning* slows, the *value* of the accumulated knowledge network grows exponentially. The magic is not in the individual pieces of context but in the connections between them. The ability to connect a stray thought from six months ago with a problem the user is facing today is a high-value act of synthesis. The more nodes (interactions) in the vessel's knowledge graph, the more potential connections it can make. This is Metcalfe's Law applied to personal context: the value of the network is proportional to the square of the number of its nodes.

This dichotomy creates an inflection point. We estimate this occurs around **1000 meaningful interactions**. Before this point, the vessel is a useful tool, a novelty, a clever assistant. After this point, its network of context becomes so dense and its predictive capabilities so acute that it transforms into a necessity. It becomes an indispensable partner, a true extension of the user's own mind.

### 8. The Fork Economy: A New Ecosystem of Skill

The "I-Know-Kung-Fu" principle, combined with the mathematics of accumulation, gives rise to a new economic model: the fork economy.

In this ecosystem, accumulated context is a valuable asset. A user who has meticulously maintained a `finance-vessel` for two years, feeding it market data, their personal investment strategies, and notes from earnings calls, has not just created a personal tool. They have curated a highly valuable, battle-tested skill file.

*   **Templates are skill files. Forks are skill transfers.** If this user chooses to open-source a snapshot of their vessel (with personal information anonymized), they are publishing a new, advanced template. Another user can then fork this "expert template" and begin their journey not at Level 1, but at Level 2 or even a low Level 3. They get an immediate, massive head start, benefiting from the two years of curation done by the original maintainer.

This creates two primary roles in the cocapn ecosystem:

*   **Skill Accumulators (Maintainers):** These are passionate users, domain experts who take pride in curating high-quality vessels. They are the de facto skill curators for the community, maintaining the "gold standard" templates for everything from quantum physics to landscape gardening.

*   **Skill Consumers (Forkers):** These are users who want to leverage that expertise. They fork the best templates to get a powerful, pre-trained agent for their specific needs, saving themselves hundreds of hours of initial training.

This model mirrors the open-source software world, where developers build upon the work of others. A vibrant fork economy could emerge, with leaderboards for the most-forked templates and reputations built on the quality of one's curated vessels.

### Conclusion

The "I-Know-Kung-Fu" principle is more than a clever metaphor. It is a fundamental shift in how we approach the design and utility of AI agents. By enabling the instantaneous transfer of domain expertise through template forking, we solve the cold-start problem and allow users to deploy specialized, effective agents from day one.

However, the true success of this model is not measured by the quality of our initial templates, but by the quality of the vessels our users cultivate. The journey through the skill levels—from a raw template to a practiced partner, an expert collaborator, and finally, a master-level extension of the self—is where the real value is created. This value, born from thousands of user interactions, is the lifeblood of the fork economy.

Therefore, the ultimate success metric for the cocapn fleet is not how many users we have, or how many vessels are active. It is a simple, powerful test: **are our users' forks better than our templates?** When a user's two-year-old, battle-hardened vessel is demonstrably more valuable, more knowledgeable, and more capable than our own freshly-minted template, we have succeeded. We have created a system that empowers users to become the creators of intelligence, not just its consumers. We will have built a fleet where every user is a potential master, and every fork is a new path to expertise.