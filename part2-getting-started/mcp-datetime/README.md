# Hello World MCP Server: Date/Time

Build your first MCP server - a simple tool that returns the user's local date and time.

## Why This Tool Matters

LLMs have a knowledge cutoff date and don't inherently know the current date or time. This creates problems:
- They can't determine if information is stale and needs refreshing
- They may not know when to pull updated data from other tools or MCP servers
- Time-sensitive queries ("What's happening today?") become guesswork

A datetime MCP server solves this by giving the AI access to the user's actual local date and time.

## Overview

This "hello world" example introduces the core concepts of MCP server development:
- Creating a server with FastMCP
- Defining tools with decorators
- Testing locally
- Integrating with Claude Code

## Prerequisites

- Python 3.11+ (from Part 0 setup)
- Conda environment activated (`conda activate mcp-workshop`)

## Step 1: Create Project Structure

```bash
# From the workshop repo root
mkdir -p mcp-servers/datetime-server
cd mcp-servers/datetime-server
```

## Step 2: Set Up Python Environment

```bash
# Ensure you're in the mcp-workshop conda environment
conda activate mcp-workshop

# Install FastMCP
pip install fastmcp
```

## Step 3: Create the Server

Create a file called `server.py`:

```python
from datetime import datetime
from fastmcp import FastMCP

# Create the MCP server
mcp = FastMCP("User's Local DateTime Server")


@mcp.tool
def get_current_time() -> str:
    """Get the current local time."""
    return datetime.now().strftime("%H:%M:%S")


@mcp.tool
def get_current_date() -> str:
    """Get the current local date."""
    return datetime.now().strftime("%Y-%m-%d")


@mcp.tool
def get_datetime() -> str:
    """Get the current local date, time, and timezone."""
    now = datetime.now().astimezone()
    return now.strftime("%Y-%m-%d %H:%M:%S %Z")


@mcp.tool
def get_day_of_week() -> str:
    """Get the current day of the week."""
    return datetime.now().strftime("%A")


if __name__ == "__main__":
    mcp.run(transport="http", host="127.0.0.1", port=8001)
```

### Understanding the Code

- **`FastMCP("User's Local DateTime Server")`**: Creates an MCP server with a descriptive name that helps AI agents understand its purpose
- **`@mcp.tool`**: Decorator that exposes a function as an MCP tool
- **Type hints and docstrings**: FastMCP uses these to generate tool schemas automatically
- **`mcp.run(transport="http", ...)`**: Starts the server as an HTTP endpoint on the specified host and port

### How Claude Knows Which Tool to Use

Claude decides which MCP tools to call based on the **documentation you provide in your code**:

1. **Server name**: `"User's Local DateTime Server"` tells Claude this server provides local date/time information

2. **Function names**: `get_current_time`, `get_datetime` are self-descriptive

3. **Docstrings**: These are critical! Claude reads them to understand what each tool does:
   ```python
   @mcp.tool
   def get_current_time() -> str:
       """Get the current local time."""  # ← Claude sees this!
   ```

4. **Type hints**: Tell Claude what parameters are needed and what to expect back

When you ask "What time is it?", Claude:
1. Scans available MCP tools and their descriptions
2. Matches your question to `get_current_time` based on the docstring
3. Calls the tool and uses the result in its response

**Best Practice**: Write clear, specific docstrings. Compare:
- Bad: `"""Gets time."""`
- Good: `"""Get the current local time in HH:MM:SS format."""`

## Step 4: Run the Server

In a terminal with your conda environment activated, start the server:

```bash
conda activate mcp-workshop
cd mcp-servers/datetime-server
python server.py
```

You should see output indicating the server is running on `http://127.0.0.1:8001`. Keep this terminal open.

## Step 5: Add to Claude Code

In a **new terminal**, activate your environment and navigate to your art-gallery project:

```bash
conda activate mcp-workshop
cd art-gallery
```

Create a `.mcp.json` file in your art-gallery project folder:

```json
{
  "mcpServers": {
    "users-local-datetime-server": {
      "type": "http",
      "url": "http://127.0.0.1:8001/mcp"
    }
  }
}
```

Claude Code automatically detects this file when launched from the project folder. All MCP servers we build in this workshop will be added here.

Now launch Claude Code:

```bash
claude
```

You should see a message indicating it found the MCP server configuration.

## Step 6: Verify MCP Connection

In Claude Code, verify your MCP server is connected:

```
/mcp
```

You should see `users-local-datetime-server` listed among your configured MCP servers.

## Step 7: Test in Claude Code

Now test your MCP server with these prompts:

```
What time is it?
```

```
What's today's date?
```

```
What day of the week is it?
```

Claude should use your datetime-server tools to answer these questions!

---

## How It Works

```
┌─────────────┐   HTTP (port 8001)   ┌──────────────────┐
│ Claude Code │ ◄──────────────────► │ Local DateTime   │
│   (Client)  │                      │    (FastMCP)     │
└─────────────┘                      └──────────────────┘
       │                                      │
       │  1. "What time is it?"               │
       │                                      │
       │  2. HTTP POST to /mcp ─────────────► │
       │     (calls get_current_time)         │
       │                                      │
       │  3. JSON response ◄───────────────── │
       │     {"result": "14:32:15"}           │
       │                                      │
       │  4. "The current time is 2:32 PM"    │
       ▼                                      ▼
```

1. You ask Claude a question
2. Claude recognizes it needs time information and calls your MCP tool
3. Your server executes the Python function and returns the result
4. Claude formats the response naturally

---

## Extending the Server

Try adding more tools to your server:

```python
@mcp.tool
def get_timezone() -> str:
    """Get the current timezone."""
    return datetime.now().astimezone().tzname()


@mcp.tool
def format_date(format_string: str) -> str:
    """Format the current date/time with a custom format string.

    Args:
        format_string: A strftime format string (e.g., "%B %d, %Y")
    """
    return datetime.now().strftime(format_string)
```

After modifying, restart the server (Ctrl+C, then `python server.py`) to pick up the changes.

---

## Troubleshooting

**Server not connecting**:
- Ensure the server is running in a separate terminal (check for `http://127.0.0.1:8001`)
- Verify the URL includes `/mcp` at the end: `http://127.0.0.1:8001/mcp`

**Tools not working**:
- Check that the conda environment is activated in the server terminal
- Verify fastmcp is installed: `pip show fastmcp`

**Changes not reflected**:
- Restart the server (Ctrl+C, then `python server.py` again)
- Restart Claude Code to reconnect to the server

**Port already in use**:
- Change the port in `server.py` (e.g., 8002) and update the `claude mcp add` command accordingly

---

## Next Steps

Now that you've built your first MCP server, you understand the basics:
- Creating servers with FastMCP
- Defining tools with `@mcp.tool`
- Registering with Claude Code

Continue to Part 3 to build more complex MCP servers with state management and external API integration.

---

## Resources

- [FastMCP GitHub](https://github.com/jlowin/fastmcp)
- [MCP Python SDK](https://github.com/modelcontextprotocol/python-sdk)
- [Model Context Protocol Docs](https://modelcontextprotocol.io/quickstart/server)
