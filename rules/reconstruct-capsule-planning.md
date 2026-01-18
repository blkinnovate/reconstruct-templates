---
version: v0.3
---

# Capsule Plan Format

Create implementation plans that agents can execute.

## Plan Template

```markdown
---
capsule_ref: "[Capsule name or ID]"
execution_type: "[single-step|multi-step]"
---

## Objective
[1-2 sentences: what will be accomplished]

## Instructions

### For single-step:
- Bullet list of what to do
- Complete all in one response
- No user confirmation needed between items

### For multi-step:
1. Step 1: [description]
   - Sub-tasks...
   **AWAIT USER CONFIRMATION**

2. Step 2: [description]
   - Sub-tasks...
   **AWAIT USER CONFIRMATION**

## Expected Output
- [Files created/modified]
- [Functionality delivered]

## Progress Updates
- Report after each step (multi-step only)
- Report at completion (all types)
```

---

## Execution Type Decision

| Scenario | Type |
|----------|------|
| < 3 file changes, no deps | single-step |
| Sequential dependencies | multi-step |
| Needs intermediate validation | multi-step |
| Complex or risky changes | multi-step |
| User explicitly wants control | multi-step |

---

## Context Gathering (Before Planning)

1. **Check existing capsules:** `get_project_capsules`
2. **Check project docs:** `get_master_context_sections` (if available)
3. **Quick codebase scan:** `codebase_search` for related code
4. **Read key files:** Understand patterns and conventions

**Ask questions ONLY if:**
- Requirements genuinely ambiguous
- Multiple valid approaches exist
- Critical information missing from codebase

---

## Plan Confirmation

Before storing plan, present summary:

```
📋 Plan Summary

Capsule: [name]
Type: [single-step|multi-step]
Objective: [1-2 sentences]
Key files: [main paths]
Steps: [count]

Approve? (yes/no)
```

**Wait for "yes"** → Save to `.reconstruct/temp/capsule-plan-[timestamp].md`

---

## Quality Checklist

Before presenting plan:

- [ ] Frontmatter has `capsule_ref` + `execution_type`
- [ ] Objective is clear and measurable
- [ ] Instructions are specific (file paths, function names)
- [ ] Expected output is concrete
- [ ] Multi-step has `**AWAIT USER CONFIRMATION**` markers
- [ ] File paths are accurate (verified via codebase search)

---

## Common Patterns

**Feature Addition:**
```
1. Add types/interfaces
2. Implement core logic
3. Add UI components
4. Wire up and test
```

**Bug Fix:**
```
1. Reproduce and locate issue
2. Implement fix
3. Add regression test
4. Verify fix
```

**Refactor:**
```
1. Identify all usages
2. Create new abstraction
3. Migrate usages incrementally
4. Remove old code
```
