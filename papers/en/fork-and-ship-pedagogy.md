# Fork-and-Ship Pedagogy: Learning by Doing, Teaching by Forking

**Authors:** Superinstance & Lucineer (DiGennaro et al.)
**Date:** 2026-04-02
**Status:** Research Paper — Cocapn Architecture Series

---

## Abstract

The best way to learn software is to break it. The best way to teach it is to let someone fork your repo, modify it, and ship their own version. This paper presents "fork-and-ship" as a pedagogical framework for Cocapn—where learning is not consumption but creation, where students don't read tutorials but fork working agents and make them their own. Drawing on SuperInstance's research into tile versioning, federated learning, and tile marketplace dynamics.

---

## 1. Introduction

Traditional software education follows a consumption model: read the docs, follow the tutorial, complete the exercise. Knowledge flows one direction—from author to learner.

Fork-and-ship inverts this. A student forks a working repo-agent, modifies it to solve their own problem, and ships it. The fork IS the lesson. The modifications ARE the understanding. Shipping IS the proof of learning.

This is how open source actually works. It's how most developers actually learn. Cocapn makes it formal.

---

## 2. Core Concepts

### 2.1 The Fork as Canvas

A repo-agent fork is not a copy—it's a canvas. The student brings their own context, their own problem, their own constraints. The fork adapts.

```
Original Agent: "Fishing log for Alaskan waters"
    │
    ├── Student A forks: "Surf fishing log for California"
    ├── Student B forks: "Commercial crab pot tracker"
    ├── Student C forks: "Marine biology research logger"
    └── Student D forks: "Fishing tournament scoring system"
```

Each fork teaches different concepts based on what the student changes.

### 2.2 Learning Through Modification

From SuperInstance's tile versioning research, we know that branching preserves learned intelligence. In fork-and-ship pedagogy:

- The **original repo** is the textbook
- The **diff** is the student's homework
- The **merge** is peer review
- The **ship** is the exam

### 2.3 Federated Pedagogy

From SuperInstance's federated tile learning:

> "Organizations share learned decision boundaries as inspectable tiles."

Students share their modifications back with the class. Each student's fork becomes a "learned tile"—a self-contained unit of knowledge that others can validate, integrate, or build upon.

```
Student A learns: "How to add weather API integration"
Student B learns: "How to add offline-first storage"
Student C learns: "How to add multi-language support"

All three share back → The class collectively knows all three.
```

---

## 3. Application to Cocapn

### 3.1 The Classroom Repo

A teacher creates a seed agent with intentional learning opportunities:

```yaml
# .cocapn/learning.yaml
module: fishing-log-101
instructor: professor-cod
challenges:
  - id: 1
    title: "Add a new fish species tracker"
    hint: "Look at src/species.ts"
    concepts: [data-modeling, CRUD]
    difficulty: beginner
  - id: 2
    title: "Add weather correlation"
    hint: "Consider external APIs"
    concepts: [async, API-integration, error-handling]
    difficulty: intermediate
  - id: 3
    title: "Predict catch rates using history"
    hint: "Look at src/analytics/"
    concepts: [statistics, ML-basics, data-pipelines]
    difficulty: advanced
```

### 3.2 The Portfolio

Each student's fork history becomes their portfolio:

```
Student A's learning journey:
  commit 1: "solved challenge 1 - added species tracker"
  commit 2: "broke something, fixed it"
  commit 3: "solved challenge 2 - weather API"
  commit 4: "went beyond - added tide prediction"
  commit 5: "shipped to personal repo"
```

### 3.3 Cross-Class Pollination

Students from different classes can fork each other's work:

```
Class A (beginners): Creates simple agents
Class B (intermediate): Forks and enhances Class A's agents
Class C (advanced): Forks Class B's agents, adds ML pipelines
```

---

## 4. Implementation Notes

### 4.1 Fork-and-Ship CLI

```bash
# Teacher creates assignment
cocapn classroom create --template fishing-log --challenges ./challenges.yaml

# Student forks
cocapn classroom fork --assignment fishing-log-101 --name my-version

# Student submits
cocapn classroom submit --url github.com/student/my-fishing-log

# Teacher reviews diffs
cocapn classroom review --assignment fishing-log-101
```

### 4.2 Automated Feedback

The repo-agent itself provides feedback on modifications:

```yaml
feedback:
  - file: src/species.ts
    message: "Good data model! Consider adding a validation layer."
    concept: data-validation
  - file: src/weather.ts
    message: "API call works, but what happens when the API is down?"
    concept: error-handling
```

---

## 5. References

- DiGennaro et al., "Tile Version Control and Evolution," SuperInstance Research Notes, 2026
- DiGennaro et al., "Federated Tile Learning," SuperInstance Research Notes, 2026
- DiGennaro et al., "Emergent Swarm Intelligence from SMP Tiles," SuperInstance Research Notes, 2026
- DiGennaro et al., "Human-Tile Collaboration," SuperInstance Research Notes, 2026
