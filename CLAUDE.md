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
- **Apply the Security Checklist** from the [Security Requirements](#security-requirements) section for every feature type being implemented

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
- [ ] Security checklist items verified for all feature types touched (see [Security Requirements](#security-requirements))

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

---

## Security Requirements

This platform handles AI agent execution, sensitive governance data, and enterprise SSO — security is non-negotiable. Apply the applicable checklist(s) for every feature implemented.

### Secure Coding Principles (Always Apply)

- **Never trust client input** — validate and sanitize all data server-side using Zod schemas before any processing
- **Deny by default** — require explicit grants; never assume access is allowed
- **Least privilege** — grant only the permissions a component needs for its specific task
- **Defense in depth** — apply security at every layer (DB, API, UI); do not rely on a single control
- **Fail securely** — errors must not expose stack traces, SQL, internal paths, or system details to the client
- **Secrets in env vars only** — no hardcoded API keys, passwords, or tokens anywhere in source code or system prompts

---

### Checklist A: Every API Route Handler

OWASP coverage: Web A01 (Broken Access Control), A03 (Injection), A05 (Misconfiguration), API1 (BOLA), API3 (Mass Assignment), API5 (Function Auth)

- [ ] **Authentication**: Verify `session` via `verifySession()` or NextAuth before any logic — return 401 if missing
- [ ] **Authorization**: Check user role AND resource ownership before returning or modifying data — never rely on client-supplied user IDs
- [ ] **Input validation**: Parse request body with a Zod schema; return 400 with `{ error: 'Invalid input' }` on failure — no schema = no processing
- [ ] **BOLA prevention**: All DB queries include `WHERE user_id = session.userId` (or equivalent org/role scope) — never accept bare resource IDs
- [ ] **Response shaping**: Return explicit DTO fields only — never `JSON.stringify(dbRecord)` or `SELECT *`
- [ ] **Error handling**: Wrap business logic in try/catch; `console.error` server-side; return generic `{ error: 'An error occurred' }` with 500 to client
- [ ] **HTTP status codes**: 401 (unauthenticated), 403 (unauthorized), 404 (not found/hide existence), 400 (bad input), 429 (rate limited)
- [ ] **Rate limiting**: Apply per-user rate limit on all mutation endpoints and any LLM-calling endpoint

```typescript
// Pattern every Route Handler MUST follow
export async function POST(req: Request, { params }: { params: { id: string } }) {
  const session = await verifySession();                          // A: auth
  if (!session) return new Response(null, { status: 401 });

  const body = await req.json();
  const parsed = MySchema.safeParse(body);                        // B: validate
  if (!parsed.success) return Response.json({ error: 'Invalid input' }, { status: 400 });

  const record = await db.query.items.findFirst({
    where: and(eq(items.id, params.id), eq(items.userId, session.userId)) // C: BOLA
  });
  if (!record) return new Response(null, { status: 404 });       // D: hide existence

  try {
    const result = await doWork(parsed.data, session.userId);
    return Response.json({ id: result.id, name: result.name }); // E: DTO only
  } catch (e) {
    console.error('POST /api/items failed:', e);                  // F: log server-side
    return Response.json({ error: 'An error occurred' }, { status: 500 }); // generic client
  }
}
```

---

### Checklist B: Supabase / Database

OWASP coverage: Web A01, A03 (Injection), A02 (Crypto Failures)

- [ ] **RLS enabled**: Every new table in `public` schema has `ENABLE ROW LEVEL SECURITY` and `FORCE ROW LEVEL SECURITY`
- [ ] **RLS policies defined**: Explicit `USING` and `WITH CHECK` clauses that scope to `auth.uid()` — use `(SELECT auth.uid())` wrapper for performance
- [ ] **Parameterized queries only**: Use Drizzle/Supabase client APIs — never string-concatenate SQL
- [ ] **Service role key server-only**: `SUPABASE_SERVICE_ROLE_KEY` used only in server-side code; never in client bundles or exposed via API response
- [ ] **No raw_user_meta_data for authz**: Do not use Supabase `raw_user_meta_data` for role/permission decisions — it is user-modifiable; use the `users` table with server-side role checks
- [ ] **Connector credentials encrypted**: `data_connectors.config` stores OAuth tokens/secrets using `ENCRYPTION_KEY` env var — plaintext never stored

```sql
-- Pattern for every new table
ALTER TABLE my_table ENABLE ROW LEVEL SECURITY;
ALTER TABLE my_table FORCE ROW LEVEL SECURITY;

CREATE POLICY "owner_access" ON my_table
  FOR ALL TO authenticated
  USING ((SELECT auth.uid()) = user_id)
  WITH CHECK ((SELECT auth.uid()) = user_id);

CREATE INDEX idx_my_table_user_id ON my_table (user_id); -- required for RLS performance
```

---

### Checklist C: LLM / AI Integration

OWASP coverage: LLM01 (Prompt Injection), LLM02 (Sensitive Disclosure), LLM05 (Improper Output Handling), LLM06 (Excessive Agency), LLM07 (System Prompt Leakage), LLM10 (Unbounded Consumption)

- [ ] **No secrets in system prompts**: API keys, DB URLs, encryption keys, internal service names — all via env vars; system prompts are treated as potentially visible to users
- [ ] **Input sanitization before LLM**: Strip control characters; enforce max token length before sending to model API; reject oversized inputs with 400
- [ ] **Treat LLM output as untrusted**: Validate structured outputs with Zod; sanitize with DOMPurify before rendering; use parameterized queries when inserting LLM output into DB
- [ ] **Per-user rate limits on LLM calls**: Use Upstash Ratelimit or equivalent; enforce `max_tokens` cap on every model call; set spending alerts on provider accounts
- [ ] **Minimal tool permissions**: LangGraph agent nodes receive only the tools required for their specific task — no broad file system, shell, or admin DB access
- [ ] **HITL for irreversible actions**: Any agent action that is destructive or external (send email, modify records, call external API) must have a LangGraph `interruptBefore` approval gate
- [ ] **Log all LLM calls**: Record `userId`, `model`, `inputTokens`, `outputTokens`, timestamp — never log raw prompt content if it may contain PII

```typescript
// Pattern for LLM node execution
const userMessage = sanitizeInput(rawInput);                        // sanitize
const { success } = await ratelimit.limit(`llm:${session.userId}`); // rate limit
if (!success) return Response.json({ error: 'Rate limit exceeded' }, { status: 429 });

const response = await model.invoke(messages, { maxTokens: 2048 }); // hard cap
const output = response.content as string;

// Validate if structured output expected
const validated = OutputSchema.safeParse(JSON.parse(output));       // validate
if (!validated.success) throw new Error('LLM output failed schema validation');

// Render safely — never dangerouslySetInnerHTML without sanitization
import DOMPurify from 'isomorphic-dompurify';
const safe = DOMPurify.sanitize(output);
```

---

### Checklist D: LangGraph Agent Execution

OWASP coverage: Agentic AAI001 (Authorization Hijacking), AAI003 (Goal Manipulation), AAI005 (Blast Radius), AAI006 (Memory Manipulation), AAI007 (Multi-Agent Exploitation), AAI011 (Untraceability)

- [ ] **Workflow ownership verified before execution**: Load `solutions` record scoped to `userId`/`orgId` — never execute a workflow by bare `solutionId` from client
- [ ] **Visibility access control enforced**: `private` workflows → owner only; `org` workflows → same `org_id` only; `public` → all authenticated users
- [ ] **Thread ID isolation**: Each `solution_run` gets its own `thread_id` for checkpointing — never reuse thread IDs across users or sessions
- [ ] **Agent memory scoped per run**: LangGraph state must not persist cross-run data that could leak between users; checkpoint cleanup on run completion
- [ ] **Blast radius limits on tools**: Agent tool functions operate only on records owned by the invoking user's `org_id` — tool implementations must re-verify ownership, not trust agent-supplied IDs
- [ ] **All agent actions traceable**: Write `node_traces` to `solution_runs` for every node executed; include node ID, model, token count, timestamps
- [ ] **No agent-to-agent trust elevation**: Sub-agents in hierarchical workflows operate with the same permissions as the invoking user — never inherit elevated orchestrator privileges

---

### Checklist E: Knowledge Base & RAG

OWASP coverage: LLM04 (Data Poisoning), LLM08 (Vector/Embedding Weaknesses), API1 (BOLA), Web A10 (SSRF)

- [ ] **KB visibility enforced at retrieval**: `similaritySearch` calls must filter by `kb_id` values the user is authorized to access — scope to `private` (owner), `org` (same org), or `public`
- [ ] **Document uploads validated**: Allowlist file types (`pdf`, `docx`, `txt`, `csv`); enforce 50MB max size server-side; rename files to UUID-based server paths — never use client-supplied filenames for storage paths
- [ ] **Web crawl URL validation**: User-supplied URLs for web connectors must be validated against HTTPS-only allowlist; block private IP ranges (10.x, 172.16.x, 192.168.x, 169.254.x) to prevent SSRF
- [ ] **Content sanitization before ingestion**: Strip executable content, scripts, and macro-enabled formats before chunking and embedding
- [ ] **Connector credentials encrypted**: OAuth tokens and API keys for SharePoint/Confluence connectors encrypted at rest using `ENCRYPTION_KEY`; decrypted server-side only at sync time

```typescript
// SSRF prevention for web crawl connector
const BLOCKED_RANGES = /^(10\.|172\.(1[6-9]|2\d|3[01])\.|192\.168\.|127\.|169\.254\.)/;
function validateCrawlUrl(url: string): URL {
  const parsed = new URL(url);
  if (parsed.protocol !== 'https:') throw new Error('Only HTTPS URLs allowed');
  if (BLOCKED_RANGES.test(parsed.hostname)) throw new Error('Private IP ranges blocked');
  return parsed;
}
```

---

### Checklist F: Authentication & SSO

OWASP coverage: Web A07 (Auth Failures), API2 (Broken Auth), Web A02 (Crypto)

- [ ] **Session cookies hardened**: `httpOnly: true`, `secure: true`, `sameSite: 'lax'` — all three required on every Set-Cookie
- [ ] **Generic auth error messages**: Return identical message for wrong user vs. wrong password — never expose which field failed (prevents user enumeration)
- [ ] **SSO tokens validated server-side**: NextAuth handles OIDC/SAML token validation — never trust client-supplied claims; never expose raw tokens to frontend
- [ ] **Role loaded from DB, not SSO claims**: After SSO callback, always read `users.role` from the `users` table; do not trust group claims from identity provider directly without mapping validation
- [ ] **Admin routes protected by role check**: All `/api/admin/*` and `/admin/*` routes verify `session.role === 'admin'` before executing — middleware alone is not sufficient

---

### Checklist G: Security Headers & Configuration

OWASP coverage: Web A05 (Misconfiguration), API8 (Security Misconfiguration)

Configure in `next.config.ts` — apply to all routes via `headers()`:

```typescript
// Required security headers — do not remove or weaken
{ key: 'X-Frame-Options', value: 'DENY' }
{ key: 'X-Content-Type-Options', value: 'nosniff' }
{ key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' }
{ key: 'Strict-Transport-Security', value: 'max-age=63072000; includeSubDomains; preload' }
{ key: 'Permissions-Policy', value: 'geolocation=(), camera=(), microphone=()' }
```

- [ ] **No stack traces in production responses**: Error responses contain only `{ error: 'message' }` — never include `error.stack`, SQL errors, or file paths
- [ ] **CORS locked down**: API routes specify exact allowed origins — never `Access-Control-Allow-Origin: *` for authenticated endpoints
- [ ] **npm audit clean**: Run `npm audit --audit-level=high` — zero high/critical vulnerabilities before committing

---

### Checklist H: Secrets & Environment Variables

OWASP coverage: Web A02 (Crypto Failures), LLM07 (System Prompt Leakage)

- [ ] **`.env*` files in `.gitignore`** — verify before every commit that no `.env.local` or `.env` files are staged
- [ ] **`NEXT_PUBLIC_` prefix only for non-sensitive values** — Supabase anon key and URL are safe to expose; service role key, encryption key, LLM API keys must NEVER use `NEXT_PUBLIC_` prefix
- [ ] **Validate env vars at startup**: Use a `requireEnv(key)` helper that throws at app startup if required vars are missing — prevents silent failures in production
- [ ] **LLM API keys per-org encrypted**: Org-level LLM keys stored in `admin_keys` table, encrypted with `ENCRYPTION_KEY`, decrypted server-side only at agent execution time — never returned to frontend

---

### Checklist I: Security Logging & Audit

OWASP coverage: Web A09 (Logging Failures), Agentic AAI011 (Untraceability)

Every implementation MUST log these events (structured JSON, server-side only):

| Event | Required Fields |
|-------|----------------|
| Auth success/failure | `userId`, `email`, `ip`, `provider`, `timestamp` |
| Authorization failure (403) | `userId`, `resource`, `action`, `timestamp` |
| Admin action | `adminId`, `targetId`, `action`, `changes`, `timestamp` |
| Agent execution | `runId`, `solutionId`, `userId`, `nodeId`, `model`, `tokens`, `timestamp` |
| LLM tool call | `runId`, `toolName`, `userId`, `timestamp` (no prompt content if PII possible) |
| KB document upload | `userId`, `kbId`, `filename`, `fileType`, `fileSize`, `timestamp` |
| Rate limit hit | `userId`, `endpoint`, `timestamp` |

**Never log**: passwords, session tokens, API keys, raw LLM prompts containing user PII, connector OAuth tokens

---

### Quick Security Review (Before Every Commit)

Run through this before marking any task `passes: true`:

```
[ ] No secrets hardcoded in source files or system prompts
[ ] All new API routes: auth + authz + Zod validation + scoped DB queries + DTO response
[ ] All new DB tables: RLS enabled with USING/WITH CHECK policies + user_id index
[ ] LLM output: validated with schema + sanitized before render/DB insert
[ ] Agent tools: minimal scope, no cross-user data access, HITL on destructive ops
[ ] File uploads: type allowlist + size limit + server-side rename
[ ] URLs from users: HTTPS-only + private IP range blocked
[ ] npm audit: no new high/critical vulnerabilities introduced
[ ] Security events logged appropriately
```
