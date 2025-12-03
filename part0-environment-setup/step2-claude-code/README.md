# Step 2: Claude Code CLI

## Overview

Claude Code is Anthropic's official CLI for AI-assisted development.

## Installation

```bash
# Install via npm (Node.js required - will be installed in Step 9)
# For now, use npx to run without global install
npx @anthropic-ai/claude-code

# Or install globally if Node.js is already available
npm install -g @anthropic-ai/claude-code
```

## Configuration

```bash
# Launch Claude Code (will prompt for API key on first run)
claude

# Or set API key via environment variable
export ANTHROPIC_API_KEY=your_api_key_here
```

## API Key Setup

1. Visit https://console.anthropic.com/
2. Create an account or sign in
3. Navigate to API Keys
4. Create a new API key
5. Copy the key and use it when prompted

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

This will be covered in detail during the workshop.
