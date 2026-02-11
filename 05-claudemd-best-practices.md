# Section 5: CLAUDE.md Best Practices

CLAUDE.md is the most important—and most frequently misused—Claude Code memory mechanism. Because
it's always loaded, every token occupies context window space in every message. This section
provides practical guidance for keeping your CLAUDE.md lean, maintainable, and effective.

---

## The Token Economy Principle

### Why Every Token Matters

CLAUDE.md is included in the system prompt for **every message** in a conversation. The Claude API
is stateless — each request sends the full prompt (system prompt + history + new message). Unlike
Skills or Commands that load on-demand, CLAUDE.md tokens are present in every exchange.

**Tokens processed per conversation:**
```
Total Tokens Processed = CLAUDE.md Tokens × Number of Messages
```

| CLAUDE.md Size | 10 Messages | 50 Messages | 100 Messages |
|----------------|-------------|-------------|--------------|
| 100 tokens | 1,000 | 5,000 | 10,000 |
| 500 tokens | 5,000 | 25,000 | 50,000 |
| 2,000 tokens | 20,000 | 100,000 | 200,000 |

A 2,000-token CLAUDE.md occupies **20x more context window space** than a 100-token CLAUDE.md
over the same conversation.

> **Prompt caching reduces financial cost, not context pressure**
>
> Claude Code automatically uses [prompt caching](https://platform.claude.com/docs/en/build-with-claude/prompt-caching).
> After the first message, stable repeated content (like CLAUDE.md) is read from cache at **10%
> of the base input token price** — a ~90% cost reduction. The first write costs 1.25x base price.
>
> | Message | Financial cost (per token) |
> |---------|--------------------------|
> | First message | 1.25x base (cache write) |
> | Subsequent messages | 0.1x base (cache read) |
>
> **However, cached tokens still occupy context window space.** The context window is the binding
> constraint — not price. A bloated CLAUDE.md crowds out room for conversation history, tool
> results, and reasoning. This is why minimizing CLAUDE.md remains critical even with caching.

### The Target

**Keep CLAUDE.md under 100-200 tokens.**

This isn't arbitrary—it's the sweet spot where you have enough context to orient Claude without
paying excessive token costs. Everything beyond essential identity should live elsewhere.

### What This Means Practically

Ask yourself: "Does Claude need this in *every single message exchange*?"

- **Yes** → Keep in CLAUDE.md (but keep it brief)
- **No** → Move to Skills, Rules, Commands, or external docs

---

## The Pointer Pattern

### CLAUDE.md Is a Table of Contents, Not the Book

Think of CLAUDE.md as a **directory** or **index**—not a comprehensive document. It should tell
Claude *what exists and where to find it*, not contain all the details.

### Bad Example (Bloated)

```markdown
# Project: ShopFlow

## Tech Stack
- Next.js 14 with App Router
- TypeScript 5.3 with strict mode
- Prisma ORM with PostgreSQL
- Redis for caching
- Stripe for payments
- SendGrid for emails

## Architecture
We use Hexagonal Architecture with the following structure:
- Domain layer in src/domain/ contains all business logic
- Application layer in src/application/ contains use cases
- Infrastructure layer in src/infrastructure/ contains adapters
- Presentation layer in src/presentation/ contains API routes
[... 50 more lines of architecture details ...]

## Testing
We use Jest for unit tests and Playwright for E2E tests.
To run tests:
- Unit tests: pnpm test
- E2E tests: pnpm test:e2e
- Coverage: pnpm test:coverage
Test files should be colocated with source files using .test.ts extension.
[... 30 more lines of testing conventions ...]

## Coding Standards
- Use functional components with hooks
- No class components
- Prefer named exports
- Use barrel files for public APIs
[... 40 more lines of coding standards ...]
```

**Token count: ~800-1,000 tokens** — loaded in every message.

### Good Example (Pointer Pattern)

```markdown
# ShopFlow

B2B e-commerce platform. Next.js 14, TypeScript, Prisma, PostgreSQL.

## Structure
- `src/domain/` - Business logic
- `src/application/` - Use cases
- `src/infrastructure/` - Adapters
- `src/presentation/` - API routes

## Quick Commands
- Tests: `pnpm test`
- Dev: `pnpm dev`

## Documentation
- Architecture: `docs/architecture.md`
- Testing: `docs/testing.md`
- Conventions: `docs/conventions.md`
```

**Token count: ~80-100 tokens** — 10x smaller, same essential information.

### Discovery Anchors

A **Discovery Anchor** is a brief pointer that tells Claude where to find detailed information
when needed. Claude will read these files when the context requires it.

**Pattern:**
```markdown
## [Topic]
See `path/to/detailed-docs.md` for [what's there].
```

**Examples:**
```markdown
## Architecture
See `docs/architecture.md` for full system design.

## Testing
Run `/test` or see `.claude/commands/test.md` for testing workflow.

## Coding Standards
TypeScript conventions in `.claude/rules/typescript.md`.
```

---

## Recommended Structure

### The Minimal CLAUDE.md Template

```markdown
# [Project Name]

[One-sentence description of what this project does.]

## Stack
[Technology list - keep to essentials]

## Structure
[Key directories only - 3-5 lines max]

## Commands
[Most common commands - 2-3 lines max]

## Docs
[Pointers to detailed documentation]
```

### Section-by-Section Guidance

#### 1. Header (Required)
- Project name
- One-sentence description
- **Keep to 1-2 lines**

#### 2. Stack (Required)
- Core technologies only
- No version numbers unless critical
- **Keep to 1 line if possible**

#### 3. Structure (Required)
- Only the directories Claude needs to navigate
- Skip obvious ones (`node_modules/`, `.git/`)
- **Keep to 3-5 lines**

#### 4. Commands (Optional)
- Only the commands you use constantly
- Full workflows belong in Commands (`.claude/commands/`)
- **Keep to 2-3 lines**

#### 5. Docs (Recommended)
- Pointers to external documentation
- This is where detailed content lives
- **Keep to 2-4 lines**

#### 6. Critical Constraints (If Any)
- "Never modify X"
- "Always check Y before Z"
- Only include if violations are costly
- **Keep to 1-2 lines if needed**

---

## Anti-Patterns to Avoid

### Anti-Pattern 1: Full Architecture Documentation

❌ **Bad:**
```markdown
## Architecture

We use Hexagonal Architecture (also known as Ports and Adapters).

The core principle is that business logic should be isolated from
infrastructure concerns. The domain layer contains pure business rules
with no dependencies on external systems...

[200+ more words explaining hexagonal architecture]
```

✅ **Good:**
```markdown
## Architecture
Hexagonal Architecture. See `docs/architecture.md` for details.
```

**Why:** Architecture explanations are universal knowledge—put them in a Skill. Project-specific
structure goes in CLAUDE.md briefly; details go in external docs.

### Anti-Pattern 2: Complete Test Commands

❌ **Bad:**
```markdown
## Testing

To run all tests:
pnpm test

To run specific tests:
pnpm test -- --grep "pattern"

To run with coverage:
pnpm test:coverage

To run E2E tests:
pnpm test:e2e

To run in watch mode:
pnpm test:watch

Before submitting a PR, always run:
1. pnpm lint
2. pnpm test
3. pnpm build
```

✅ **Good:**
```markdown
## Tests
Run `/test` for full test workflow. Quick: `pnpm test`
```

**Why:** Full testing workflows belong in a Command (`.claude/commands/test.md`). CLAUDE.md
only needs the quick reference.

### Anti-Pattern 3: Inline Code Style Guides

❌ **Bad:**
```markdown
## Code Style

- Use functional components, never class components
- Prefer named exports over default exports
- Use TypeScript strict mode
- No `any` types - use `unknown` and narrow
- Components should have a single responsibility
- Use hooks for state management
- Prefer composition over inheritance
- Use barrel files for public APIs
- File names should be kebab-case
- Component names should be PascalCase
[... continues for 30 more lines ...]
```

✅ **Good:**
```markdown
## Style
TypeScript + React conventions in `.claude/rules/`.
```

**Why:** Code style rules should live in Rules (triggered when editing relevant files) or
external docs. CLAUDE.md just points to them.

> **When to Use Rules vs External Files**
>
> | Content Type | Use Rules | Use External Files |
> |--------------|-----------|-------------------|
> | TypeScript conventions | ✓ (glob: `*.ts`) | |
> | Test file patterns | ✓ (glob: `*.test.*`) | |
> | General coding standards | | ✓ + pointer |
> | Architecture guidelines | | ✓ + pointer |
>
> **Key insight:** Rules only save tokens when they have **specific** glob patterns. General
> content that isn't file-type-specific should use external files with a pointer in CLAUDE.md.

### Anti-Pattern 4: Long Lists of Rules

❌ **Bad:**
```markdown
## Rules

1. Never commit directly to main
2. Always create feature branches
3. Run tests before pushing
4. Use conventional commits
5. No console.log in production code
6. Always handle errors
7. No magic numbers
8. Use constants for repeated values
[... 20 more rules ...]
```

✅ **Good:**
```markdown
## Conventions
See `docs/conventions.md`. Key: conventional commits, feature branches.
```

**Why:** Rules lists grow unbounded. Keep the top 1-2 critical ones inline; everything else
goes to external docs or Rules files.

---

## Self-Assessment Checklist

Use this checklist to evaluate your CLAUDE.md:

### Token Economy
- [ ] Under 200 tokens total?
- [ ] No content that could live in Skills, Rules, or Commands?
- [ ] Using pointer pattern for detailed documentation?

### Structure
- [ ] Clear project identity (name + one-sentence description)?
- [ ] Essential tech stack only?
- [ ] Key directories only (3-5 max)?
- [ ] Common commands only (2-3 max)?

### Content Quality
- [ ] No full architecture documentation inline?
- [ ] No complete style guides inline?
- [ ] No long lists of rules?
- [ ] No step-by-step procedures (those are Commands)?

### Discovery Anchors
- [ ] Pointing to external docs where appropriate?
- [ ] Claude can find detailed information when needed?

---

## Before and After: Complete Example

### Before (Bloated - ~600 tokens)

```markdown
# ShopFlow E-Commerce Platform

ShopFlow is a B2B e-commerce platform designed for wholesale distributors
to manage their product catalogs, process orders, and handle customer
relationships.

## Technology Stack
- **Frontend**: Next.js 14 with App Router, React 18, TypeScript 5.3
- **Styling**: Tailwind CSS 3.4, Radix UI components
- **Backend**: Next.js API Routes, tRPC for type-safe APIs
- **Database**: PostgreSQL 15 with Prisma ORM
- **Caching**: Redis for session storage and query caching
- **Authentication**: NextAuth.js with JWT tokens
- **Payments**: Stripe for payment processing
- **Email**: SendGrid for transactional emails
- **Storage**: AWS S3 for product images
- **Hosting**: Vercel for frontend, Railway for database

## Project Structure
src/
├── app/                 # Next.js App Router pages
│   ├── (auth)/         # Authentication routes
│   ├── (dashboard)/    # Main dashboard routes
│   └── api/            # API routes
├── components/         # React components
│   ├── ui/            # Base UI components
│   ├── forms/         # Form components
│   └── layouts/       # Layout components
├── lib/               # Utility functions
├── hooks/             # Custom React hooks
├── stores/            # Zustand stores
├── types/             # TypeScript types
└── server/            # Server-side code
    ├── db/            # Database utilities
    ├── services/      # Business logic
    └── trpc/          # tRPC routers

## Development Commands
- Start dev server: `pnpm dev`
- Run tests: `pnpm test`
- Run E2E tests: `pnpm test:e2e`
- Lint code: `pnpm lint`
- Format code: `pnpm format`
- Build: `pnpm build`
- Database migrations: `pnpm db:migrate`
- Seed database: `pnpm db:seed`

## Coding Standards
- Always use TypeScript strict mode
- No `any` types allowed
- Use functional components only
- Prefer named exports
- Use Tailwind for styling, no CSS files
- All components must have tests
```

### After (Lean - ~100 tokens)

```markdown
# ShopFlow

B2B e-commerce for wholesale distributors.

## Stack
Next.js 14, TypeScript, Prisma, PostgreSQL, Stripe, Tailwind

## Structure
- `src/app/` - Pages (App Router)
- `src/components/` - React components
- `src/server/` - API & business logic

## Commands
Dev: `pnpm dev` | Tests: `pnpm test` | Build: `pnpm build`

## Docs
- Architecture: `docs/architecture.md`
- API: `docs/api.md`
- Conventions: `docs/conventions.md`
```

**Result:** Same essential information, 6x fewer tokens, better maintainability.

---

## Summary

1. **Token Economy**: Every CLAUDE.md token occupies context window space in every message. Prompt caching reduces the financial cost (~90% after the first message), but the context window pressure remains. Target 100-200 tokens.

2. **Pointer Pattern**: CLAUDE.md is an index, not the book. Point to detailed docs.

3. **Essential Content Only**:
   - Project identity (name + one-liner)
   - Core tech stack
   - Key directories (3-5)
   - Common commands (2-3)
   - Documentation pointers

4. **Avoid Anti-Patterns**:
   - No full architecture docs
   - No complete style guides
   - No long rule lists
   - No step-by-step procedures

5. **Use the Checklist**: Regularly audit your CLAUDE.md against the self-assessment.

---

*Previous: [Section 4 - Two-Phase Decision Guide](04-two-phase-decision-guide.md)*
*Next: [Section 6 - Migration Guide](06-migration-guide.md)*
