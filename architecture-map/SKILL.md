---
name: architecture-map
description: Generate or update PROJECT_ARCHITECTURE.md - a comprehensive architectural map for AI context. Use this skill when asked to "map the architecture", "document the project structure", "onboard a new AI", "analyze the codebase", "create context for AI", or any time you need to understand a codebase's architecture. This skill is essential for new project onboarding and should trigger automatically when starting work on unfamiliar projects.
---

# Architecture Map Generator

Generate a comprehensive architectural map that helps AI agents (and humans) quickly understand a codebase.

## Output

Create `PROJECT_ARCHITECTURE.md` in the project root using the write tool. This file should be committed to version control.

## Process

Follow these phases in order:

### 1. Discovery

Read configuration files to understand the stack:
- `package.json`, `composer.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`
- `app.json`, `expo.json` for mobile apps
- `.env.example` for environment hints
- Entry points: `app/_layout.tsx`, `index.ts`, `main.py`, `src/main.rs`

**Look for:**
- Project type (mobile/web/backend/fullstack)
- Core frameworks and versions
- Key dependencies and their purpose
- Database/ORM used
- Testing framework

### 2. Structure Analysis

Map the directory structure and identify:
- Where business logic lives
- How routes/navigation are organized
- State management patterns
- API/service layer organization
- Data models and schemas

### 3. Pattern Recognition

Identify conventions used:
- Naming patterns (files, variables, functions)
- Code organization style
- Authentication/authorization flow
- Error handling patterns
- Testing strategies

### 4. Document Generation

Using all gathered information, write `PROJECT_ARCHITECTURE.md` with these sections:

---

## Document Structure

The output file MUST include these sections:

### 1. Project Essence

A 3-5 sentence summary answering:
- What business problem does this solve?
- Who are the primary users?
- What's the core value proposition?

### 2. Tech Stack & Versions

Create a table:

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|

Include: frameworks, key libraries, runtime versions, databases, caching, queues.

### 3. Architecture Diagram (ASCII)

Example for a fullstack app:
```
┌─────────────────┐
│   Mobile App    │ (React Native / Expo)
│   Frontend      │
└────────┬────────┘
         │ HTTP/REST
         ▼
┌─────────────────┐
│   API Server    │ (Laravel / Node / Python)
│   Backend       │
└────────┬────────┘
         │
    ┌────┴────┐
    ▼         ▼
┌───────┐ ┌───────┐
│  DB   │ │ Cache │
└───────┘ └───────┘
```

Adapt the diagram to match the actual architecture.

### 4. Backend Architecture

- **Core Entities & Schema**: List main models/tables with relationships
- **API Endpoints**: Group by domain. Note auth requirements.
- **Background Jobs**: Queue workers, scheduled tasks, crons
- **Services Layer**: Key service classes and their responsibilities

### 5. Frontend/Mobile Architecture

- **Navigation Structure**: Route hierarchy (tree format)
- **State Management**: Where state lives, how it flows
- **Key Components**: Shared/reusable components by category
- **Hooks/Utilities**: Custom hooks and their purposes
- **API Integration**: How the app communicates with backends

### 6. Critical Business Logic ("Source of Truth")

For each major feature, identify WHERE the logic lives:

```
Feature: Authentication
├── Backend: app/Services/AuthService.php
├── Controller: app/Http/Controllers/AuthController.php
├── Mobile: context/AuthContext.tsx
└── Hooks: hooks/useAuth.ts
```

### 7. Data Flow

Describe how data moves through the system for key operations:

```
[User Action] → [Component] → [API Client] → [Backend Controller]
                                          ↓
                                    [Service Layer]
                                          ↓
                                      [Database]
```

### 8. Inter-Service Communication

- Base URLs and environment configuration
- Authentication/Authorization headers
- Request/Response interceptors
- Error handling patterns
- API versioning strategy

### 9. Development Standards

- **Naming conventions**: files, variables, functions
- **Code style**: linters, formatters used
- **Testing patterns**: frameworks, coverage expectations, test location
- **Git workflow**: branch naming, commit conventions
- **Documentation**: README structure, inline docs

### 10. Key Files Entry Points

List the 10-15 most important files to understand the system:

```
├── app/_layout.tsx          # Root navigation
├── app/(auth)/              # Auth flow
├── services/api.ts          # API client
├── context/AppContext.tsx   # Global state
├── ...etc
```

---

## Constraints

- **NO raw code dumps** - summarize logic in prose
- Use bullet points, tables, and ASCII diagrams
- Focus on relationships and data flow
- Be specific about file paths
- If a section doesn't apply, note "N/A" and explain why
- Keep the document under 500 lines - it's a map, not a novel

### 5. AGENTS.md Integration

After creating `PROJECT_ARCHITECTURE.md`, verify that AI agents are instructed to use it as context:

1. Check if `AGENTS.md` exists in the project root
2. Search for existing references to `PROJECT_ARCHITECTURE.md` in AGENTS.md
3. If AGENTS.md exists but does NOT reference the architecture map, add the following section:

```markdown
## Project Architecture Context

Before making any changes to this codebase, ALWAYS read `PROJECT_ARCHITECTURE.md` in the project root. This file contains:

- Tech stack and versions
- Module/service architecture
- Navigation and routing structure
- Critical business logic locations
- Data flow patterns
- API endpoints and contracts
- Development standards

Use this as your primary context source when onboarding to or working on this project.
```

4. If AGENTS.md does NOT exist, create one with this section as its content.
5. Use the edit tool to insert the section — place it after the project overview section if one exists, otherwise append to the end.

**Why this matters**: AI agents in future sessions will automatically read AGENTS.md but may not discover PROJECT_ARCHITECTURE.md without explicit instruction. This ensures continuity.

## Completion

After writing the file and updating AGENTS.md, inform the user:
> Architecture map saved to PROJECT_ARCHITECTURE.md
> AGENTS.md updated to reference architecture context.

Suggest they commit both files to version control for future AI sessions.