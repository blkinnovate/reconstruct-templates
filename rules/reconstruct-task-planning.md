---
version: v0.1
---

# Task Plan Creation Rules

## Overview

Create detailed task plans from user context. Explore codebase, ask questions only when necessary, create comprehensive plan, present summary for confirmation, store in temporary file.

## Workflow

### 1. Context Gathering

-   **Read user request** - Understand what needs to be done
-   **Check project context** - Read `.reconstruct/preferences.json` for `project_id` if available
-   **Gather related context:**
    -   Use `get_project_tasks` to see existing tasks
    -   Use `get_master_context_sections` for project documentation
    -   Use `get_file_structure` to understand codebase layout
    -   Use `get_project_capsules` to see related work

### 2. Codebase Exploration

**Explore systematically:**

-   **Identify relevant files** - Use `codebase_search` for semantic queries about the task domain
-   **Read key files** - Use `read_file` for files mentioned in context or discovered through search
-   **Understand patterns** - Look for similar implementations, existing patterns, conventions
-   **Check dependencies** - Identify what files/modules the task will affect

**Exploration strategy:**

-   Start broad (semantic search for task domain)
-   Narrow to specific files (read discovered files)
-   Check related components/utilities
-   Understand integration points

### 3. Question Strategy

**Ask questions ONLY when:**

-   Task requirements are ambiguous or incomplete
-   Multiple implementation approaches exist and choice matters
-   Missing critical information (e.g., API endpoints, data formats)
-   User preferences needed (e.g., UI style, error handling approach)

**Do NOT ask if:**

-   Information can be found in codebase
-   Standard patterns exist
-   Decision can be made from context
-   Question is trivial or can be inferred

**Question format:**

-   Be specific and actionable
-   Provide context for why question is needed
-   Offer options if applicable

### 4. Task Plan Creation

**Analyze task complexity:**

-   **Single-step:** Simple, self-contained, no dependencies, can complete in one response
-   **Multi-step:** Requires sequential steps, user confirmation between steps, or has dependencies

**Determine agent assignment:**

-   `Agent_Implementation` - Coding, file operations, feature development
-   `Agent_Research` - Investigation, analysis, documentation research
-   `Agent_Debug` - Troubleshooting, bug fixes, error resolution

**Create markdown with YAML frontmatter:**

```yaml
---
task_ref: "Task [id] - [Title]" # Or "New Task - [Title]" if no ID
agent_assignment: "[Agent_Implementation|Agent_Research|Agent_Debug]"
execution_type: "[single-step|multi-step]"
---
```

**Required sections:**

1. **## Objective** - Clear, concise statement of what will be accomplished
2. **## Detailed Instructions** - Step-by-step or bullet-point instructions
    - Single-step: Use bullets (`-`), prefix with "Complete all items in one response:"
    - Multi-step: Use numbered steps (`1.`, `2.`), include step names `**Step Name:**`, add "Complete in [N] exchanges, one step per response. **AWAIT USER CONFIRMATION** before proceeding."
3. **## Expected Output** - What deliverables/files/changes will result
4. **## Task Memory Updates** - For multi-step tasks: What should be updated in task description throughout implementation. For single-step: What should be added to task summary at completion.

**Instruction detail level:**

-   Be specific about file paths, function names, patterns to follow
-   Reference existing code patterns when applicable
-   Include validation/testing requirements
-   Specify error handling approach
-   Note any guardrails or constraints

### 5. Summary & Confirmation

**Create summary:**

-   **Task:** [task_ref from frontmatter]
-   **Type:** [execution_type] execution
-   **Agent:** [agent_assignment]
-   **Key Steps:** [2-3 sentence overview of main work]
-   **Files Affected:** [list key files/directories]
-   **Estimated Complexity:** [Simple/Medium/Complex]

**Present to user:**

```
## Task Plan Summary

[Summary content above]

**Full task plan:**
[Show full markdown task plan]

**Confirm:** Does this plan look correct? (yes/no)
```

**Wait for confirmation:**

-   If "yes" → Proceed to storage
-   If "no" → Ask what needs changing, revise, re-present summary
-   If user requests changes → Update plan, re-present summary

### 6. Temporary Storage

**Store in temporary file:**

-   Path: `.reconstruct/temp/task-plan-[task_plan_id]-[timestamp].md`
-   Format: Full markdown with YAML frontmatter (as created above)
-   Timestamp format: `YYYYMMDD-HHMMSS`

**After storage:**

-   Confirm: "✅ Task plan saved to `.reconstruct/temp/task-plan-[timestamp].md`"
-   Note: "Ready for task creation via Reconstruct MCP"

## Implementation Memory Updates

### During Implementation (Multi-Step Tasks Only)

**Update task memory after each step:**

1. **After completing each step:**

    - Call `update_project_task` MCP tool with `project_id` and `task_id`
    - Update `description` field by appending step progress:
        - What was completed in this step
        - Files created/modified
        - Any issues encountered and resolutions
        - Next step to be executed

2. **Format for task description updates:**

    ```
    ## Implementation Progress

    **Step 1: [Step Name] - Completed**
    - Created files: [list files]
    - Modified files: [list files]
    - Issues: [if any]
    - Status: Complete

    **Step 2: [Step Name] - In Progress**
    - [Current step details]
    ```

3. **Do NOT update task memory for single-step tasks** - Wait until completion

### At Task Completion (All Tasks)

**Add completion summary to task:**

1. **Update task summary:**

    - Call `update_project_task` MCP tool
    - Update `summary` field with brief completion summary (1-2 sentences)
    - Update `description` field with full completion details:
        - All files created/modified
        - Testing results
        - Issues resolved
        - Final status

2. **Update task status:**
    - Call `update_project_task` MCP tool
    - Set `status` to `"Done"` when task is complete, or `"In Progress"` if task is still in progress.

**Note:** Context upload is optional - only create context sections if the implementation produces reusable knowledge that should be preserved in master context.

## Decision Guidelines

**Execution Type:**

-   **Multi-step if:** User confirmation needed, sequential dependencies, ad-hoc delegation required, complex validation needed
-   **Single-step if:** Self-contained, no dependencies, straightforward implementation

**Agent Assignment:**

-   **Implementation:** File creation/modification, API endpoints, components, utilities, features
-   **Research:** Understanding codebase, finding patterns, analyzing requirements, documentation
-   **Debug:** Fixing errors, troubleshooting, resolving issues, debugging

## Quality Checklist

Before presenting summary, verify:

-   [ ] YAML frontmatter complete and correct
-   [ ] All required sections present
-   [ ] Instructions are specific and actionable
-   [ ] File paths are accurate
-   [ ] Patterns referenced exist in codebase
-   [ ] Expected output is clear
-   [ ] Task memory updates section specifies what to document
-   [ ] Summary accurately reflects plan
-   [ ] No ambiguous instructions

## Example Structure

```markdown
---
task_ref: "New Task - Add user authentication"
agent_assignment: "Agent_Implementation"
execution_type: "single-step"
---

## Objective

Implement email/password authentication system with sign-in, sign-up, and password reset flows.

## Detailed Instructions

Complete all items in one response:

-   Create authentication API routes in `app/api/auth/`:
    -   `POST /api/auth/signup` - User registration
    -   `POST /api/auth/signin` - User login
    -   `POST /api/auth/reset-password` - Password reset request
-   Follow existing pattern from `app/api/auth/callback/route.ts` for error handling
-   Use Supabase Auth client from `lib/supabase/client.ts`
-   Add validation using existing patterns from `lib/utils/validation.ts`
-   Create UI components in `components/auth/`:
    -   `SignUpForm.tsx` - Registration form
    -   `SignInForm.tsx` - Login form
    -   `ResetPasswordForm.tsx` - Password reset form
-   Follow component patterns from `components/ui/` for styling
-   Add error handling and loading states
-   Test all flows end-to-end

## Expected Output

-   3 new API routes in `app/api/auth/`
-   3 new React components in `components/auth/`
-   Updated authentication flow working
-   Users can sign up, sign in, and reset passwords

## Task Memory Updates

**For single-step tasks:** Add completion summary to task description at end:

-   API routes created: `/api/auth/signup`, `/api/auth/signin`, `/api/auth/reset-password`
-   Components created: `SignUpForm.tsx`, `SignInForm.tsx`, `ResetPasswordForm.tsx`
-   Testing: All flows tested and working
-   Issues resolved: [list any issues and resolutions]

**For multi-step tasks:** Update task description after each step via `update_project_task` MCP tool:

-   Step 1: API routes created - update task description with progress
-   Step 2: Components created - append to task description
-   Step 3: Testing complete - append to task description
-   Final: Add completion summary and upload context section
```
