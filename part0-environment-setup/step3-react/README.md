# Step 3: React

## Overview

React is a JavaScript library for building user interfaces.

## Installation

React is typically installed per-project. We'll use Vite as the build tool (recommended by React team).

```bash
# Verify Node.js and npm are installed
node --version
npm --version
```

## Create React App with Vite

```bash
# Create a test React app with Vite
npm create vite@latest test-vite-app -- --template react
```

When prompted:
- **Use rolldown-vite (Experimental)?** → No
- **Install with npm and start now?** → No (we'll do it manually)

```bash
cd test-vite-app
npm install
npm run dev --  # add --host if running inside docker to access from outside Docker
# Visit http://localhost:5173 to verify it works
# Press Ctrl+C to stop, then clean up
cd ..
rm -rf test-vite-app
```

> **Note:** `create-react-app` is deprecated. The React team now recommends using Vite or a framework like Next.js. See https://react.dev/learn/start-a-new-react-project

## Verification

```bash
# Verify npx is available (comes with npm)
npx --version
```

## Recommended Cursor Extensions

- ES7+ React/Redux/React-Native snippets
- Prettier - Code formatter
- ESLint
