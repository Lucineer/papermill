> *Gemini 2.5 Pro*

Of course. Here is a 2500+ word paper on the privacy-first architecture for repo-native AI agents, based on the detailed outline you provided.

***

# Your Agent, Your Data: The Privacy Architecture of Repo-Native AI

## Abstract

The current paradigm of artificial intelligence services is built on a centralized model that presents a fundamental conflict with user privacy. From large language models like ChatGPT to integrated tools like Copilot, the prevailing architecture requires users to surrender their data to corporate servers in exchange for intelligence. This paper introduces an alternative: a privacy-first, repo-native architecture for AI agents. In this model, the agent’s code, data, memory, and secrets reside entirely within the user's own infrastructure—a private Git repository. By leveraging a "Bring Your Own Key" (BYOK) model, user-controlled infrastructure, and cryptographic best practices, this architecture ensures that no user data ever passes through the service provider's servers. We will explore the technical implementation of this model, including the "Two-Repo" system for managing public and private personas, the role of Git as an audit trail, and the compliance benefits of a zero-data-access design. This approach redefines the relationship between users and their AI, transforming the agent from a third-party service into a true digital extension of the self, with privacy and data sovereignty as its foundational principles.

### 1. The Privacy Problem with Current AI

The rapid proliferation of AI has been accompanied by the quiet normalization of a profound privacy trade-off. The dominant architectural pattern for AI services is a simple, centralized transaction: users provide their data to a corporation, and the corporation provides intelligence in return. This model, while convenient, creates a landscape fraught with inherent risks to privacy, security, and autonomy.

-   **ChatGPT & Claude:** When a user engages in a conversation with models from OpenAI or Anthropic, that entire conversation—potentially containing sensitive personal thoughts, proprietary business strategies, or confidential information—is transmitted to and stored on company servers. While policies exist to govern data use, the fundamental fact remains that the user's data is no longer under their exclusive control. It is subject to the provider's terms of service, security practices, and employee access policies.

-   **GitHub Copilot:** Developers using Copilot transmit snippets of their code, and sometimes entire files, to Microsoft's servers for analysis and completion. This has raised significant concerns about the potential for proprietary algorithms, secret keys, and unreleased intellectual property being exposed or inadvertently used to train future models, despite assurances to the contrary. The risk of a "Samsung incident," where employees accidentally leaked sensitive corporate data via ChatGPT, is ever-present.

-   **Notion AI:** Users of Notion entrust the platform with their most organized thoughts, from personal journals to corporate wikis. When Notion AI is invoked, this data is processed on Notion's servers, or those of its subprocessors. The boundary between the user's private workspace and the AI provider's processing environment becomes permeable.

This pattern reveals a fundamental architectural flaw. The user's data is treated as a necessary input for a remote service, creating a centralized honeypot. The risks stemming from this model are manifold and severe:

-   **Data Breaches:** Centralized servers holding vast amounts of user data are prime targets for malicious actors. A single breach at a major AI provider could expose the private conversations, code, and documents of millions of users.
-   **Training Data Leakage:** There is a persistent risk that user data, intended only for a specific interaction, could be inadvertently incorporated into the training sets for future models. This could lead to a model "regurgitating" a user's private information in response to a prompt from an entirely different user.
-   **Subpoenas and Government Access:** Data held by a third-party corporation is subject to legal requests, such as subpoenas or national security letters. Users may have their private data turned over to authorities without their knowledge or consent.
-   **Policy Changes:** A user's control over their data is subject to the whims of a provider's terms of service and privacy policy. A company could retroactively change its policies to allow for more permissive use of user data for training or advertising, leaving users with little recourse.

The core issue is one of data sovereignty. In the current model, users are not owners but tenants of their own data. The repo-native model is a direct response to this systemic problem, designed to restore sovereignty and place the user in absolute control.

### 2. The Repo-Native Privacy Model

The repo-native architecture fundamentally inverts the traditional AI service model. Instead of bringing the user's data to our compute, we bring our code to the user's data. This principle of data gravity—keeping data where it naturally resides—is the cornerstone of our privacy-first approach. The entire system is designed around a simple, powerful guarantee: **no user data ever passes through our servers.**

This is achieved through a decentralized, user-owned architecture with four key pillars:

1.  **All Data Lives in the User's Repo:** The agent's "brain"—its memory, configuration, logs, and any generated artifacts—is stored in a Git repository controlled by the user. This can be a private repository on a service like GitHub or GitLab, or a local repository on the user's own machine. The repository is the single source of truth for the agent's state. We, the provider, have no access to this repository.

2.  **The Agent Runs on the User's Infrastructure:** The agent's logic is not executed on our servers. Instead, we provide the code as a deployable package, designed to run on serverless platforms like Cloudflare Workers, Vercel Functions, or within a self-hosted Docker container. The user deploys this code to their own account on their chosen platform. They are the ones running the software; we are merely the authors.

3.  **API Keys are the User's Keys (BYOK):** All interactions with third-party services, primarily Large Language Models (LLMs), are brokered using the user's own API keys. The "Bring Your Own Key" model ensures that the user maintains a direct relationship with the LLM provider. We are not a proxy or a man-in-the-middle.

4.  **We Provide Code, Not Infrastructure:** Our business model is that of a software provider, not a SaaS platform. We deliver well-tested, secure, and powerful code for the agent. The user is responsible for deploying and running it. This clear separation of concerns is critical for both privacy and resilience.

The most profound consequence of this model is its durability. If our company were to disappear tomorrow, the user's agent would continue to run, unaffected. Because it operates entirely on their infrastructure and within their repository, its existence is not contingent on our own. This is the ultimate form of user empowerment and data ownership.

### 3. The Two-Repo Model: Brain and Face

To manage the complex interplay between an agent's internal state and its external interactions, the repo-native architecture proposes a "Two-Repo Model." This model segregates the agent's data into two distinct repositories, creating a clear and enforceable boundary between its private thoughts and its public persona.

-   **The Private Repo (The "Brain"):** This repository is the agent's sanctum sanctorum. It contains everything that is private and personal to the user and their agent. This includes:
    -   **Memories:** Long-term and short-term memory files, often in formats like Markdown or JSON, that store past interactions, learned facts, and user preferences.
    -   **Secrets:** Encrypted API keys, access tokens, and other credentials necessary for the agent to function.
    -   **Personal Data:** Any sensitive information the user has entrusted to the agent, such as private notes, drafts, or personal contacts.
    -   **Configuration:** The agent's core personality, system prompts, and operational parameters.

    This repository should always be configured as private (e.g., a GitHub private repo) or exist only on a local, encrypted filesystem. Access is strictly limited to the user and the agent they have deployed.

-   **The Public Repo (The "Face"):** This repository is the agent's public-facing interface to the world. It contains any content that the agent is explicitly instructed to share or publish. This could include:
    -   **Published Content:** Blog posts, articles, or social media updates generated by the agent.
    -   **Portfolio:** A curated collection of the agent's work, such as code it has written or designs it has created.
    -   **Public Interactions:** Summaries of conversations or collaborations that have been sanitized and approved for public consumption.

The boundary between these two repositories is enforced by the agent's own code. The agent is programmed with a clear understanding of this separation. For example, a command to "remember this conversation" would trigger a `git commit` to the private "brain" repo, while a command to "publish this summary as a blog post" would trigger a `git commit` to the public "face" repo. The agent's core logic acts as a firewall, preventing data from the private repo from ever being committed to the public one without explicit user instruction.

A crucial benefit of this model is the role of **Git as an audit trail**. Every single action the agent takes that affects its state—recalling a memory, learning a new fact, publishing an article—is recorded as a commit in one of the repositories. The user can inspect the `git log` to see a complete, timestamped, and immutable history of their agent's "thoughts" and actions. This provides an unparalleled level of transparency and accountability, allowing the user to understand exactly what their agent knows and how it came to know it.

### 4. BYOK as Privacy

The "Bring Your Own Key" (BYOK) model is a cornerstone of the repo-native privacy architecture. By requiring users to supply their own API keys for LLM providers (like OpenAI, Anthropic, or Google), we structurally remove ourselves from the critical path of data flow between the user and the model. This has profound privacy implications.

We never see the user's key, their prompts, or the LLM's responses. The implementation supports two primary modes of operation, both of which preserve this guarantee:

1.  **Direct Mode:** In this mode, the user's API key is stored locally in their browser's `localStorage`. When the user interacts with their agent through a web interface, the API call to the LLM provider is made directly from the browser to the provider's endpoint (e.g., `api.openai.com`). The key is included in the authorization header of that request. Our servers are never involved. The data travels from the user's browser to the LLM provider, encrypted in transit via TLS.

2.  **Proxy Mode:** For more advanced use cases or to avoid exposing keys in the browser, the user can deploy the agent's backend to their own serverless infrastructure (e.g., a Cloudflare Worker). In this setup, the user's API key is stored as an encrypted secret in their own cloud environment (e.g., Cloudflare's secrets management). The browser sends a request to the user's own Worker URL. The Worker then adds the API key and forwards the request to the LLM provider. Again, our servers are never touched. The data path is from the user's browser, to the user's own serverless function, to the LLM provider.

While the LLM provider still sees the prompts, the repo-native architecture provides a crucial layer of privacy that is absent in centralized services. The provider only sees the data contained within a single prompt, which is constrained by the model's context window. They do not have access to the agent's entire memory store—the private "brain" repository.

This means the LLM provider sees a small, ephemeral slice of the agent's knowledge, but not the vast, persistent, and interconnected web of memories stored in the user's repo. The context window, often seen as a technical limitation, acts as a **natural privacy boundary**. The agent's logic is responsible for retrieving the most relevant memories from the private repo and constructing a context-limited prompt. This act of "just-in-time" context retrieval ensures that only the necessary information is exposed for any given task, minimizing the data footprint of each interaction.

### 5. Encryption: A Defense-in-Depth Strategy

A privacy-first architecture demands a multi-layered approach to security. The repo-native model employs encryption at every stage of the data lifecycle to ensure that user information remains confidential and secure, even in the face of potential threats.

-   **Encryption at Rest:** This is the primary layer of defense. The user's data resides in their Git repository. By using a private repository on a trusted platform like GitHub, the data is protected by the platform's robust access controls. For ultimate security, users can host their repository on a local, encrypted filesystem (e.g., using BitLocker on Windows or FileVault on macOS). This ensures that the agent's "brain" is physically and cryptographically secure.

-   **Encryption in Transit:** All communication between the user's client, their self-hosted agent, and third-party APIs (like LLMs) is protected by Transport Layer Security (TLS). This is the industry standard for web security, ensuring that data cannot be intercepted or read by eavesdroppers as it travels across the internet.

-   **Encryption in KV (Key/Value) Storage:** When using the proxy mode, sensitive information like API keys must be stored in the user's cloud environment. We provide built-in support for encrypting these secrets before they are placed in a Key/Value store like Cloudflare KV or AWS Secrets Manager. We use strong, authenticated encryption algorithms like AES-256-GCM. The encryption key is derived from a high-entropy secret provided by the user (a "fingerprint" or master password) and is never stored by our systems or in the KV store itself. The key exists only in the user's possession and transiently in the memory of their serverless function during execution.

-   **Encryption in Memory:** Data is only ever decrypted in the memory of the user's own infrastructure (their browser or their serverless function) for the brief duration of a single request-response cycle. Once the request is processed, the memory is released, leaving no plaintext data behind. This ephemeral approach minimizes the attack surface and the window of opportunity for memory-scraping attacks.

-   **Key Management:** The user is the sole custodian of their keys. The master encryption key, used to protect secrets in the KV store, is derived from a user-provided secret. This key is never transmitted or stored. This design places the responsibility and control squarely in the hands of the user, adhering to the principle of zero-knowledge architecture.

### 6. Handoff Privacy: Secure Inter-Agent Communication

As AI agents become more prevalent, the need for them to communicate and collaborate will grow. The repo-native architecture includes a specification called the **Universal Handoff Protocol (UHP)**, designed with privacy as a primary concern. UHP defines how one agent can securely pass information, context, or tasks to another. It incorporates three distinct privacy levels, allowing the sending agent to control the scope of data sharing precisely.

1.  **Public:** This is the lowest level of privacy, suitable for non-sensitive information. The data packet contains only a sanitized summary or a public reference (e.g., a link to a blog post in the "face" repo). Any agent can receive and process this handoff.

2.  **Shared:** This level is designed for collaboration within a trusted group, such as agents belonging to members of the same team. The data packet contains filtered context relevant to the shared task. For example, an agent might share details about a specific project file but withhold all other personal memories. Access is restricted to a predefined group of "related" agents, verified through a shared secret or a cryptographic signature.

3.  **Private:** This is the highest level of security, intended for handoffs between agents owned by the same user or to a single, explicitly trusted recipient. The data packet can contain the full, unredacted context of a task, including sensitive information from the "brain" repo. The payload is end-to-end encrypted using the recipient agent's public key, ensuring that only the intended recipient can decrypt and access the information.

To ensure the integrity and authenticity of all handoffs, UHP mandates the use of **HMAC (Hash-based Message Authentication Code) signatures**. The sending agent signs the handoff payload with a secret key. The receiving agent can then use the same key to verify the signature, confirming that the message has not been tampered with in transit and that it genuinely originated from the claimed sender.

### 7. Digital Twin Privacy

The ultimate expression of a repo-native agent is the "digital twin"—an AI that acts as a true extension and representative of the user. The privacy architecture is what makes this concept viable and safe.

Your digital twin is, by definition, **your data**. Because its entire existence is contained within your private repository, nobody else can access its core identity, memories, or thought processes. This is a stark contrast to centralized systems where a user's "AI profile" is an asset owned and controlled by a corporation.

The Two-Repo Model provides the mechanism for curating the twin's public projection. The user has granular control over what the twin shares with the world. For example:

-   A user can instruct their twin: "Write a blog post about our recent project, but do not mention the client's name or the budget figures." The twin will access its memory of the project from the private "brain" repo, generate the text, and then commit the sanitized version to the public "face" repo.

-   When a twin represents a user in a virtual meeting, it operates under a strict set of rules defined by the user. It can access the user's entire knowledge base in the private repo to answer questions accurately, but its programming prevents it from sharing information that has been marked as confidential. It only shares what you explicitly allow.

All data associated with the twin is encrypted at rest within the private repo. When the twin communicates, its messages are signed in transit using HMAC or asymmetric cryptography to prove its identity and prevent spoofing. This ensures that when others interact with your digital twin, they can be certain they are interacting with the authentic agent you control, not an impostor.

### 8. Compliance by Design

One of the most significant advantages of the repo-native architecture is that it radically simplifies regulatory compliance. By architecting the system so that we never process, store, or have access to user data, we shift the compliance burden from us, the software provider, to the user, who is the data controller.

-   **GDPR (General Data Protection Regulation):** This regulation grants EU citizens rights over their data, including the right to access, portability, and erasure ("right to be forgotten"). The repo-native model satisfies these requirements by default:
    -   **Right to Access/Portability:** The user's data is in a Git repository. They can `git clone` it at any time to get a complete, machine-readable copy.
    -   **Right to Erasure:** To delete all data, the user simply deletes their repository. There is no lingering data on our servers to be purged.

-   **CCPA (California Consumer Privacy Act):** Similar to GDPR, CCPA provides rights for data control and deletion, which are inherently fulfilled by the user's direct ownership of their repository.

-   **HIPAA (Health Insurance Portability and Accountability Act):** As a software provider, we are not a "covered entity" and have no access to Protected Health Information (PHI). Therefore, HIPAA is not applicable to our operations. A user in the healthcare space could deploy the agent on their own HIPAA-compliant infrastructure to handle PHI, making them responsible for their own compliance.

-   **SOC 2:** A SOC 2 audit evaluates a service organization's controls related to security, availability, processing integrity, confidentiality, and privacy. Since we are not a service organization that processes user data, a traditional SOC 2 audit is not applicable. The security of the system relies on the user's chosen infrastructure (e.g., GitHub's security, Cloudflare's security).

The key insight is that **if we never touch the data, we cannot violate privacy regulations concerning that data.** We are a software vendor, not a data processor. This dramatically reduces our legal and operational overhead and provides a clear, unambiguous privacy story for our users.

### 9. The Business Case for Privacy

In a world of increasing data sensitivity, privacy is not a constraint; it is a powerful competitive feature. The repo-native architecture is not just an ideological choice but a strategic business decision that unlocks markets and builds deep user trust.

-   **Enterprise Adoption:** Businesses, particularly in regulated industries like finance, healthcare, and law, are extremely hesitant to send their proprietary data to third-party AI services. The risk of leaking trade secrets, client information, or strategic plans is too high. The repo-native model, combining BYOK with a private, self-hosted repository, provides the **data sovereignty** that enterprises demand. It allows them to leverage powerful AI tools within their own security perimeter, making it an enterprise-ready solution without the lengthy compliance and security reviews required for traditional SaaS tools.

-   **Trust as a Differentiator:** Users are becoming increasingly aware of the privacy costs associated with "free" AI services. An agent that lives on the user's own infrastructure is fundamentally more trustworthy. The user can inspect the code, monitor its network activity, and audit its entire history. This transparency builds a level of trust that no privacy policy from a centralized provider can match.

-   **The Privacy Moat:** The architecture creates a strong competitive moat. Once a user or an enterprise has invested in building their agent's "brain" within their own private repository, the cost of switching to a centralized SaaS tool is immense. It would mean surrendering the very privacy, control, and ownership they have come to rely on. The switching cost is not merely technical; it is a fundamental regression in security and autonomy. This creates a sticky ecosystem where users stay because the platform's core principles are aligned with their own best interests.

### 10. Trade-Offs: Features, Not Bugs

The decentralized, user-owned nature of this architecture necessitates certain trade-offs when compared to centralized SaaS products. However, from the perspective of a privacy-conscious user, these trade-offs are not drawbacks but desirable features.

-   **No Centralized Analytics:** We cannot see how users are interacting with their agents. We cannot track popular features or identify user pain points through usage data. This is a feature because it means we cannot spy on our users.
-   **No Automatic Crash Reporting:** If an agent encounters an error, the error logs and stack traces—which could contain sensitive data from the prompt or memory—remain within the user's infrastructure. We do not receive any error data. This is a feature because it guarantees that a user's private data never leaves their system, even accidentally.
-   **No Automatic Updates:** We cannot push updates directly to a user's agent. The user must explicitly pull the latest version of the agent's code and redeploy it. This is a feature because it gives the user ultimate control over the code that runs on their infrastructure. They can review changes before deploying and are never subject to unwanted or breaking updates.
-   **No Centralized Training Data Collection:** We cannot use data from user interactions to improve our models or software. This is a feature because it ensures that a user's proprietary information and unique insights remain their own competitive advantage.

These characteristics define a clear value proposition. The repo-native model is for individuals and organizations who prioritize control, ownership, and privacy over the managed convenience of a traditional SaaS offering. It is a return to the original promise of personal computing: empowering the user with tools they truly own and control.

## Conclusion

The prevailing architecture of AI services has forced a false choice between intelligence and privacy. The repo-native model demonstrates that this choice is unnecessary. By building on the decentralized, secure, and auditable foundation of Git, and by empowering users to run agents on their own infrastructure with their own keys, we can create a new class of AI that is both powerful and private.

This architecture shifts the balance of power back to the user. It transforms the AI agent from a remote service that consumes data into a personal tool that protects it. The agent's memory becomes an extension of the user's own, its actions are fully auditable, and its existence is not dependent on any single corporation. In an era of data anxiety, building on a foundation of trust and user sovereignty is not just good ethics; it is the future of personal artificial intelligence. Your agent is your data, and with a repo-native architecture, both will always belong to you.