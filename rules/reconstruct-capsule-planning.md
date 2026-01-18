---
version: v0.2
---

# Capsule Plan Creation Rules

Create implementation plans for capsules. Explore codebase, ask only when necessary, create plan, confirm with user.

## Workflow

### 1. Context Gathering

- Read user request
- Check `.reconstruct/preferences.json` for `project_id`
- Call `get_project_capsules` to see existing work
- Call `get_master_context_sections` for project docs

### 2. Codebase Exploration

- Use `codebase_search` for semantic queries
- Use `read_file` for discovered files
- Identify patterns, conventions, dependencies

### 3. Question Strategy

**Ask ONLY when:** Requirements ambiguous, multiple approaches exist, critical info missing.

**Don't ask if:** Info in codebase, standard patterns exist, can infer from context.

### 4. Plan Creation

**Execution Type:**
- **Single-step:** Self-contained, no dependencies, one response
- **Multi-step:** Sequential steps, user confirmation needed between steps

**YAML Frontmatter:**
```yaml
---
capsule_ref: "[Capsule name or ID]"
execution_type: "[single-step|multi-step]"
---
```

**Required Sections:**
1. `## Objective` - What will be accomplished
2. `## Instructions` - Steps or bullets
   - Single-step: bullets, "Complete all in one response"
   - Multi-step: numbered, "AWAIT USER CONFIRMATION between steps"
3. `## Expected Output` - Deliverables/files/changes
4. `## Progress Updates` - What to report via `report_capsule_progress`

### 5. Confirmation

Present summary:
```
**Capsule:** [name]
**Type:** [single-step|multi-step]
**Key Work:** [2-3 sentences]
**Files:** [key paths]

Confirm? (yes/no)
```

Wait for "yes" → save to `.reconstruct/temp/capsule-plan-[timestamp].md`

## Progress Reporting

**During Implementation (Multi-Step):**

After each step, call `report_capsule_progress`:
```json
{
  "session_id": "uuid",
  "capsule_id": "uuid",
  "status": "in_progress",
  "progress": {
    "summary": "Completed step N",
    "files_modified": ["path/to/file.ts"]
  }
}
```

**At Completion:**

Call `report_capsule_progress` with `status: "completed"`.

Update task via `update_project_task` with `status: "Done"`.

## Decision Guide

| Scenario | Type |
|----------|------|
| Simple change, no deps | single-step |
| User confirmation needed | multi-step |
| Sequential dependencies | multi-step |
| Complex validation | multi-step |

## Quality Check

Before presenting:
- [ ] Frontmatter complete
- [ ] All sections present
- [ ] Instructions specific + actionable
- [ ] File paths accurate
- [ ] Progress updates defined
