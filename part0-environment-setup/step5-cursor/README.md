# Step 5: Cursor

## Overview

Cursor is an AI-powered code editor built on VS Code, designed for AI-assisted development.

## Installation

### macOS

```bash
# Using Homebrew
brew install --cask cursor
```

Or download directly from: https://cursor.sh/

### Windows / Linux

Download the installer from: https://cursor.sh/

## Configuration

1. Launch Cursor
2. Sign in or create an account
3. Import your VS Code settings (optional, but recommended)
4. Configure your AI model preferences

## Key Features

- **AI Chat**: Press `Cmd+L` (Mac) or `Ctrl+L` (Windows/Linux) to open AI chat
- **Inline Editing**: Press `Cmd+K` (Mac) or `Ctrl+K` (Windows/Linux) for inline AI edits
- **Codebase Context**: AI understands your entire codebase
- **Tab Completion**: AI-powered autocomplete

## Recommended Extensions

Cursor includes VS Code extension support. Install these:

- Python
- Pylance
- ESLint
- Prettier
- Django
- ES7+ React/Redux/React-Native snippets
- PostgreSQL

## Settings Sync

If migrating from VS Code:

1. Go to File > Preferences > Settings
2. Click "Import VS Code Settings"
3. Select which settings to import

## Verification

1. Open Cursor
2. Create a new file `test.py`
3. Press `Cmd+L` and ask: "Write a hello world function"
4. Verify the AI responds and generates code

## Tips for Workshop

- Use `@codebase` in chat to reference your entire project
- Use `@file` to reference specific files
- Use `Cmd+K` for quick inline edits during coding
