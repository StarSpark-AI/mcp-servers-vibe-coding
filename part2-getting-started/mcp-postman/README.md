# Postman MCP Server

## Overview
Postman's MCP server lets Claude or Codex call the Postman API to inspect workspaces, collections, environments, and even run saved requests. Adding it to the workshop creates a full-stack story: Playwright handles UI checks, Postgres provides data, and Postman exercises your HTTP layer.

## Prerequisites
- Claude Code CLI (`@anthropic-ai/claude-code`) installed and authenticated.
- Codex CLI (`@openai/codex`) if you plan to use Codex in addition to Claude.
- A Postman account with an API key (create one under **Settings → API keys** (https://<team name>.postman.co/settings/me/api-keys)).
- Optional for local installs: Node.js 18+ and npm.

Set your API key before running any commands (or use your shell's secrets manager):
```bash
export POSTMAN_API_KEY=pmak-...
```

## Option 1: Remote HTTP server (recommended)
Postman hosts a streamable HTTP endpoint so you don’t have to run anything locally. Minimal mode exposes a small set of safe tools; full mode exposes 100+ operations.

### Claude CLI
Minimal mode:
```bash
claude mcp add --transport http postman \
  https://mcp.postman.com/minimal \
  --header "Authorization: Bearer $POSTMAN_API_KEY"
```
Full mode (all tools): change the URL to `https://mcp.postman.com/mcp`. For EU tenants, use `https://mcp.eu.postman.com/{minimal|mcp}`.

### Codex CLI
```bash
codex mcp add postman \
  --url https://mcp.postman.com/minimal \
  --bearer-token-env-var POSTMAN_API_KEY
```
Swap the URL with `/mcp` for full mode if you need the extended toolset. Codex automatically sends `Authorization: Bearer $POSTMAN_API_KEY` by reading the env var you specified. Run `codex mcp list` (or `/mcp` inside the TUI) to confirm the server is connected.

## Option 2: Local STDIO server (optional)
If you prefer to run the MCP server locally (or your MCP client requires STDIO), install the npm package globally so the binary is always on your PATH:
```bash
npm install -g @postman/postman-mcp-server@latest
postman-mcp-server --help  # confirms the binary is available
```

Once installed, point Claude or Codex at the local STDIO server:
```bash
claude mcp add postman-local \
  -e POSTMAN_API_KEY=$POSTMAN_API_KEY \
  -- postman-mcp-server --full

# Codex example
codex mcp add postman-local \
  --env POSTMAN_API_KEY=$POSTMAN_API_KEY \
  -- postman-mcp-server --full
```
Add `--region eu` if your Postman data lives in the EU, and remove `--full` to stay on the minimal tool set.

## Verification Exercise
1. Launch `claude` (or `codex`) and ensure the Postman MCP server shows up under `/servers` (`/mcp` in Codex).
2. Run the prompt:
   > Use the postman MCP server to list my Postman workspaces and print their names.
3. Successful output should echo at least your default workspace (often **My Workspace**) plus any others in your account.

## Troubleshooting
- **401 Unauthorized**: Double-check the API key and ensure it has not been restricted. Tokens start with `pmak-`.
- **Missing tools in minimal mode**: Switch to the `/mcp` URL or add `--full` when running the local server.
- **Proxy/egress issues**: Remote HTTP servers require outbound HTTPS access to `mcp.postman.com`. For air-gapped labs, fall back to the local STDIO server.

With Postman connected, you now have MCP coverage across UI (Playwright), APIs (Postman), and data (Postgres) for true full-stack agent demos.
