# CLAUDE.md Section Identification

Identify which CLAUDE.md section matches the content being stored.

## Section Table

| Section | Purpose | Content Signals | Common Variants |
|---------|---------|-----------------|-----------------|
| Header | Project identity: name, description | Project name, one-liner, purpose | Top of file (before first `##`) |
| Stack | Core technologies | Language, framework, DB, infra | "Tech Stack", "Technologies", "Technology Stack" |
| Structure | Key directories | Directory paths, file locations, layout | "Project Structure", "Directory Structure", "Layout" |
| Commands | Common dev commands | `pnpm`, `npm`, build/test/deploy commands | "Quick Commands", "Dev Commands", "Development Commands", "Scripts" |
| Docs | Pointers to detailed docs | `docs/`, references, links to external files | "Documentation", "References", "Links" |
| Critical Constraints | Must-follow rules | "Never", "Always", warnings, constraints | "Constraints", "Important", "Rules", "Warnings" |

## Category → Section Mapping

| Category | Recommended Section | If Section Missing |
|----------|--------------------|--------------------|
| Cat 1 (Project Identity) | Header or Stack | Create "Stack" |
| Cat 2 (Codebase Structure) | Structure | Create "Structure" |
| Cat 3 (Operational Commands) | Commands | Create "Commands" |
| Cat 5 (Architectural Guidance) | Docs (pointer) or new section | Create section by topic |
| Cat 6 (Iterative Corrections) | Critical Constraints | Create "Constraints" |

Cat 4 (Reusable Patterns) maps to Skills, not CLAUDE.md — section mapping N/A.

## Matching Logic

1. Parse existing `## ` headings from CLAUDE.md
2. Normalize: lowercase, strip punctuation
3. Match against Common Variants column above
4. If multiple matches: prefer exact match
5. If no match: create new section at template-recommended position

## Template Section Order (for new sections)

When inserting a new section, place it after the last existing section that precedes it in this order:

1. Header (top, before first `##`)
2. `## Stack`
3. `## Structure`
4. `## Commands`
5. `## Docs`
6. `## Critical Constraints`

## When CLAUDE.md Does Not Exist

Recommend creating with minimal template:

```markdown
# [Project Name]
[One-sentence description]

## Stack
## Structure
## Commands
## Docs
```

Place content in the appropriate section per the Category → Section Mapping above.
