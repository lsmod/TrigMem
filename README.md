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

## License

This method is free and open source under the [GPL v3 license](gpl-3.0.txt).

## Version

0.1.3 Alpha - 12/02/2026 - adds MkDocs and fix bad anchor link

### Previous releases

0.1.2 Alpha - 12/02/2026
0.1.0 Alpha - 05/02/2026

## Author

Arno Trigallez - Copyright© February 2026
