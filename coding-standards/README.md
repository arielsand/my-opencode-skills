# Coding Standards Skill

Generates `CODING_STANDARDS.md` - a comprehensive coding standards document for teams.

## What it does

This skill analyzes a codebase and creates a detailed standards document covering:

### Generated sections (18)

1. **Overview** - Purpose and scope
2. **Project Stack** - Technologies and versions table
3. **Architectural Conventions** - Structure, layers, dependencies
4. **Naming Conventions** - Files, variables, functions, components
5. **Language Policy** - Code in English, user text in target language
6. **Code Style** - Indentation, quotes, semicolons, formatting
7. **Import Organization** - Ordering and grouping rules
8. **Component Patterns** - Functional components, styles, props
9. **Context Pattern** - React Context with TypeScript generics
10. **API Layer** - Axios setup, interceptors, error handling
11. **Testing Standards** - Philosophy, location, naming, coverage
12. **Error Handling** - Types, logging, user-facing messages
13. **Git Conventions** - Branch naming, commits, PR requirements
14. **Security Guidelines** - Secrets, validation, auth patterns
15. **Performance Standards** - Bundle size, optimization patterns
16. **Accessibility** - WCAG, touch targets, screen readers
17. **Documentation** - Comments, JSDoc, README structure
18. **Pre-Commit Checklist** - Verification steps before committing

### User interaction

The skill asks clarifying questions for things it cannot infer:
- Testing philosophy (TDD, test-after, optional)
- Commit message format (Conventional Commits, simple, gitmoji)
- Linting preferences (add ESLint + Prettier, manual only)
- Other preferences specific to the project

### Additional actions

- Updates `AGENTS.md` to reference `CODING_STANDARDS.md`

## When to use

- "Document coding standards"
- "Create style guide"
- "Establish conventions"
- Starting a new project that needs standards
- Onboarding developers to a team
- Enforcing code quality practices

## Trigger phrases

The skill triggers automatically when:
- "document coding standards"
- "create style guide"
- "establish conventions"
- "generate CODING_STANDARDS.md"

## Example

```
Use the coding-standards skill on /path/to/project
```

The skill will:
1. Discover the tech stack
2. Analyze code patterns
3. Ask clarifying questions
4. Generate `CODING_STANDARDS.md`
5. Update `AGENTS.md`

## Constraints

- No raw code dumps - use short examples
- Focus on rules objectively checkable
- Be specific (e.g., "Use PascalCase for components")
- Include "Why" for controversial rules
- Under 400 lines (reference, not a book)

## Language Policy

All generated documents follow this rule:
- **Code**: Variables, functions, comments in English
- **User-facing text**: Error messages, UI labels in the app's target language (detected or asked)