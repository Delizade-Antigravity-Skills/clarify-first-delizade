# Clarify First (Clarify & Plan Protocol - Delizade Edition)

An Antigravity & Agentic IDE Skill that unifies autonomous codebase fact-finding, dynamic decision tree traversal, interactive modal panel questioning (`ask_question` tool), continuous deep grilling, and **2-question incremental in-place plan document synchronization with zero data loss**.

## Overview

When designing features, refactoring architecture, or clarifying specifications, developers often suffer from two extremes:
1. **The Over-Questioning Trap:** Agents asking endless open-ended questions in chat, overwhelming the user with decision fatigue.
2. **The Assumption Trap:** Agents jumping into code prematurely based on unverified assumptions, causing massive rework and diff churn.

`clarify-first-delizade` provides a streamlined, zero-overhead alignment loop:
- **Silent Fact-Finding (AI Owns Facts):** The agent autonomously inspects existing files, types, routes, and tokens. It never burdens the user with trivia that can be grepped.
- **Interactive Modal Panel Alignment (User Owns Decisions):** Open architectural decisions are queried strictly **ONE AT A TIME** directly inside the IDE's interactive UI panel/modal dialog (`ask_question` tool) with clickable options and an explicit agent recommendation (**`(Recommended)`**). No raw question text cluttering chat.
- **⚡ 2-Question Incremental In-Place Sync Cadence:** Every 2 questions answered, the agent immediately persists settled decisions into the active plan document on disk (`docs/plan-*.md` or `implementation_plan.md`) with **Zero Data Loss**, ensuring progress is never lost, and immediately resumes the grilling loop.
- **Plan-Only Invariant (No Auto-Execution):** The agent stops immediately upon completing or updating the plan. It is strictly prohibited from touching application source code (`src/`).

## Origins & Synthesis

This skill is a unified synthesis and architectural evolution of two foundational skills:
* **`grilling` (Antigravity Core / Gemini):** Provides the dynamic decision tree (frontier) model, continuous deep interview rigor, and the strict invariant that codebase fact-finding is the AI's autonomous responsibility.
* **`ask-then-build` (by David Ondrej):** Provides the low-cognitive-load, sequential single-question format (elevated to interactive modal UI panels).
* **Delizade Directive:** Plan-only execution boundary that guarantees the agent never touches source code (`src/`), combined with the 2-question in-place plan sync protocol with zero data loss.

## Installation

### Antigravity Global Skill
Place `SKILL.md` under your global skills directory:
```bash
mkdir -p ~/.gemini/config/skills/clarify-first-delizade
cp SKILL.md ~/.gemini/config/skills/clarify-first-delizade/SKILL.md
```

### Project-Specific Skill
Place `SKILL.md` under your workspace `.agents/skills/` directory:
```bash
mkdir -p .agents/skills/clarify-first-delizade
cp SKILL.md .agents/skills/clarify-first-delizade/SKILL.md
```

## Structure
- [`SKILL.md`](SKILL.md): Authoritative operational rules, workflow phases, modal panel directives, 2-question sync cadence, and plan synthesis specifications.
