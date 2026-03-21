# Architecture Map Skill

Generates `PROJECT_ARCHITECTURE.md` - a comprehensive architectural map for AI context.

## What it does

This skill analyzes a codebase and creates a detailed architectural document that helps:
- AI agents quickly understand project structure
- Developers onboard to new projects
- Teams maintain consistent documentation

### Generated sections (10)

1. **Project Essence** - Business problem, users, value proposition
2. **Tech Stack & Versions** - Framework, language, dependencies table
3. **Architecture Diagram** - ASCII representation of system
4. **Backend Architecture** - Entities, API endpoints, services
5. **Frontend/Mobile Architecture** - Navigation, state, components
6. **Critical Business Logic** - Source of truth for each feature
7. **Data Flow** - How data moves through the system
8. **Inter-Service Communication** - APIs, auth, error handling
9. **Development Standards** - Naming, testing, Git conventions
10. **Key Files Entry Points** - Most important files to understand

### Additional actions

- Updates `AGENTS.md` to reference `PROJECT_ARCHITECTURE.md`
- Ensures AI agents will read architecture context in future sessions

## When to use

- Starting work on a new/unknown project
- Asked to "document the architecture"
- Asked to "onboard a new AI"
- Need to understand codebase structure quickly
- Before making significant architectural changes

## Trigger phrases

The skill triggers automatically when:
- "map the architecture"
- "document the project structure"
- "onboard a new AI"
- "analyze the codebase"
- "create context for AI"

## Example

```
Use the architecture-map skill on /path/to/project
```

Output: Creates `PROJECT_ARCHITECTURE.md` in project root (under 500 lines).

## Constraints

- No raw code dumps - summaries in prose
- Uses bullet points, tables, and ASCII diagrams
- Focuses on relationships and data flow
- Specific about file paths
- Under 500 lines (it's a map, not a novel)