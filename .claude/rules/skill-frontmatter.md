---
globs: "**/SKILL.md"
---

Every SKILL.md file MUST begin with YAML frontmatter. The content passed to the Write tool MUST start with "---\n" on line 1.

Required frontmatter fields:
- name: {kebab-case-skill-name}
- description: | followed by indented text
- allowed-tools: comma-separated list

Example of a correct SKILL.md:
---
name: error-handling-patterns
description: |
  Standard error handling patterns for this project. Use when writing try/catch blocks or error boundaries.
allowed-tools: Read, Grep, Glob
---

# Error Handling Patterns

{skill body here}

If you are about to write a SKILL.md and the content does not start with ---, STOP and fix it before calling Write.
