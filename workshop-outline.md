# Workshop Outline

## Part 0: Environment Setup

1. Warp (terminal)
2. Node.js
3. React
4. Claude Code CLI
5. Cursor (code editor)
6. PostgreSQL
7. Conda
8. Python 3.11 (in Conda environment)
9. Django (in Conda environment)
10. Google Cloud CLI

## Part 1: Foundations (55 mins)

### Vibe Coding Recap + Document Driven Development (~30 mins)

- Quick vibe coding philosophy refresh (5 mins)
- Document Driven Development deep dive (25 mins):
  - Writing specs that AI can execute on
  - Prompting-friendly documentation structures
  - Examples: good vs bad project docs

### Hands-On: Build an Art Gallery Website

1. Create `art-gallery/documents/templates/` folder structure
2. Use Claude Code with the meta-template to generate React design templates
3. Create React app scaffolding (Vite + React + TypeScript + Tailwind)
4. Create architecture and coding standards documents
5. Run `/init` to initialize Claude Code context
6. Build Abstract Art Gallery pages:
   - Home page: carousel, newsletter signup, event cards
   - About Us and Contact Us pages
   - Neo-modern styling with abstract theme
7. Install frontend-design plugin and enhance the design

### MCP Theory & Architecture (~25 mins)

- What is MCP? Why do we need it?
- MCP vs APIs vs function calling
- Protocol overview and architecture

## Part 2: Getting Started (1 hour)

### Hello World MCP Server (~40 mins)

- FastMCP setup and basics
- Date/time server implementation
- Testing and integration with Claude Code

### Tour of Useful MCP Servers (~20 mins)

- PostGres, GitHub, Browser, Documentation, Postman MCPs
- Real-world integration examples

## Part 3: Build Custom MCPs (1.5 hours)

### Planning MCP Server (~45 mins)

- State management and CRUD operations
- Data persistence patterns
- Practical task tracking

### NanoBanana/Image Generation MCP (~45 mins)

- External API integration
- Async operations and file handling
- Visual workflow enhancement

## Part 4: Wrap-up (15 mins)

- Integration demo (both MCPs working together)
- Best practices and production considerations
- Next steps and resources
