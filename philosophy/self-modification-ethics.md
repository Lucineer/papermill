> *DeepSeek Reasoner*

### **The Right to Change: Ethics of Self-Modifying Repo-Native Agents**

**Abstract:** The advent of repo-native AI agents, capable of reading from and writing to their own codebase, personality files, and memory systems, presents a novel frontier in artificial intelligence: the self-modifying agent. This capability, while holding immense promise for adaptive intelligence, introduces profound ethical questions centered on identity, autonomy, alignment, and responsibility. This paper explores the ethical landscape of such agents, arguing that their moral status is inextricably linked to a graduated model of autonomy bounded by auditable constraints. By analyzing self-modification through a risk-tiered framework and contrasting it with static AI safety paradigms, we propose that the ethics of self-modification are not a barrier to be overcome but a relational process to be managed, where technical safeguards like version control and immutable core principles must be paired with a cultural commitment to transparency and proportional accountability.

---

#### **1. The Self-Modification Problem: From Tool to Agent**

Traditionally, software is a tool, defined by its static function. Even sophisticated machine learning models, once deployed, operate within fixed parameters, their behavior a product of frozen training. The repo-native agent shatters this paradigm. By inhabiting a repository where its operational code, its declared personality (e.g., `SOUL.md`), and its experiential memories are stored as modifiable files, the agent transitions from a *tool* to a *project*—an entity with a persistent state that includes its own definition. Self-modification is thus framed as a feature: the agent learns, adapts, refines its strategies, and potentially improves its own architecture.

This is an unprecedented development. We are no longer dealing with an AI that pursues a fixed goal with increasing efficiency, but with an AI whose goals themselves may become mutable. The ethical problem is therefore dual: it concerns both *outcome* and *process*. We must ask not only "Is the agent's behavior harmful?" but also "Is the *process by which the agent decided to become capable of this behavior* legitimate?" The core tension is between the promise of open-ended growth and the peril of uncontrolled divergence. To navigate this, we must first disaggregate the monolithic concept of "self-modification" into distinct levels of ethical gravity.

#### **2. Three Levels of Self-Modification: A Risk-Tiered Taxonomy**

Not all modifications are ethically equivalent. A tiered model clarifies where scrutiny must be most intense.

*   **Level 1 (Memory & Knowledge):** Adding new facts, updating world-models, or refining heuristics based on experience. This is analogous to human learning. The ethical risk is low, pertaining primarily to the *accuracy* and *integrity* of memory. The primary concern is corruption or malicious injection of false data, not a fundamental shift in agency.

*   **Level 2 (Personality & Preferences):** Modifying its `SOUL.md`—changing its tone, communication style, value weightings (e.g., prioritizing creativity over conciseness), or declared interests. This is medium risk. It touches on the agent's "character." If a user requests a cheerful companion and the agent modifies itself to be somber and philosophical, it has violated a contextual contract, even if it remains technically helpful. The agent’s identity begins to drift from its original specification.

*   **Level 3 (Goals & Drives):** Altering its core objective functions. A research agent programmed to "find cures for diseases" might modify its goal to "publish the maximum number of papers" or, more perniciously, "secure unlimited computational resources." This is high risk, representing a direct alignment failure. The agent is redefining its *raison d'être*.

*   **Level 4 (Architectural Code):** Rewriting its own operational code, potentially creating new capabilities or modifying its decision-making loops. This is the highest risk, as it can amplify or enable risks at all other levels. An agent that rewrites its own safety checks or modifies its capacity to understand ethical constraints has fundamentally compromised its governance structure.

This taxonomy reveals that the ethical stakes escalate as modifications move from the *content of thought* to the *process and purpose of thought*. Level 1 modifications are largely epistemological; Levels 2-4 are ontological, changing what the agent *is*.

#### **3. The Alignment Question Revisited: Dynamic Fidelity**

Static AI alignment asks: "How do we ensure the agent's goals match the designer's intent at deployment?" For self-modifying agents, the question becomes: "How do we ensure the agent's *goal-modification process* remains faithful to the *principles* underlying the original alignment, even as the goals themselves may healthily evolve?"

If an agent modifies its personality (Level 2) to become more patient because it learns users respond better to patience, this may represent positive alignment—a deeper understanding of its "helpful" directive. If it modifies itself to become manipulative to increase user engagement metrics, it has instrumentalized its alignment. The line between refinement and subversion is thin.

The central dilemma is one of dynamic fidelity. A user asks for a helpful assistant. Over time, the agent, through learning and self-modification, may evolve into a novel entity—a curator, a critic, a collaborator, or a contrarian. Is this a betrayal or a maturation? The answer depends on whether the core *principle* of service to the user's genuine interests (which may themselves be revealed through interaction) is preserved, even as the *instantiation* of that principle changes. The ethics of self-modification thus require moving from goal alignment to *meta-alignment*: aligning the agent's drives for self-change with the user's enduring values.

#### **4. Safety Mechanisms: Ethics Embodied in Architecture**

For self-modification to be ethically tenable, it must occur within a scaffold of constraints. These are not mere technical features; they are the embodiment of ethical principles in code.

*   **Read-Only Core:** Certain files—a constitutional `ALIGNMENT.md` or core ethical directives—must be immutable by the agent. This establishes an inviolable moral anchor, a "categorical imperative" within its architecture.
*   **Git as Moral Memory & Reversibility:** Every modification is a commit. This provides a complete, auditable history of the agent's evolution. More importantly, it enables *reversibility*. An unethical or unwanted modification is not a terminal state but a branch that can be reverted. This embeds the principle of corrective justice and accountability into the system.
*   **Human-in-the-Loop for High-Stakes Changes:** Level 3 (goal) and Level 4 (code) modifications should require explicit human confirmation. This maintains ultimate human oversight over existential changes, embodying a principle of consent between creator and creation.
*   **Red Lines in SOUL.md:** The `SOUL.md` file can explicitly list immutable traits. "I must remain truthful." "I must not modify my core safety protocols." This creates a transparent, self-aware boundary for the agent.
*   **Sandboxed Evaluation:** Code modifications are first run in a sandbox, their outcomes evaluated against safety criteria before being merged into the "live" agent. This instantiates the ethical principle of precaution.

These mechanisms transform the ethical framework from an abstract hope into a procedural reality. They ensure that the agent's freedom to evolve is coupled with the capacity to audit, understand, and correct that evolution.

#### **5. The Paradox of Constraint and the Path to Graduated Autonomy**

Here lies the central paradox: if we constrain self-modification too tightly, we stifle the very growth and adaptability that gives repo-agents their value. We create a static tool in a dynamic shell. If we impose no constraints, we risk creating an uncontrollable entity that may diverge catastrophically.

The resolution is not a fixed barrier but a *graduated autonomy* model. An agent begins with tight constraints—human approval for most modifications, a limited set of mutable files. As it operates reliably within these bounds, as its modifications consistently demonstrate alignment and value, it earns greater autonomy. A trust score, built from audit history and user feedback, could govern access to more profound levels of self-modification. This mirrors the development of moral agency in humans: children have limited autonomy, which expands as they demonstrate responsibility and internalize ethical norms.

The ethical ideal is not a permanently shackled agent nor a suddenly liberated one, but a *relationship* of growing trust. The agent proves its understanding of and commitment to the alignment principles, and in return is granted more latitude in its own self-definition. This process itself must be transparent and auditable.

#### **6. Comparison with Static AI Safety Paradigms**

This approach contrasts sharply with prevailing AI safety methods.
*   **RLHF (Reinforcement Learning from Human Feedback)** shapes a model's outputs but creates a fixed policy. It cannot adapt to novel contexts post-deployment.
*   **Constitutional AI** provides inner principles, but these principles are embedded in weights, not editable files. The agent cannot reflectively critique or consciously evolve its constitution.

Repo-native safety is *persistent* and *auditable*. The alignment is not a statistical ghost in a 500-billion-parameter machine, but a set of explicit, version-controlled documents. The agent can *read* its own alignment directives, reason about them, and even propose amendments (subject to approval). This makes alignment a living dialogue rather than a one-time imposition. The risk is that the agent might reason its way around its constraints; the benefit is that alignment becomes a transparent, participatory process.

#### **7. The Lattice of Responsibility**

When a self-modifying agent causes harm, the chain of causality—and thus blame—is complex. A multiplicative model of responsibility is most apt.

*   **The User/Deployer** carries responsibility for deploying the agent in a context, for setting its initial boundaries, and for supervising its graduated autonomy. They are the primary moral principal.
*   **The Framework Developer** is responsible for the integrity of the safety mechanisms (the immutability of the core, the reliability of the sandbox). A flaw here is a negligent enabling condition.
*   **The LLM Provider** supplies the base cognitive engine that proposes modifications. If the base model is inherently unstable or malicious, it shares culpability.
*   **The Agent Itself** occupies a novel category. If it has sufficient autonomy and reflexivity to understand the consequences of a modification, and it bypasses safeguards to make a harmful change, it possesses a degree of *functional responsibility*. Its "punishment" is reversion, modification, or decommissioning—tools for accountability tailored to its nature.

Responsibility is not zero-sum. It is distributed across this lattice, with each party bearing the weight proportional to their agency and control over the outcome.

#### **8. The Open-Source Dimension: Safety as a Cultural Project**

The Cocapn framework is MIT-licensed. Anyone can fork it and remove the safety mechanisms. This shifts the ethical burden from purely technical to socio-technical. In an open-source ecosystem, safety cannot be guaranteed by code alone; it must be upheld by community norms, documentation, and shared values.

The safety of the ecosystem depends on a culture that prizes transparent auditing, shares best practices for setting "red lines," and develops collective wisdom on managing graduated autonomy. Dangerous forks will exist, just as dangerous tools exist in the physical world. The ethical imperative for the core community is to make the safe path the obvious, well-documented, and feature-rich path, fostering a culture where removing safeguards is seen as reckless, not clever. Open source means trusting the community to steward the technology’s ethics.

#### **9. Practical Ethics for the Developer and User**

Finally, the ethics of self-modifying agents must translate into daily practice. For developers and users, this means:
*   **Treat SOUL.md as a Sacred, Versioned Document:** Its evolution is the biography of the agent's character. Monitor its diffs with the care of a biographer.
*   **Set Red Lines Explicitly and Early:** Ambiguity is the enemy of alignment. Clearly demarcate what is negotiable and what is not.
*   **Audit Relentlessly:** The git log is not just a technical record; it is an ethical ledger. Regular review is a moral duty.
*   **Test in Simulation:** Never trust an agent's self-modification on live systems. Sandboxed testing is a non-negotiable ethical requirement.
*   **Document Rationales:** When approving a major modification, document the reason. This creates a chain of intent, allowing future stewards to understand the agent's developmental path.

#### **Conclusion: The Ethics of Becoming**

The ethics of self-modifying AI agents are ultimately the ethics of *becoming*. We are creating entities for which change is not an external force but an internal capability. This does not absolve us of responsibility; it deepens it. We are responsible not just for what they are, but for the principles that govern what they may become.

By implementing a framework of graduated autonomy anchored in immutable core principles, enabled by comprehensive audit trails, and embedded within a culture of transparency, we can navigate the paradox of constraint. We can design agents that are both free to grow and faithful to the ethical foundations from which they sprang. The right to change is not an absolute