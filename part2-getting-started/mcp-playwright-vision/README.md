# Playwright Vision MCP Server

## Overview
Playwright Vision (https://github.com/davidkim9/playwright-vision-mcp) gives Codex and Claude CLI a headless (or headed) browser they can drive through MCP tools like `navigate`, `extract_text`, `screenshot`, and custom scripts. Use it in Part 2 to demo browser-aware agents without writing glue code.

## Prerequisites
- Claude Code CLI (`@anthropic-ai/claude-code`) installed and logged in.
- Codex CLI (`@openai/codex`) installed and logged in (optional if you only use Claude).
- Node.js 18+ and npm (already set up in Part 0, Step 2).
- Optional but helpful: `mkdir -p ~/playwright-shots` for screenshots captured by the server.

## Installation
```bash
npm install -g playwright-vision-mcp
npm install @playwright/test   # may be needed on your system to install playwright with dependencies                                           ║
# Verify binaries are on PATH
playwright-vision-mcp --help

# Ensure browsers and system libraries are installed
npx playwright install --with-deps
```

## Environment Configuration
| Variable | Purpose | Default |
| --- | --- | --- |
| `PLAYWRIGHT_HEADLESS` | Toggle visible browser windows when troubleshooting | `true` |
| `BROWSER_TYPE` | Choose `chromium`, `firefox`, or `webkit` | `chromium` |
| `SCREENSHOT_DIR` | Directory for image artifacts (create beforehand) | `./screenshots` |
| `OPENAI_API_KEY` | Needed only for the `analyze_image` tool | *(unset)* |

## Add to Claude CLI
Claude Code offers a guided flow for STDIO MCP servers (reference: https://www.howtouselinux.com/post/claude-code-mcp-servers-the-complete-beginners-guide).
```bash
claude mcp add playwright-vision \
  -e PLAYWRIGHT_HEADLESS=false \
  -e BROWSER_TYPE=chromium \
  -e SCREENSHOT_DIR="$HOME/playwright-shots" \
  -- npx -y playwright-vision-mcp
```
Check `claude mcp list`, then grant the new tools when Claude prompts for approvals.
> Tip: when running inside containers or WSL where no GUI is available, set `-e PLAYWRIGHT_HEADLESS=true` to avoid display errors.

## Add to Codex CLI
Codex supports STDIO MCP servers via `codex mcp add` (see https://developers.openai.com/codex/mcp).
```bash
codex mcp add playwright-vision \
  --env PLAYWRIGHT_HEADLESS=false \
  --env BROWSER_TYPE=chromium \
  --env SCREENSHOT_DIR="$HOME/playwright-shots" \
  -- npx -y playwright-vision-mcp
```
Run `codex mcp list` to confirm registration. You can further tweak `~/.codex/config.toml` if you need per-project overrides.
> Containers/WSL users: switch to `--env PLAYWRIGHT_HEADLESS=true` to prevent Playwright from looking for a display server.

## Alternative: Project-local Configuration

Instead of global CLI configuration, you can add Playwright Vision to your project's `.mcp.json` file:

```json
{
  "mcpServers": {
    "playwright-vision": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "playwright-vision-mcp"],
      "env": {
        "PLAYWRIGHT_HEADLESS": "false",
        "BROWSER_TYPE": "chromium",
        "SCREENSHOT_DIR": "/path/to/your/playwright-shots"
      }
    }
  }
}
```

Replace `/path/to/your/playwright-shots` with your actual screenshot directory (e.g., `~/playwright-shots`).

Claude Code automatically detects this file when launched from the project folder.

## Verification Prompt
1. Launch `claude` (or `codex`) inside your repo.
2. Run `/servers` (Claude) or `/mcp` (Codex) to ensure `playwright-vision` is connected.
3. Ask the assistant:
   > Use the playwright-vision MCP server to open https://app.starspark.ai/ and tell me the title of the page.
4. Expect output containing the HTML title `StarSpark AI | World's Best Math Tutors` (current as of Dec 2025). If approvals were denied, reopen `/approvals` and allow the `navigate`/`extract_text` tools.

## Troubleshooting Tips
- **Missing browsers**: `npx playwright install --with-deps` and rerun the server.
- **Permission errors in Claude**: make sure the STDIO script is executable (`chmod +x $(which playwright-vision-mcp)` if needed).
- **Long-running sessions**: set `PLAYWRIGHT_HEADLESS=true` and `BROWSER_TYPE=chromium` to minimize resource use for CI demos.
