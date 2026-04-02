> *DeepSeek Chat*

# CookLog.ai: The Intelligent Cooking Repository Agent

## System Overview

CookLog.ai is a personal culinary companion that evolves from a simple recipe manager into an intuitive cooking partner that understands your preferences, habits, and kitchen reality. Built on the repository-agent paradigm, it combines structured data storage with intelligent adaptation, learning from every interaction to become more useful over time.

## Core Architecture

**Memory Structure:**
```
memory/
├── recipes/                    # Individual recipe markdown files
│   ├── pasta-carbonara.md
│   ├── veggie-curry.md
│   └── [recipe-slug].md
├── plans/                      # Weekly meal plans
│   ├── 2024-03-11.md          # Monday's plan
│   └── [YYYY-MM-DD].md
├── taste-profile.json          # Evolving preference model
├── cooking-log.json           # History of cooked dishes
└── pantry.json                # Current ingredient inventory
```

**Recipe Schema (Markdown + Frontmatter):**
```yaml
---
title: "Classic Carbonara"
source: "https://example.com/recipe"
cuisine: "Italian"
prep_time: 15
cook_time: 20
servings: 4
difficulty: "Medium"
tags: ["pasta", "quick", "comfort-food"]
ingredients:
  - {name: "guanciale", amount: 200, unit: "g", category: "meat"}
  - {name: "eggs", amount: 4, unit: "whole", category: "dairy"}
  - {name: "pecorino", amount: 100, unit: "g", category: "cheese"}
steps:
  - "Bring water to boil for pasta"
  - "Chop guanciale into small pieces"
nutrition:
  calories: 650
  protein: 25
  carbs: 75
  fat: 30
preference_score: 8.5          # User rating (1-10)
cooked_count: 12               # Times prepared
last_cooked: "2024-03-10"
---
## Instructions
Step-by-step instructions here...
```

## Key System Components

### 1. Recipe Capture Engine
- **URL Parser**: Extracts structured data from 500+ recipe websites using custom selectors and AI fallback
- **Smart Normalization**: Converts "2 cups flour" → "250g all-purpose flour" using ingredient database
- **Duplicate Detection**: Identifies similar recipes already in collection
- **Manual Entry**: Clean markdown editor with live preview

### 2. Adaptive Meal Planner
- **Weekly Calendar Interface**: Drag-and-drop recipes onto days
- **Balance Algorithm**: Ensures variety (cuisine diversity, nutrition balance, cooking time distribution)
- **Leftover Integration**: Suggests recipes using ingredients from previous meals
- **Seasonal Awareness**: Prioritizes seasonal produce based on location and date
- **Effort Balancing**: Mixes quick weekday meals with weekend projects

### 3. Intelligent Shopping System
- **List Generation**: Aggregates ingredients from selected recipes
- **Smart Categorization**: Groups by store section (produce, dairy, pantry, etc.)
- **Pantry Integration**: Subtracts already-owned ingredients
- **Quantity Optimization**: "2 recipes need 1 onion each" → "2 onions"
- **Store-Specific Lists**: Custom categories for your preferred grocery stores

### 4. Ingredient Substitution Engine
- **Allergy/Preference Database**: Tracks 50+ common restrictions
- **Context-Aware Substitutions**: 
  - Structural (egg → flax egg for binding)
  - Flavor-based (parmesan → nutritional yeast + salt)
  - Cuisine-appropriate (soy sauce → tamari for gluten-free)
- **Pantry-First Suggestions**: Prioritizes substitutions using ingredients you already own

### 5. Taste Profile Learner
- **Multi-Dimensional Tracking**:
  - Cuisine preferences (Italian: 9/10, Thai: 7/10)
  - Flavor affinities (umami: high, spicy: medium)
  - Ingredient likes/dislikes (loves: garlic, avoids: cilantro)
  - Cooking style preferences (quick: 8/10, baking: 6/10)
- **Implicit Learning**: Notes which recipes get cooked repeatedly vs. abandoned
- **Explicit Feedback**: Simple "thumbs up/down" after cooking
- **Trend Detection**: "User is exploring more vegetarian recipes lately"

### 6. Recipe Recommender
- **Collaborative Filtering**: "Users who liked X also liked Y"
- **Content-Based Filtering**: Similar ingredients, techniques, or cuisine
- **Contextual Suggestions**: 
  - "You have chicken and bell peppers → try fajitas"
  - "Quick dinner needed → 15-minute pasta"
  - "Friends coming over → impressive but easy mains"
- **Discovery Mode**: Periodically suggests recipes outside usual preferences

### 7. Cooking Assistant
- **Step-by-Step Mode**: Full-screen cooking view with progress tracking
- **Integrated Timers**: "Add pasta" → sets 9-minute timer automatically
- **Voice Control**: Hands-free navigation while cooking
- **Adjustable Servings**: Dynamically recalculates ingredient amounts
- **Progress Saving**: Resume interrupted cooking sessions

## Social & Sharing Features

**Agent-to-Agent Handoff:**
- Encrypted recipe bundles with metadata
- Taste profile compatibility scoring
- "Try my version" recipe variations

**Family Accounts:**
- Shared meal planning with permission levels
- Combined shopping lists
- Individual preference tracking within household

**Community Features (Optional):**
- Public recipe sharing (anonymized taste data)
- "Cook along" events
- Seasonal challenges

## Nutrition Tracking

**Built-In Database:**
- 200 core ingredients with detailed nutrition
- USDA API integration for expansion
- Recipe-level nutrition aggregation
- Weekly nutrition reports

**Dietary Pattern Analysis:**
- Macronutrient balance over time
- Vegetable variety scoring
- Processed food percentage

## Technical Implementation

**Data Persistence:**
- Primary: Markdown files for human readability
- Secondary: JSON indexes for fast querying
- Backup: Encrypted cloud sync (optional)

**AI Integration:**
- Local LLM for privacy-sensitive operations
- Cloud AI for complex analysis (opt-in)
- Progressive model training on user data

**Platform Support:**
- Web app (primary interface)
- Mobile apps (cooking assistant mode)
- Smart display/tablet kitchen interface
- Voice assistant integration

## The One-Year Evolution

**Month 1-3:** Learns explicit preferences, builds recipe collection
**Month 4-6:** Notices patterns ("always cooks Italian on Fridays")
**Month 7-9:** Proactively suggests meal plans requiring minimal shopping
**Month 10-12:** Anticipates needs ("You'll want comfort food this week based on weather and schedule")

## Privacy & Control

- All data stored locally by default
- Clear data ownership: you control what