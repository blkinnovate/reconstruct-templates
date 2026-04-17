---
priority: 6
command_name: recon-ask-constructor
description: "Generate a capsule + implementation plan via ask_constructor"
version: v0.2
aliases: [ask-constructor, recon-constructor]
---

# recon-ask-constructor

Use `ask_constructor` to generate a coding-ready task package (virtual capsule context + implementation plan) in one call, convert it into a real capsule + stored plan, then execute the plan in this same chat (no separate manager handoff required).

## Usage

```
/recon-ask-constructor <mission>
/ask-constructor <mission>
```

Optional: include extra context cues in the mission text, like file paths (`/app/...`) or key terms to search.

---

## Flow

1. Read `.reconstruct/preferences.json` → `project_id`
   - Missing? → "❌ Run /recon-setup first"

2. Call `ask_constructor`:

   - Required:
     - `project_id`
   - Recommended:
     - `mission`: user-provided mission text (required for this command)
     - `search`: short query that helps retrieval (derive from mission)
     - `linked_file_paths`: include any explicit file paths mentioned by the user

3. Present the result clearly:
   - Show the returned `implementation_plan` (Objective / Instructions / Expected Output / Progress Updates if available)
   - Summarize key risks/assumptions and the proposed scope (paths to edit)

4. Convert the result into a standard Reconstruct workflow (manager-style):
   - Create a real capsule via `create_project_capsule` with:
     - `name`: short descriptive name derived from mission
     - `task_summary`: the mission
     - `allowed_path_patterns`: narrow globs based on the planned edits (prefer the smallest set that fits)
     - `forbidden_path_patterns`: exclude secrets/config and build outputs (e.g. `/.env*`, `/.reconstruct/**`, `/node_modules/**`, `/.next/**`, `/dist/**`)
     - `guardrails`: at minimum, “stay in mission scope” + “avoid unrelated refactors”
   - Store the implementation plan via `store_task_plan` (with `project_id`, plan content, `session_id`, and metadata as required) and capture the returned plan id.
   - Link the plan to the active session via `update_session` so both `manager_context.active_plan_id` and `session_context.active_task_plan_id` reference the stored plan.
   - Link the capsule to the session via `submit_capsule_plan(session_id, capsule_id, planned_paths)` using the MCP fields your tool schema defines.

5. Execute the plan immediately (worker-style, in this same chat):
   - Load the stored plan via `get_task_plan(session_id)`
   - Load capsule scope via `get_capsule_context(session_id, capsule_id)` — if several capsules are linked, use `session.linked_capsules` (prefer the capsule tied to this plan / non-archived entry).
   - Confirm scope with the user (allowed paths / forbidden paths / guardrails) and wait for explicit approval before edits.
   - Apply the plan step-by-step:
     - For each change, show the proposed diff/content first
     - Wait for explicit user approval before applying
     - Validate with `read_lints` after substantive edits
   - Report progress after each step via `report_capsule_progress(session_id, capsule_id, progress)` with `status: "in_progress"` while work continues.
   - On completion, call `report_capsule_progress` with:
     - `status: "completed"`
     - `summary`, `files_modified`
     - `learnings` (required)

---

## Output contract

At the end of this command, you must report:
- Session ID
- Capsule ID
- Stored plan ID
- Files modified
