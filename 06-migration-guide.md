# Section 6: Migration Guide

Most projects start with a messy, bloated CLAUDE.md—and that's okay. You don't need to over-engineer
from day one. But when your CLAUDE.md grows unwieldy or you want to share patterns across projects,
it's time to migrate.

This guide provides a step-by-step process for reorganizing your existing CLAUDE.md using the
TrigMem methodology.

---

## When to Migrate

### Signs You Need Migration

- [ ] CLAUDE.md is over 200 lines
- [ ] Claude seems to ignore some instructions (context overload)
- [ ] You want to reuse patterns in another project
- [ ] Team members keep overwriting each other's instructions
- [ ] You're hitting token limits in long conversations

### When NOT to Migrate

- Your CLAUDE.md is small and working fine
- You're in the middle of a critical sprint
- The project is short-lived or throwaway

**Rule of thumb:** If it's not causing problems, don't fix it. Migrate when the pain exceeds
the effort.

---

## The 3-Step Migration Process

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        THE MIGRATION PROCESS                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  STEP 1: AUDIT                                                              │
│  Tag every section with its Information Category (1-6)                      │
│                                                                             │
│  STEP 2: SPLIT                                                              │
│  Move each category to its proper destination                               │
│                                                                             │
│  STEP 3: LINK                                                               │
│  Replace moved content with Discovery Anchors                               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Step 1: Audit

### What You're Doing

Reading through your CLAUDE.md and tagging every section with one of the 6 Information Categories
from [Section 2](02-what-youre-storing.md).

### The Category Reference

| # | Category | Quick Test |
|---|----------|------------|
| 1 | Project Identity | "What is this project?" |
| 2 | Codebase Structure | "Where is everything?" |
| 3 | Operational Commands | "How do I run/build/deploy?" |
| 4 | Reusable Patterns | "Universal how-to knowledge" |
| 5 | Architectural Guidance | "Our specific conventions" |
| 6 | Iterative Corrections | "Don't make that mistake again" |

### How to Tag

Add category markers directly in your CLAUDE.md (you'll remove them after migration):

```markdown
# MyProject                           <!-- Cat 1: Identity -->

A React e-commerce application.       <!-- Cat 1: Identity -->

## Tech Stack                         <!-- Cat 1: Identity -->
- React 18, TypeScript, Tailwind
- Node.js, Express, PostgreSQL

## Project Structure                  <!-- Cat 2: Map -->
- src/components/ - React components
- src/api/ - Express routes
- src/db/ - Database utilities

## Testing                            <!-- Cat 3: Commands -->
Run all tests: `npm test`
Run with coverage: `npm test:coverage`
E2E tests: `npm run test:e2e`

Before PR:
1. Run lint
2. Run tests
3. Build

## Component Patterns                 <!-- Cat 4: Reusable Pattern -->
All components should:
- Use functional components with hooks
- Have a single responsibility
- Include PropTypes or TypeScript types
[... 30 lines of patterns ...]

## Our Conventions                    <!-- Cat 5: Architectural Guidance -->
- Components go in src/components/{Feature}/
- API routes follow REST conventions
- Database queries use raw SQL, not ORM

## Rules                              <!-- Cat 6: Corrections -->
- Never use `any` in TypeScript
- Always handle loading/error states
- Don't commit console.log statements
```

### Audit Checklist

After tagging, verify:
- [ ] Every section has a category tag
- [ ] No section is tagged with multiple categories (split if needed)
- [ ] You understand why each tag applies

---

## Step 2: Split

### What You're Doing

Moving content from CLAUDE.md to its proper destination based on category.

### The Destination Map

| Category | Destination | File Location |
|----------|-------------|---------------|
| 1. Project Identity | **Keep in CLAUDE.md** | `CLAUDE.md` |
| 2. Codebase Structure | **Keep in CLAUDE.md** (brief) | `CLAUDE.md` |
| 3. Operational Commands | **Commands** | `.claude/commands/{name}.md` |
| 4. Reusable Patterns | **Skills** | `.claude/skills/{name}/SKILL.md` |
| 5. Architectural Guidance | **Rules** or **Docs** | `.claude/rules/{name}.md` or `docs/` |
| 6. Iterative Corrections | **Rules** | `.claude/rules/{name}.md` |

### Creating Commands (Category 3)

For operational workflows, create command files:

**Before (in CLAUDE.md):**
```markdown
## Testing
Run all tests: `npm test`
Run with coverage: `npm test:coverage`
E2E tests: `npm run test:e2e`

Before PR:
1. Run lint
2. Run tests
3. Build
```

**After (in `.claude/commands/test.md`):**
```markdown
# Test

Run the test suite for this project.

## Quick Test
npm test

## Full Test Workflow
1. Run linting: `npm run lint`
2. Run unit tests: `npm test`
3. Run E2E tests: `npm run test:e2e`
4. Check coverage: `npm test:coverage`

## Before PR Checklist
- [ ] All tests pass
- [ ] Coverage meets threshold
- [ ] No linting errors
```

### Creating Skills (Category 4)

For reusable patterns, create skill directories:

**Before (in CLAUDE.md):**
```markdown
## Component Patterns
All components should:
- Use functional components with hooks
- Have a single responsibility
- Include PropTypes or TypeScript types
- Be tested with React Testing Library
[... detailed patterns ...]
```

**After (in `.claude/skills/react-components/SKILL.md`):**
```yaml
---
name: react-components
description: |
  React component patterns and best practices.
  Use when creating or reviewing React components.
---

# React Component Patterns

## Core Principles
- Use functional components with hooks
- Single responsibility per component
- Include TypeScript types for all props

## Component Structure
[... detailed patterns ...]
```

### Creating Rules (Categories 5 & 6)

For conventions and corrections, create rule files:

**Before (in CLAUDE.md):**
```markdown
## Rules
- Never use `any` in TypeScript
- Always handle loading/error states
- Don't commit console.log statements
```

**After (in `.claude/rules/typescript.md`):**
```yaml
---
globs: ["*.ts", "*.tsx"]
---

# TypeScript Rules

- Use `unknown` instead of `any` - narrow the type explicitly
- Always handle loading and error states in async operations
- Remove all console.log statements before committing
```

### Moving to Docs (Long Content)

For detailed documentation that doesn't fit Rules or Skills:

**Before (in CLAUDE.md):**
```markdown
## Architecture
We use a modified MVC pattern with the following structure:
[... 50+ lines of architecture explanation ...]
```

**After (in `docs/architecture.md`):**
```markdown
# Architecture

We use a modified MVC pattern with the following structure:
[... full content ...]
```

### File Naming Guide

| Type | Convention | Example |
|------|------------|---------|
| Commands | `{action}.md` | `test.md`, `deploy.md`, `build.md` |
| Skills | `{name}/SKILL.md` | `react-components/SKILL.md` |
| Rules | `{scope}.md` | `typescript.md`, `api-routes.md` |
| Docs | `{topic}.md` | `architecture.md`, `conventions.md` |

---

## Step 3: Link

### What You're Doing

Replacing the moved content with **Discovery Anchors**—brief pointers that tell Claude where to
find detailed information.

### The Discovery Anchor Pattern

```markdown
## [Topic]
[One-line summary]. See `path/to/file` for details.
```

Or for commands:

```markdown
## [Topic]
Run `/command-name` or see `.claude/commands/command-name.md`.
```

### Before and After Example

**Before Migration (~400 tokens):**
```markdown
# MyProject

A React e-commerce application.

## Tech Stack
- React 18, TypeScript, Tailwind
- Node.js, Express, PostgreSQL

## Project Structure
- src/components/ - React components
- src/api/ - Express routes
- src/db/ - Database utilities

## Testing
Run all tests: `npm test`
Run with coverage: `npm test:coverage`
E2E tests: `npm run test:e2e`

Before PR:
1. Run lint
2. Run tests
3. Build

## Component Patterns
All components should:
- Use functional components with hooks
- Have a single responsibility
- Include PropTypes or TypeScript types
[... 30 more lines ...]

## Our Conventions
- Components go in src/components/{Feature}/
- API routes follow REST conventions
- Database queries use raw SQL, not ORM

## Rules
- Never use `any` in TypeScript
- Always handle loading/error states
- Don't commit console.log statements
```

**After Migration (~100 tokens):**
```markdown
# MyProject

React e-commerce app. React 18, TypeScript, Node.js, PostgreSQL.

## Structure
- `src/components/` - React components
- `src/api/` - Express routes
- `src/db/` - Database utilities

## Commands
- Tests: `/test` or `npm test`
- Build: `npm run build`

## Docs
- Conventions: `docs/conventions.md`
- Architecture: `docs/architecture.md`
```

**Files Created:**
- `.claude/commands/test.md` - Testing workflow
- `.claude/skills/react-components/SKILL.md` - Component patterns
- `.claude/rules/typescript.md` - TypeScript rules
- `docs/conventions.md` - Project conventions

### Verification Checklist

After migration, verify:
- [ ] CLAUDE.md is under 200 tokens (ideally under 100)
- [ ] Every piece of content exists somewhere (nothing lost)
- [ ] Discovery Anchors point to correct files
- [ ] Commands work when invoked (`/test`, etc.)
- [ ] Skills are recognized by Claude

---

## Common Migration Scenarios

### Scenario 1: Greenfield Project

**Situation:** Starting a new project, want to do it right from the start.

**Approach:**
1. Create minimal CLAUDE.md with identity and structure only
2. Create Skills for your standard patterns as you develop them
3. Add Rules as you encounter repeated corrections
4. Create Commands for workflows you repeat

**Starting Template:**
```markdown
# [ProjectName]

[One-sentence description].

## Stack
[Technologies]

## Structure
- `src/` - Source code
- `tests/` - Test files

## Docs
See `docs/` for detailed documentation.
```

### Scenario 2: Brownfield with Small CLAUDE.md

**Situation:** Existing project with a CLAUDE.md under 200 lines that works okay.

**Approach:**
1. Don't migrate yet—if it works, keep it
2. When you want to reuse a pattern, extract just that pattern to a Skill
3. Migrate incrementally as patterns emerge

**Rule:** Only migrate when the pain exceeds the effort.

### Scenario 3: Brownfield with Huge CLAUDE.md

**Situation:** CLAUDE.md has grown to 500+ lines, Claude ignores some instructions.

**Approach:**
1. Full Audit: Tag everything with categories
2. Aggressive Split: Move all Cat 3, 4, 5, 6 content out
3. Keep only Cat 1 (Identity) and Cat 2 (Map) in CLAUDE.md
4. Add Discovery Anchors for everything moved

**Priority Order:**
1. Move Commands first (biggest token savings, easiest)
2. Move Patterns to Skills (enables reuse)
3. Move Corrections to Rules (enables pattern-matching)
4. Move long docs last

### Scenario 4: Team with Conflicting Instructions

**Situation:** Multiple team members have added conflicting instructions to CLAUDE.md.

**Approach:**
1. Audit with the team: Tag and discuss each section
2. Resolve conflicts before splitting
3. Use Skills for shared patterns (version-controlled, reviewable)
4. Use Rules for enforced conventions
5. Keep CLAUDE.md as minimal shared context

**Process:**
1. Create a branch for migration
2. PR review the split content
3. Team agrees on what goes where
4. Merge and update CLAUDE.md

---

## Migration Checklist

### Pre-Migration
- [ ] Backup current CLAUDE.md
- [ ] Count current token size (rough estimate: lines × 5)
- [ ] Decide if full or incremental migration

### During Migration
- [ ] Step 1: Audit complete (all sections tagged)
- [ ] Step 2: Split complete (files created in correct locations)
- [ ] Step 3: Link complete (Discovery Anchors in place)

### Post-Migration
- [ ] CLAUDE.md under 200 tokens
- [ ] All moved content accessible
- [ ] Commands work when invoked
- [ ] Skills recognized by Claude
- [ ] Rules trigger on correct file patterns
- [ ] No information lost

### Validation
- [ ] Start new Claude conversation
- [ ] Verify Claude has project context
- [ ] Test a command (`/test` or similar)
- [ ] Ask Claude about a pattern (should load skill)
- [ ] Edit a file that should trigger a rule

---

## Quick Reference

| What You Have | Where It Goes | How to Link |
|---------------|---------------|-------------|
| Project description | Keep in CLAUDE.md | N/A |
| Directory map | Keep in CLAUDE.md (brief) | N/A |
| Run/build/deploy steps | `.claude/commands/` | `/command-name` |
| How-to patterns | `.claude/skills/` | Claude auto-loads |
| File conventions | `.claude/rules/` | Auto-triggered |
| Corrections | `.claude/rules/` | Auto-triggered |
| Long documentation | `docs/` | `See docs/file.md` |

---

## Summary

1. **Audit**: Tag every section with its Information Category (1-6)
2. **Split**: Move content to proper destinations (Commands, Skills, Rules, Docs)
3. **Link**: Replace moved content with Discovery Anchors

**The Goal:** A lean CLAUDE.md (~100 tokens) that serves as an index, with detailed content
living in the appropriate mechanism for each type of information.

**Remember:** Migrate when the pain exceeds the effort. A working messy CLAUDE.md is better
than a broken organized one.

---

*Previous: [Section 5 - CLAUDE.md Best Practices](05-claudemd-best-practices.md)*
*Next: [Section 7 - Configurable Skills](07-configurable-skills.md)*
