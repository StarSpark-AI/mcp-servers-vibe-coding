# Step 4a: Codex CLI (Optional)

## Overview

Codex CLI is OpenAI's local coding agent that can inspect your repo, edit files, run commands, and connect to MCP servers directly from the terminal. This step is optional but recommended if you want parity with Claude Code or other AI pair-programming CLIs.

## Prerequisites

- Node.js and npm installed (see Step 2).
- An OpenAI account with a ChatGPT Plus/Pro/Business/Edu/Enterprise plan **or** API access that includes `gpt-5-codex`.
- macOS or Linux shell (Windows users should run through WSL per the [Codex Windows guide](https://developers.openai.com/codex/windows)).

## Installation

```bash
# Install the Codex CLI globally via npm
npm install -g @openai/codex

# Alternatively, if you use Homebrew (macOS)
brew install openai/codex/codex

# Upgrade anytime
npm install -g @openai/codex@latest
```

## Authentication & Login

1. Launch the CLI once to trigger login:
   ```bash
   codex
   ```
2. When prompted, choose one of the following:
   - **ChatGPT sign-in** – authenticate with the OpenAI account that has Codex access. This is the fastest path for Plus/Pro users.
   - **API key** – paste an API key with Codex scope. Keys can be generated at https://platform.openai.com/api-keys.
3. To skip the interactive prompt, export your key before launching codex:
   ```bash
   export OPENAI_API_KEY=sk-...
   codex --auth token
   ```
4. For multi-account setups, run `codex login` to re-authenticate or switch accounts later.

## First Run Checklist

```bash
# Confirm the CLI is installed
codex --version

# Start an interactive session in your repo
codex
```

During the first session, grant Codex the permissions it needs (editing files, running commands, etc.). Use `/approvals` inside the session to review those defaults.

## Useful Commands

- `/help` – list the built-in slash commands.
- `/model` – change between Codex model sizes and reasoning effort.
- `/status` – show the current directory, approvals, and active MCP servers.
- `/init` – scaffold an `AGENTS.md` file with context Codex will load automatically.
- `/review` – ask Codex to summarize or critique local diffs.

## MCP Integration

Codex supports Model Context Protocol out of the box.

```bash
# Register an MCP server with Codex
codex mcp add <server-name> <command>
```

This will be covered in detail during the workshop.

## Troubleshooting

- Run `codex doctor` to gather diagnostics if authentication or permissions fail.
- If the CLI cannot open a browser for login, pass `--auth device` to use a device code flow.
- Stay current with the [Codex CLI changelog](https://developers.openai.com/codex/changelog) for fixes and new capabilities.
