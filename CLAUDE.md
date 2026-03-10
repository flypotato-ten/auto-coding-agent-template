# AgentForge - Project Instructions

## Project Context

A no-code agentic AI platform for security risk and governance professionals. Users visually compose multi-agent workflows, manage knowledge bases, and publish solutions to an internal marketplace. See `architecture.md` for full design details.

---

## MANDATORY: Agent Workflow

Every new agent session MUST follow this workflow:

### Step 1: Initialize Environment

```bash
./init.sh
```

This will:
- Install all dependencies
- Start the development server at http://localhost:3000

**DO NOT skip this step.** Ensure the server is running before proceeding.

### Step 2: Select Next Task

Read `task.json` and select ONE task to work on.

Selection criteria (in order of priority):
1. Choose a task where `passes: false`
2. Consider dependencies - fundamental features should be done first
3. Pick the highest-priority incomplete task

### Step 3: Implement the Task

- Read the task description and steps carefully
- Implement the functionality to satisfy all steps
- Follow existing code patterns and conventions

### Step 4: Test Thoroughly

After implementation, verify ALL steps in the task:

**MANDATORY Testing Requirements:**

1. **Major page modifications** (new pages, component rewrites, core interaction changes):
   - **MUST test in browser!** Use MCP Playwright tool
   - Verify page loads and renders correctly
   - Verify form submission, button clicks, and other interactions
   - Take screenshots to confirm UI displays correctly

2. **Minor code modifications** (bug fixes, style adjustments, helper functions):
   - Can use unit tests or lint/build verification
   - Recommend browser testing if unsure

3. **All modifications must pass**:
   - `npm run lint` with no errors
   - `npm run build` succeeds
   - Browser/unit tests verify functionality works

**Testing Checklist:**
- [ ] Code has no TypeScript errors
- [ ] Lint passes
- [ ] Build succeeds
- [ ] Functionality works correctly in browser (for UI-related changes)

### Step 5: Update Progress

Write your work to `progress.txt`:

```
## [Date] - Task: [task description]

### What was done:
- [specific changes made]

### Testing:
- [how it was tested]

### Notes:
- [any relevant notes for future agents]
```

### Step 6: Commit Changes (Including task.json Update)

**IMPORTANT: All changes must be submitted in a single commit, including task.json updates!**

Process:
1. Update `task.json`, changing the task's `passes` from `false` to `true`
2. Update `progress.txt` to record the work done
3. Submit all changes at once:

```bash
git add .
git commit -m "[task description] - completed"
```

**Rules:**
- Mark `passes: true` only after all steps have been verified
- Never delete or modify task descriptions
- Never remove tasks from the list
- **All content for one task (code, progress.txt, task.json) must be committed in the same commit**

---

## ⚠️ Blocking Issues

**If a task cannot be completed or requires human intervention, you must follow these rules:**

### Situations requiring task stoppage and requesting human help:

1. **Missing environment configuration**:
   - .env.local needs real API keys filled in
   - Supabase project needs to be created and configured
   - External services require account setup

2. **External dependencies unavailable**:
   - Third-party API services are down
   - OAuth flows require manual authorization
   - Services require paid upgrades

3. **Testing cannot proceed**:
   - Login/registration features need real user accounts
   - Functionality depends on external systems not yet deployed
   - Specific hardware environment required

### Correct actions when blocked:

**DO NOT (prohibited):**
- ❌ Submit a git commit
- ❌ Set task.json `passes` to true
- ❌ Pretend the task is completed

**DO (required):**
- ✅ Record current progress and blocking reason in progress.txt
- ✅ Output clear blocking message explaining what manual intervention is needed
- ✅ Stop the task and wait for human intervention

### Blocking message format:

```
🚫 Task Blocked - Requires Manual Intervention

**Current Task**: [task name]

**Completed Work**:
- [completed code/configuration]

**Blocking Reason**:
- [specific explanation of why progress is blocked]

**Required Manual Help**:
1. [specific step 1]
2. [specific step 2]
...

**After Blocking is Resolved**:
- Run [command] to continue the task
```

---

## UI/UX Design Workflow

This project uses a **Code-First + Screenshot-to-Code** design workflow powered by three MCP servers. See `ui_ux_design.md` for the full design strategy, color system, component conventions, and page-by-page UX notes.

### MCP Servers (configured in `agentforge-app/.mcp.json`)

| Server | Purpose |
|--------|---------|
| **shadcn MCP** | Live component docs — use before writing any shadcn component code |
| **21st.dev Magic MCP** | Generate new components from natural language descriptions |
| **Playwright MCP** | Screenshot and visually verify all UI changes |

### Mandatory UI Rules

**Before writing any UI component:**
1. Check `src/components/` — extend existing components rather than duplicating
2. Use the **shadcn MCP** to confirm correct component APIs and prop names
3. Use the **Magic MCP** to generate a starting point for new components

**After writing any UI component:**
4. Run the dev server and use **Playwright MCP** to take a screenshot
5. Verify: colors match design tokens, spacing is consistent, dark theme looks correct
6. Never hardcode colors — always use CSS custom properties (`text-foreground`, `bg-card`, etc.)
7. Never use inline styles — Tailwind classes only

**Full UI workflow:**
```
Describe component → Magic MCP generates → shadcn MCP validates →
Claude integrates → Playwright screenshots → Claude self-corrects
```

---

## Project Structure

```
/
├── CLAUDE.md               # This file - workflow instructions
├── task.json               # Task definitions (source of truth)
├── progress.txt            # Progress log from each session
├── init.sh                 # Initialization script
├── architecture.md         # System architecture and design decisions
├── ui_ux_design.md         # UI/UX strategy, design tokens, component conventions
├── agentic_comparison.md   # Framework evaluation (LangGraph decision rationale)
└── agentforge-app/         # Next.js application
    ├── .mcp.json           # MCP server configuration (shadcn, Magic, Playwright)
    ├── src/app/            # App Router pages
    ├── src/components/     # Shared UI components
    ├── src/lib/            # DB, auth, runtime, processing utils
    └── ...
```

## Commands

```bash
# In agentforge-app/
npm run dev      # Start dev server (http://localhost:3000)
npm run build    # Production build
npm run lint     # Run linter
```

## Coding Conventions

- TypeScript strict mode
- Functional components with hooks
- Tailwind CSS for styling — no hardcoded colors, no inline styles
- shadcn/ui as the component primitive library
- Dark-first design (dark theme is the default, not an override)
- All new components need a skeleton loading state

---

## Key Rules

1. **One task per session** - Focus on completing one task well
2. **Test before marking complete** - All steps must pass
3. **Browser testing for UI changes** - New or significantly modified pages must be tested in the browser
4. **Document in progress.txt** - Help future agents understand your work
5. **One commit per task** - All changes (code, progress.txt, task.json) must be submitted in the same commit
6. **Never remove tasks** - Only flip `passes: false` to `true`
7. **Stop if blocked** - When manual intervention is required, do not submit; output the blocking message and stop
