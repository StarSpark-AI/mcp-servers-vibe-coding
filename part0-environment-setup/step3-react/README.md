# Step 3: React

## Overview

React is a JavaScript library for building user interfaces.

## Installation

React is typically installed per-project, but we'll set up the tools needed.

```bash
# Verify Node.js and npm are installed
node --version
npm --version
```

## Create React App (for testing)

```bash
# Create a test React app to verify setup
npx create-react-app test-react-app
cd test-react-app
npm start
# Visit http://localhost:3000 to verify it works
# Press Ctrl+C to stop, then clean up
cd ..
rm -rf test-react-app
```

## Alternative: Vite (faster)

```bash
# Create a test React app with Vite
npm create vite@latest test-vite-app -- --template react
cd test-vite-app
npm install
npm run dev
# Visit http://localhost:5173 to verify it works
# Press Ctrl+C to stop, then clean up
cd ..
rm -rf test-vite-app
```

## Verification

```bash
# Verify npx is available
npx --version

# Check that create-react-app can be accessed
npx create-react-app --version
```

## Recommended Cursor Extensions

- ES7+ React/Redux/React-Native snippets
- Prettier - Code formatter
- ESLint
