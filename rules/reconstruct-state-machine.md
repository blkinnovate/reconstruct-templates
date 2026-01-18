---
version: v0.3
---

# Reconstruct Workflow States

Know where you are in the workflow. Use this to detect state and suggest next action.

## State Detection

Check these in order:

1. **No `.reconstruct/preferences.json`?**
   → State: `UNCONFIGURED`
   → Action: "Run `/recon-setup`"

2. **Has preferences, but `get_session` returns empty?**
   → State: `NO_SESSION`
   → Action: "Run `/recon-manager`"

3. **Has session, but no `manager_context.active_plan_id`?**
   → State: `SESSION_NO_PLAN`
   → Action: "Complete planning in `/recon-manager`"

4. **Has session with plan, capsule not completed?**
   → State: `READY_TO_WORK`
   → Action: "Run `/recon-worker`"

5. **Capsule status = "completed"?**
   → State: `COMPLETED`
   → Action: "Archive session, start new work with `/recon-manager`"

---

## State Diagram

```
UNCONFIGURED
    │
    ▼ /recon-setup
NO_SESSION
    │
    ▼ /recon-manager (start)
SESSION_NO_PLAN
    │
    ▼ /recon-manager (complete plan)
READY_TO_WORK
    │
    ▼ /recon-worker
COMPLETED
    │
    ▼ archive session
NO_SESSION (ready for new work)
```

---

## Quick State Check

When user asks "where am I?" or something seems off:

```
1. Check .reconstruct/preferences.json exists
2. If yes: Call get_session with project_id
3. If session: Check manager_context.active_plan_id
4. If plan: Check capsule status
5. Report state and suggest action
```

Example output:
```
📍 Current State: READY_TO_WORK

Session: [session_id]
Plan: [plan summary]
Capsule: [capsule name]

Next: Run /recon-worker to execute
```

---

## Invalid States (Errors)

| State | Symptom | Fix |
|-------|---------|-----|
| Orphaned session | Session exists but preferences.json missing | Re-run `/recon-setup` |
| Orphaned plan | Plan exists but session archived | Create new session via `/recon-manager` |
| Session limit | 2+ active sessions | Archive one via `archive_session` |
| Stale plan | Plan references deleted capsule | Return to `/recon-manager` |

---

## Agent Identification

**Manager Agent** (`/recon-manager`):
- Broad project context
- Creates capsules and plans
- Coordinates work

**Worker Agent** (`/recon-worker`):
- Focused task context
- Executes plans
- Makes code changes

**Rule:** Each agent runs in separate chat. No switching mid-conversation.
