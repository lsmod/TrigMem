# Category Reference

Resolves category classification ambiguity. Read only when SKILL.md Step 1 is unclear.

## Self-Assessment Questions

| Question | If Yes → |
|----------|----------|
| About WHAT the project is? (name, purpose, stack) | Cat 1: Project Identity |
| About WHERE things are? (dirs, file locations) | Cat 2: Codebase Structure |
| A user-triggered ACTION with side effects? (build, test, deploy) | Cat 3: Operational Commands |
| UNIVERSAL how-to knowledge, portable across projects? | Cat 4: Reusable Patterns |
| How WE specifically do it HERE? (project paths, local conventions) | Cat 5: Architectural Guidance |
| A DON'T-DO-THAT-AGAIN correction? | Cat 6: Iterative Corrections |

## Cat 4 vs Cat 5: The Critical Distinction

The most common misclassification. Key signal: **portability**.

| Signal | Cat 4 (Reusable Pattern) | Cat 5 (Architectural Guidance) |
|--------|--------------------------|-------------------------------|
| Scope | Any project with same tech | THIS project only |
| Contains paths? | No project-specific paths | Has project-specific paths |
| Key test | "Portable across projects?" → Yes | "Includes THIS project's paths?" → Yes |
| Destination | Skills | Rules / CLAUDE.md |
| Example | "Hexagonal Architecture separates domain from infrastructure" | "Domain goes in `src/domain/`, adapters in `src/infra/`" |

**Mixed content resolution:** If content mixes universal pattern (Cat 4) + project paths (Cat 5), decompose:
- Universal pattern → Skill
- Project paths → Rule or CLAUDE.md
- Or use configurable skill pattern (see SKILL.md Notes)

## Ambiguous Case Resolution

| Content | Seems Like | Resolution |
|---------|-----------|------------|
| "We use TypeScript with strict mode" | Cat 1 or Cat 6 | Core tech stack → Cat 1 (CLAUDE.md). Compiler-specific rules → Cat 6 (Rules for `.ts`) only if Claude keeps getting it wrong. |
| "Run tests with `pnpm test`" | Cat 3 or Cat 1 | Simple command mention → Cat 1 (CLAUDE.md: "Tests: `pnpm test`"). Full test workflow with steps → Cat 3 (Command). |
| "Components should have single responsibility" | Cat 4 or Cat 6 | Claude already knows this → Cat 6 (correction, triggered when relevant). Teaching specific methodology → Cat 4 (Skill). |

**General rule:** If Claude already knows the concept, it's a correction (Cat 6). If teaching a new methodology/pattern, it's a reusable pattern (Cat 4).
