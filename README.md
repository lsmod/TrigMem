# TrigMem - Trigallez's Memory Management Method

**WARNING: Alpha Version** - This method is under active development.

## Introduction

TrigMem is a methodology for organizing Claude Code's memory and context effectively. It provides a systematic approach to deciding what information to store, where to store it, and how to balance the competing concerns of context economy, precision, and reusability.

## The Problem

If you use Claude Code, you've probably encountered these pain points:

- **Where should this go?** CLAUDE.md? A Rule? A Skill? A Command?
- **No clear decision criteria**: Without guidance, your choices feel arbitrary and inconsistent
- **You mix universal patterns with project-specific paths** and kill reusability - your Skills and Rules can't travel between projects
- **Your context window is finite** - wrong placement wastes tokens and reduces effectiveness

## The Solution

TrigMem addresses these problems through:

1. **Clear categorization**: 6 distinct information types, each with different characteristics and optimal storage locations
2. **Multiple storage mechanisms**: Each optimized for different trade-offs between persistence, visibility, and token cost
3. **Two-phase decision guide**: A practical triage → refinement process for making storage decisions
4. **Explicit trade-off framework**: Balance Context Economy, Precision, and Reusability intentionally

## Getting Started

Ready to bring order to your Claude Code memory management?

### **[>>Start here<<](01-theory.md)** - Understand the core concepts and trade-offs, then follow the path through the method.

## Examples

See the method in practice with worked examples:

- [examples/worked-examples.md](examples/worked-examples.md) - Real scenarios showing the decision process

## Installation

Install the TrigMem skill bundle (decision skill + store command + rules) into your project:

    npx skills add lsmod/trigmem

> **Note:** The `skills` CLI installs the `TrigMem:decision` skill automatically. You may need
> to copy commands and rules manually if they are not included.

**Manual installation** (if commands/rules are not copied automatically):

    BASE=https://raw.githubusercontent.com/lsmod/trigmem/master
    curl --create-dirs -fsSL $BASE/.claude/commands/TrigMem/store.md -o .claude/commands/TrigMem/store.md
    curl --create-dirs -fsSL $BASE/.claude/rules/skill-frontmatter.md   -o .claude/rules/skill-frontmatter.md
    curl --create-dirs -fsSL $BASE/.claude/rules/command-frontmatter.md -o .claude/rules/command-frontmatter.md

The rules files enforce frontmatter on all skill and command files — they are required for the
`/TrigMem:store` command's frontmatter constraint to work correctly.

## License

TrigMem uses dual licensing:

- **Documentation** (all `.md` files in `method/` except under `.claude/`): [GPL v3](gpl-3.0.txt)
- **Skills, Commands & Rules** (all files under `method/.claude/`): [LGPL v3](.claude/LICENSE)

The LGPL v3 license for skills/commands/rules allows you to use and integrate TrigMem's
automation components into projects with less restrictive licenses, without requiring your
project to be GPL-licensed.

## Version

0.2.1 Beta - 06/03/2026 - dual licensing + skills CLI installation

### Previous releases

0.1.3 Alpha - 12/02/2026 - adds MkDocs and fix bad anchor link
0.1.2 Alpha - 12/02/2026
0.1.0 Alpha - 05/02/2026

## Author

Arno Trigallez - Copyright© February 2026
