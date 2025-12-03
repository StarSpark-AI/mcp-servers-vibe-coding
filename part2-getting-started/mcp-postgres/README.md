# PostgreSQL MCP Server (`@modelcontextprotocol/server-postgres`)

## Overview
This MCP server exposes read-only SQL access so Codex or Claude can inspect schemas and run SELECT queries against your workshop database. We will reuse the `mcp_workshop` database and `workshop_tasks` table created in Part 0, Step 6.

## Prerequisites
- Claude Code CLI (`@anthropic-ai/claude-code`) installed and authenticated.
- Codex CLI (`@openai/codex`) installed/authenticated if you plan to use Codex as well.
- PostgreSQL running locally with credentials from Part 0 (`your_username` / `YOUR_STRONG_PASSWORD`).
- Database `mcp_workshop` populated with the sample `workshop_tasks` table.
- Node.js 18+ and npm.

## Installation
```bash
npm install -g @modelcontextprotocol/server-postgres
# Optional sanity check
mcp-server-postgres --help
```
The binary is read-only, but always point it at non-production data.

## Connection String
Export a reusable URL so both CLIs share it:
```bash
export WORKSHOP_DB_URL="postgresql://your_username:YOUR_STRONG_PASSWORD@localhost:5432/mcp_workshop"
```
Use your actual username/password if you renamed them earlier.

## Add to Claude CLI
```bash
claude mcp add workshop-postgres \
  -- npx -y @modelcontextprotocol/server-postgres "$WORKSHOP_DB_URL"
```
Use `claude mcp list` to verify and approve the `query` tool the first time Claude prompts you.

## Add to Codex CLI
```bash
codex mcp add workshop-postgres \
  --env PGSSLMODE=disable \
  -- npx -y @modelcontextprotocol/server-postgres "$WORKSHOP_DB_URL"
```
Confirm with `codex mcp list`. Inside the Codex TUI, `/mcp` toggles the server per session.

## Verification Prompt
1. Launch Claude (or Codex) with the server enabled.
2. Ask:
   > Use the workshop-postgres MCP server to run `SELECT id, title, status FROM workshop_tasks ORDER BY id;` and summarize how many tasks are in each status.
3. A successful response echoes the two seeded rows (`Review MCP docs`, `Add Codex server`) and reports the counts, e.g., *"2 tasks total: 1 in_progress, 1 todo"*.

## Troubleshooting
- **Auth failures**: Open `psql` as the `postgres` superuser and run `ALTER ROLE your_username WITH LOGIN PASSWORD 'YOUR_STRONG_PASSWORD';` then re-run the `codex mcp add` / `claude mcp add` command so the new connection string is saved.
- **Host/port issues**: Ensure PostgreSQL is started (see Part 0 Step 6). On WSL/containers without `systemctl`, use `sudo service postgresql start`.
- **Tool permissions**: If Codex/Claude refuses to call the `query` tool, open `/approvals` (Codex) or rerun the CLI with `--allowedTools "mcp__workshop-postgres__*"`.

With the database server online, you can move to the Playwright Vision guide next and combine both MCP sources during the Part 2 exercises.
