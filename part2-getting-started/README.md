# Part 2: Getting Started

## Hello World MCP Server

Build your first MCP server - a simple date/time tool.

| Guide | Description |
| --- | --- |
| [DateTime Server](mcp-datetime/README.md) | Your first MCP server using FastMCP - returns local date and time |

**What you'll learn:**
- FastMCP setup and basics
- Defining tools with `@mcp.tool` decorator
- Testing and integration with Claude Code

## Tour of Useful MCP Servers


| MCP server | Description | Guide |
| --- | --- | --- |
| Playwright Vision | Gives Codex/Claude a Playwright-driven browser for navigation, extraction, and screenshots. | [Playwright guide](mcp-playwright-vision/README.md) |
| PostgreSQL (`@modelcontextprotocol/server-postgres`) | Provides read-only SQL access to the `mcp_workshop` database for data-aware prompts. | [Postgres guide](mcp-postgres/README.md) |
| Postman | Connects Codex/Claude to the Postman API for managing workspaces, collections, and running saved requests—covering the API layer of the stack. | [Postman guide](mcp-postman/README.md) |
```