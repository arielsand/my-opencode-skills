---
name: coding-standards
description: Generate or update CODING_STANDARDS.md - a comprehensive coding standards document for a project. Use this skill when asked to "document coding standards", "create style guide", "establish conventions", "generate CODING_STANDARDS.md", or any time you need to define or enforce coding practices for a team. Trigger when starting work on a new project that lacks documented standards.
---

# Coding Standards Generator

Generate a comprehensive coding standards document that helps developers and AI agents write consistent, high-quality code.

## Output

Create `CODING_STANDARDS.md` in the project root using the write tool. This file should be committed to version control and referenced in AGENTS.md.

## Process

Follow these phases in order:

### 1. Discovery

Read configuration files to understand the stack:
- `package.json`, `composer.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`
- `.eslintrc*`, `.prettierrc*`, `phpcs.xml`, `.editorconfig`
- `tsconfig.json`, `tslint.json`
- `.github/workflows/` for CI/CD linting rules
- Any existing `CODING_STANDARDS.md` or `STYLE_GUIDE.md`

**Look for:**
- Language versions and runtimes
- Linter/formatter configurations
- Framework-specific conventions
- Existing documentation of standards

### 2. Code Analysis

Examine the codebase for conventions:

**Naming patterns:**
- Files: `PascalCase.tsx`, `kebab-case.ts`, `snake_case.py`
- Variables: `camelCase`, `snake_case`, `PascalCase`
- Constants: `UPPER_SNAKE_CASE`, `camelCase`
- Classes/Components: naming conventions
- Functions: `camelCase`, `snake_case`

**Import organization:**
- Group order (built-ins, external, internal, relative)
- Import style (default vs named)
- Path aliases (`@/`, `~/`, etc.)

**Code organization:**
- Directory structure patterns
- Module/file responsibility
- Separation of concerns
- Layer boundaries

**Framework-specific patterns:**
- React: hooks rules, component structure
- Laravel: service location, facade usage
- Django: app structure, model patterns
- Express: middleware patterns

**Testing conventions:**
- Test file location (`__tests__/`, `*.test.ts`, `*.spec.ts`)
- Test naming (`should_`, `it_`, `describe_`)
- Mocking patterns
- Coverage requirements

### 3. User Preferences

Ask the user questions for things that cannot be reliably inferred:

**Essential questions (ask if unknown):**

1. **Indentation & Formatting:**
   - Tabs or spaces? If spaces, how many?
   - Semicolons required or omitted?
   - Trailing commas (ES5, all, none)?
   - Single or double quotes for strings?

2. **Testing Philosophy:**
   - Test-Driven Development (TDD) required, encouraged, or optional?
   - Minimum coverage threshold? (e.g., 80%)
   - Unit tests required for all functions?

3. **Code Review Requirements:**
   - Maximum PR size limits?
   - Required reviewers count?
   - Self-review checklist requirements?

4. **Commit Conventions:**
   - Commit message format? (Conventional Commits, etc.)
   - Branch naming convention?

5. **Documentation:**
   - Docstring/JSDoc requirements?
   - README structure requirements?
   - API documentation standards?

6. **Error Handling:**
   - Typed errors required?
   - Error logging patterns?
   - User-facing error message format?

Ask 2-4 of the most relevant questions in a single message. Don't overwhelm the user.

### 4. Document Generation

Write `CODING_STANDARDS.md` with these sections:

---

## Document Structure

### 1. Overview

A brief intro explaining:
- Purpose of this document
- Who must follow it (developers, AI agents, contributors)
- How to handle exceptions

### 2. Project Stack

Brief table or list of technologies:

| Layer | Technology | Version |
|-------|-----------|---------|
| Language | TypeScript | 5.x |
| Framework | React | 19.x |
| etc. | | |

### 3. Architectural Conventions

- **Project Structure**: Directory organization with purpose
- **Layer Boundaries**: What goes where (domain, application, infrastructure)
- **Dependency Rules**: Which layers can depend on which

### 4. Naming Conventions

Comprehensive naming rules for:
- Files and directories
- Variables and constants
- Functions and methods
- Classes and interfaces
- Components (if applicable)
- Database tables/columns

Include examples for each:

```
✅ Good:
const userProfile = useUserProfile(userId);

❌ Bad:
const data = useData(id);
```

### 5. Code Language Policy

Establish the language for code:

- **Code in English**: All variable names, function names, classes, interfaces, comments, and documentation must be in English
- **User-facing text in target language**: Error messages, UI labels, and user-facing strings should be in the application's target language (determine from project context)
- **Git commits in English**: Commit messages follow Conventional Commits in English

Ask the user if you cannot determine the app's target language for user-facing content.

Example section:
```markdown
## Language Policy

**All code must be written in English.**

This includes:
- Variable names (`isLoading`, `hasError`)
- Function names (`handleLogin`, `validateEmail`)
- Interface names (`User`, `LoginResponse`)
- Comments (`// Check if user is authenticated`)
- Git commit messages

**Exceptions**:
- User-facing error messages and UI text **must be in [target language]**

```typescript
// ✅ Good - English code, Spanish user message
const handleLogin = async (email: string, password: string) => {
  try {
    await authApi.login(email, password);
  } catch (error) {
    setError('Error de conexión. Verifica tu conexión.');
  }
};
```
```

### 6. Code Style

Language-specific formatting rules:
- Indentation (tabs/spaces, width)
- Quotes (single/double)
- Semicolons
- Trailing commas
- Line length limits
- Bracket style (Allman, K&R, etc.)

If a linter/formatter is configured:
```bash
# Run linting
npm run lint

# Auto-fix issues
npm run lint:fix
```

### 7. Import Organization

Rules for ordering imports:
1. Built-in modules
2. External packages
3. Internal modules (with alias paths)
4. Relative imports
5. Type imports (separate section)

Example:
```typescript
// 1. React built-ins
import { useState, useEffect } from 'react';

// 2. External packages
import axios from 'axios';

// 3. Internal modules
import { useAuth } from '@/hooks/useAuth';

// 4. Relative imports
import { Button } from './Button';

// 5. Types
import type { User } from '@/types';
```

### 8. Testing Standards

- **Philosophy**: TDD, test-after, or hybrid
- **Frameworks**: Jest, Vitest, PHPUnit, etc.
- **File location**: Convention for test files
- **Naming**: Test naming pattern
- **Coverage**: Minimum threshold and how to check
- **What to test**: Requirements for different code types

### 9. Error Handling

- Exception types and when to use
- Error logging requirements
- User-facing error message format
- Recovery patterns

### 10. Documentation

- Inline comment requirements
- JSDoc/Docstring formats
- README requirements
- API documentation (if applicable)
- Changelog requirements

### 11. Git Conventions

- Branch naming: `feat/`, `fix/`, `break/`
- Commit message format
- PR size limits
- Review requirements
- Merge strategy

### 12. Security Guidelines

- Secrets management (never commit)
- Input validation
- Authentication/authorization patterns
- OWASP considerations for the stack

### 13. Performance Standards

- Bundle size limits (if frontend)
- Query optimization requirements
- Caching patterns
- Lazy loading requirements

### 14. Accessibility (if applicable)

- WCAG compliance level
- Required ARIA attributes
- Screen reader compatibility
- Color contrast requirements

---

## Constraints

- **NO raw code dumps** - use short examples
- Focus on rules that can be objectively checked
- Be specific: "Use PascalCase for components" not "Use good naming"
- Include "Why" explanations for controversial rules
- Provide runnable commands for linters/formatters
- Keep under 400 lines - this is a reference, not a book

### 5. AGENTS.md Integration

After creating `CODING_STANDARDS.md`, verify AI agents are instructed to use it:

1. Check if `AGENTS.md` exists in the project root
2. Search for existing references to `CODING_STANDARDS.md` in AGENTS.md
3. If NOT referenced, add this section:

```markdown
## Coding Standards

This project has strict coding standards documented in `CODING_STANDARDS.md`. Before submitting code:

1. Read the full document
2. Run linting: `npm run lint` (or equivalent)
3. Ensure tests pass
4. Check naming conventions match the guide
5. Verify import organization follows the standard

All code must adhere to these standards. No exceptions.
```

4. If AGENTS.md doesn't exist, create one with this section and the PROJECT_ARCHITECTURE.md reference.

## Completion

After writing the file and updating AGENTS.md, inform the user:

> Coding standards documented in CODING_STANDARDS.md
> AGENTS.md updated to reference coding standards.

Suggest they commit both files and consider adding a pre-commit hook to run linting automatically.

## Handling Conflicts

If the skill discovers conflicting conventions in the codebase:

1. Count occurrences of each convention
2. Note which is more prevalent in newer code
3. **Ask the user** which convention to standardize on:
   > "I found both `snake_case` and `camelCase` function names in the Python codebase:
   > - `snake_case`: 127 occurrences (mostly in older modules)
   > - `camelCase`: 89 occurrences (mostly in newer modules)
   > 
   > Which should be the standard going forward?"

Never guess. Always ask when there's genuine ambiguity.