# Decision Support Reference

Resolves destination ambiguity. Read when SKILL.md Steps 2–3 or Step 5 need more detail.

## 5 Dimensions of Storage Mechanisms

| Dimension | Values | Key Question |
|-----------|--------|--------------|
| Portability | Universal / Project-bound / Mixed | Can this be reused across projects? |
| Loading | Always / Pattern-matched / On-demand | When does this enter the context window? |
| Invocation | Always active / Automatic / User-explicit | Who decides when this gets used? |
| Isolation | Main context / Isolated | Where does this run? |
| Composability | High / Medium / Low | How well does this combine with other mechanisms? |

## Mechanism × Dimension Matrix

| Mechanism | Portability | Loading | Invocation | Isolation | Composability |
|-----------|-------------|---------|------------|-----------|---------------|
| CLAUDE.md | Project-bound | Always | Always active | Main | Low |
| Rules | Mixed | Pattern-matched | Automatic | Main | Medium |
| Skills | Universal | On-demand | Auto or User | Main | High |
| Commands | Universal | On-demand | User-explicit | Main | Low |
| Sub-agents | Universal | On-demand | Explicit or Auto | Isolated | Medium |

## Matrix Lookup: Need → Mechanism

| Need | → Mechanism | Key Differentiator |
|------|-------------|-------------------|
| Project-specific context in every conversation | CLAUDE.md | Only always-loaded mechanism |
| Reusable how-to knowledge, auto-invoked | Skills | Universal + on-demand + high composability |
| User-triggered workflow/action | Commands | User-explicit invocation |
| Context isolation or parallel execution | Sub-agents | Only isolated mechanism |
| File-type-specific conventions | Rules | Pattern-matched loading |

## Trade-off Comparisons

| Decision | CLAUDE.md | Rules |
|----------|-----------|-------|
| Loading | Always (tokens every conversation) | Pattern-matched (only when matching files edited) |
| Best when | Critical project-wide context, brief | File-type-specific, targetable with globs |

| Decision | Skills | Commands |
|----------|--------|---------|
| Invocation | Claude can auto-invoke | User must invoke explicitly |
| Best when | Knowledge Claude should use proactively | Actions with side effects, user-controlled |

## Phase 1: Triage Questions

Answer in order. Stop at first "Yes."

| # | Question | If Yes → |
|---|----------|----------|
| Q1 | User-triggered ACTION with side effects? (build, deploy, test) | → Command |
| Q2 | UNIVERSAL how-to knowledge, portable across projects? | → Skill |
| Q3 | Needs CONTEXT ISOLATION or parallel execution? | → Sub-agent |
| Q4 | ALWAYS-NEEDED project context or corrections? | → CLAUDE.md / Rules |
| — | None of the above | → External Docs + Pointer Pattern |

## Phase 2: Refinement per Destination

### Command

| Check | Action |
|-------|--------|
| Simple single action? | Create `.claude/commands/{name}.md` |
| Needs arguments? | Add `argument-hint` frontmatter |
| Has reusable knowledge inside? | Extract to Skill, keep only action steps in Command |

### Skill

| Check | Action |
|-------|--------|
| Truly portable? (no hardcoded project paths) | Create `.claude/skills/{name}/SKILL.md` |
| Has project-specific paths mixed in? | Decompose: universal pattern → Skill, paths → Rules/CLAUDE.md |
| Needs project-specific config? | Use configurable skill pattern (see SKILL.md Notes) |

### Sub-agent

| Check | Action |
|-------|--------|
| Parallel work / independent workstreams? | Spawn specialist sub-agents |
| Complex multi-step task? | Single agent with plan → implement → test |
| Needs specific skills? | Reference via `skills:` frontmatter |
| Note | Sub-agents don't inherit CLAUDE.md, skills, or rules |

### CLAUDE.md / Rules

| Check | Action |
|-------|--------|
| Project identity or high-level structure? | → CLAUDE.md |
| Tied to specific file patterns? | → Rules (with glob patterns) |
| Correction for repeated mistakes? | → Rules (targeted) or CLAUDE.md (if critical/universal) |
| Too long for CLAUDE.md? | → If Skill exists for same topic: Skill companion (`.claude/skills/{name}/reference.md`); otherwise Pointer pattern: brief ref in CLAUDE.md, full content in `docs/` |
| Rules with broad `**/*` globs? | No token savings — use CLAUDE.md instead |
