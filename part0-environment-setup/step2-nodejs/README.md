# Step 2: Node.js

## Overview

Node.js is a JavaScript runtime required for React development, Claude Code CLI, and other tools.

## Installation

### macOS

```bash
# Using Homebrew
brew install node@20

# Add to PATH if needed (add to ~/.zshrc or ~/.bash_profile)
export PATH="/opt/homebrew/opt/node@20/bin:$PATH"
```

### Windows

Download and run the LTS installer from: https://nodejs.org/

### Linux (Ubuntu/Debian)

```bash
# Using NodeSource
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### Alternative: Using nvm (recommended)

```bash
# Install nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# Restart terminal, then install Node.js
nvm install 20
nvm use 20
nvm alias default 20
```

## Verification

```bash
# Check Node.js version (should be 20.x)
node --version

# Check npm version (should be 10.x)
npm --version

# Test Node.js
node -e "console.log('Node.js is working\!')"
```

## Global Packages

```bash
# Update npm to latest, requires `sudo` on *nix systems
npm install -g npm@latest
```
