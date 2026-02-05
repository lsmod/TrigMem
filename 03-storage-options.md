# Section 3: The Storage Options

> **Already familiar with Claude Code storage?**
>
> If you already know how CLAUDE.md, Rules, Skills, Commands, and Sub-agents work,
> you can [skip to the Two-Phase Decision Guide](04-two-phase-decision-guide.md).
> This section is a reference—come back when you need specifics.

This section provides a complete reference of all memory mechanisms available in Claude Code. With the
theory and categories from Sections 1-2, you can now understand *why* each mechanism is designed the way
it is.

---

## Overview

Claude Code provides **five distinct memory mechanisms**, each designed for specific use cases. The key to
effective AI memory management is understanding when to use each one.

| Mechanism | Primary Purpose | When Loaded |
|-----------|-----------------|-------------|
| CLAUDE.md | Project identity & essential context | Always (every conversation) |
| Rules | Pattern-triggered behavioral guidance | When file patterns match |
| Skills | Reusable procedures & knowledge | On-demand (user or Claude) |
| Commands | User-controlled workflows | Explicit user invocation |
| Sub-agents | Isolated, focused task execution | When spawned for specific tasks |

---

## 1. CLAUDE.md

### Purpose
The CLAUDE.md file serves as the **project identity card**. It provides essential context that Claude needs
in every conversation about your project.

### When Loaded
**Always** - automatically loaded at the start of every conversation. This means every token in CLAUDE.md
has a cost multiplied by the number of messages in your conversation.

### File Location
```
project-root/
└── CLAUDE.md
```

Claude Code also checks for CLAUDE.md files in parent directories, creating a hierarchy of context.

### Key Characteristics
- **Always in context**: No invocation needed
- **Project-bound**: Tied to this specific project
- **Token-sensitive**: Keep it concise (target 100-200 tokens)
- **Identity-focused**: Who/what/where, not detailed how-to

### Best For
- Project name and brief description
- Tech stack summary (e.g., "TypeScript + React + PostgreSQL")
- Key directory pointers (e.g., "Source code in `src/`, tests in `tests/`")
- Critical constraints (e.g., "Never modify `legacy/` folder")
- Links to detailed documentation (pointer pattern)

### Anti-Patterns
- Full architecture documentation
- Complete coding style guides
- Long lists of rules
- Step-by-step procedures

---

## 2. Rules

### Purpose
Rules provide **automatic behavioral guidance** that activates when Claude is working with specific file
types or patterns. Think of them as "if working on X, remember Y."

### When Loaded
**Pattern-triggered** - loaded automatically when Claude is working with files that match the rule's
glob pattern.

### File Location
```
project-root/
└── .claude/
    └── rules/
        ├── typescript.md      # Triggered by *.ts files
        ├── tests.md           # Triggered by *.test.* files
        └── api-routes.md      # Triggered by src/api/**
```

### Rule File Format
```markdown
---
globs: ["*.ts", "*.tsx"]
---

# TypeScript Rules

When working with TypeScript files:
- Use strict mode
- Prefer interfaces over types for object shapes
- ...
```

### Key Characteristics
- **Automatic activation**: No user action needed
- **Pattern-scoped**: Only loads when relevant files are accessed
- **Contextual**: Guidance appears when it's most relevant
- **Composable**: Multiple rules can apply simultaneously

### Best For
- File-type-specific coding standards
- Directory-specific conventions
- Pattern-based reminders
- Contextual warnings

### Considerations
- Rules add to context when triggered—keep them focused
- Overlapping patterns can cause multiple rules to load
- Good for "always do this when working on X" guidance

> **⚠️ Warning: Broad Patterns**
>
> Rules with broad globs (e.g., `**/*`, `*`, or no globs at all) load on nearly every file access,
> providing **no token savings over CLAUDE.md**. Use specific patterns like `*.ts` or `src/api/**`.
>
> | Pattern | Effect |
> |---------|--------|
> | `*.ts` | Loads only for TypeScript files ✓ |
> | `src/api/**` | Loads only in API directory ✓ |
> | `**/*` | Loads for all files ✗ (no savings) |
> | *(no globs)* | May load frequently ✗ |

---

## 3. Skills

### Purpose
Skills are **reusable knowledge and procedures** that Claude can invoke when needed. They encapsulate
expertise that applies across many situations.

### When Loaded
**On-demand** - loaded when:
- User explicitly invokes with `/skill-name`
- Claude determines the skill is relevant (auto-invocation)

### File Location
```
project-root/
└── .claude/
    └── skills/
        └── skill-name/
            └── SKILL.md
```

### Skill File Format
```yaml
---
name: skill-name
description: |
  What this skill does.
  Use when [specific triggers], [scenarios], or when user asks [questions].
allowed-tools: Read, Grep, Glob
---

# Skill Name

# Purpose
Brief description of what this skill accomplishes.

# Instructions
Step-by-step instructions Claude follows.

# Configuration (if configurable)
YAML config block with project-specific values.
```

### Key Characteristics
- **Lazy loading**: Only loaded when invoked
- **Reusable**: Can be universal (portable) or project-specific
- **Self-documenting**: Description tells Claude when to use it
- **Composable**: Skills can reference other skills

### Invocation Control

| Frontmatter | User invokes | Claude invokes |
|-------------|--------------|----------------|
| (default) | Yes | Yes |
| `disable-model-invocation: true` | Yes | No |
| `user-invocable: false` | No | Yes |

### Best For
- How-to procedures (e.g., "how to implement X pattern")
- Reusable methodologies (e.g., code review checklists)
- Domain expertise (e.g., security best practices)
- Complex multi-step workflows

---

## 4. Commands

### Purpose
Commands are **user-controlled workflows** with explicit triggers. They represent actions the user
consciously initiates.

### When Loaded
**Explicit invocation only** - loaded when user types `/command-name`.

### File Location
```
project-root/
└── .claude/
    └── commands/
        └── command-name.md
```

### Command File Format
```markdown
# Command Name

Brief description of what this command does.

## Instructions

Steps Claude follows when this command is invoked.
```

### Key Characteristics
- **User-initiated**: Never auto-invoked by Claude
- **Action-oriented**: Typically has side effects (creates files, runs commands)
- **Explicit**: User knows exactly what they're triggering
- **Visible**: Always appears in the `/` menu

### Best For
- Deployment workflows
- Code generation templates
- Project-specific automation
- Actions with significant side effects

---

## 5. Sub-agents

### Purpose
Sub-agents provide **isolated execution contexts** for focused, complex tasks. They run in a separate
context with their own conversation history.

### When Loaded
**When spawned** - created on-demand for specific tasks, typically via the Task tool or explicit
agent configuration.

### File Location
```
project-root/
└── .claude/
    └── agents/
        └── agent-name.md
```

### Key Characteristics
- **Context isolation**: Separate conversation history
- **Focused**: Single responsibility per agent
- **Parallel-capable**: Multiple agents can run simultaneously
- **Clean handoff**: Results returned to parent context

### Best For
- Large refactoring tasks
- Parallel independent work streams
- Tasks requiring clean slate context
- Complex operations benefiting from isolation

---

## Skills and Commands: Understanding the Merger

Recent Claude Code updates have unified the skill and command systems. Here's what you need to know:

### Current Behavior
- **Both live in `.claude/`**: Skills in `skills/`, commands in `commands/`
- **Both use frontmatter**: Same YAML configuration options
- **Invocation differs**: Commands are user-only; skills can be auto-invoked

### Key Distinction
The primary difference is **invocation control**:

| Type | Auto-invocable by Claude | User invocable |
|------|--------------------------|----------------|
| Skill (default) | Yes | Yes |
| Skill (`disable-model-invocation: true`) | No | Yes |
| Command | No | Yes |

### Practical Guidance
- **Use Commands when**: The action has side effects and should only run when user explicitly wants it
- **Use Skills when**: The knowledge/procedure might be useful and Claude should be able to invoke it
- **Use `disable-model-invocation: true`**: When you want skill organization but command-like behavior

---

## Priority and Precedence

When multiple mechanisms provide conflicting guidance, Claude Code follows this general precedence:

### Loading Priority
1. **CLAUDE.md** - Always loaded first, establishes baseline context
2. **Rules** - Loaded when file patterns match, layer on top of CLAUDE.md
3. **Skills/Commands** - Loaded on invocation, most specific context

### Conflict Resolution
- **Later context generally wins**: Skills invoked during a task can override earlier guidance
- **Specificity matters**: More specific guidance (skill for exact task) typically takes precedence
- **Explicit > implicit**: User-invoked commands/skills override auto-loaded rules

### Best Practice
Avoid conflicts by design:
- CLAUDE.md: Identity and pointers (what/where)
- Rules: File-type conventions (how for specific files)
- Skills: Procedures and methods (how to do X)
- Commands: User actions (do X now)

When mechanisms overlap, the most recently loaded, most specific guidance typically prevails.

---

## Quick Reference Table

| Aspect | CLAUDE.md | Rules | Skills | Commands | Sub-agents |
|--------|-----------|-------|--------|----------|------------|
| **Location** | Root | `.claude/rules/` | `.claude/skills/` | `.claude/commands/` | `.claude/agents/` |
| **Loading** | Always | Pattern-match | On-demand | User-invoke | When spawned |
| **Scope** | Project | File patterns | Universal/Project | Project | Task-specific |
| **Context Cost** | Every message | When triggered | When invoked | When invoked | Isolated |
| **Auto-invoke** | N/A | Yes (by pattern) | Yes (optional) | No | No |
| **Best For** | Identity | Conventions | Procedures | Actions | Isolation |

---

## Official Documentation

For authoritative details on each mechanism, see the official Claude Code documentation:

| Mechanism | Official Docs |
|-----------|---------------|
| **CLAUDE.md** | [Memory](https://code.claude.com/docs/en/memory) |
| **Skills** | [Extend Claude with skills](https://code.claude.com/docs/en/skills) |
| **Sub-agents** | [Create custom subagents](https://code.claude.com/docs/en/sub-agents) |
| **Overview** | [Extend Claude Code](https://code.claude.com/docs/en/features-overview) |

> **Note:** Skills and Commands have been merged in recent Claude Code versions. See the Skills
> documentation for current behavior.

---

## Summary

Choosing the right mechanism depends on:

1. **When should this be available?** (always vs. on-demand vs. pattern-triggered)
2. **Who should trigger it?** (automatic vs. user-controlled)
3. **Is context isolation needed?** (main context vs. sub-agent)
4. **Is it reusable across projects?** (portable vs. project-specific)

Now that you understand the mechanisms, the next section brings it all together with a practical
decision framework.

---

*Previous: [Section 2 - What You're Storing](02-what-youre-storing.md)*
*Next: [Section 4 - Two-Phase Decision Guide](04-two-phase-decision-guide.md)*
