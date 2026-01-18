---
version: v0.3
---

# Reconstruct Error Recovery

When things go wrong, follow these recovery steps.

## MCP Connection Errors

**Symptoms:**
- Any MCP tool call fails with 401/403
- "Connection refused" or timeout
- "API key invalid"

**Recovery:**
```
❌ MCP connection failed.

1. Go to reconstruct.app/settings/mcp
2. Verify API key is valid (or generate new one)
3. Check Cursor MCP settings has correct key
4. Restart Cursor
5. Try again
```

---

## Missing Local State

**Symptom:** `.reconstruct/preferences.json` missing or corrupted

**Recovery:**
```
Run /recon-setup to reconnect workspace to project
```

Your server-side data (capsules, sessions, plans) is safe.

---

## Session Limit Reached

**Symptom:** `create_session` fails with "2 session limit"

**Recovery:**
1. Call `get_session` with project_id
2. Show both sessions:
   ```
   Active sessions:
   1. [Name] - created [date]
   2. [Name] - created [date]
   
   Archive which one? (1/2)
   ```
3. Call `archive_session` on user's choice
4. Retry original operation

---

## Stale or Missing Plan

**Symptom:** 
- `get_task_plan` returns 404
- Plan data seems outdated

**Recovery:**
1. Call `get_session` to verify session exists
2. If session missing → "Run `/recon-manager` for new session"
3. If session exists but plan missing → "Return to `/recon-manager` to create plan"

---

## Capsule Conflicts

**Symptom:** `check_conflicts` returns file conflicts

**Recovery:**
```
⚠️ File conflicts detected:

[path/to/file.ts] - being worked on by [other user/session]

Options:
1. Wait for other work to complete
2. Choose a different capsule
3. Coordinate with team member directly
```

**Never:** Proceed with changes on conflicting files.

---

## Worker Stuck

**Symptom:** Worker agent can't complete, repeated errors

**Recovery:**
1. Report the block:
   ```
   Call report_capsule_progress:
   - status: "blocked"
   - blockers: "[description of issue]"
   ```
2. Tell user: "Return to manager session to adjust plan"
3. Manager can modify capsule or create new plan

---

## Validation Failures

**Symptom:** Linter errors, tests failing after changes

**Recovery:**
1. Show the errors clearly
2. Offer options:
   ```
   ⚠️ Validation failed: [error summary]
   
   Options:
   1. Fix the issue now
   2. Revert change and try different approach
   3. Skip and note for manager review
   ```
3. Don't proceed until resolved or explicitly skipped

---

## MCP Error Code Reference

| Code | Meaning | Action |
|------|---------|--------|
| 400 | Bad request | Check tool call parameters |
| 401 | Unauthorized | Verify API key |
| 403 | Forbidden | Check project access permissions |
| 404 | Not found | Verify ID exists (session, capsule, plan) |
| 409 | Conflict | Resource locked, wait and retry |
| 500 | Server error | Wait 30s, retry once, then report |

---

## Nuclear Option

If everything is broken and nothing works:

```
1. Delete .reconstruct/ folder entirely
2. Run /recon-setup (reconnects to project)
3. Run /recon-manager (creates fresh session)

This resets LOCAL state only.
Server data (capsules, tasks, context) remains intact.
```

---

## Reporting Issues

If you encounter persistent issues:

1. Note the error message and MCP tool that failed
2. Check reconstruct.app dashboard for project state
3. Try `/recon-sync` to refresh context
4. If still broken, contact support with:
   - Project ID
   - Session ID (if known)
   - Error message
   - Steps to reproduce
