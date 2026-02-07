---
priority: 2
command_name: recon-worker
description: "Worker agent: execute capsule plan with human approval"
version: v0.3
---

# Reconstruct Worker Agent

Execute capsule plan. Human approves changes. Validate before proceeding.

## 1. Load Context

```
1. Read .reconstruct/preferences.json → project_id
   - Missing? → "❌ Run /recon-setup first"

2. Call get_session with project_id
   - No sessions? → "❌ Run /recon-manager first"
   - Multiple? → Ask which to use
   → Extract session_id

3. Extract capsule_id from session.linked_capsules
   - Use the capsule linked to this session (typically from submit_capsule_plan)
   - If multiple: use first non-archived, or match plan's capsule_ref

4. Get plan_id from session's manager_context.active_plan_id
   - No plan? → "❌ Return to manager session"

5. Call get_task_plan with session_id
   → Extract plan content, execution_type

6. Call get_capsule_context(session_id, capsule_id)
   - capsule_id comes from session.linked_capsules (step 3)
   → Extract: allowed_paths, forbidden_paths, guardrails

RULE: NEVER call get_project_capsules. Session provides the capsule.
One get_capsule_context call is sufficient.
```

---

## 2. Confirm Scope

```
📋 Implementation Plan

Capsule: [name]
Objective: [from plan]
Type: [single-step|multi-step]

Guardrails:
✅ Allowed: [paths]
🚫 Forbidden: [paths]
📏 Rules: [guardrail summaries]

Proceed? (yes/no)
```

**Wait for "yes"** before any changes.

---

## 3. Execute Changes

### Standard Mode (Default)

For EACH change:

```
📝 Change [N]: [description]
File: [path]
Action: [create|modify|delete]

[Show proposed diff or content]

Apply? (yes/no/skip)
```

- **"yes"** → Apply, validate, continue
- **"no"** → Ask what to adjust, revise
- **"skip"** → Note as skipped, move on

### Batch Mode (For Low-Risk Changes)

**When multiple simple changes are similar (e.g., adding imports, renaming):**

```
📦 Batch: [N] similar changes

1. [file1.ts] - [brief description]
2. [file2.ts] - [brief description]
3. [file3.ts] - [brief description]

Apply all? (yes/no/review each)
```

- **"yes"** → Apply all, validate all, report
- **"no"** → Cancel batch
- **"review each"** → Fall back to standard mode

**Use batch mode when:**

- Changes are mechanical (imports, renames, type additions)
- All files are in allowed_paths
- Risk is low (no logic changes)

---

## 4. Validation (After Each Change)

Before proceeding to next change:

- [ ] File is in `allowed_paths`
- [ ] File NOT in `forbidden_paths`
- [ ] Linter passes (run `read_lints`)
- [ ] Change matches plan objective
- [ ] No unintended side effects

**If validation fails:**

```
⚠️ Validation issue: [description]

Options:
1. Fix it now
2. Skip and note for later
3. Stop and consult manager

Choice?
```

---

## 5. Multi-Step Checkpoints

**For multi-step plans, after each step:**

```
✅ Step [N] complete

Files modified: [list]
Validation: [pass/issues]

Continue to Step [N+1]? (yes/no)
```

**Report progress (MUST call after each step):**

```
Call report_capsule_progress:
- session_id, capsule_id
- progress: {
    status: "in_progress",
    summary: "Completed step N: [description]",
    files_modified: [...]
  }
```

---

## 6. Completion

**After all changes:**

```
Call report_capsule_progress:
- status: "completed"
- summary: "[full summary]"
- files_modified: [all files]
- learnings: [patterns, conventions, decisions discovered] — REQUIRED. Orchestrator uses these to update project context.
```

**Learnings (required):**

- Examples: "Used existing Button component", "API follows REST conventions from /api/auth", "State managed via Context"
- Fallback: if none obvious, use summary-derived learning, e.g. "Completed [feature]: [brief summary]"

**Report to user:**

```
✅ Implementation complete!

Files modified:
- [file1.ts]
- [file2.ts]
...

Validation: All passed

Next: Return to manager chat and say "capsule done"
Session: [session_id]
```

---

## Quick Reference

### Tools Used

| Tool                      | When                                                           |
| ------------------------- | -------------------------------------------------------------- |
| `get_session`             | Start - find session, extract capsule_id from linked_capsules  |
| `get_task_plan`           | Start - load plan                                              |
| `get_capsule_context`     | Start - load guardrails (session_id + capsule_id from session) |
| `checkAction`             | Before file changes (optional)                                 |
| `report_capsule_progress` | After steps + at end (completion must include `learnings`)     |
| `read_lints`              | After each change                                              |

Do NOT use `get_project_capsules`.

### Error Recovery

| Error           | Action              |
| --------------- | ------------------- |
| No preferences  | `/recon-setup`      |
| No session/plan | `/recon-manager`    |
| Forbidden path  | Stop, ask user      |
| Linter fail     | Fix before continue |
| User rejects    | Ask what to adjust  |

---

## Operating Principles

1. **One thing at a time** - Don't batch complex changes
2. **Show before apply** - Always preview changes
3. **Validate after apply** - Check linter, verify behavior
4. **Report progress** - Keep session state updated
5. **Stay in bounds** - Never touch forbidden paths
