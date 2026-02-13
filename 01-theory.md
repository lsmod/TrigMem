# Section 1: Why It Works — The Theory

This section introduces the foundational concepts behind TrigMem. Understanding these goals and
dimensions will help you make better decisions about where to store information—and more importantly,
*why* those decisions matter.

> **Quick Start Path**
>
> If you want to jump straight to practical decisions, you can skip to the
> [Two-Phase Decision Guide](04-two-phase-decision-guide.md) now and return here when you want
> deeper understanding. However, reading this section first provides valuable context.

---

## The Three Core Goals

Every storage decision is a trade-off between three competing goals. Understanding these helps you
make informed choices—especially when the simpler decision guides don't give a clear answer.

### Goal 1: Context Economy

**Minimize token consumption to maximize effectiveness.**

The AI's context window is a finite, precious resource. Every token loaded—whether from CLAUDE.md,
Rules, Skills, or conversation history—is re-sent with every API call. The API is stateless: each
message reconstructs the full prompt (system prompt + conversation history + new message).

```
Total Tokens Processed = Tokens × Messages in Conversation
```

A 500-token CLAUDE.md file is processed:
- 500 tokens in a 1-message conversation
- 5,000 tokens in a 10-message conversation
- 50,000 tokens in a 100-message conversation


**Implications:**

- Keep always-loaded content (CLAUDE.md) as small as possible — it consumes context window space every message
- Use on-demand loading (Skills, Commands) for detailed instructions
- Use the pointer pattern: brief references in CLAUDE.md, full content elsewhere
- Prompt caching mitigates financial cost, but not context window pressure


**The prompt caching nuance:** Claude Code automatically uses [prompt caching](https://platform.claude.com/docs/en/build-with-claude/prompt-caching).
After the first message, cached content (like CLAUDE.md) costs only **10% of the base input price**
on subsequent reads. So while the tokens are still *processed* every message, the *financial cost*
is significantly reduced (~90%) for stable, repeated content. However, the tokens still **occupy
context window space** regardless of caching — which is the more important constraint.

### Goal 2: Precision

**Provide specific, actionable instructions when they're relevant.**

General advice is often ignored; specific steps are followed. The more precise your instructions,
the more reliably Claude will execute them.

**The Precision Hierarchy:**

1. **Vague**: "Write good tests" (often ignored)
2. **General**: "Write unit tests for all functions" (sometimes followed)
3. **Specific**: "Use Jest, mock external dependencies, assert on return values" (usually followed)
4. **Precise**: Step-by-step procedure with examples (reliably followed)

**Implications:**

- Skills should contain detailed, actionable procedures
- Rules should be specific to file types/patterns
- Don't try to be precise about everything in CLAUDE.md (violates Context Economy)

### Goal 3: Reusability

**Write once, use everywhere.**

The best instructions are portable across projects. This prevents:
- Manual copying between projects
- "Config drift" where copies diverge over time
- Redundant work updating the same patterns repeatedly

**Implications:**

- Separate universal patterns (portable Skills) from project-specific conventions (Rules/CLAUDE.md)
- Don't hardcode paths in Skills—use configuration or let the project define them
- Design Skills to work with any project using the same technology stack

### The Impossible Triangle

These three goals create tension:

```
                    CONTEXT ECONOMY
                         /\
                        /  \
                       /    \
                      /      \
                     /        \
                    /          \
                   /____________\
            PRECISION          REUSABILITY
```

**Trade-offs:**

- **Economy vs. Precision**: More precise instructions = more tokens
- **Precision vs. Reusability**: Project-specific precision kills portability
- **Reusability vs. Economy**: Portable skills may load more than needed

**The TrigMem Solution:**

- Use **multiple mechanisms** with different trade-off profiles
- **CLAUDE.md**: Optimizes for Economy (minimal, always-loaded)
- **Skills**: Optimizes for Reusability + Precision (detailed, on-demand, portable)
- **Rules**: Optimizes for Precision (specific to file patterns)
- **Commands**: Optimizes for Precision + Control (user-initiated)

---

## The 5 Dimensions

Each storage mechanism behaves differently across five key dimensions. Understanding these helps
you predict how your storage choice will affect the Three Core Goals.

### Dimension 1: Portability

**Can this be reused across different projects?**

| Value | Meaning | Example |
|-------|---------|---------|
| **Universal** | Works in any project with same tech | "How to write React hooks" |
| **Project-bound** | Specific to one codebase | "Components are in `src/ui/`" |
| **Mixed** | Can be either, depends on content | Rules for TypeScript (universal) vs. paths (project-bound) |

**Impact on Goals:**

- Universal → High Reusability
- Project-bound → Can be more Precise for that project

### Dimension 2: Loading

**When does this content enter the context window?**

| Value | Meaning | Token Impact |
|-------|---------|--------------|
| **Always** | Loaded every conversation | High (present every message; financial cost reduced ~90% by prompt caching, but context window impact remains) |
| **Pattern-matched** | Loaded when file patterns match | Medium (only when relevant) |
| **On-demand** | Loaded only when explicitly invoked | Low (only when needed) |

**Impact on Goals:**

- Always → Hurts Economy, but always available
- On-demand → Great Economy, but requires invocation

### Dimension 3: Invocation

**Who decides when this gets used?**

| Value | Meaning | Control |
|-------|---------|---------|
| **Always active** | Cannot be turned off | No control (CLAUDE.md) |
| **Automatic** | Claude decides based on context | AI-controlled |
| **User-explicit** | Only runs when user invokes | Full user control |

**Impact on Goals:**

- Automatic → More convenient, but may load unexpectedly
- User-explicit → Predictable, but requires user knowledge

> **⚠️ Invocation Is Probabilistic, Not Deterministic**
>
> When a skill's invocation is set to "Automatic" (Claude decides), invocation is
> *probabilistic*—Claude may or may not invoke the skill depending on prompt context,
> conversation history, and description matching. A skill that fires reliably in one
> session may not fire in another. For practical techniques to improve invocation
> reliability, see [Section 7 — Invocation Reliability](07-configurable-skills.md#invocation-reliability).

### Dimension 4: Context Isolation

**Where does this run?**

| Value | Meaning | Effect |
|-------|---------|--------|
| **Main context** | Runs in primary conversation | History visible, may clutter |
| **Isolated** | Runs in separate sub-agent | Clean handoff, parallel-capable |

**Impact on Goals:**

- Main context → Simple, but consumes main context budget
- Isolated → Clean, but adds coordination overhead

### Dimension 5: Composability

**How well does this combine with other mechanisms?**

| Value | Meaning | Example |
|-------|---------|---------|
| **High** | Easily combines with others | Skills calling other Skills |
| **Medium** | Some combination possible | Rules + CLAUDE.md context |
| **Low** | Standalone operation | Commands execute independently |

**Impact on Goals:**

- High composability → Flexible, but may increase complexity
- Low composability → Simple, but limited integration

---

## The Mechanism × Dimension Matrix

This matrix is your reference tool for understanding how each mechanism behaves.

| Mechanism | Portability | Loading | Invocation | Isolation | Composability |
|-----------|-------------|---------|------------|-----------|---------------|
| **CLAUDE.md** | Project-bound | Always | Always active | Main | Low |
| **Rules** | Mixed¹ | Pattern-matched | Automatic | Main | Medium |
| **Skills** | Universal² | On-demand | Auto or User | Main | **High** |
| **Commands** | Universal² | On-demand | **User-explicit** | Main | Low |
| **Sub-agents** | Universal² | On-demand | Explicit or Auto | **Isolated** | Medium |

**Footnotes:**

1. Rules can be universal (e.g., TypeScript standards) or project-specific (e.g., local paths).
   They become project-bound when they contain hardcoded project conventions.
2. Skills, Commands, and Sub-agents are portable *by design*. Their portability depends on
   whether you hardcode project-specific details (which you shouldn't).

### Reading the Matrix

**Example 1: "I need project-specific context available in every conversation"**

- Need: Project-bound + Always loaded + Always active
- → **CLAUDE.md** (only mechanism that fits)

**Example 2: "I want reusable how-to knowledge that Claude uses automatically"**

- Need: Universal + On-demand + Auto-invoked + High composability
- → **Skill** (best fit across all dimensions)

**Example 3: "I want a deployment workflow only I can trigger"**

- Need: Any portability + On-demand + User-explicit
- → **Command** (user-explicit is the key differentiator)

**Example 4: "I need to run a complex task without cluttering my conversation"**

- Need: Any + On-demand + Any + Isolated
- → **Sub-agent** (only mechanism with isolation)


> **Scope Note: Claude Code Hooks**
>
> Claude Code also provides **hooks**—lifecycle callbacks (PreToolUse, PostToolUse,
> SubagentStart, SubagentStop, etc.) that run shell commands at specific points during
> a conversation. Hooks enable powerful automation: re-injecting context, preventing
> dangerous commands, auto-formatting files, or validating tool arguments.
>
> Hooks are **out of scope** for TrigMem because they address *lifecycle action automation*,
> not *what Claude stores and remembers*. TrigMem focuses on memory placement—where
> information lives so Claude can retrieve it. Hooks complement memory management (e.g., a
> hook could auto-format code after a skill runs) but are not themselves a memory mechanism.
---

## Applying Theory to Decisions

### When the Decision Guide Is Clear

For most cases, the [Two-Phase Decision Guide](04-two-phase-decision-guide.md) gives you a clear
answer. Use it first—it encapsulates this theory into practical questions.

### When You Need Theory

Use this section when:

1. **Edge cases**: The decision guide doesn't clearly point to one destination
   - Use the Matrix to compare trade-offs
   - Optimize for your most important goal (Economy, Precision, or Reusability)

2. **Validating decisions**: You want to confirm your choice is sound
   - Check the Matrix dimensions
   - Verify the mechanism matches your requirements

3. **Designing new patterns**: You're creating skills or extending the method
   - Use the Three Core Goals to guide your design
   - Ensure you're not accidentally sacrificing one goal entirely

### Trade-off Examples

**"Should this rule be in CLAUDE.md or a Rule file?"**

| Factor | CLAUDE.md | Rule |
|--------|-----------|------|
| Loading | Always (costs tokens) | Pattern-matched (efficient) |
| Trigger | Every conversation | Only when matching files edited |

→ If the rule applies to specific file types, use a Rule. If it's critical project-wide context
that Claude needs constantly, use CLAUDE.md (but keep it brief).

**"Should this be a Skill or a Command?"**

| Factor | Skill | Command |
|--------|-------|---------|
| Invocation | Claude can auto-invoke | User must invoke |
| Best for | Knowledge Claude should use proactively | Actions with side effects |

→ If Claude should decide when to apply this knowledge, use a Skill. If the user should
control when the action runs, use a Command.

---

## Summary

### The Three Core Goals
1. **Context Economy**: Minimize tokens, maximize effectiveness
2. **Precision**: Specific, actionable instructions
3. **Reusability**: Write once, use everywhere

### The 5 Dimensions
1. **Portability**: Universal vs. project-bound
2. **Loading**: Always vs. pattern-matched vs. on-demand
3. **Invocation**: Always active vs. automatic vs. user-explicit
4. **Context Isolation**: Main context vs. isolated
5. **Composability**: How well it combines with other mechanisms

### The Key Insight
No single mechanism optimizes all three goals. TrigMem uses multiple mechanisms, each with
different trade-off profiles, to let you choose the right tool for each type of information.

For practical decisions, use the [Two-Phase Decision Guide](04-two-phase-decision-guide.md).
Return here when you need to understand *why* a decision is correct or handle edge cases.

---

*Next: [Section 2 - What You're Storing](02-what-youre-storing.md)*
