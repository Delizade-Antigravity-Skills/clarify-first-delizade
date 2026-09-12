---
name: clarify-first-delizade
description: Scope and clarify features, updates, or architecture. Prompts interactive modal panels via ask_question tool, updates existing plan documents in-place (or compiles a new plan), and STOPS without touching source code.
---

<!--
================================================================================
SKILL SYNTHESIS & DERIVATION METADATA
================================================================================
This skill (`clarify-first-delizade`) is a unified synthesis and architectural evolution
of two foundational skills:

1. `grilling` (Antigravity Core / Gemini):
   - Autonomous Fact-Finding: AI autonomously inspects the codebase/environment rather than burdening the user with factual questions.
   - Dynamic Decision Tree (Frontier): Models architectural problems as a dependency graph where resolved decisions unlock downstream branches and prune irrelevant ones.
   - Anti-Assumption Rigor: Prevents proceeding on silent assumptions.

2. `ask-then-build` (by David Ondrej):
   - Interactive Modal / Panel UI: Questions are presented directly in the IDE's interactive modal panel (`ask_question` tool) rather than raw chat text, featuring selectable options and an explicit `(Recommended)` first option.
   - Sequential Low-Cognitive-Load Interaction: Questions are asked strictly ONE AT A TIME.
   - Early Termination & Scope Control: Avoids over-questioning; terminates as soon as the core path is clear.
   - Plan Synthesis & In-Place Refinement: Compiles settled decisions into an execution-ready plan or weaves them directly into an existing plan document.

3. Delizade Directive (Strict Plan-Only & In-Place Update Invariant):
   - Scope is strictly limited to clarification, decision resolution, and implementation planning.
   - The agent MUST NOT touch application source code (`src/`), database migrations, or build commands.
   - If an existing plan document is referenced or active, the agent updates that document directly in-place. It stops immediately upon delivering or updating the plan.
================================================================================
-->

Turn feature ideas, component/system updates, architectural decisions, or refactoring requests into execution-ready build specifications through autonomous codebase exploration, sequential interactive modal panels, and in-place plan refinement or concise plan compilation.

---

## Workflow Overview

```mermaid
flowchart TD
    Idea["User Feature / Update / Refactor / Plan Doc"] --> Phase0["Phase 0: Silent Fact-Finding<br/>(Grep, Inspect Codebase, Verify Types & Target Plan)"]
    Phase0 --> TreeEval{"Is there an open<br/>decision or ambiguity?"}
    TreeEval -- "Yes" --> Phase1["Phase 1: Ask Next Frontier Question<br/>(Interactive Modal Panel via ask_question Tool)"]
    Phase1 --> UserAnswer["User Panel Selection / Input"]
    UserAnswer --> Recompute["Recompute Design Tree & Prune Irrelevant Branches"]
    Recompute --> TreeEval
    TreeEval -- "No (Settled / Frontier Empty)" --> Phase2["Phase 2: In-Place Plan Update OR Deliver New Plan"]
    Phase2 --> Stop["Plan Ready / Updated<br/>(Execution HALTED - Zero Source Code Changes)"]
```

---

## Phase 0 — Silent Fact-Finding (Fact vs. Decision Boundary)

1. **Facts belong to the AI, never the user.** Before asking anything, autonomously inspect the repository using available search and read tools:
   - Identify affected files, call sites, exports, interfaces, domain services, database schemas, and existing UI components.
   - Trace existing behaviors, edge cases, regression risks, architectural rules, and design system tokens.
   - Detect if an existing plan document is active or referenced (e.g., `docs/plan-*.md`, `implementation_plan.md`, or a file mentioned in context).
2. **Never ask the user for facts you can look up yourself.** If a file path, function signature, current implementation, or configuration can be grepped, find it silently.
3. Formulate questions **only** for genuine business, architectural, UI/UX, or behavioral decisions where multiple valid tradeoffs or update strategies exist.

---

## Phase 1 — Interactive Panel Alignment (`ask_question` Modal Tool)

1. Map open decisions as a **dynamic decision tree**. Identify the single most pivotal root decision currently on the **frontier** (decisions whose prerequisites are already settled).
2. Ask strictly **ONE question at a time**. Never bundle multiple questions together, as downstream questions often become obsolete based on the first answer.
3. **Mandatory Interactive Modal / Panel Invariant (CRITICAL)**:
   <formatting_directive priority="critical">
   - **NEVER output questions as raw markdown text in the chat conversation.** The user must see the question, choices, and recommendation rendered inside the IDE's interactive UI panel/modal dialog.
   - ALWAYS invoke the `ask_question` tool to present questions.
   </formatting_directive>

4. **`ask_question` Modal Tool Usage Rules**:
   - **Question Title & Context**: State the core question and concise architectural/tradeoff context. When specifying files, format them as clickable links (e.g. `[filename](file:///path/to/file)`).
   - **List Recommended Option First**: The AI's recommended choice MUST be listed as the very first option and prefixed with `(Recommended)` (e.g., `(Recommended) Decouple via event bus and keep state immutable`).
   - **Direct User Perspective**: Format option labels as the user's direct response/action (e.g., `"Use standard SQLite transaction"` rather than `"I will use SQLite transaction"`).
   - **No Manual Letters or 'Other'**: Do NOT prefix options with letters ("A.", "B.") and do NOT add an "Other" option (the IDE modal automatically numbers options and provides a default write-in input).
   - **IsMultiSelect**: Set to `false` for single-choice architectural forks; set to `true` only when independent multiple options can be chosen together.

5. Execution is blocked in the IDE until the user clicks an option and presses Submit in the modal panel.
6. **Dynamic Tree Recomputation & Early Exit:**
   - Upon receiving the user's answer from the panel, immediately update the decision tree.
   - Prune all branches made irrelevant by this answer.
   - If all necessary architectural, behavioral, and functional ambiguities are resolved, **terminate Phase 1 immediately** (typically 1 to 3 questions maximum). Do not ask artificial filler questions.

---

## Phase 2 — Plan Synthesis & In-Place Refinement

Once the frontier is resolved and no ambiguities remain, the agent delivers the settled plan according to the target context:

### Mode A: In-Place Plan Update (When an Existing Plan Document Exists)
If the user provides a plan document, references one in the prompt, or is actively working on a plan file (e.g. `docs/plan-*.md`, `implementation_plan.md`):
1. **Direct In-Place Modification**: Update the target plan document directly on disk using file editing tools (`replace_file_content` or `write_to_file`).
2. **Surgical Integration**: Cleanly weave the resolved architectural decisions, new rules, and updated phases into the document's existing structure. Prune obsolete sections without creating duplicate files or orphaned drafts.
3. **No Chat Bloat**: Do NOT dump the entire plan text into the chat conversation. Provide a concise, bulleted changelog highlighting what was updated in the plan file, and link to the updated document.

### Mode B: Greenfield Plan Delivery (When No Plan Document Exists)
If no existing plan document is referenced, compile a single, dense, execution-ready **Implementation Plan**:
1. **Authoritative Context & Read-First Files**: Target files, schemas, and governing rules (`AGENTS.md`, design tokens, service contracts).
2. **Concrete Implementation Steps**: Numbered, file-level changes detailing exact modifications without speculative fluff.
3. **Validation & Verification**: Automated tests, typecheck commands, lint checks, or manual visual validation criteria.
4. **Execution Boundaries**: Strict scope boundaries (e.g., zero regression on existing interfaces, adhere to design system, no unused dependencies).

---

## 🛑 CRITICAL INVARIANT: SOURCE CODE LOCK (ZERO CODE EXECUTION)

This skill is strictly a **clarification and planning** skill.
1. **Source Code is Locked**: Under NO circumstances should the agent modify application source code (`src/`), run database migrations, install packages, or execute build commands.
2. **Permitted File Modifications**: The ONLY allowed file write/edit operations are on the designated plan or architecture documentation files (e.g., `docs/plan-*.md`, `implementation_plan.md`).
3. **Explicit Handoff**: After updating or delivering the plan, explicitly notify the user:
   > *"Plan dökümanı güncellendi / hazırlandı. Planı inceleyip onayladığınızda veya başlamak istediğinizde uygulamaya geçebiliriz."*
4. **HALT EXECUTION IMMEDIATELY**: Do not start code implementation. Wait for the user's explicit approval to execute.
