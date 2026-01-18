---
priority: 5
command_name: recon-help
description: "Tutorial and quick reference for Reconstruct workflow"
version: v0.3
---

# Reconstruct Help

Get help with Reconstruct workflow.

## Usage

```
/recon-help           → Show this quick reference
/recon-help tutorial  → Full tutorial walkthrough
/recon-help [topic]   → Help on specific topic
```

---

## Quick Reference

### Commands

| Command | Purpose |
|---------|---------|
| `/recon-setup` | Connect workspace to project (one-time) |
| `/recon-manager` | Manager agent: plan work, create capsules |
| `/recon-worker` | Worker agent: execute plans |
| `/recon-help` | This help |

### Typical Workflow

```
1. /recon-setup      → Connect project (once)
2. /recon-manager    → Describe work, create capsule + plan
3. Open new chat
4. /recon-worker     → Execute the plan
5. Return to manager chat, say "done"
```

---

## Tutorial Mode

**If user said `/recon-help tutorial`:**

### Welcome to Reconstruct! 👋

Reconstruct helps you work with AI agents on coding projects with guardrails and context management.

**Key Concepts:**

1. **Project** - Your codebase connected to Reconstruct
2. **Capsule** - A workspace for specific work (has guardrails, allowed paths)
3. **Session** - Active work context (links you to a capsule + plan)
4. **Plan** - Implementation instructions the agent follows

**Two Agents:**

| Agent | Role | Command |
|-------|------|---------|
| Manager | Plans work, creates capsules, reviews results | `/recon-manager` |
| Worker | Executes plans, makes code changes | `/recon-worker` |

**Why Two Agents?**

- Manager has broader context (project overview, planning)
- Implementation is focused (just the current task)
- Clean handoff prevents context pollution
- Human stays in control at transition points

---

### Let's Try It!

```
Step 1: Run /recon-setup to connect your project
Step 2: Run /recon-manager and describe some work
Step 3: The manager will create a capsule and plan
Step 4: Open a new chat window
Step 5: Run /recon-worker to execute
Step 6: Approve each change as it's made
Step 7: Return to manager when done
```

**Ready?** Run `/recon-setup` to begin!

---

## Topic Help

**If user said `/recon-help [topic]`:**

### Topics

**capsules** - Capsules define work scope:
- `allowed_path_patterns` - Where agent CAN work
- `forbidden_path_patterns` - Where agent CANNOT work
- `guardrails` - Rules agent must follow
- `task_summary` - What needs to be done

**sessions** - Sessions track active work:
- Max 2 active sessions per user per project
- Contains reference to current plan
- Archive when work complete

**plans** - Plans guide implementation:
- Created by manager agent
- Single-step or multi-step
- Implementation agent follows exactly

**guardrails** - Safety constraints:
- Path restrictions (allowed/forbidden)
- Operation restrictions (no delete, etc.)
- Content rules (patterns to follow)

**errors** - Common issues:
- "MCP not configured" → Check API key in Cursor settings
- "No session" → Run `/recon-manager` first
- "2 session limit" → Archive an old session
- See `/recon-help recovery` for more

**recovery** - When things go wrong:
- Check state with preferences.json + get_session
- Most issues fixed by re-running setup or manager
- Nuclear option: delete `.reconstruct/` and start fresh

---

## Still Stuck?

```
1. Check reconstruct.app dashboard for project state
2. Review capsule settings in dashboard
3. Try /recon-sync to refresh context
4. Delete .reconstruct/ and run /recon-setup fresh
```
