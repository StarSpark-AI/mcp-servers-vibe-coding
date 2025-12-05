# Task Tracker MCP Server

Build an MCP server that provides CRUD operations for the PostgreSQL task database we set up in Part 0.

## Overview

This MCP server demonstrates:
- Connecting to PostgreSQL from an MCP server
- State management with a real database
- CRUD operations (Create, Read, Update)
- Practical task tracking for your projects

## Prerequisites

- PostgreSQL running with `mcp_workshop` database (from Part 0, Step 6)
- `workshop_tasks` table created (from Part 0 verification step)
- Conda environment activated (`mcp-workshop`)

## Step 1: Create Project Structure

```bash
# From the workshop repo root
mkdir -p mcp-servers/task-tracker
cd mcp-servers/task-tracker
```

## Step 2: Install Dependencies

```bash
conda activate mcp-workshop
pip install psycopg2-binary python-dotenv
```

- **psycopg2-binary**: PostgreSQL adapter for Python
- **python-dotenv**: Load environment variables from `.env` file

(Note: `fastmcp` was already installed in Part 2)

## Step 3: Configure Database Credentials

Create a `.env` file in the `mcp-servers/task-tracker` folder with your database credentials:

```bash
# Create the .env file
cat > .env << 'EOF'
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=mcp_workshop
POSTGRES_USER=your_username
POSTGRES_PASSWORD=YOUR_STRONG_PASSWORD
EOF
```

**Important:** Replace `your_username` and `YOUR_STRONG_PASSWORD` with the credentials you created in Part 0, Step 6.

## Step 4: Create the Server

Create a file called `server.py`:

```python
import os
import psycopg2
from psycopg2.extras import RealDictCursor
from dotenv import load_dotenv
from fastmcp import FastMCP

# Load environment variables from .env file
load_dotenv()

# Create the MCP server
mcp = FastMCP("Task Tracker")

# Database connection settings
DB_CONFIG = {
    "host": os.getenv("POSTGRES_HOST", "localhost"),
    "port": os.getenv("POSTGRES_PORT", "5432"),
    "dbname": os.getenv("POSTGRES_DB", "mcp_workshop"),
    "user": os.getenv("POSTGRES_USER", "your_username"),
    "password": os.getenv("POSTGRES_PASSWORD", "YOUR_STRONG_PASSWORD"),
}


def get_connection():
    """Create a database connection."""
    return psycopg2.connect(**DB_CONFIG, cursor_factory=RealDictCursor)


@mcp.tool
def list_tasks(project_name: str = None, status: str = None) -> list[dict]:
    """List all tasks, optionally filtered by project name or status.

    Args:
        project_name: Filter by project name (optional)
        status: Filter by status: 'todo', 'in_progress', 'done' (optional)
    """
    with get_connection() as conn:
        with conn.cursor() as cur:
            query = "SELECT id, project_name, title, status, task_order FROM workshop_tasks WHERE 1=1"
            params = []

            if project_name:
                query += " AND project_name = %s"
                params.append(project_name)

            if status:
                query += " AND status = %s"
                params.append(status)

            query += " ORDER BY task_order"
            cur.execute(query, params)
            return cur.fetchall()

@mcp.tool
def search_tasks_by_title(query: str) -> list[dict]:
    """Search tasks by title.

    Args:
        query: Search term to find in task titles
    """
    with get_connection() as conn:
        with conn.cursor() as cur:
            cur.execute(
                """
                SELECT id, project_name, title, status, task_order
                FROM workshop_tasks
                WHERE title ILIKE %s
                ORDER BY task_order
                """,
                (f"%{query}%",)
            )
            return cur.fetchall()

@mcp.tool
def get_task_by_id(task_id: int) -> dict:
    """Get a specific task by ID.

    Args:
        task_id: The ID of the task to retrieve
    """
    with get_connection() as conn:
        with conn.cursor() as cur:
            cur.execute(
                "SELECT id, project_name, title, status, task_order FROM workshop_tasks WHERE id = %s",
                (task_id,)
            )
            result = cur.fetchone()
            if result:
                return dict(result)
            return {"error": f"Task {task_id} not found"}


@mcp.tool
def create_task(project_name: str, title: str, status: str = "todo") -> dict:
    """Create a new task.

    Args:
        project_name: The project this task belongs to
        title: The task title/description
        status: Task status: 'todo', 'in_progress', or 'done' (default: 'todo')
    """
    if status not in ("todo", "in_progress", "done"):
        return {"error": "Status must be 'todo', 'in_progress', or 'done'"}

    with get_connection() as conn:
        with conn.cursor() as cur:
            # Get the next task_order value
            cur.execute("SELECT COALESCE(MAX(task_order), 0) + 1 AS next_order FROM workshop_tasks")
            next_order = cur.fetchone()["next_order"]

            cur.execute(
                """
                INSERT INTO workshop_tasks (project_name, title, status, task_order)
                VALUES (%s, %s, %s, %s)
                RETURNING id, project_name, title, status, task_order
                """,
                (project_name, title, status, next_order)
            )
            conn.commit()
            return dict(cur.fetchone())


@mcp.tool
def update_task(task_id: int, title: str = None, status: str = None) -> dict:
    """Update an existing task.

    Args:
        task_id: The ID of the task to update
        title: New title (optional)
        status: New status: 'todo', 'in_progress', or 'done' (optional)
    """
    if status and status not in ("todo", "in_progress", "done"):
        return {"error": "Status must be 'todo', 'in_progress', or 'done'"}

    if not title and not status:
        return {"error": "Provide at least one field to update (title or status)"}

    with get_connection() as conn:
        with conn.cursor() as cur:
            updates = []
            params = []

            if title:
                updates.append("title = %s")
                params.append(title)

            if status:
                updates.append("status = %s")
                params.append(status)

            params.append(task_id)

            cur.execute(
                f"""
                UPDATE workshop_tasks
                SET {', '.join(updates)}
                WHERE id = %s
                RETURNING id, project_name, title, status, task_order
                """,
                params
            )
            conn.commit()
            result = cur.fetchone()
            if result:
                return dict(result)
            return {"error": f"Task {task_id} not found"}


@mcp.tool
def reorder_task(task_id: int, new_order: int) -> dict:
    """Move a task to a new position in the task list.

    Args:
        task_id: The ID of the task to move
        new_order: The new position (1-based) for the task
    """
    if new_order < 1:
        return {"error": "new_order must be >= 1"}

    with get_connection() as conn:
        with conn.cursor() as cur:
            # Get current task_order
            cur.execute("SELECT task_order FROM workshop_tasks WHERE id = %s", (task_id,))
            result = cur.fetchone()
            if not result:
                return {"error": f"Task {task_id} not found"}

            old_order = result["task_order"]

            if old_order == new_order:
                return {"message": "Task is already at that position"}

            # Shift other tasks to make room
            if new_order < old_order:
                # Moving up: shift tasks between new and old position down
                cur.execute(
                    """
                    UPDATE workshop_tasks
                    SET task_order = task_order + 1
                    WHERE task_order >= %s AND task_order < %s
                    """,
                    (new_order, old_order)
                )
            else:
                # Moving down: shift tasks between old and new position up
                cur.execute(
                    """
                    UPDATE workshop_tasks
                    SET task_order = task_order - 1
                    WHERE task_order > %s AND task_order <= %s
                    """,
                    (old_order, new_order)
                )

            # Update the task's position
            cur.execute(
                """
                UPDATE workshop_tasks
                SET task_order = %s
                WHERE id = %s
                RETURNING id, project_name, title, status, task_order
                """,
                (new_order, task_id)
            )
            conn.commit()
            return dict(cur.fetchone())


if __name__ == "__main__":
    mcp.run(transport="http", host="127.0.0.1", port=8002)
```

### Understanding the Code

**Database Connection:**
- Uses environment variables for credentials (with fallback defaults)
- `RealDictCursor` returns rows as dictionaries (easier to work with)
- Context managers (`with`) ensure connections are properly closed

**CRUD Tools:**
- `list_tasks`: Read all tasks with optional filtering (ordered by task_order)
- `search_tasks_by_title`: Search tasks by title text
- `get_task_by_id`: Read a single task by ID
- `create_task`: Create a new task (auto-assigns next task_order)
- `update_task`: Update title or status (use status='done' to mark complete)
- `reorder_task`: Move a task to a new position in the list

**Task Ordering:**
- `task_order` column controls display order (separate from `id`)
- New tasks are appended to the end automatically
- Use `reorder_task` to change priority or reorganize after planning changes

**Docstrings:**
- Detailed descriptions help Claude understand when to use each tool
- `Args:` section documents parameters for Claude

## Step 5: Run the Server

```bash
conda activate mcp-workshop
cd mcp-servers/task-tracker
python server.py
```

You should see the server running on `http://127.0.0.1:8002`. Keep this terminal open.

## Step 6: Add to Claude Code

Update the `.mcp.json` in your art-gallery project to include the task tracker:

```json
{
  "mcpServers": {
    "users-local-datetime-server": {
      "type": "http",
      "url": "http://127.0.0.1:8001/mcp"
    },
    "task-tracker": {
      "type": "http",
      "url": "http://127.0.0.1:8002/mcp",
      "_comment": "Use task tracker to keep track of tasks while doing multi-step work like adding a feature"
    }
  }
}
```

The `_comment` field helps remind Claude when to use this MCP server.

## Step 7: Verify MCP Connection

Launch Claude Code from your art-gallery folder:

```bash
conda activate mcp-workshop
cd art-gallery
claude
```

Verify the server is connected:

```
/mcp
```

You should see both `users-local-datetime-server` and `task-tracker` listed.

## Step 8: Test the Task Tracker

Try these prompts in Claude Code:

```
Show me all my tasks
```

```
Create a new task for the Art Gallery project: "Add image gallery component"
```

```
What tasks are in progress?
```

```
Mark task 1 as done
```

Claude will use your task-tracker MCP tools to manage tasks in your PostgreSQL database!

---

## How It Works

```
┌─────────────┐   HTTP (8002)    ┌──────────────┐   SQL    ┌────────────┐
│ Claude Code │ ◄──────────────► │ Task Tracker │ ◄──────► │ PostgreSQL │
│   (Client)  │                  │  (FastMCP)   │          │  Database  │
└─────────────┘                  └──────────────┘          └────────────┘
       │                                │                        │
       │  "Create a task for..."        │                        │
       │                                │                        │
       │  ─► create_task() ───────────► │ ─► INSERT INTO ... ──► │
       │                                │                        │
       │  ◄── {id: 4, title: ...} ◄──── │ ◄── RETURNING ... ◄─── │
       │                                │                        │
       │  "Created task #4..."          │                        │
       ▼                                ▼                        ▼
```

---

## Exercise: Document Driven Development with Task Tracking

Now let's put it all together - use Document Driven Development (from Part 1) combined with the Task Tracker to add a new feature to the Art Gallery website.

### The Feature: Artwork Detail Page

The home page carousel displays artworks. Clicking on an artwork should navigate to a detail page showing:
- The artwork image (larger view)
- Artist name
- Artwork title/caption
- Short description from the artist

### Step 1: Write the Design Document

Ask Claude to create a design document first:

```
Create a design document for adding an Artwork Detail Page feature to the art gallery.

The feature should:
- Add a new route /artwork/:id for viewing individual artworks
- Display the artwork image in a larger format
- Show artist name, artwork title, and artist's description
- Include navigation back to home
- Match the neo-modern aesthetic of the site

Save the design doc in art-gallery/documents/features/artwork-detail-page.md

Follow the feature design template we created earlier. Include:
- Overview and requirements
- Component structure
- Routing changes
- Data model for artwork details

Use the task tracker to create tasks for this feature as you plan.
```

### Step 2: Review and Revise the Plan

Review the design document and notice a missing requirement - we need to handle invalid artwork IDs:

```
I'm reviewing the design document. We're missing error handling - what happens
when someone visits /artwork/999 for an artwork that doesn't exist?

Update the design document to include 404 handling, and add a task for it.
Reorder it to come before any testing tasks.
```

This demonstrates the `reorder_task` tool - plans change during review, and the task tracker adapts. Watch how Claude:
- Updates the design document
- Creates the new task (appended to end)
- Uses `reorder_task` to move it to the right position in the plan

### Step 3: Implement

Once the plan is finalized, ask Claude to implement:

```
Implement the Artwork Detail Page feature based on the design document.

Use the task tracker to:
1. Update task status as you work
2. Mark tasks done when complete

Show me the task list as you progress.
```

### Step 4: Verify Tasks with PostgreSQL MCP

After implementation, use the PostgreSQL MCP server (from Part 2) to query the tasks directly:

```
Using the postgres MCP, show me all tasks in the workshop_tasks table for the Art Gallery project.
```

This demonstrates two MCP servers working together:
- **Task Tracker MCP** - created and updated the tasks
- **PostgreSQL MCP** - queries the same database directly

You can also verify manually:

```bash
PGPASSWORD=your_password psql -U your_username -d mcp_workshop -c "SELECT * FROM workshop_tasks ORDER BY id;"
```

### Step 5: Test with Playwright MCP

Use the Playwright MCP server (from Part 2) to test the new feature:

```
Using playwright MCP:
1. Navigate to the art gallery home page
2. Click on the first artwork in the carousel
3. Verify the artwork detail page loads with the expected elements
4. Take a screenshot of the detail page
5. Navigate back to home
```

Now you have three MCP servers working together:
- **Task Tracker** - tracked the implementation work
- **PostgreSQL** - verified the task data
- **Playwright** - tested the resulting feature

### Step 6: Review the Full Workflow

Reflect on what just happened:

1. **Document Driven Development** - Wrote a spec before coding
2. **Plan Revision** - Caught missing requirements during review, updated doc and reordered tasks
3. **Task Tracker MCP** - Tracked implementation progress in PostgreSQL
4. **PostgreSQL MCP** - Queried task data directly
5. **Playwright MCP** - Automated testing of the new feature

This is the power of MCP servers - specialized tools that work together to enhance your development workflow.

### What You'll Learn

- How Document Driven Development guides AI implementation
- How doc review catches missing requirements before coding
- How to adapt task order when plans change during review
- How MCP servers provide persistent state across conversations
- How multiple MCP servers can work together seamlessly

---

## Exercise: Add More Query Tools

Try implementing these additional tools on your own:

```python
@mcp.tool
def get_tasks_by_project(project_name: str) -> list[dict]:
    """Get all tasks for a specific project.

    Args:
        project_name: The project name to filter by
    """
    # Your implementation here
    pass


@mcp.tool
def get_tasks_by_status(status: str) -> list[dict]:
    """Get all tasks with a specific status.

    Args:
        status: Filter by status: 'todo', 'in_progress', or 'done'
    """
    # Your implementation here
    pass
```

Hint: Look at how `list_tasks` filters by project_name and status for guidance.

---

## Extending the Server

Try adding more features:

```python
@mcp.tool
def get_project_summary() -> list[dict]:
    """Get a summary of tasks grouped by project with counts per status."""
    with get_connection() as conn:
        with conn.cursor() as cur:
            cur.execute("""
                SELECT
                    project_name,
                    COUNT(*) FILTER (WHERE status = 'todo') as todo,
                    COUNT(*) FILTER (WHERE status = 'in_progress') as in_progress,
                    COUNT(*) FILTER (WHERE status = 'done') as done
                FROM workshop_tasks
                GROUP BY project_name
                ORDER BY project_name
            """)
            return cur.fetchall()
```

---

## Troubleshooting

**Connection refused:**
- Ensure PostgreSQL is running: `brew services list` or `systemctl status postgresql`
- Check credentials in `.env` match what you set in Part 0

**Authentication failed:**
- Verify username and password in `.env`
- See Part 0 troubleshooting for password reset steps

**Table not found:**
- Run the verification SQL from Part 0, Step 6 to create the table

**Server already running on port 8002:**
- Change the port in `server.py` and update `.mcp.json`

---

## Key Concepts Learned

1. **Database Integration**: Connecting MCP servers to PostgreSQL
2. **CRUD Operations**: Create, read, and update functionality
3. **Parameter Validation**: Checking inputs before database operations
4. **Error Handling**: Returning meaningful error messages
5. **Environment Variables**: Keeping credentials secure

---

## Next Steps

Continue to the NanoBanana MCP server to learn about external API integration and async operations.
