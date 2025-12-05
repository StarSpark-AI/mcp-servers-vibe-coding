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
```

> **Important:** Swap `your_username` / `YOUR_STRONG_PASSWORD` for real values before running the statements. PostgreSQL will happily create a user literally named `your_username` if you skip this step.

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
	project_name TEXT NOT NULL,
	title TEXT NOT NULL,
	status TEXT NOT NULL,
	task_order INTEGER
);
INSERT INTO workshop_tasks (project_name, title, status, task_order)
VALUES ('MCP Workshop', 'Review MCP docs', 'in_progress', 1),
	   ('MCP Workshop', 'Add Codex server', 'todo', 2)
ON CONFLICT DO NOTHING;
SELECT * FROM workshop_tasks ORDER BY task_order;
SQL
```

## Optional: pgAdmin

pgAdmin is a GUI tool for managing PostgreSQL databases.

### macOS

```bash
brew install --cask pgadmin4
```

### Windows

Included with the PostgreSQL installer, or download from: https://www.pgadmin.org/download/

### Linux

```bash
# Add pgAdmin repository
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/pgadmin.gpg
echo "deb [signed-by=/usr/share/keyrings/pgadmin.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" | sudo tee /etc/apt/sources.list.d/pgadmin4.list

# Install
sudo apt update
sudo apt install pgadmin4-desktop
```

### Setup

1. Launch pgAdmin
2. Right-click "Servers" → "Register" → "Server"
3. Name: `mcp_workshop`
4. Connection tab:
   - Host: `localhost`
   - Port: `5432`
   - Username: your PostgreSQL username
   - Password: your PostgreSQL password

---

## Troubleshooting

### Password authentication failed (macOS)

If you see `FATAL: password authentication failed` and forgot your password:

**Step 1:** Edit pg_hba.conf to temporarily allow trust authentication

```bash
# For official PostgreSQL installer
sudo nano /Library/PostgreSQL/16/data/pg_hba.conf

# For Homebrew
nano /opt/homebrew/var/postgresql@15/pg_hba.conf
```

Change `scram-sha-256` or `md5` to `trust` for these lines:
```
local   all             all                                     trust
host    all             all             127.0.0.1/32            trust
host    all             all             ::1/128                 trust
```

**Step 2:** Restart PostgreSQL

```bash
# For official PostgreSQL installer
sudo -u postgres /Library/PostgreSQL/16/bin/pg_ctl restart -D /Library/PostgreSQL/16/data

# For Homebrew
brew services restart postgresql@15
```

**Step 3:** Connect and reset password

```bash
psql -U postgres
```

```sql
ALTER USER postgres WITH PASSWORD 'your_new_password';
\q
```

**Step 4:** Revert pg_hba.conf back to `scram-sha-256` and restart PostgreSQL again

### Homebrew vs Official Installer (macOS)

- **Homebrew:** Runs as your macOS user, no password needed by default, use `psql postgres`
- **Official Installer:** Creates a `postgres` system user, requires the password set during installation, use `psql -U postgres`

To check which one is running:
```bash
ps aux | grep postgres | grep -v grep
```
- If you see `/Library/PostgreSQL/` → Official installer
- If you see `/opt/homebrew/` → Homebrew
