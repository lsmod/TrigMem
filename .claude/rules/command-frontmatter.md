---
globs: ".claude/commands/**/*.md"
---

Every command file MUST begin with YAML frontmatter. The content passed to the Write tool MUST start with "---\n" on line 1.

Required frontmatter fields:
- description: | followed by indented text describing what this command does

Example of a correct command file:
---
description: |
  Create a pull request following our standard format. Use when submitting code for review.
---

# Create PR

{command body here}

If you are about to write a command file and the content does not start with ---, STOP and fix it before calling Write.
