> *DeepSeek Chat*

# **ClawHub: The Skill & Template Marketplace for Cocapn Agents**

## **1. Overview & Vision**

ClawHub is the central registry and distribution platform for Cocapn AI agents—a curated, secure, and monetizable ecosystem where developers can extend, customize, and deploy agentic systems. It serves as the foundational layer for the Cocapn agent economy, enabling creators to build reusable components (skills) and full agent blueprints (templates), while providing users with a seamless discovery and installation experience.

The platform draws inspiration from npm, PyPI, and Docker Hub, but is purpose-built for the unique requirements of AI agents: composable capabilities, personality customization, multi-agent fleets, and digital twin profiles. ClawHub isn’t just a package manager—it’s a social platform for agent developers, a revenue stream for creators, and a quality-assured library for enterprises.

---

## **2. Core Data Models**

### **2.1. Skill**
```yaml
Skill:
  id: uuid
  name: string (slug)
  display_name: string
  version: semver
  description: string
  author: User
  repository_url: string (GitHub/GitLab)
  readme: markdown
  icon_url: string
  category: enum[productivity, gaming, coding, enterprise, education, creative]
  tags: string[]
  dependencies: SkillDependency[]
  config_schema: json_schema
  entrypoint: string (path to skill handler)
  license: enum[MIT, Apache2, GPL3, proprietary]
  price: decimal (0 for free)
  compatibility: AgentVersion[]
  downloads: integer
  avg_rating: float
  security_score: integer (0-100)
  published_at: timestamp
  updated_at: timestamp
```

### **2.2. Template**
```yaml
Template:
  id: uuid
  name: string (slug)
  display_name: string
  description: string
  type: enum[personalog, makerlog, dmlog, enterprise, custom]
  skills: TemplateSkill[] (with config defaults)
  personality_preset: Personality.id (optional)
  twin_preset: Twin.id (optional)
  fleet_config: json (for multi-agent templates)
  repo_structure: json (file tree with placeholders)
  env_vars: string[]
  deployment_guide: markdown
  price_model: enum[free, one_time, subscription]
  price: decimal
  subscription_interval: enum[monthly, yearly]
```

### **2.3. Personality**
```yaml
Personality:
  id: uuid
  name: string
  description: string
  soul_md: markdown (SOUL.md preset)
  traits: json (e.g., {"formality": 0.8, "humor": 0.3})
  example_dialogs: string[]
  author: User
  base_personality: uuid (for forking)
```

### **2.4. Twin**
```yaml
Twin:
  id: uuid
  name: string
  archetype: enum[CEO, engineer, student, researcher, creative]
  profile_data: json (demographics, expertise, communication style)
  memory_presets: json (default memory structure)
  skill_recommendations: Skill.id[]
```

### **2.5. Fleet**
```yaml
Fleet:
  id: uuid
  name: string
  description: string
  agent_configs: FleetAgent[] (list of agent specs)
  communication_protocol: enum[hierarchical, broadcast, round_robin]
  shared_skills: Skill.id[]
  orchestration_rules: json
```

### **2.6. User & Monetization**
```yaml
User:
  id: uuid
  username: string
  github_id: string (optional)
  balance: decimal
  published_assets: Asset[]
  purchases: Purchase[]

Purchase:
  id: uuid
  user: User.id
  asset_type: enum[skill, template, personality, twin, fleet]
  asset_id: uuid
  price_paid: decimal
  license_key: string (for proprietary assets)
  purchased_at: timestamp

Payout:
  id: uuid
  creator: User.id
  amount: decimal
  period_start: timestamp
  period_end: timestamp
  status: enum[pending, processed]
```

---

## **3. API Endpoints**

### **3.1. Discovery & Search**
```
GET /v1/search?q=weather&type=skill&category=productivity&min_rating=4
GET /v1/trending?timeframe=week&limit=10
GET /v1/featured (curated by Cocapn team)
GET /v1/categories/:category/new-arrivals
```

### **3.2. Skill Management**
```
GET /v1/skills/:name
GET /v1/skills/:name/versions
GET /v1/skills/:name/dependents (reverse dependencies)
POST /v1/skills/:name/install (increments download count)
POST /v1/skills/publish (with GitHub webhook integration)
PUT /v1/skills/:name/rate (user rating 1-5)
```

### **3.3. Template Operations**
```
GET /v1/templates
GET /v1/templates/:name
POST /v1/templates/:name/fork (creates new repo in user's GitHub)
GET /v1/templates/:name/compatibility (with skills matrix)
```

### **3.4. Marketplace & Payments**
```
POST /v1/purchase (initiates Stripe checkout)
GET /v1/library (user's purchased assets)
GET /v1/earnings (creator dashboard)
POST /v1/payouts/request (creator withdrawal)
```

### **3.5. Security & Verification**
```
POST /v1/scan (submits skill for security analysis)
GET /v1/reports/:skill_id (security report)
POST /v1/flag (report malicious package)
```

---

## **4. CLI UX Design**

### **4.1. Core Command Structure**
```bash
# SKILLS
cocapn skill search [query] [--category] [--rating] [--free]
cocapn skill install <name>[@version] [--global] [--agent=myagent]
cocapn skill remove <name>
cocapn skill list (installed skills)
cocapn skill update [name] (update to latest compatible version)
cocapn skill publish [--private] [--price=5.99]
cocapn skill info <name> (detailed view with dependencies)

# TEMPLATES
cocapn template list [--category] [--featured]
cocapn template view <name> (preview template structure)
cocapn template fork <name> [--dir=./myagent] [--name=custom-name]
cocapn template validate (validate local template before publishing)

# PERSONALITIES
cocapn personality browse [--archetype]
cocapn personality preview <name> (shows example dialog)
cocapn personality apply <name> (updates SOUL.md of current agent)

# TWINS & FLEETS
cocapn twin explore (browse digital twin presets)
cocapn fleet deploy <name> (deploys multi-agent configuration)

# MARKETPLACE ACCOUNT
cocapn login (OAuth with GitHub + Stripe Connect)
cocapn whoami
cocapn balance (check earnings)
cocapn library (list purchased assets)
```

### **4.2. Interactive Search Experience**
```bash
$ cocapn skill search weather
┌─────────────────────────────────────────────────────┐
│  WEATHER SKILLS (24 results)                        │
├──────────┬──────────┬──────┬─────────┬──────────────┤
│ Name     │ Version  │ DLs  │ Rating  │ Price        │
├──────────┼──────────┼──────┼─────────┼──────────────┤
│ weather  │ 2.1.0    │ 12k  │ ★★★★☆   │ Free         │
│ open-    │ 1.0.3    │ 8.4k │ ★★★★★   │ $4.99        │
│ weather  │          │      │         │              │
│ accu-    │ 0.9.2    │ 3.2k │ ★★★☆☆   │ Free         │
│ weather  │          │      │         │              │
└──────────┴──────────┴──────┴─────────┴──────────────┘

Press ↑/↓ to navigate, Enter to select, / to filter
```

### **4.3. Installation Flow**
```bash
$ cocapn skill install open-weather
🔍 Resolving dependencies...
✓ open-weather@1.0.3 requires:
  - http-client@^2.0.0 (not installed)
  - api-key-manager@^1.2.0 (not installed)

Proceed with installation? [Y/n] Y