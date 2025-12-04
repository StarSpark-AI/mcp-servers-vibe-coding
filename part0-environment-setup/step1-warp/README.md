# Step 1: Warp Terminal

## Overview

Warp is a modern, GPU-accelerated terminal with AI-powered features built for productivity.

## Installation

### macOS

```bash
# Using Homebrew
brew install --cask warp
```

Or download directly from: https://www.warp.dev/

### Linux

Download from: https://www.warp.dev/download

Packages available for Ubuntu/Debian (.deb), Fedora/RHEL (.rpm), and AppImage.

### Windows

Windows support is in beta. Download from: https://www.warp.dev/

## Configuration

1. Launch Warp
2. Sign in or create an account
3. Complete the onboarding tutorial

## Key Features

- **AI Command Search**: Press `Ctrl+`` to search for commands using natural language
- **Blocks**: Commands and outputs are organized into blocks for easy navigation
- **Workflows**: Save and share command sequences
- **Modern Editing**: Full text editor features in the command line

## Recommended Settings

1. Go to Settings (⌘ + ,)
2. Set your preferred theme
3. Configure your default shell (zsh recommended)
4. Enable "Restore windows on startup"

## Verification

Launch Warp and run:

```bash
echo "Warp is working!"
```

You should see the output organized in a block.

> **Note:** Warp is a GUI application and requires a display server. It cannot be tested in Docker containers.