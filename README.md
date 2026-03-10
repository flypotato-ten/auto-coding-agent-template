# AgentForge

**A no-code agentic AI platform for security risk and governance professionals.**

AgentForge lets your team visually compose multi-agent AI workflows, connect secured knowledge bases, and publish solutions to an internal marketplace — all without writing a single line of code.

---

## What It Does

- **Visual Agent Builder** — drag-and-drop canvas to compose agentic workflows using 5 architecture patterns (Single Agent, Sequential Chain, Parallel Fan-out, Hierarchical, Router)
- **Multi-LLM Support** — configure each agent node with its own LLM provider and model (OpenAI, Anthropic, Azure OpenAI, Google Gemini)
- **Human-in-the-Loop** — mark any node to require human approval before it executes — essential for governance-sensitive workflows
- **Secured Knowledge Base** — upload documents (PDF, DOCX, TXT, CSV) or connect data sources (SharePoint, Confluence, web URLs); AI retrieves context via vector search
- **Solution Marketplace** — publish solutions for your organisation to discover, run, and copy
- **Enterprise SSO** — authenticate via Azure Active Directory (OIDC) or Ping Identity (SAML 2.0); no username/password accounts
- **Role-Based Access Control** — three roles: `admin`, `builder`, `viewer`
- **Audit Logging** — every action is logged for compliance and governance review

---

## Technology Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js 14+ (App Router) · TypeScript · Tailwind CSS · shadcn/ui |
| Agent Canvas | React Flow (`@xyflow/react`) |
| Agent Orchestration | LangGraph.js (`@langchain/langgraph`) |
| LLM Abstraction | LangChain.js (`@langchain/core` + provider packages) |
| Database | Supabase (PostgreSQL + pgvector) |
| Authentication | NextAuth.js v5 (Azure AD OIDC · Ping Identity SAML) |
| File Storage | Supabase Storage |
| Vector Search | pgvector extension on Supabase |

---

## Repository Structure

```
/
├── CLAUDE.md               # Agent workflow instructions (mandatory reading for AI agents)
├── task.json               # Task definitions — source of truth for what to build
├── progress.txt            # Per-session progress log written by AI agents
├── architecture.md         # Full system architecture, data model, and API design
├── ui_ux_design.md         # UI/UX strategy, design tokens, component conventions
├── agentic_comparison.md   # Framework evaluation — why LangGraph was chosen
├── init.sh                 # Environment initialisation script
└── agentforge-app/         # Next.js application
    ├── .mcp.json           # MCP server config (shadcn · Magic · Playwright)
    ├── .env.example        # Environment variable template — copy to .env.local
    ├── src/app/            # App Router pages
    ├── src/components/     # Shared UI components
    └── src/lib/            # DB · auth · LangGraph runtime · document processing
```

---

## How This Project Is Built

AgentForge is developed using a **fully automated AI agent workflow**. Claude Code (Anthropic's CLI) reads `task.json`, picks the next incomplete task, implements it, tests it in the browser, and commits the result — all autonomously.

### Agent Workflow (defined in `CLAUDE.md`)

```
1. ./init.sh              → install deps, start dev server
2. Read task.json         → pick next task where passes: false
3. Implement              → write code following architecture.md conventions
4. Test                   → lint + build + Playwright browser screenshot
5. Update progress.txt    → document what was done
6. Commit                 → code + progress.txt + task.json (passes: true) in one commit
```

### MCP Servers Used by Agents

Agents have access to three MCP servers that assist with UI development:

| MCP | Purpose |
|-----|---------|
| **shadcn/ui MCP** | Live component docs and registry — prevents hallucinated prop names |
| **21st.dev Magic MCP** | Generate polished shadcn/Tailwind components from descriptions |
| **Playwright MCP** | Screenshot the running app and visually verify every UI change |

---

## Prerequisites

- [Claude Code](https://claude.ai/code) — Anthropic's AI coding CLI
- Node.js 20+
- A [Supabase](https://supabase.com) project with pgvector enabled
- An Azure AD app registration (for SSO) or Ping Identity SAML configuration
- API keys for at least one LLM provider (OpenAI, Anthropic, etc.)
- A [GitHub Personal Access Token](https://github.com/settings/tokens) (read-only, for shadcn MCP)
- A [21st.dev](https://21st.dev) API key (free tier available, for Magic MCP)

---

## Getting Started

### 1. Clone and configure environment

```bash
git clone <repo-url>
cd secure-auto-coding-agent/agentforge-app

cp .env.example .env.local
# Edit .env.local and fill in all required values
```

### 2. Load MCP environment variables

The MCP servers in `.mcp.json` read API keys from your shell environment. Add these to your shell session before launching Claude Code:

```bash
export MCP_GITHUB_API_KEY="your_github_token"
export MCP_21ST_API_KEY="your_21st_dev_key"
```

Or use [direnv](https://direnv.net/) to auto-load from `.env.local` when you enter the directory:

```bash
# .envrc (create in agentforge-app/)
dotenv .env.local
```

### 3. Run the next task manually (most stable)

```bash
cd ..  # back to repo root
claude  # start Claude Code
# Tell Claude: "Please read CLAUDE.md and complete the next task in task.json"
```

### 4. Run autonomously without permission prompts

```bash
claude -p --dangerously-skip-permissions \
  "Read CLAUDE.md and complete the next task in task.json"
```

> **Warning:** Only use `--dangerously-skip-permissions` in a trusted local environment. Review all commits before pushing.

### 5. Run multiple tasks in a loop (unattended)

```bash
./run-automation.sh 5   # run 5 task cycles unattended
```

> **Warning:** This is the least supervised method. Monitor resource usage and review all output before merging.

---

## Key Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Agent orchestration | LangGraph.js | Best-in-class multi-agent patterns, JS/TS native, human-in-the-loop as first-class feature, MIT license |
| UI component library | shadcn/ui + Radix UI | Unstyled primitives, full ownership of styles, works perfectly with Tailwind |
| Canvas library | React Flow | Battle-tested graph canvas, custom node types, used by Flowise internally |
| Database | Supabase (PostgreSQL + pgvector) | Single platform for auth, relational data, file storage, and vector search |
| Authentication | NextAuth.js v5 | Best OIDC/SAML support in the Next.js ecosystem |
| Design workflow | Code-first + Playwright verification | No Figma subscription needed; MCP servers close the generate→verify loop |

See `agentic_comparison.md` for the full framework evaluation across 8 open-source options.

---

## Environment Variables

Copy `agentforge-app/.env.example` to `agentforge-app/.env.local` and fill in:

| Variable | Description |
|----------|-------------|
| `NEXTAUTH_SECRET` | Random string ≥32 chars — `openssl rand -base64 32` |
| `NEXT_PUBLIC_SUPABASE_URL` | Your Supabase project URL |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase anon key |
| `SUPABASE_SERVICE_ROLE_KEY` | Supabase service role key (server-side only) |
| `AZURE_AD_CLIENT_ID/SECRET/TENANT_ID` | Azure AD app registration credentials |
| `OPENAI_API_KEY` | Fallback LLM key for local dev (prod keys stored per-org in DB) |
| `ENCRYPTION_KEY` | 32-byte hex key — `openssl rand -hex 32` |
| `MCP_GITHUB_API_KEY` | GitHub token for shadcn MCP |
| `MCP_21ST_API_KEY` | 21st.dev key for Magic MCP |

Full reference in `agentforge-app/.env.example`.

---

## Progress

Task completion is tracked in `task.json`. Each task has a `passes` field:
- `false` — not yet implemented
- `true` — implemented, tested, and committed

Current status: **0 / 25 tasks complete**

See `progress.txt` for a per-session log of what each agent did.

---

## Security Notes

- `.env.local` and `.mcp.json` are gitignored — never commit real secrets
- LLM provider API keys are stored encrypted (AES-256-GCM) in the database per organisation
- All API routes require an authenticated session — enforced by NextAuth middleware
- Row Level Security (RLS) is enabled on all Supabase tables
- Every user action is written to the `audit_logs` table

---

## Disclaimer

This project is developed using AI-assisted automation. All generated code should be reviewed before deployment to production. The authors are not responsible for any unintended consequences of running automated code generation.
