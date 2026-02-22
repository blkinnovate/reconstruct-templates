---
priority: 2
command_name: recon-seed
description: "Onboarding flow: seed Context Cloud with project knowledge"
version: v0.1
---

# recon-seed

Local-first onboarding that seeds the Context Cloud. Runs question rounds, deep repo scan, synthesis—writes to project_context only with user approval.

---

## Prereqs

- Read `.reconstruct/preferences.json` → `project_id`
- Call `get_user_projects` (verify MCP)
- No project_id? → "Run /recon-setup first"
- Call `get_master_context_sections` → existing sections (for re-seed)

---

## Flow

1. **Rounds** – Round 1: purpose, goals. Round 2: tech stack, architecture. Round 3: conventions, constraints. Round 4: validation + approval checkpoint.
2. **Scan** – README, docs, package.json, tsconfig, key dirs (see Scan List below).
3. **Synthesis** – Combine Q&A + scan into sections. High-confidence only. Present for approval.
4. **Seed** – Write approved sections via MCP. User approval required before each write.

---

## Round Prompts

**Round 1 (Purpose, goals, current state):** What is the purpose? Main goals? Current state? Primary users/stakeholders? → `project_overview`

**Round 2 (Tech stack, architecture, patterns):** Technologies/frameworks? Architecture (monolith, microservices)? Patterns (state, API, testing)? Key libraries? → `tech_stack`, `architecture`

**Round 3 (Conventions, constraints, off-limits):** Coding conventions? Constraints? Off-limits/deprecated? Team preferences? → `code_conventions`

**Round 4 (Validation):** Summarize in bullet list. "Approve to continue to repo scan?" Wait for approval before scan.

---

## Scan List

| Item                                         | Purpose                                 |
| -------------------------------------------- | --------------------------------------- |
| README.md                                    | Project overview, setup, usage          |
| package.json / requirements.txt / Cargo.toml | Dependencies, scripts, project metadata |
| tsconfig.json / jsconfig.json                | TypeScript/JS config                    |
| docs/                                        | Documentation, design docs              |
| Key dirs (src/, app/, lib/, etc.)            | Structure, entry points                 |
| .cursor/                                     | Cursor rules, project-specific config   |
| Config files (eslint, prettier, etc.)        | Conventions                             |

Use `file_structure` for folder org and key paths.

---

## Section Types

| Type               | Source  | Content                       |
| ------------------ | ------- | ----------------------------- |
| `project_overview` | Round 1 | Purpose, goals, current state |
| `architecture`     | Round 2 | Stack, structure, patterns    |
| `tech_stack`       | Round 2 | Technologies, frameworks      |
| `code_conventions` | Round 3 | Style, naming, constraints    |
| `file_structure`   | Scan    | Folder org, key paths         |
| `decisions`        | Rounds  | Key decisions and rationale   |

---

## Synthesis Rules

1. **High-confidence only** – Omit uncertain or speculative content.
2. **Combine Q&A + scan** – Merge user answers with scan. Resolve conflicts in favor of user input.
3. **Present before write** – Get approval before any MCP write.
4. **Omit uncertain** – When in doubt, leave it out.

---

## MCP Write

**Current state:** `store_context_section` MCP tool does not exist. Workaround: use `store_task_plan` for implementation plans only, or note sections for manual entry. When added, use for: `project_overview`, `architecture`, `tech_stack`, `code_conventions`, `file_structure`, `decisions`. Valid types as above. `source_type`: "manual" (from Q&A) or "imported" (from scan).

---

## Approval Checkpoints

- After Round 4: "Approve to continue to repo scan?"
- After Synthesis: "Approve to write to cloud?"
- Before each write: user must approve

---

## Re-seed

- If existing sections: "Re-seed will update/merge. Continue?"
- User chooses: full replace or add-only

---
