# Step 6: PostgreSQL

## Overview

PostgreSQL is a powerful, open-source relational database system.

## Installation

### macOS

```bash
# Using Homebrew
brew install postgresql@15

# Start PostgreSQL service
brew services start postgresql@15

# Add to PATH (add to ~/.zshrc or ~/.bash_profile)
export PATH="/opt/homebrew/opt/postgresql@15/bin:$PATH"
```

### Windows

Download and run the installer from: https://www.postgresql.org/download/windows/

### Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install postgresql postgresql-contrib

# Start PostgreSQL service (systemd hosts)
sudo systemctl start postgresql
sudo systemctl enable postgresql

# If systemctl is unavailable (WSL2/containers)
sudo service postgresql start
```

## Configuration

```bash
# Open a psql shell as the postgres superuser
#   Linux:  sudo -u postgres psql
#   macOS:  psql postgres
#   Windows: run "psql -U postgres"

# Inside psql, replace placeholders and run:
CREATE ROLE your_username WITH LOGIN PASSWORD 'YOUR_STRONG_PASSWORD';
CREATE DATABASE mcp_workshop OWNER your_username;
GRANT CONNECT ON DATABASE mcp_workshop TO your_username;
GRANT ALL PRIVILEGES ON DATABASE mcp_workshop TO your_username;
\q

> **Important:** Swap `your_username` / `YOUR_STRONG_PASSWORD` for real values before running the statements. PostgreSQL will happily create a user literally named `your_username` if you skip this step.
```

## Verification

```bash
# Check PostgreSQL version
psql --version

# Connect using the workshop credentials (replace placeholders)
PGPASSWORD=YOUR_STRONG_PASSWORD \
psql -U your_username -d mcp_workshop -h localhost \
	 -c "SELECT current_user, current_database();"

# Create a test table and seed sample rows
PGPASSWORD=YOUR_STRONG_PASSWORD psql -U your_username -d mcp_workshop -h localhost <<'SQL'
CREATE TABLE IF NOT EXISTS workshop_tasks (
	id SERIAL PRIMARY KEY,
	title TEXT NOT NULL,
	status TEXT NOT NULL
);
INSERT INTO workshop_tasks (title, status)
VALUES ('Review MCP docs', 'in_progress'),
	   ('Add Codex server', 'todo')
ON CONFLICT DO NOTHING;
SELECT * FROM workshop_tasks;
SQL
```
