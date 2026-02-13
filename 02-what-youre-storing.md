# Section 2: What You're Storing

Before choosing *where* to put information, you must first identify *what* it is. Most confusion comes
from mixing different types of information together—for example, embedding universal architectural
patterns alongside project-specific folder paths. This section helps you categorize your content so
later sections can point you to the right destination.

---

## The 6 Information Categories

Every piece of information you want to store falls into one of six distinct categories. Understanding
these categories is the foundation for making the right storage decision.

| # | Category | Character | Typical Destination |
|---|----------|-----------|---------------------|
| 1 | Project Identity | Unique, rarely changes | CLAUDE.md |
| 2 | Codebase Structure | Unique, helps navigation | CLAUDE.md / Rules |
| 3 | Operational Commands | Procedural, project-specific | Commands |
| 4 | Reusable Patterns | Universal, portable | Skills |
| 5 | Architectural Guidance | Project-specific application | Rules / CLAUDE.md |
| 6 | Iterative Corrections | Reactive, accumulated | Rules / CLAUDE.md |

---

## Category 1: Project Identity

### Description
High-level context about what the project is and its core technology stack. This is the "business
card" of your project—information that rarely changes and provides essential orientation for any AI
assistant.

### Character
**Unique** per project. Rarely changes once established.

### Examples

**Example 1: E-commerce Platform**
```
This is ShopFlow, a B2B e-commerce platform for wholesale distributors.
Built with: Next.js 14, TypeScript, Prisma, PostgreSQL, Stripe.
```

**Example 2: CLI Tool**
```
TrigMem is a methodology and skill collection for organizing Claude Code memory.
Pure Markdown deliverables, no runtime dependencies.
```

**Example 3: Mobile App Backend**
```
PetTracker API - Backend service for pet health monitoring mobile app.
Stack: Go, gRPC, Redis, MongoDB, deployed on GCP Cloud Run.
```

### Destination Hints
→ Almost always goes in **CLAUDE.md** (always-loaded, identity-focused).

---

## Category 2: Codebase Structure

### Description
Knowledge of where things live in *this specific* codebase. This helps Claude navigate your project
without having to re-discover the structure each time.

### Character
**Unique** per project. Helps Claude navigate efficiently.

### Examples

**Example 1: Monorepo Layout**
```
packages/
├── web/          # Next.js frontend
├── api/          # Express backend
├── shared/       # Shared types and utilities
└── mobile/       # React Native app
```

**Example 2: Domain-Driven Design Layout**
```
src/
├── domain/       # Business logic, entities
├── application/  # Use cases, services
├── infrastructure/  # Database, external APIs
└── presentation/ # Controllers, views
```

**Example 3: Simple Project**
```
Components in src/components/
API routes in src/pages/api/
Database migrations in prisma/migrations/
```

### Destination Hints
→ **CLAUDE.md** for high-level map (always needed)

→ **Rules** for directory-specific conventions (triggered when working in those areas)

---

## Category 3: Operational Commands

### Description
Instructions on how to build, test, deploy, and run the project. These are the "verbs"—actions
that execute specific workflows.

### Character
**Procedural** but project-specific. Step-by-step procedures with side effects.

### Examples

**Example 1: Development Workflow**
```
To start development:
1. pnpm install
2. docker compose up -d
3. pnpm run dev
```

**Example 2: Deployment Process**
```
Deploy to staging:
1. Run tests: pnpm test
2. Build: pnpm build
3. Deploy: ./scripts/deploy-staging.sh
4. Smoke test: curl https://staging.example.com/health
```

**Example 3: Database Operations**
```
Reset database:
1. docker compose down -v
2. docker compose up -d postgres
3. pnpm prisma migrate reset --force
4. pnpm prisma db seed
```

### Destination Hints
→ **Commands** for user-initiated workflows (explicit invocation)

→ **CLAUDE.md** for frequently-needed quick commands (just the essential ones)

---

## Category 4: Reusable Patterns

### Description
Universal "how-to" knowledge that applies across many projects using the same technology or
methodology. This is the **concept**—the pattern itself, independent of any specific implementation.

### Character
**Universal**. Portable across projects using the same stack.

### Examples

**Example 1: Hexagonal Architecture Pattern**
```
Hexagonal Architecture separates business logic from infrastructure:
- Domain layer contains pure business rules
- Ports define interfaces the domain needs
- Adapters implement ports for specific technologies
- Dependencies point inward (adapters depend on ports, not vice versa)
```

**Example 2: React Custom Hook Pattern**
```
Custom hooks encapsulate reusable stateful logic:
- Name starts with "use"
- Can call other hooks
- Returns values and functions the component needs
- Follows the Rules of Hooks
```

**Example 3: Test-Driven Development Pattern**
```
TDD cycle:
1. Write a failing test for the new behavior
2. Write minimal code to make the test pass
3. Refactor while keeping tests green
4. Repeat
```

### Destination Hints
→ **Skills** (reusable, on-demand, can be shared across projects)

---

## Category 5: Architectural Guidance

### Description
How a universal pattern is implemented *specifically in this project*. This is the **convention**—
the project-specific decisions about file locations, naming, and structure.

### Character
**Project-specific** application of universal patterns.

### Examples

**Example 1: Hexagonal Implementation (This Project)**
```
In this project, Hexagonal Architecture is implemented as:
- Domain: src/domain/
- Ports: src/domain/ports/
- Adapters: src/infrastructure/adapters/
- Use cases: src/application/
```

**Example 2: Component Conventions (This Project)**
```
Our component file structure:
- ComponentName/
  ├── index.tsx         # Main component
  ├── ComponentName.test.tsx
  ├── ComponentName.styles.ts
  └── types.ts
```

**Example 3: API Patterns (This Project)**
```
API endpoint conventions:
- Routes: src/pages/api/v1/{resource}/[id].ts
- Handlers: src/services/{resource}Service.ts
- Validators: src/validators/{resource}Schema.ts
```

### Destination Hints
→ **Rules** for directory-specific conventions (load when relevant)

→ **CLAUDE.md** for project-wide conventions (if brief and always needed)

→ **Linked file** for detailed conventions (e.g., `See docs/architecture.md` in CLAUDE.md)

---

## Category 6: Iterative Corrections

### Description
"Remember this" fixes accumulated when Claude makes repeated mistakes. These are reactive learnings
that prevent the same errors from recurring.

### Character
**Reactive**. Accumulated learnings from experience.

### Examples

**Example 1: Language/Framework Quirks**
```
Don't use 'any' in TypeScript - use 'unknown' and narrow the type.
```

**Example 2: Project-Specific Gotchas**
```
The 'user' table has a trigger - never INSERT directly, use the create_user() function.
```

**Example 3: Tool-Specific Behavior**
```
When using pnpm, always run 'pnpm install' after switching branches.
```

### Destination Hints
→ **Rules** for file-type-specific corrections (e.g., TypeScript rules for `.ts` files)

→ **CLAUDE.md** for project-wide corrections (if critical and always needed)

---

## The Critical Distinction: Category 4 vs. Category 5

The most important separation to understand is between **Reusable Patterns** (Cat 4) and
**Architectural Guidance** (Cat 5).

### The Problem
When you mix these categories, you kill reusability:

```markdown
# BAD: Mixed Categories
When implementing Hexagonal Architecture:
- Put domain in src/domain/           ← This is Cat 5 (project-specific)
- Put ports in src/ports/             ← This is Cat 5 (project-specific)
- Domain should have no dependencies  ← This is Cat 4 (universal)
```

If you share this "skill" with another project, those paths won't work.

### The Solution
Separate the **Universal How** (Cat 4) from the **Local Where** (Cat 5):

```markdown
# GOOD: Category 4 (Skill - Portable)
Hexagonal Architecture separates business logic from infrastructure:
- Domain layer contains pure business rules
- Ports define interfaces the domain needs
- Adapters implement ports
```

```markdown
# GOOD: Category 5 (Rule - Project-Specific)
Our Hexagonal implementation:
- Domain: src/domain/
- Ports: src/domain/ports/
- Adapters: src/infrastructure/
```

Now the skill is reusable, and the conventions are project-local.

> **Experimental Alternative:** [Configurable Skills](07-configurable-skills.md) offer a way to get
> the best of both worlds—keeping universal patterns portable while allowing project-specific
> configuration within the same skill file.

---

## Self-Assessment: What Category Is My Content?

Use these questions to identify what you're storing:

### Question 1: Is this about WHAT my project is?
**Yes** → **Category 1: Project Identity**

- Project name, purpose, tech stack
- Rarely changes
- Anyone new needs to know this immediately

### Question 2: Is this about WHERE things are in my codebase?
**Yes** → **Category 2: Codebase Structure**

- Directory structure, file locations
- Project-specific navigation
- Helps Claude find things

### Question 3: Is this about HOW TO DO something (action/workflow)?
**Yes, and it has side effects** → **Category 3: Operational Commands**

- Build, test, deploy, run
- Step-by-step procedures
- User initiates these actions

**Yes, and it's universal knowledge** → **Category 4: Reusable Patterns**

- Could apply to any project with this tech stack
- The concept, not the convention
- "How to implement X pattern"

**Yes, and it's how we specifically do it here** → **Category 5: Architectural Guidance**

- Project-specific implementation details
- File paths, naming conventions, local decisions
- "In THIS project, we put X in Y"

### Question 4: Is this a correction for a repeated mistake?
**Yes** → **Category 6: Iterative Corrections**

- "Don't do X" or "Always do Y"
- Accumulated from experience
- Prevents recurring errors

---

## Decision Flow: Ambiguous Cases

Some information could fit multiple categories. Here's how to resolve ambiguity:

### "We use TypeScript with strict mode"
- **Identity** (Cat 1) if it's part of defining the project
- **Correction** (Cat 6) if it's reminding Claude of a standard

→ **Resolution**: Put core tech stack in Cat 1 (CLAUDE.md). Add specific compiler rules to Cat 6
(Rules for `.ts` files) only if Claude keeps getting it wrong.

### "Run tests with `pnpm test`"
- **Operational Command** (Cat 3) if it's a workflow step
- **Identity** (Cat 1) if it's just stating the test runner

→ **Resolution**: Simple command mentions go in Cat 1 (CLAUDE.md: "Tests: `pnpm test`"). Full
test workflows go in Cat 3 (Command with pre/post steps).

### "Components should have a single responsibility"
- **Reusable Pattern** (Cat 4) if it's teaching a principle
- **Correction** (Cat 6) if Claude keeps making bloated components

→ **Resolution**: If Claude already knows this, it's a Correction (triggered when relevant). If
you're teaching a specific methodology, it's a Pattern (Skill).

---

## Quick Reference

| Question | Yes Points To |
|----------|---------------|
| Is this about *what* my project is? | Cat 1: Project Identity |
| Is this about *where* things are? | Cat 2: Codebase Structure |
| Is this a *command/action* with side effects? | Cat 3: Operational Commands |
| Is this *universal how-to* knowledge? | Cat 4: Reusable Patterns |
| Is this *our specific implementation*? | Cat 5: Architectural Guidance |
| Is this a *don't do that again* fix? | Cat 6: Iterative Corrections |

---

## Summary

Understanding these six categories is essential before using the Two-Phase Decision Guide:

1. **Project Identity** → What is this project? (CLAUDE.md)
2. **Codebase Structure** → Where is everything? (CLAUDE.md / Rules)
3. **Operational Commands** → How do I run/build/deploy? (Commands)
4. **Reusable Patterns** → Universal how-to knowledge (Skills)
5. **Architectural Guidance** → Our specific implementation (Rules)
6. **Iterative Corrections** → Don't make that mistake again (Rules)

The key insight: **Separate the Universal How (Cat 4) from the Local Where (Cat 5)** to maximize
reusability while keeping project-specific conventions where they belong.

---

*Previous: [Section 1 - Why It Works: The Theory](01-theory.md)*
*Next: [Section 3 - The Storage Options](03-storage-options.md)*
