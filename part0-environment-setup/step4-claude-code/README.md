# Step 4: Claude Code CLI

## Overview

Claude Code is Anthropic's official CLI for AI-assisted development.

## Prerequisites

- Node.js and npm must be installed (Step 2)
- **Claude Pro subscription (minimum)** - Required for this workshop

## Installation

```bash
# Install globally via npm
npm install -g @anthropic-ai/claude-code
```

## Login with Claude Subscription

```bash
# Launch Claude Code
claude

# Select "Login with Claude subscription" when prompted
# This will open a browser window to authenticate
```

Follow the browser prompts to log in with your Claude account. You must have an active Claude Pro (or higher) subscription.

## Verification

```bash
# Check Claude Code is installed
claude --version

# Start Claude Code
claude
```

You should see the Claude Code interface ready for use.

## Useful Commands

Within Claude Code:
- `/help` - Show available commands
- `/clear` - Clear conversation
- Type your request and press Enter to get AI assistance

## MCP Integration

Claude Code supports MCP servers. To add an MCP server:

```bash
# Add an MCP server configuration
claude mcp add <server-name> <command>
```

See [Part 2: Getting Started](../../part2-getting-started/) for detailed MCP server setup.

## Frontend Design Plugin

The frontend-design plugin helps Claude Code create distinctive, production-grade UI components that avoid generic "AI aesthetics".

### Installation

Within Claude Code, run:

```
/plugin marketplace add anthropics/claude-code
/plugin install frontend-design@claude-code-plugins
```

### Usage

Once installed, Claude automatically uses this skill for frontend work. Try prompts like:

- "Create a dashboard for a music streaming app"
- "Build a landing page for an AI startup"
- "Design a settings panel with dark mode"

For more details, see the [official plugin documentation](https://github.com/anthropics/claude-code/tree/main/plugins/frontend-design).

---

## Optional: API Key Setup

If you prefer to use an API key instead of your subscription:

```bash
# Set API key via environment variable
export ANTHROPIC_API_KEY=your_api_key_here

# Or Claude Code will prompt for it on first run
claude
```

To get an API key:
1. Visit https://console.anthropic.com/
2. Create an account or sign in
3. Navigate to API Keys
4. Create a new API key
5. Copy the key and use it when prompted
