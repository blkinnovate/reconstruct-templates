---
priority: 2
command_name: recon-seed
description: "Onboarding flow: seed Context Cloud with project knowledge"
version: v0.1
---

# recon-seed

Local-first onboarding that seeds the Context Cloud. Default to scan-first inference, ask only essential questions when the repo/docs cannot answer them with high confidence, then present a seeded context draft for approval.

---

## Prereqs

- Read `.reconstruct/preferences.json` → `project_id`
- Call `get_user_projects` (verify MCP)
- No project_id? → "Run /recon-setup first"
- Call `get_master_context_sections` → existing sections (for re-seed)

---

## Flow

1. **Scan first** – README, docs, package.json, tsconfig, key dirs, configs, and stable entry points (see Scan List below).
2. **Draft inferred sections** – Generate high-confidence seeded context from code/docs/config alone.
3. **Ask only essential questions** – Ask a small set of follow-up questions only for material gaps, ambiguity, or business rules that are not inferable with high confidence.
4. **Synthesis** – Merge repo evidence + user answers into final sections. Present for approval.
5. **Seed** – Write approved seeded context only through the correct seeded context path. Never use task-plan storage as a substitute.

---

## Essential Questions Only

Ask questions only when the answer materially changes seeded context and cannot be inferred from the repo/docs with high confidence.

Good reasons to ask:
- Core business rules or invariants are unclear
- Primary users/stakeholders are not visible from the code/docs
- Sensitive or off-limits areas are not documented in the repo
- Docs and implementation conflict in a way that changes the seed
- Team-specific conventions are not inferable from the codebase

Do not ask when:
- Stack, structure, and conventions are already visible in code/config/docs
- The answer would just confirm something already strongly evidenced
- The uncertainty is minor and can simply be omitted

Target:
- `quick`: zero interview questions
- `deep`: usually 2-4 essential questions total, not large batches

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

Also use:
- package scripts / lockfile for workflow clues
- API routes and migrations for architecture and data boundaries
- recent git history only if it reveals stable decisions, not transient churn

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
5. **Inference-first** – If the repo provides high-confidence evidence, include it without asking.
6. **Essential questions only** – Keep follow-up questions minimal and tied to real gaps.

---

## MCP Write

Use `create_context_section` for approved seeded context writes.

Required fields:
- `project_id`
- `type`
- `title`
- `content`

Recommended fields for seeding:
- `tags: ["seeded"]`
- `source_type: "seeded"`
- `git_integration_id` or `repo_slug` when repo-scoped context is known

Never use `store_task_plan` as a substitute for seeded context. Task plans are not seeded project context and pollute the wrong workflow.

If `create_context_section` is unavailable in the current environment, stop after producing the draft and tell the user plainly that seeded writes are unavailable instead of routing through another tool.

---

## Approval Checkpoints

- After scan + inferred draft: "Approve this draft / answer essential questions?"
- After Synthesis: "Approve to write to cloud?"
- Before each write: user must approve

---

## Re-seed

- If existing sections: "Re-seed will update/merge. Continue?"
- User chooses: full replace or add-only

---
