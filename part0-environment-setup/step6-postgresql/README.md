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

# Start PostgreSQL service
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

## Configuration

```bash
# Create a database user (replace YOUR_USERNAME)
createuser -s YOUR_USERNAME

# Create a database for the workshop
createdb mcp_workshop
```

## Verification

```bash
# Check PostgreSQL version
psql --version

# Connect to the database
psql -d mcp_workshop -c "SELECT version();"
```

You should see the PostgreSQL version information.
