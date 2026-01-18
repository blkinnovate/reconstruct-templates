---
priority: 0
command_name: recon-setup
description: "One-time project setup and optional tutorial for new users"
version: v0.3
---

# Reconstruct Project Setup

Connect workspace to a Reconstruct project. Run once per workspace.

## 1. Verify MCP

```
Call get_user_projects
```

- **Fails?** → Show setup instructions:
  ```
  ❌ MCP not configured.
  
  1. Go to reconstruct.app/settings/mcp
  2. Copy your API key
  3. Add to Cursor MCP settings
  4. Restart Cursor
  5. Run /recon-setup again
  ```
- **Works?** → Continue

---

## 2. Select Project

**Present projects:**
```
Your projects:

1. [Project Name] - [task count] tasks
2. [Project Name] - [task count] tasks
...

Select project (1-N):
```

**Extract `project_id` from selection**

---

## 3. Save Preferences

**Create `.reconstruct/preferences.json`:**
```json
{
  "project_id": "[selected UUID]",
  "version": "1.0.0"
}
```

**Add to `.gitignore`** if not present:
```
.reconstruct/
```

---

## 4. First Time Check

```
Is this your first time using Reconstruct? (yes/no)
```

**If "yes":** 
```
Would you like a quick tutorial? (yes/no)
```
- "yes" → Run `/recon-help tutorial`
- "no" → Continue to completion

**If "no":** Continue to completion

---

## 5. Done

```
✅ Project connected: [Project Name]

Next steps:
- /recon-manager → Plan and create capsules
- /recon-worker → Execute implementation plans
- /recon-help → Tutorial and reference

Ready to start? Run /recon-manager
```

---

## Notes

- Run once per workspace
- Change projects by running setup again
- Preferences stored locally (not committed)
- For help anytime: `/recon-help`
