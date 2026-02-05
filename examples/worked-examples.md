# Worked Examples

This document contains 6 worked examples demonstrating the TrigMem decision process. Each example
walks through Phase 1 (Triage) and Phase 2 (Refinement) to arrive at the correct storage location.

**How to Use These Examples:**
1. Read the scenario
2. Try to decide the destination yourself using the [Two-Phase Decision Guide](../04-two-phase-decision-guide.md)
3. Expand the solution to check your answer
4. Review the rationale to understand the reasoning

---

## Example 1: Project Tech Stack

### Scenario

> "We use PostgreSQL with Prisma as our ORM. The database runs in Docker locally."

**Try it first:** Where does this information go?

<details>
<summary>Click to see solution</summary>

### Phase 1: Triage

**Q1: Is this a user-triggered action with side effects?**
No. This is information, not an action.

**Q2: Is this universal how-to knowledge portable across projects?**
No. This is about *this specific project's* tech stack, not a general pattern.

**Q3: Does this need context isolation or parallel execution?**
No. This is just context information.

**Q4: Is this always-needed project context or corrections?**
**Yes!** This is project identity—Claude needs to know this in every conversation.

→ **Destination: CLAUDE.md**

### Phase 2: Refinement

**Is it project identity or structure?**
Project identity (what technologies we use).

**Is it too long for CLAUDE.md?**
No, it's one line.

### Final Decision

**→ CLAUDE.md**

```markdown
# MyProject
PostgreSQL + Prisma. Docker for local database.
```

### Rationale

This is Category 1 (Project Identity)—essential context that Claude needs in every conversation
to understand the project. It's brief enough to include directly in CLAUDE.md without violating
token economy principles.

</details>

---

## Example 2: Deployment Workflow

### Scenario

> "To deploy to staging: run tests, build the Docker image, push to registry, and trigger
> the Kubernetes deployment."

**Try it first:** Where does this information go?

<details>
<summary>Click to see solution</summary>

### Phase 1: Triage

**Q1: Is this a user-triggered action with side effects?**
**Yes!** This is a multi-step workflow that deploys code—definitely has side effects.

→ **Destination: Command**

### Phase 2: Refinement

**Is it a simple, single action?**
No, it's a multi-step workflow with prerequisites.

**Does it need arguments?**
Possibly—environment (staging vs production) could be an argument.

**Should Claude NEVER auto-invoke this?**
Correct. Commands are user-only by default. This is appropriate for deployment.

### Final Decision

**→ Command** at `.claude/commands/deploy.md`

````markdown
# Deploy

Deploy the application to an environment.

## Usage
/deploy [environment]

## Environments
- staging (default)
- production

## Steps

1. Run tests
   ```bash
   pnpm test
   ```

2. Build Docker image
   ```bash
   docker build -t myapp:latest .
   ```

3. Push to registry
   ```bash
   docker push registry.example.com/myapp:latest
   ```

4. Trigger Kubernetes deployment
   ```bash
   kubectl rollout restart deployment/myapp -n $ENVIRONMENT
   ```

## Pre-flight Checks
- [ ] All tests pass
- [ ] On main branch (for production)
- [ ] CHANGELOG updated
````

### Rationale

This is Category 3 (Operational Commands)—a workflow with significant side effects that the user
should explicitly trigger. Commands are the right mechanism because:
- User controls when deployment happens
- Side effects (deploying to servers) are significant
- Multi-step process benefits from documented procedure

</details>

---

## Example 3: Repository Pattern Implementation

### Scenario

> "How to implement the Repository Pattern: repositories abstract data access, provide collection-like
> interfaces for domain objects, and hide persistence details from the domain layer."

**Try it first:** Where does this information go?

<details>
<summary>Click to see solution</summary>

### Phase 1: Triage

**Q1: Is this a user-triggered action with side effects?**
No. This is knowledge/guidance, not an action.

**Q2: Is this universal how-to knowledge portable across projects?**
**Yes!** The Repository Pattern is a universal design pattern that applies to any project using
similar architecture.

→ **Destination: Skill**

### Phase 2: Refinement

**Is it truly portable?**
Yes. The Repository Pattern concept applies to any project using domain-driven design,
regardless of language or framework.

**Should Claude auto-invoke it?**
Yes, Claude should use this knowledge when the user is working on data access or domain logic.
Default skill behavior (both user and Claude can invoke) is appropriate.

**Is it configurable?**
Could be—repository locations vary by project. But the core pattern is universal.

### Final Decision

**→ Skill** at `.claude/skills/repository-pattern/SKILL.md`

````yaml
---
name: repository-pattern
description: |
  Implements the Repository Pattern for data access abstraction.
  Use when creating repositories, working with domain entities,
  or designing data access layers.
allowed-tools: Read, Write, Grep, Glob
---

# Repository Pattern

## Purpose
Guide implementation of the Repository Pattern to abstract data access
from domain logic.

## Core Principles

1. **Abstract Data Access**: Repositories provide a collection-like
   interface for domain objects

2. **Hide Persistence**: Domain layer shouldn't know about databases,
   files, or APIs

3. **Single Responsibility**: Each repository handles one aggregate root

## Implementation Steps

### Step 1: Define Repository Interface
```typescript
interface UserRepository {
  findById(id: string): Promise<User | null>;
  findByEmail(email: string): Promise<User | null>;
  save(user: User): Promise<void>;
  delete(user: User): Promise<void>;
}
```

### Step 2: Implement Repository
[... implementation details ...]
````

### Rationale

This is Category 4 (Reusable Patterns)—universal knowledge that applies across projects.
Skills are ideal because:
- Loaded on-demand (no token cost when not needed)
- Portable (works in any project using this pattern)
- Auto-invokable (Claude uses it when relevant)
- Composable (can be combined with other patterns)

</details>

---

## Example 4: Large Refactoring Task

### Scenario

> "Refactor all 15 API handlers in the `src/api/` folder to use the new error handling pattern.
> Each handler needs updated try/catch blocks and standardized error responses."

**Try it first:** Where does this information go?

<details>
<summary>Click to see solution</summary>

### Phase 1: Triage

**Q1: Is this a user-triggered action with side effects?**
Yes, but this is more about *execution* than a stored workflow.

**Q2: Is this universal how-to knowledge portable across projects?**
No. This is about refactoring *this specific* codebase.

**Q3: Does this need context isolation or parallel execution?**
**Yes!** This is a large, multi-file refactoring task. Benefits from:
- Isolated context (don't clutter main conversation)
- Potentially parallel work (multiple handlers can be updated independently)
- Focused execution (single task, clear completion criteria)

→ **Destination: Sub-agent**

### Phase 2: Refinement

**Why do you need isolation?**
- Large task (15 files)
- Don't want intermediate steps cluttering the main conversation
- Clear handoff: "refactoring complete" with summary of changes

**Does the agent need special context?**
Yes—the error handling pattern. Could reference an error-handling skill.

### Final Decision

**→ Sub-agent** (spawned for this task, not a persistent file)

The user would invoke this as a task, not store it permanently:

```
"Use a sub-agent to refactor all API handlers in src/api/ to use the
new error handling pattern. The pattern is documented in
.claude/skills/error-handling/SKILL.md."
```

Or if this is a recurring task, create an agent definition:

`.claude/agents/refactor-agent.md`
```markdown
# Refactoring Agent

You are a refactoring specialist focused on systematic code updates.

## Context
- Reference the error-handling skill for the target pattern
- Work through files methodically
- Report changes made in each file

## Approach
1. List all files matching the pattern
2. For each file:
   - Read current implementation
   - Apply the new pattern
   - Verify changes compile
3. Summarize all changes
```

### Rationale

This is a complex, multi-file task that benefits from context isolation:
- Main conversation stays clean
- Agent can work through files systematically
- Clear completion handoff with summary
- Could potentially parallelize across multiple agents

This isn't a "storage" decision per se—it's recognizing that sub-agents are the right
*execution mechanism* for this type of work.

</details>

---

## Example 5: Coding Style Preferences (Edge Case)

### Scenario

> "We prefer functional components over class components. Use arrow functions for handlers.
> Destructure props in the function signature. Keep components under 200 lines."

**Try it first:** This one is tricky—where does this information go?

<details>
<summary>Click to see solution</summary>

### Phase 1: Triage

**Q1: Is this a user-triggered action with side effects?**
No. These are preferences/guidelines.

**Q2: Is this universal how-to knowledge portable across projects?**
Partially. "Prefer functional components" is universal React knowledge, but "keep under 200 lines"
is a project-specific convention.

**Q3: Does this need context isolation or parallel execution?**
No.

**Q4: Is this always-needed project context or corrections?**
Depends on interpretation...

→ **This is ambiguous—could go multiple places!**

### The Ambiguity

This content mixes multiple categories:
- "Prefer functional components" → Could be Category 4 (Reusable Patterns) — universal React knowledge
- "Keep under 200 lines" → Could be Category 5 (Architectural Guidance) or Category 6 (Iterative Corrections)
- All of it could go in Rules triggered by `.tsx` files

### Phase 2: Analyze Each Piece

| Preference | Category | Best Destination |
|------------|----------|------------------|
| Functional over class | Universal React knowledge | Could be Skill or Rule |
| Arrow functions for handlers | Convention | Rule (triggered by React files) |
| Destructure props | Convention | Rule (triggered by React files) |
| Under 200 lines | Project convention | Rule (triggered by all files) |

### Final Decision

**→ Rules** at `.claude/rules/react-components.md`

```yaml
---
globs: ["*.tsx", "*.jsx"]
---

# React Component Guidelines

When working with React components:

- Use functional components (not class components)
- Use arrow functions for event handlers
- Destructure props in the function signature
- Keep components under 200 lines—split if larger
```

### Why Rules?

1. **Pattern-triggered**: These guidelines apply when editing React files
2. **Contextual**: Loaded only when relevant (editing `.tsx` files)
3. **Project-specific**: While some are universal React practices, they're codified
   as *this project's* conventions

### Alternative: Split It

If some guidelines are truly universal (and you want to share them):
- Universal patterns → Skill (e.g., `react-best-practices`)
- Project conventions → Rules (e.g., `react-components.md`)

**Example Skill** at `.claude/skills/react-best-practices/SKILL.md`:

````yaml
---
name: react-best-practices
description: |
  Universal React component patterns and best practices.
  Use when writing or reviewing React components.
allowed-tools: Read, Write, Grep, Glob
---

# React Best Practices

## Functional Components

Prefer functional components over class components:
- Simpler to read and test
- Better performance with React hooks
- Easier to share logic between components

## Event Handlers

Use arrow functions for event handlers:
```tsx
// Good
const handleClick = () => { ... }

// Avoid
function handleClick() { ... }
```

## Props Destructuring

Destructure props in the function signature:
```tsx
// Good
const Button = ({ label, onClick, disabled }: ButtonProps) => { ... }

// Avoid
const Button = (props: ButtonProps) => { ... }
```
````

**Example Rules** at `.claude/rules/react-components.md` (referencing the skill):

````yaml
---
globs: ["*.tsx", "*.jsx"]
---

# React Component Conventions

For universal React patterns, see the `react-best-practices` skill.

Project-specific conventions for this codebase:

- Keep components under 200 lines—split into smaller components if larger
- Place shared components in `src/components/shared/`
- Use the naming convention `ComponentName.tsx` (PascalCase)
````

### Rationale

This edge case shows that:
1. Content can mix categories
2. Rules are good for file-specific conventions
3. When in doubt, ask: "When should this load?" (always → CLAUDE.md, file pattern → Rules)

</details>

---

## Example 6: Testing Fixtures (Edge Case)

### Scenario

> "In test files, always use fixtures from `tests/fixtures/`. Never create inline test data.
> Import fixtures at the top of the test file."

**Try it first:** Another tricky one—where does this go?

<details>
<summary>Click to see solution</summary>

### Phase 1: Triage

**Q1: Is this a user-triggered action with side effects?**
No. This is a testing convention.

**Q2: Is this universal how-to knowledge portable across projects?**
No. The fixture location (`tests/fixtures/`) is project-specific.

**Q3: Does this need context isolation or parallel execution?**
No.

**Q4: Is this always-needed project context or corrections?**
Yes, but only when writing tests.

→ **Destination: Rules** (pattern-triggered)

### Phase 2: Refinement

**Is it tied to specific file patterns?**
Yes! This applies when editing test files (`.test.ts`, `.spec.ts`, etc.)

**Is it a correction for repeated mistakes?**
Partially—"never create inline test data" suggests Claude has done this wrong before.

### Final Decision

**→ Rules** at `.claude/rules/testing.md`

````yaml
---
globs: ["*.test.ts", "*.test.tsx", "*.spec.ts", "*.spec.tsx"]
---

# Testing Conventions

When writing or modifying tests:

- Use fixtures from `tests/fixtures/` for test data
- Never create inline test data objects
- Import fixtures at the top of the test file

Example:
```typescript
import { mockUser, mockOrder } from 'tests/fixtures/users';

describe('OrderService', () => {
  it('should process order', () => {
    const result = processOrder(mockUser, mockOrder);
    // ...
  });
});
```
````

### Why Not CLAUDE.md?

While you could put "we use fixtures" in CLAUDE.md, it's more effective in Rules because:
- Loads only when editing test files (token economy)
- Appears at the right moment (contextual)
- Can include specific examples

### Why Not a Skill?

The fixture pattern is project-specific (the path `tests/fixtures/`). A skill would need to be
configurable to be portable.

### Rationale

This is Category 6 (Iterative Corrections) combined with Category 5 (Architectural Guidance).
Rules are ideal because they trigger automatically when editing test files, ensuring the guidance
appears when it's most relevant.

</details>

---

## Quick Reference: Pattern Matching

| If the content is about... | Consider... |
|----------------------------|-------------|
| What the project is | CLAUDE.md |
| Where things are | CLAUDE.md (brief) or Rules |
| How to run/build/deploy | Command |
| Universal patterns/methods | Skill |
| Project-specific conventions | Rules |
| "Don't do X" corrections | Rules |
| Large multi-file tasks | Sub-agent |
| Detailed reference docs | External docs + pointer |

---

## Self-Check Questions

After working through an example, ask yourself:

1. **Did Phase 1 identify the right destination?**
   - If Q1 was Yes → Command
   - If Q2 was Yes → Skill
   - If Q3 was Yes → Sub-agent
   - If Q4 was Yes → CLAUDE.md / Rules

2. **Did Phase 2 refine the decision correctly?**
   - For CLAUDE.md: Is it brief enough? Use pointer pattern if long.
   - For Commands: Is it user-triggered with side effects?
   - For Skills: Is it truly portable? Consider configurable pattern if not.
   - For Rules: Is it tied to file patterns?

3. **What category is this content?**
   - Cat 1: Project Identity → CLAUDE.md
   - Cat 2: Codebase Structure → CLAUDE.md
   - Cat 3: Operational Commands → Command
   - Cat 4: Reusable Patterns → Skill
   - Cat 5: Architectural Guidance → Rules
   - Cat 6: Iterative Corrections → Rules

---

*Back to: [Two-Phase Decision Guide](../04-two-phase-decision-guide.md)*
