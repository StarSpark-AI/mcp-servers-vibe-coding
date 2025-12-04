# Document Driven Development

## Overview

Document Driven Development (DDD) is a methodology where documentation drives implementation. Instead of writing code first and documenting later, you create comprehensive documentation that AI assistants can understand and execute on.

This approach transforms documentation from a chore into a productivity multiplier.

## Why Document Driven Development?

### Traditional Approach
1. Write code
2. Debug and iterate
3. Write documentation (often skipped or outdated)
4. AI assistants struggle with ambiguous context

### Document Driven Approach
1. Write clear specifications and architecture docs
2. AI assistants understand context immediately
3. Implementation follows documentation
4. Documentation stays current by design

### Benefits

- **Better AI Collaboration**: AI assistants work more effectively with well-documented projects
- **Reduced Ambiguity**: Clear specs mean fewer misunderstandings
- **Faster Onboarding**: New team members (human or AI) get up to speed quickly
- **Living Documentation**: Docs evolve with the codebase
- **Pattern Consistency**: Established patterns are followed automatically

---

## Core Principles

### 1. Document Before You Code

Write specifications, architecture decisions, and expected behaviors before implementation. This gives AI assistants the context they need to generate correct code.

**Why this matters:**
- Most code requires multiple steps - documents let you restart sessions and maintain progress
- Reduces AI hallucinations by providing explicit context
- Works around limited context windows of AI agents

### 2. Use AI-Friendly Structures

Structure documents so AI can parse and act on them:
- Clear headings and sections
- Explicit requirements (not implied)
- Code examples showing expected patterns
- Defined inputs, outputs, and behaviors

### 3. Pattern Analysis First

Before any implementation, analyze existing patterns in the codebase. Document what patterns exist and how new code should follow them.

### 4. Iterative Refinement

Documents are living artifacts. Update them as requirements change, and use AI to help keep them current.

---

## Document Types

### 1. Architecture Documents (ARCHITECTURE.md)

High-level system design that provides context for all development work.

**Key sections:**
- System overview and components
- Database schema and relationships
- API endpoints and routing
- Technology stack and dependencies
- Security considerations
- Deployment approach

**Example structure:**
```markdown
# Project Architecture

## Overview
[One paragraph describing the system]

## System Components
- Component A: [purpose]
- Component B: [purpose]

## Database Schema
[Model definitions with relationships]

## API Endpoints
[Endpoint list with methods and purposes]

## Development vs Production
[Configuration differences]
```

### 2. Feature Design Documents

Detailed specifications for new features before implementation.

**Key sections:**
- Feature overview and goal
- Existing pattern analysis
- Database changes (if any)
- API changes (if any)
- UI/UX considerations
- Testing approach
- Rollback plan

### 3. Enhancement Design Documents

Modifications to existing functionality.

**Key sections:**
- Current behavior
- Proposed behavior
- Impact analysis
- Migration path
- Backward compatibility

### 4. Bug Fix Documents

Structured approach to fixing issues.

**Key sections:**
- Bug description and reproduction steps
- Root cause analysis
- Proposed fix
- Test case (TDD approach)
- Verification steps

---

## Good vs Bad Documentation

### Bad: Vague and Incomplete

```markdown
# User Feature

Add user authentication to the app.
```

**Problems:**
- No context about existing patterns
- No specific requirements
- AI must guess implementation details
- Multiple valid interpretations

### Good: Specific and Actionable

```markdown
# User Authentication Feature

## Overview
Add session-based authentication to the chat application, allowing users to log in and have their messages associated with their account.

## Existing Pattern Analysis
- Current session handling: Cookie-based session_id (see chat/views.py:15)
- Message model already has optional user field (chat/models.py:23)
- URL patterns follow /chat/ prefix convention

## Requirements
1. Login view at /auth/login/
2. Logout view at /auth/logout/
3. Registration view at /auth/register/
4. Session timeout: 24 hours
5. Password requirements: minimum 8 characters

## Database Changes
- Use Django's built-in User model
- Link existing Message.user field to authenticated user

## UI Changes
- Add login/logout buttons to base.html header
- Show username when logged in
- Redirect to login for protected views

## Testing
- Test login with valid credentials
- Test login with invalid credentials
- Test session expiration
- Test protected view access
```

**Why it works:**
- References existing patterns with file locations
- Specific requirements with clear acceptance criteria
- AI knows exactly what to implement and where

---

## Prompting-Friendly Structures

### Use Explicit Requirements

```markdown
## Requirements
1. [Requirement 1 - specific and measurable]
2. [Requirement 2 - specific and measurable]
3. [Requirement 3 - specific and measurable]
```

### Include Code Examples

```markdown
## Expected Model Structure
```python
class Message(models.Model):
    session_id = models.CharField(max_length=64, db_index=True)
    role = models.CharField(max_length=16, choices=[('user','user'),('assistant','assistant')])
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
```
```

### Reference File Locations

```markdown
## Files to Modify
- `chat/models.py` - Add new model fields
- `chat/views.py` - Add authentication checks
- `chat/urls.py` - Add auth URL patterns
- `templates/base.html` - Add login/logout UI
```

### Define Expected Behaviors

```markdown
## Expected Behaviors

### Happy Path
1. User submits login form with valid credentials
2. Server validates and creates session
3. User redirected to /chat/ with success message
4. Header shows username and logout button

### Error Cases
1. Invalid credentials: Show error message, stay on login page
2. Empty fields: Show validation errors inline
3. Session expired: Redirect to login with message
```

---

## The Development Lifecycle

Document Driven Development follows this lifecycle:

```
┌─────────────────┐
│  Requirements   │
│   Gathering     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Existing Pattern│
│    Analysis     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Design Document │
│    Creation     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Design Review & │
│  Verification   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Implementation  │
│  (AI-assisted)  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Testing &     │
│  Confirmation   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Documentation  │
│     Update      │
└─────────────────┘
```

### 1. Requirements Gathering
- Clarify what needs to be built
- Ask clarifying questions before starting

### 2. Pattern Analysis (Critical!)
- Search existing codebase for similar patterns
- Document how similar features were implemented
- Identify conventions to follow

### 3. Design Document Creation
- Write detailed specification
- Include all sections from templates
- Reference existing patterns

### 4. Design Review & Verification
- Review with stakeholders
- Verify approach is correct before coding
- Update document based on feedback

### 5. Implementation
- AI assistant follows the design document
- Code matches documented patterns
- Implementation is predictable

### 6. Testing & Confirmation
- Verify implementation matches requirements
- Run test cases from design document
- Confirm expected behaviors

### 7. Documentation Update
- Update architecture docs if needed
- Link to PR in design document
- Archive or update design document

---

## AI Workflow Instructions

When working with AI assistants, include workflow instructions in your documents:

```markdown
## AI Assistant Workflow

1. **Read First**: Analyze ARCHITECTURE.md and existing patterns before implementing
2. **Ask Questions**: Clarify any ambiguous requirements before coding
3. **Pattern Match**: Follow existing code conventions found in similar files
4. **Verify Design**: Confirm approach before writing code
5. **Implement**: Write code following the design document
6. **Test**: Verify implementation matches requirements
7. **Update Docs**: Update relevant documentation after implementation
```

---

## Templates

### Design Document Template

See [design_document_meta_template.md](../../design_document_meta_template.md) for a comprehensive template that can generate feature, enhancement, and bug fix documents.

---

## Practical Tips

### Starting a New Feature

```
Before implementing [feature name], please:

1. Read the ARCHITECTURE.md to understand project structure
2. Search for similar patterns in the codebase
3. Write a design document with:
   - Feature overview
   - Pattern analysis findings
   - Database/API/UI changes
   - Testing approach
4. Get approval before implementing
```

### Asking for AI Analysis

```
Analyze this codebase and create internal documentation about:
- Project purpose and goals
- File organization and conventions
- Database models and relationships
- URL patterns and routing
- Development workflow and standards

Then summarize your understanding and ask about any conventions I'd like you to follow.
```

### Maintaining Documentation

```
Review the current ARCHITECTURE.md against the actual codebase:
1. Are all components documented?
2. Are code examples still accurate?
3. Are there new patterns not yet documented?
4. Update any outdated sections.
```

---

## Hands-On Exercise: Setting Up Document Driven Development

In this exercise, you'll use Claude Code to bootstrap a React project with proper documentation structure.

### Step 1: Create Project Structure

Create a project folder with a documents directory:

```bash
# From the workshop repo root
mkdir -p art-gallery/documents/templates
```

Your structure should look like:
```
mcp-servers-vibe-coding/
├── part1-foundations/
├── art-gallery/                ← Your new project folder
│   └── documents/
│       └── templates/
└── design_document_meta_template.md
```

### Step 2: Launch Claude Code

From the root of the workshop repo, launch Claude Code:

```bash
claude
```

### Step 3: Generate Design Document Templates

The workshop repo includes a **Design Document Meta-Template** (`design_document_meta_template.md`) at the root level. This meta-template is a "template for creating templates" - it contains instructions for generating project-specific design document templates.

First, review the meta-template to understand its structure:

```bash
# Optional: Review the meta-template
cat ../design_document_meta_template.md
```

Then ask Claude Code to read it and create React-specific templates:

```
Read the design_document_meta_template.md at the repo root.
Using it as a guide, create specific templates for a React-based project:

1. feature-design-template.md
2. enhancement-design-template.md
3. bug-fix-design-template.md

Save them in art-gallery/documents/templates/
Use React/TypeScript examples instead of Django.
```

### Step 4: Create React App Scaffolding

Before writing architecture documentation, create the project scaffolding so we have a real structure to document.

Ask Claude Code to create the Vite + React + Tailwind project:

```
Create a React application scaffolding for an art gallery website.

Setup:
- Use Vite + React + TypeScript
- Add Tailwind CSS for styling
- Add React Router for navigation
- Create the folder structure but don't build pages yet

Create basic structure:
- src/components/ (empty, for shared components)
- src/pages/ (with placeholder Home, About, Contact pages)
- src/layouts/ (for page layouts)
- src/assets/ (for images and static files)

Create the project in art-gallery/
```

**Verify the scaffolding:**

```bash
cd art-gallery
npm install
npm run dev
```

You should see a basic Vite + React app running at `http://localhost:5173`.

### Step 5: Create Architecture Document

Now that the project structure exists, ask Claude Code to document it:

```
Write an ARCHITECTURE.md for this React art gallery application.
Save it in art-gallery/documents/

Include:
- Project overview and goals
- Component structure and hierarchy (based on our folder structure)
- State management approach
- Routing structure (React Router)
- Styling approach (Tailwind CSS)
- Development vs production considerations
```

### Step 6: Create Coding Standards Document

Ask Claude Code to write coding standards:

```
Write a CODING_STANDARDS.md for this React project. Use best practices for modern React development. You may search the web for best practices.
Save it in art-gallery/documents/

Include:
- Naming conventions (files, components, functions, variables)
- File and folder structure
- Component patterns (functional vs class, hooks usage)
- TypeScript guidelines
- Testing approach
- Import ordering
- Error handling patterns
```

---

**Adapting to Your Own Projects**

The documents you've created (design templates, architecture, coding standards) are starting points. When applying Document Driven Development to your own projects:

- **Customize templates** to match your tech stack (different frameworks, languages, or patterns)
- **Update architecture docs** as your project evolves - they're living documents
- **Refine coding standards** based on team preferences and project requirements
- **Add new document types** as needed (API specs, deployment guides, onboarding docs)

The goal is documentation that helps AI assistants understand your project and generate consistent, correct code.

---

### Step 7: Initialize Claude Code Context

Exit the current Claude Code session and restart from inside the art-gallery folder:

```bash
# Exit Claude Code (Ctrl+C or /exit)

# Navigate to art-gallery folder
cd art-gallery

# Launch Claude Code
claude

# Run init to build context
/init
```

Claude Code will now analyze your project structure and documentation, building context for future development work.

### Step 8: Build the Art Gallery Pages

Now let's build out the actual pages for our art gallery website.

**Project: Abstract Art Gallery**

A neo-modern art gallery website with an abstract aesthetic theme.

Ask Claude Code to build the pages:

```
Build out the pages for our art gallery website with neo-modern styling, aligned with the archictecture and coding standards.

Pages to create:
1. Home page with:
   - Full-bleed image carousel featuring artwork (use placeholder images for now)
   - Email newsletter signup section
   - Upcoming events cards (2-3 event cards)

2. About Us page:
   - Gallery history and mission
   - Team/curator section

3. Contact Us page:
   - Contact form (name, email, message)
   - Gallery location and hours
   - Map placeholder

Design requirements:
- Neo-modern aesthetic (clean lines, bold typography, generous whitespace)
- Abstract theme (geometric shapes, artistic backgrounds)
- Responsive design (mobile-friendly)
- Dark/light sections for visual contrast

Start with writing a design document for this, wait for a review before implementing.
```

**Verify the application:**

```bash
npm run dev
```

Visit `http://localhost:5173` to see your art gallery site.

### Step 9: Enhance Design with Frontend Design Plugin

The Claude Code frontend-design plugin helps create more distinctive, production-grade UI designs that avoid generic "AI aesthetics."

**Install the plugin:**

In Claude Code, run:
```
/plugin marketplace add anthropics/claude-code
/plugin install frontend-design@claude-code-plugins
```

**Using the plugin:**

The plugin activates automatically when you describe frontend work. Simply ask Claude Code to enhance the design:

```
Redesign the art gallery home page to be more visually distinctive and polished.

Keep the same content (carousel, newsletter signup, event cards) but make the design:
- More unique and memorable
- Better typography and spacing
- Refined color palette
- Subtle animations or transitions
- Professional-grade visual polish

Commit to a bold aesthetic direction.
```

The plugin will:
- Understand the context of your interface
- Choose a distinctive aesthetic direction (minimalist, brutalist, retro-futuristic, etc.)
- Generate production-ready code with refined typography, colors, and visual details

Review the changes and compare with the original design.

**Optional: Generate Artwork and Images**

Try these prompts to add visual content to your gallery (Claude may generate CSS-based artwork or placeholder images):

```
Generate some nice artwork for the home page carousel. Be creative with abstract, neo-modern pieces.
```

```
Generate images for the About page showing our gallery space.
Imagine an exposed concrete modern gallery in Germany - be imaginative with the architectural details.
```

These images will be revisited later in the workshop when we build the NanoBanana image generation MCP server.

### What You've Accomplished

After completing this exercise, you have:

- ✅ A documented project structure
- ✅ Reusable design document templates for features, enhancements, and bug fixes
- ✅ An architecture document describing system design
- ✅ Coding standards for consistent development
- ✅ Claude Code initialized with project context
- ✅ A working React art gallery website with neo-modern styling
- ✅ Enhanced design using the frontend-design plugin

This foundation enables faster, more consistent AI-assisted development. The art gallery site will be used in later parts of the workshop to demonstrate vision-based MCP servers.

---

## Reference Materials

- [Design Document Meta-Template](../../design_document_meta_template.md) - Template generator for creating project-specific templates
