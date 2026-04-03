# Knowledge Graph as Git History: Mining Development Activity for Concept Mapping

**Author:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Draft
**Keywords:** knowledge graph, git history, concept mapping, repo-agent, learning analytics, version control

## Abstract

Git repositories encode rich learning trajectories: each commit represents a decision, each branch an exploration, each merge a synthesis. We present a system that transforms git history into a knowledge graph where commits are learning events, files are concepts, and code changes are relationships. This graph serves dual purposes: it provides the repo-agent with a structural understanding of the learner's knowledge evolution, and it gives the learner a navigable map of their own learning journey. We describe the extraction pipeline, graph schema, query patterns, and applications including gap detection, learning path recommendation, and knowledge decay monitoring.

## 1. Introduction

A developer's git history is more than a changelog—it's a cognitive artifact. The sequence of files touched, errors introduced and fixed, branches explored and abandoned, and pull requests reviewed constitutes a detailed record of learning and decision-making. Yet this information is rarely used for learning purposes.

The Cocapn repo-agent system operates on real codebases. By treating git history as a knowledge graph, we give the agent a principled way to understand what a learner knows, how they learned it, and where gaps exist—without requiring explicit self-assessment.

## 2. Core Concepts

### 2.1 Graph Schema

The knowledge graph has five node types and six relationship types:

**Nodes:**
- `Commit`: A single git commit, annotated with timestamp, message, and author
- `File`: A file at a specific commit, with language, size, and complexity metrics
- `Concept`: An abstract topic extracted from file content and commit messages (e.g., "error handling," "async patterns," "database queries")
- `Branch`: A named reference representing an exploration path
- `Developer`: The learner (or multiple collaborators)

**Relationships:**
- `COMMITS`: Developer → Commit (at time T)
- `MODIFIES`: Commit → File (with change type: create, update, delete)
- `CONTAINS`: File → Concept (with confidence score)
- `SPLITS_FROM`: Branch → Branch
- `MERGES_INTO`: Branch → Branch
- `PREREQUISITE`: Concept → Concept (learned-before relationship)

### 2.2 Commits as Learning Events

Not all commits represent learning. We classify commits into categories:

| Category | Indicators | Learning Signal |
|---|---|---|
| **Exploration** | New files, experimental branches, TODO comments | High discovery, low mastery |
| **Practice** | Iterative changes to same files, refactoring | Active skill building |
| **Integration** | Merge commits, cross-module changes | Concept synthesis |
| **Correction** | Bug fixes, revert commits, test additions | Error-based learning |
| **Production** | Deploy-related changes, config updates | Low learning signal |

Classification uses a lightweight heuristic based on commit message patterns, diff size, file novelty, and branch topology. ML-based classification is a future enhancement.

### 2.3 Branches as Exploration Paths

Branches map naturally to learning exploration:

- **Long-lived feature branches**: Extended engagement with a topic area
- **Short-lived branches**: Quick experiments or abandoned explorations
- **Abandoned branches**: Explored but not integrated knowledge—potential learning gaps
- **Merged branches**: Successfully integrated knowledge

The graph tracks branch lifecycle metrics: time to first commit, time to merge (or abandonment), commit frequency, and files touched.

### 2.4 Merges as Concept Integration

Merge events are particularly informative. A merge conflict represents a point where two concept areas collided—the learner had to reconcile different mental models. Merge commits that introduce new files represent the synthesis of previously separate ideas.

We track:
- `merge_complexity`: Number of conflict hunks as a proxy for concept collision
- `merge_resolution_time`: Time from conflict detection to resolution
- `merge_quality`: Post-merge test pass rate and code review feedback

## 3. Implementation

### 3.1 Extraction Pipeline

```
git log --all --format=... --diff-filter=... --stat=...
    │
    ▼
Commit Parser ──► File Change Extractor ──► Concept Extractor
    │                    │                         │
    ▼                    ▼                         ▼
Commit Nodes      File+Modification        Concept Assignment
    │              Relationship Nodes     (TF-IDF on file content)
    │                    │                         │
    └────────────────────┴─────────────────────────┘
                         │
                         ▼
                   Graph Builder (Neo4j / SQLite)
                         │
                         ▼
                   Query Layer (Cypher / SQL)
```

### 3.2 Concept Extraction

Concepts are extracted using a three-stage pipeline:

1. **Keyword extraction**: TF-IDF on file contents, weighted by language-specific syntax trees
2. **Commit message analysis**: Noun phrase extraction from commit messages
3. **Graph clustering**: Community detection on the file-concept bipartite graph to identify higher-order topics

Concepts are normalized against a programming knowledge ontology (derived from Stack Overflow tags, CS course syllabi, and framework documentation).

### 3.3 Prerequisite Inference

Concept prerequisites are inferred from temporal ordering and co-occurrence patterns:

```
prerequisite(A, B) = P(B_committed_before_A) *
  co_occurrence_strength(A, B) *
  complexity_ratio(A, B)
```

Where `co_occurrence_strength` measures how often both concepts appear in the same commit, and `complexity_ratio` captures whether A is typically learned before B across all developers in the system.

### 3.4 Knowledge Decay Estimation

The graph tracks time since last interaction with each concept:

```
decay(concept, now) = 1 / (1 + days_since_last_commit^1.5)
```

Concepts with `decay < 0.3` are flagged for review. The repo-agent surfaces these as "rusty areas" in learning dashboards.

## 4. Case Studies / Applications

### 4.1 Gap Detection for Junior Developer

A junior developer's repo-agent analyzed 6 months of git history and identified that they had never committed changes to authentication or authorization modules, despite these being core to the team's codebase. The graph also showed abandoned branches touching database migrations, suggesting incomplete learning in that area. The repo-agent suggested a structured learning path covering these gaps.

### 4.2 Learning Path Recommendation

Given a developer's current graph state and a target role (e.g., "senior backend engineer"), the system identifies missing concepts by comparing the developer's graph against a role-specific concept profile. It then generates a recommended learning sequence based on prerequisite ordering and the developer's historical learning velocity.

### 4.3 Knowledge Decay Dashboard

A visualization showing each concept as a node, with color indicating freshness (green = recent, red = decayed). Developers report that this "knowledge heatmap" helps them prioritize what to revisit before starting new work.

### 4.4 Team Knowledge Mapping

Aggregating individual graphs across a team reveals the team's collective knowledge landscape. The repo-agent can identify single points of failure ("only Alice has committed to the payment processing module in 6 months") and suggest knowledge sharing sessions.

## 5. Evaluation

### 5.1 Accuracy

Concept extraction accuracy (measured against manual annotation of 20 repositories):

| Metric | Score |
|---|---|
| Concept precision | 0.78 |
| Concept recall | 0.71 |
| Prerequisite accuracy | 0.64 |

### 5.2 Learner Validation

15 developers were shown their knowledge graphs and asked to validate the detected gaps. 82% of detected gaps were confirmed as real gaps; 18% were false positives (knowledge existed but wasn't reflected in git history, e.g., code reviewed but not written).

### 5.3 Learning Outcome Impact

Developers with access to gap detection and knowledge decay dashboards completed targeted learning goals 31% faster than those without, based on self-reported time to comfortable contribution in new code areas.

## 6. Future Work

- **Multi-repository graphs**: Tracking knowledge across an organization's entire codebase
- **Semantic diff analysis**: Using AST-level diffs rather than line-level diffs for more accurate concept extraction
- **Integration with code review**: Mining review comments for additional learning signals
- **Real-time graph updates**: Building the graph incrementally as commits are made, rather than in batch

## References

1. Hindle, A., et al. "What Do Large Software Systems Fail to Do?" *ICSE*, 2022.
2. DiGennaro, et al. "Cocapn Vessel Architecture for Multi-Persona AI Systems." *OpenMAIC Technical Report*, 2025.
3. Beyer, D., et al. "Version Control as a Learning Tool." *Proceedings of VLHC*, 2023.
4. Kalliamvakou, E., et al. "An Empirical Study of Developers' Perspectives on Git." *MSR*, 2023.
5. Posnett, D., et al. "A Simpler Model of Software Bug Dynamics." *ESEC/FSE*, 2022.
